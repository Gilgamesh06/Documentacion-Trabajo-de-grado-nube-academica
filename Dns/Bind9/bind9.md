# Bind9

* Bind9 (**BIND - Berkeley Internet Name Domain, versión 9**) es el **servidor DNS más usado y estándar en sistemas Linux/Unix**, ampliamente utilizado en **producción, on-premise, ISPs, data centers y laboratorios Kubernetes/bare-metal**

* En tu contenxto (MicroK8s + MetalLB + NGINX Ingress), **Bind9 es perfecto** para:

    * Asociar un **dominio propio -> IP fija de MetalLB**
    * Controlar DNS **sin depender de Internet**
    * Simular un **entorno de producción real**

    ## Para qué sirve Bind9 ?

    * Bind9 sirve para:

        * Resolver **nombres de domínio -> IP**
        * Administrar **zonas DNS**
        * Ser **DNS autoritativo** de tus dominios
        * Actuar como **DNS recursivo/caché**
        * Delegar domínios y subdominios
        * Resolver DNS internos para cluster y microservicios

    * Ejemplos reales

        ```bash
        app.lab        → 192.168.1.240 (MetalLB / NGINX)
        api.app.lab    → 192.168.1.240
        db.app.lab     → 10.152.183.10 (ClusterIP interno)
        ``` 
    
    ## Funcionamiento

    * Ejemplo: un cliente consulta `app.lab`

    1. **El cliente pregunta a Bind9**

        ```bash
        Quien es app.lab ?
        ```
        * Bind9 recibe la consuta en **puerto 53 (UDP/TCP)**

    2. **Bind9 busca en sus zonas**

        * Si `app.lab` está en una **zona autoritativa** -> responde directamente
        * Si no lo esta:

            * Puede actuar como **recursivo**
            * O reenviar la consutal a otro DNS (forwarders)

    3. **Bind9 responde**

        ```bash
        app.lab -> 192.168.1.240
        ```
        * La respuesta se envía al cliente y se guarda en caché (TTL)
    
    ## Tipos de servidores Bind9

    * **DNS Autoritativo**

        * Fuente oficial del dominio
        * Responde solo por zonas que administra
        * Ejemplo: `app.lab`

    * **DNS Recursivo / Caché**

        * Busca respuestas en Internet
        * Guarda respuestas temporalmente
        * Ejemplo: resolver dominios externos
        
    > Bind9 puede cumplir **ambos roles**

    ## Elementos que componen Bind9

    1. **`named` (el demonio)**

        * Proceso principal de Bind9
        * Escucha en el puerto **53**
        * Gestiona consultas DNS
        * Aplica políticas y seguridad

        ```bash
        systemctl status named
        ```
    
    2. **Archivo principal de configuración**

        * `/etc/bind/named.conf`

            * Archivo raíz que incluye los demas

                ```conf
                include "/etc/bind/named.conf.options";
                include "/etc/bind/named.conf.local";
                include "/etc/bind/named.conf.default-zones";
                ```
    3. **`named.conf.options`**

        * Configuración **global**

            * Ejemplo típico:

                ```conf
                options {
                    directory "/var/cache/bind";

                    recursion yes:
                    allow-query {any;};

                    forwarders {
                        8.8.8.8;
                        1.1.1.1;
                    };

                    dnssec-validation auto;

                }
                ```
                * Funciones:

                    * Recursión
                    * Caché
                    * Forwarders
                    * Seguridad

    4. **`named.conf.local`**

        * Aqui defines **tus zonas propias**

            ```conf
            zone "app.lab" {
                type master;
                file "/etc/bind/zones/db.app.lab";
            };
            ```
    
    5. **Archivos de zona (Zone Files)**

        * `/etc/bind/zones/db.app.lab`

            ```zone
            $TTL 86400

            @   IN  SOA ns.app.lab. admin.app.lab. (
                    2026011601
                    3600
                    1800
                    604800
                    86400
            )

            @       IN NS ns.app.lab.
            ns      IN A  192.168.1.10
            @       IN A  192.168.1.240
            gateway IN A  192.168.1.240
            api     IN A  192.168.1.240
            ```
            * Aqui es donde

                * Dominio -> IP MetalLB
                * Subdominios -> Ingress

    6. **Registros DNS**

        * Bind9 soporta todos los registros estándar

            | Registro  | Función        |
            | --------- | -------------- |
            | **A**     | Dominio → IPv4 |
            | **AAAA**  | Dominio → IPv6 |
            | **CNAME** | Alias          |
            | **MX**    | Correo         |
            | **NS**    | Servidores DNS |
            | **TXT**   | Metadatos      |
            | **SRV**   | Servicios      |
            | **PTR**   | DNS inverso    |

    7. **Zonas directas e inversas**

        * **Zona directa**

            ```bash
            app.lab -> 192.168.1.240
            ```
        
        * **Zona inversa**

            ```bash
            192.168.1.240 -> app.lab
            ```

    8. **Caché DNS**

        * Bind9 guarda respuestas externas
        * Reduce latencia
        * Mejora rendimiento

    9. **Seguridad**

        * Bind9 soporta:

            * ACLs (`allow-query`, `allow-recursion`)
            * TSIG (trasferencias seguras)
            * DNSSEC
            * Rate limiting
            * Views (DNS por red)

        * Ejemplo

            ```conf
            acl trusted {
                192.168.1.0/24;
            };

            options {
                allow-query { trusted; };
            };
            ```

    ## Bind9 en tu arquitectura MicroK8s

    * Donde instalarlo:

        * En el **control-plane**
        * O en una VM externa
        * No instalar dentro del clsuter (Para DNS de entrada)

    * Rol:

        * Resolver **dominio externo -> Ingress**
        * Punto único de entrada DNS

    ## Comparación rápida: Bind vs CoreDNS

    | Característica                | Bind9  | CoreDNS |
    | ----------------------------- | ------ | ------- |
    | Estándar histórico            | ✅     | ❌      |
    | Simulación producción on-prem | ✅     | ⚠️      |
    | Kubernetes interno            | ❌     | ✅      |
    | Fácil para labs               | ⚠️     | ✅      |
    | Archivos de zona              | ✅     | ❌      |

    * Para **entrada al cluster: Bind9**
    * Para **DNS interno de K8s: CoreDNS**

    ## Resumen final

    * **Bind9** es un servidor DNS completo y robusto
    * Traduce dominios -> IPs
    * Administra zonas DNS
    * Ideal para **on-prem / bare-metal**
    * Encaja perfectamente con **MetalLB + NGINX Ingress**