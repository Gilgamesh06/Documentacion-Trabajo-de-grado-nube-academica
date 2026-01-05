# Ingress

* **Ingress** es un **objeto de Kubernetes** que define **reglas de enrutamiento HTTP/HTTPS** hacia Services internos del clúster

* **Ingress No es un proxy**
* **Ingress No balancea tráfico por si solo**

* Es **solo la configuracion**

* **Definición corta**

    > Ingress describe cómo el tráfico HTTP/HTTPS externo debe ser enrutarlo hacia Service dentro del clúster, usando reglas de host y path

    ## Para que sirve ?

    1. Exponer múltiples servicios HTTP por una sola IP
    2. Routing por dominio (`host`)
    3. Routing por path (`/api` `/auth`)
    4. Terminar TLS (HTTPS)
    5. Centralizar acceso externo

    * Evita crear un LoadBalancer por cada Service

    ## Funcionamiento

    * Flujo general

        ```bash
        Cliente
        ↓
        LoadBalancer (MetalLB / Cloud)
        ↓
        Ingress Controller (NGINX)
        ↓
        Service (ClusterIP)
        ↓
        Pods
        ```
        ### Paso 1: El cliente hace un request HTTP

        ```http
        GET https://api.example.com/users
        ```

        ### Paso 2: Llega al LoadBalancer

        * IP asignada por MetalLB
        * Apunta al Ingress Controller

        ### Paso 3: Ingress Controller recibe el trafico

        * NGINX escucha 80 / 443
        * Actúa como reverse proxy

        ### Paso 4: Lee los objetos Ingress

        * Consulta kube-apiserver
        * Traduce reglas a configuración real

        ### Paso 5: Routing interno

        * Selecciona Service correcto
        * Reenvia trafico

        #### Paso 6: Service -> Pods

        * kube-proxy balancea a Pods

    ## Elementos  que componen Ingress

    1. **Ingress Resource (objeto)**

        * Es el YAML con las reglas

            ```yaml
            kind: Ingress
            ```
            * Define:

                * Host
                * Paths
                * Service destino

    2. **Ingress Controller**

        * La pieza mas importante 

        * Es el componente que:

            * Ejecuta el proxy real
            * Interpreta los ingress
        
        * Ejemplo:

            * NGINX Ingress
            * Traefik
            * HAProxy
            * Kong

        * Sin Controller, Ingress **No funciona**

    3. **Service (ClusterIP)**

        * Ingress **siempre apunta a un Service**, nunca a Pods

    4. **LoadBalancer / NodePort**

        * El Ingress Controller se expone usando:

            * Service type LoadBalancer (MetalLB)
            * NodePort (alternativamente)

    5. **DNS**

        * Dominios apuntan a la IP del Ingress

    6. **TLS Secrets**

        * Certificados HTTPS

            ```yaml
            tls:
            - hosts:
              - api.example.com
              secretName: api-tls
            ```

    ## Características

    1. **Routing L7 (HTTP)**

        * Host-based routing
        * Path-based routing
        * Headers (según controller)

    2. **TLS centralizado**

        * HTTPS en un solo punto
        * Certificados compartidos

    3. **Un solo punto de entrada**

        * Menos IPs
        * Menos LoadBalancers
    
    4. **Declarativo**

        * YAML
        * Versionable
        * Auditable

    ## Que Ingress No hace

    * No funciona sin Controller
    * No balancea L4
    * No es para TCP/UDP (sin extensiones)
    * No maneja estado

    ## Ingress vs Service LoadBalancer

    | Ingress               | Service LoadBalancer |
    | --------------------- | -------------------- |
    | L7 HTTP/HTTPS         | L4 TCP/UDP           |
    | Muchos servicios      | Uno por Service      |
    | Routing por host/path | Sin routing          |
    | TLS                   | No                   |


    ## Ejemplo

    ```yaml
    apiVersion: networking.k8s.io/v1
    kind: Ingress
    metadata:
        name: api-ingress
    spec:
        rules:
        - host: api.example.com
          http:
            paths:
            - path: /users
              pathType: Prefix
              backend:
                service:
                    name: api
                    port:
                        number: 80
    ```

    ## Ingress vs Gateway API

    * Ingress:

        * Simple
        * Limitado
    
    * Gateway API:

        * Más potente
        * Más granular
        * Evolución natural

