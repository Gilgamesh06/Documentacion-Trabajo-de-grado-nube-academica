# MetalLB

* **MetalLB** es un **Load Balancer para Kubernetes en entornos bare-metal u on-premise**

    > Implementa el comportamiento del tipo de Service **LoadBalancer** en clústeres donde **No existe un proveedor cloud**

    * Kubernetes **no trae un LoadBalancer real por defecto**
    * MetalLB *simula* lo que hace AWS / GCP / Azure

* **Definición clara**

    > MetalLB asigna direcciones IP externas a Services de tipo LoadBalancer y enruta el tráfico hacia los nodos del clúster

    ## Para que sirve ?

    1. Usar `Service type: LoadBalancer` en bare-metal
    2. Exponer servicios con IP externa estable
    3. Evitar NodePort en Porduccion
    4. Integrar ingress Controller (NGINX, Traefik)
    5. Simualr cloud en entornos locales (Kind)

    ## Problemas que resuelve

    * **Sin MetalLB**

        * `Service LoadBalancer -> EXTERNAL-IP: <pending>`

        * Porque:

            * Kubernetes espera un cloud provider
            * No existe LB real

    * **Con MetalLB**

        * `Service LoadBalancer -> External-IP: 192.168.100.50`

        * MetalLB **asigna y anuncia la IP**

    ## Funcionamiento

    * MetalLB tiene **dos responsabilidades principales**

        1. **Asignar Ips**
        2. **Anunciar IPs en la red**

    * **Flujo completo**

        ```bash
        Service LoadBalancer creado
                ↓
        MetalLB detecta Service
                ↓
        Asigna IP disponible
                ↓
        Anuncia IP en la red
                ↓
        Tráfico llega al nodo
                ↓
        kube-proxy → Pod
        ```    
    ## Modos de funcionamiento de MetalLB

    * MetalLB soporta **dos modos**

        ### Modo Layer 2 (L2)

        > MetalLB responde a ARP (IPv4) o NDP (IPv6)

        * Modo mas simple
        * Ideal para Kind, MicroK8s, labs

        * **Funcionamiento**

            * Una IP es *poseida* por un nodo
            * Ese nodo responde ARP:

                ```bash
                Quien tiene 192.168.1.240 -> Yo (nodo worker-1)
                ```
                * Trafico entra por ese nodo
                * kube-proxy lo reparte

        * **Características**

            * Simple
            * Sin BGP
            * Un solo nodo anuncia la IP
            * No HA real de red

        ### Modo BGP (Layer 3)

        > MetalLB habla BGP con routers reales

        * Producción bare-metal 
        * Alta disponibilidad real

        * **Funcionamiento**

            * Cada nodo anuncia rutas
            * Router decide camino
            * Trafico entra balanceado
        
        * **Características**

            * HA real
            * Balanceo real
            * Requiere routers BGP
            * Configuración compleja

    ## Elementos que componen MetalLB

    * MetalLB se compone de **dos componentes principales:**

        ### Controller

        * Es un controlador de Kubernetes
        * Observa Services tipo LoadBalancer

        * Sirve para:

            * Asignar IPs
            * Gestionar estado
            * Coordinar anuncios

        * Funciones

            * Watch Services
            * Seleccionar IP libre
            * Actualizar status del Service

        ### Speaker

        * Es un DaemonSet
        * Corre en cada nodo

        * Sirve para:

            * Anunciar IPs
            * Responder ARP / DNP
            * Hablar BGP si aplica
        
        * Funciones

            * Anunciar IPs en red
            * Retirar anuncios si el nodo cae

    ## Objetos CRD de MetalLB

    * MetalLB usa **Custom Resource Definitions**

        ### IPAddressPool

        * Define el rango de IPs disponibles

            ```yaml
            apiVersion: metallb.io/v1beta1
            kind: IPAddressPool
            metadata:
                name: pool
                namespace: metallb-system
            spec:
                addresses:
                - 172.18.255.200-172.18.255.250
            ```
        
        ### L2Advertisement
        
        * Indica cómo anunciar IPs en L2

            ```yaml
            apiVersion: metallb.io/v1beta1
            kind: L2Advertisement
            metadata:
                name: L2
                namespace: metallb-system
            ```
            * Necesario en **modo L2**

        ### BGPAdvertisement / BGPPeer

        * Solo para modo BGP

    ## Características 

    1. **Compatible con Service LoadBalancer**

        * No cambias YAML
        * Transparente

    2. **Funciona on-prem**

        * Bare-metal
        * VMs
        * Kind
        * Microk8s
    
    3. **Declarativo**

        * Configurado por YAML
        * Integrado con Kubernetes API

    4. **Alta disponibilidad (dependiendo del modo)**

        * L2: limitada
        * BGP: completa

    ## MetalLB vs NodePort

    | MetalLB            | NodePort    |
    | ------------------ | ----------- |
    | IP externa real    | Puerto alto |
    | Transparente       | Manual      |
    | Producción         | Testing     |
    | Compatible Ingress | Limitado    |
s