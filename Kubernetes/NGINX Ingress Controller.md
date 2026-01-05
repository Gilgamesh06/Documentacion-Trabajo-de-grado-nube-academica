# NGINX Ingress Controller

* El **NGINX Ingress Controller** es una **Implementación de Ingress Controller** basada en **NGINX** que actúa como **reverse proxy y load balancer HTTP/HTTPS** dentro de Kubernetes

    * Es un **componente que corre dentro del clúster**
    * Traduce objetos `Ingress` -> configuración NGINX real

* **Definición clara**

    > NGINX Ingress Controller observa los objetos Ingress del clúster y configura dinamicamente NGINX para enrutar tráfico HTTP/HTTPS hacia Services Internos

    ## Para que sirve ?

    1. Exponer servicios HTTP/HTTPS
    2. Centralizar acceso externo
    3. Routing por host y path
    4. Terminar TLS
    5. Balancear tráfico HTTP
    6. Aplicar políticas (timeouts, headers, auth)

    * Es la **entrada principal** de microservicios

    ## Funcionamiento

    * Flujo general

        ```bash
        Cliente
        ↓
        LoadBalancer (MetalLB)
        ↓
        NGINX Ingress Controller
        ↓
        Service (ClusterIP)
        ↓
        Pods
        ```

        ### Paso 1: Se despliega el Controller

        * Corre como **Deployment**
        * Generalmente 1-3 replicas
        * Escuchando en 80 / 443

        ### Paso 2: Se expone con un Service

        ```yaml
        type: LoadBalancer
        ```
        * MetalLB asigna IP externa

        ### Paso 3: Observa Objetos Kubernetes

        * El Controller:

            * Escucha Ingress
            * Escucha Services
            * Escucha Endpoints

        ### Paso 4: Genera configuración NGINX  

        * Convierte reglas ingress -> bloques `server` y `location`
        * Configuración dinámica (sin restart)

        ### Paso 5: Proxy de tráfico

        * Recibe request
        * Aplica reglas
        * Reenvía al Service correcto

    ## Elementos que componen NGINX Ingress Controller

    1. **Controller Pod**

        * Es el proceso principal:

            * NGINX
            * Control Loop
            * Cliente Kubernetes API

    2. **Deployment**

        * Gestiona réplicas
        * Autho-healing

    3. **Service (LoadBalancer/ NodePort)**

        * Expone el Controller

    4. **ConfigMap**

        * Configura comportamiento global:

            ```yaml
            kind: ConfigMap
            ```       
        * Ejemplos:

            * timeouts
            * buffers
            * headers
            * logs

    5. **IngressClass**

        * Define que ingress maneja

            ```yaml
            kind: IngressClass
            ```
    6. **RBAC**

        * ServiceAccount
        * ClusterRole
        * ClusterRoleBinding

    * Permite leer recursos Kubernetes

    ## Características

    1. **Reverse Proxy L7**

        * HTTP/HTTPS
        * WebSockets
        * gRPC

    2. **Balanceo de carga**

        * Round-robin
        * Least connections (segun config)
    
    3. **TLS termination**

        * HTTPS centralizado
        * Integración con cert-manager

    4. **Alta disponibilidad**

        * Múltiples réplicas
        * Stateless

    5. **Configuración dinámica**

        * Sin reinicios
        * Hot reload

    ## NGINX Ingress vs NGINX normal

    | NGINX             | NGINX Ingress Controller |
    | ----------------- | ------------------------ |
    | Config manual     | Config automática        |
    | Standalone        | Integrado Kubernetes     |
    | IP fija           | Service LoadBalancer     |
    | No auto discovery | Auto discovery           |

    ## Que No hace NGINX Ingress Controller

    * No gestiona Pods
    * No reemplaza Service
    * No asigna IPs (eso es MetalLB)
    * No es un API Gateway completo

    ## Ejemplo

    ```yaml
    apiVersion: networking.k8s.io/v1
    kind: Ingress
    metadata:
        name: api-ingress
        annotations:
            nginx.ingress.kubernetes.io/rewrite-target: /
    spec:
        ingressClassName: nginx
        rules:
        - host: api.local
          http:
            paths:
            - path: /
              pathType: Prefix
              backend:
                service:
                    name: api
                    port:
                      number: 80
    ```
    