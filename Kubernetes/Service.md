# Service

* Un **Service** es un **Objeto de red** que:

    > Proporciona un **punto de acceso estable** para un conjunto dinamíco de Pods

* Los Pods:

    * Cambian de IP
    * Mueren y se recrean

* El Service:

    * Tienen IP estable
    * Nombre DNS estable
    * Se mantiene aunque los Pods cambien

* **Definición clara**

    > Un Service abstrae un conjunto de Pods y expone una dirección IP y DNS estables para acceder a ellos

    ## Para que sirve ?

    1. Exponer Pods dentro o fuera del clúster
    2. Balancear tráfico entre Pods
    3. Desacoplar clientes de Pods
    4. Permitir descubrimiento de Servicios (DNS)
    5. Conectar Microservicios

    ## Funcionamiento

    * Flujo interno

        ### Paso 1: Selector

        ```yaml
        selector:
            app: api
        ```
        * El Service **selecciona Pods por labels**

        ### Paso 2: Endpoints / EndpointsSlices

        * Kubernetes crea:

            * `Endpoints` o `EndpointSlice`
            * Lista de IPs reales de los pods

        ### Paso 3: kube-proxy

        * En cada nodo 

            * kube-proxy programa reglas (iptables / ipvs)
            * Redirige tráfico del Service -> Pods

        ### Paso 4: Balanceo

        * Round-robin basico
        * A nivel de red (L4)
        * No es un load Balancer L7

    ## Características

    1. **IP estable**

        * ClusterIP no cambia
        * DNS estable

    2. **Balanceo de carga**

        * Distribuye tráfico entre Pods

    3. **Desacoplamiento**

        * El cliente no conoce Pods
        * Solo el Service

    4. **Declarativo**

        * Estado deseado
        * Kubernetes mantiene endpoints actualizados

    ## Tipos de Service

    * Kubernetes define varios tipos, los **3 principales** son:

        1. ClusterIP
        2. NodePort
        3. LoadBalancer

        ### ClusterIP

        * Es el **tipo por defecto** de Service

            > Expone el servicio **solo dentro del clúster**

        * Sirve para:
            
            * Comunicacion entre microservicios
            * Backends
            * Bases de datos internas

        * No es accesible desde fuera del cluster

        * **Funcionamiento**

            1. Kubernetes asigna una IP virtual (ClusterIP)
            2. DNS apunta al Service
            3. kube-proxy redirige tráfico a Pods

            * `client-pod -> ClusterIP -> Pod Backend`

        * **Ejemplo**

            ```yaml
            apiVersion: v1
            kind: Service
            metadata:
                name: api
            spec:
                type: ClusterIP
                selector:
                    app: api
                ports:
                - port: 80
                  targetPort: 8080
            ```
        
        * **Características**

            * IP interna
            * DNS interno
            * Balanceo L4
            * Default

        ### NodePort

        * Un Service que:

            > Expone el servicio en **puerto fijo de todos los nodos**

        * sirve para:

            * Acceso externo simple
            * Testing
            * Clústeres sin LoadBalancer

        * **Funcionamiento**

            1. Kubernentes abre un puerto (30000-32767)
            2. El puerto existe en **todos los nodos**
            3. Tráfico llega al nodo -> Service -> Pod

            * `client -> nodeIP:nodePort -> Pod`

        * **Ejemplo**

            ```yaml
            apiVersion: v1
            kind: Service
            metadata:
                name: api
            spec:
                type: NodePort
                selector:
                    app: api
                ports:
                - port: 80
                  targetPort: 8080
                  nodePort: 30080
            ```

        * **Características**

            * Acceso externo
            * Puerto fijo
            * No balancea entre nodos externamente
            * No es amigable para produccion

        ### LoadBalancer

        * Un Service que:

            > Solicita un **balanceador de carga externo** y asigna una IP publica o privada

        * Sirve para:

            * Exponer servicios en producción
            * Integrarse con cloud providers
            * Acceso externo estable

        * **Funcionamiento**

            * **En cloud (AWS, GCP, Azure)**

                1. Kubernetes pide LB al provider
                2. Se crea ELB / ALB / GLB
                3. LB apunta a NodePorts
                4. Tráfico fluye a Pods

            * **On-prem / Kind / MicroK8s**

                * No existe LoadBalancer real

                * Necesitas:

                    * **MetalLB**

        * **Ejemplo**

            ```yaml
            apiVersion: v1
            kind: Service
            metadata:
                name: api
            spec:
                type: LoadBalancer
                selector:
                    app: api
                ports:
                - port: 80
                  targetPort: 8080
            ```
            
        * **Características**

            * IP externa
            * Integración cloud
            * Ideal producción
            * Requiere infraestructura

    ## Comapración rápida

        | Tipo         | Acceso  | IP              | Uso            |
        | ------------ | ------- | --------------- | -------------- |
        | ClusterIP    | Interno | Virtual         | Microservicios |
        | NodePort     | Externo | Nodo            | Testing        |
        | LoadBalancer | Externo | Pública/Privada | Producción     |


    ## Service + Deployment + Ingress

    ```bash
    Internet
    ↓
    Ingress
    ↓
    Service (ClusterIP)
    ↓
    Pods
    ```
    * Ingress **No reemplaza** Service

    ## Que No hace Service

    * No maneja HTTP
    * No TLS
    * No routing por path
    * No auth

    * Eso es Ingress / Gateway

    