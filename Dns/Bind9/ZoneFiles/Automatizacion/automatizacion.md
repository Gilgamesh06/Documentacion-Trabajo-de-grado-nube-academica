# Automatización

* **Pregunta**

    > Si despliego de forma automaticamente diferentes app cada una con un ingress y con sus host y todos misma IP un solo Ingress NGINX Controller proxy inverso (Type: LoadBalancer) pero quiero que esos subdominios se agregen al bind9 de forma automatica me dijeron que existia algo llamado sidecar.

1. **Tu escenario**

    * Un solo **NGINX Ingress Controller**
    * Tipo **LoadBalancer** (MetalLB)
    * Una sola IP pública/externa
    * Muchas apps
    * Cada app tiene:

        * Su propio `Ingress`
        * Su propio `host`(`app1.lab`, `app2.lab`, etc)
    
    * **Problema**

        * Quieres que **Bind9 agregue esos subdominios automáticamente** cuando se despliega una app.

2. **Que es un sidecar?**

    * Un **sidecar** es un **contenedor adicional** dentro del **mismo Pod** que:

        * Comparte red (`localhost`)
        * Comparte volúmenes
        * Vive y muere con el contenedor principal

    * Ejemplo:

        * App + Envoy
        * App + Log shipper
        * App + Proxy
        * App + Agent

3. **Podira usarse un sidecar para Bind9**

    * Tecnicamente si
    * Arquitectonicamente: No recomedado

    * Porque no ?

        * Cada app tendria lógica DNS
        * Duplicas responsabilidades
        * Riegos de corrupción de zone files
        * Problemas de concurrencia
        * Dificil de auditar
        * No es el rol de la app

    * DNS es infraestructura, no aplicación

4. **Entonces...¿que te dijeron realmente con *sidecar*?**

    * Muchas veces cuando alguien dice *usa un sidecar* en este contexto, **en realidad se refiere a uno de estos patrones**

        * Controller
        * Operator
        * ExternalDNS

    * Y aqui esta **la respuesta correcta**

5. **Lo solución real para tu caso: ExternalDNS**

    * **Que es ExternalDNS?**

        * **ExternalDNS** es un **controller de Kubernetes** que:

            * Observa `Ingress`, `Service`, `Gateway`
            * Lee `hostnames`
            * Crea / actualiza registros DNS automaticamente
            * Soporta **Bind9**

6. **Como funciona ExternalDNS**

    ```yaml
    Ingress creado
        |
        v
    ExternalDNS detecta:
    host: app1.lab
        |
        v
    Actualiza Bind9:
    app1.lab → 192.168.50.100
    ```
    * Sin toncar zona files manualmente
    * Sin reiniciar Bind
    * Sin sidecars raros

7. **Arquitecura correcta**

    ```bash
    Kubernetes
    │
    ├── Ingress NGINX (LoadBalancer)
    │     └── IP MetalLB: 192.168.50.100
    │
    ├── ExternalDNS (Deployment)
    │
    └── Bind9 (DNS)
    ```

8. **Cómo ExternalDNS habla con Bind9?**

    * ExternalDNS puede usar:

        * **RFC2136 (DNS dinámico)**

    * Bind9 permite **actualizaciones dinámicas seguras**

9. **Configuración minima en Bind9**

    * En `named.conf.local`

        ```conf
        zone "lab" {
            type master;
            file "/etc/bind/zones/db.lab";
            allow-update {key externaldns-key; };
        };
        ```
    * **TSIG key**

        ```bash
        tsig-keygen externaldns-key
        ```

10. **ExternalDNS Deployment**

    ```yaml
    args:
    - --source=ingress
    - --domain-filter=lab
    - --provider=rfc2136
    - --rfc2136-host=192.168.50.10
    - --rfc2136-port=53
    - --rfc2136-zone=lab
    - --rfc2136-tsig-secret=externaldns
    ```

11. **Que pasa cuando despliegas una app nueva?**

    * **Ingress nuevo**

        ```yaml
        spec:
            rules:
            - host: app3.lab
        ```
    
    * **Automáticametne**

        ```dns
        app3.lab -> 192.169.50.100
        ```
        * **Plataforma automática real**

12. **Si quieres hacerlo *mas entreprise***

    * Opciones avanzadas

        * ExternalDNS + RBAC
        * ExternalDNS + Múltiples zonas
        * Wildcards (`*.lab`)
        * Cert-manager + ExternalDNS
        * API propia que cree Ingres + DNS

