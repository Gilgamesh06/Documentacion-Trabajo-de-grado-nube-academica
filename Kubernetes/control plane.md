# Control Plane

* El **Control Plane** es el **cerebro del clúster Kubernetes**

* Es el conjunto de **componentes que toma decisiones**, mantiene el **estado deseado** del clúster y **orquestan** todo lo que ocurre
    
    * **No ejecuta aplicaciones de usuario**
    * **Decide qué debe pasar y verificar que pase**

* Definición corta

    > El **Control Plane** es la parte de Kubernetes que **gestiona, coordina y controla** el clúster.

    ## Para que sirve ?

    1. **Recibir solicitudes** (kubectl, API, controllers)
    2. **Validar y autenticar** peticiones
    3. **Guardar el estado** del clúster
    4. **Decidir dónde y cómo** se ejecutan los pods
    5. **Detectar fallos**
    6. **Corregir desviaciones** entre estado deseados y real

    ## Ejemplo simple

    ```bash
    kubectl apply -f deployment.yaml
    ```
    * El Control Plane:

        * Recibe el YAML
        * Lo valida
        * Guarda el estado deseado
        * Crea Pods
        * Reubica Pods si algo falla

    ## Funcionamiento

    * Pasos internos cuando se crea un Deployment.

        ### Paso 1: `Solicitud al API Server`

        ```bash
        kubectl -> kube-apiserver
        ```
        * El API Server recibe la solicitud
        * Autentica al usuario
        * Autoriza la acción
        * Valida el objeto

        ### Paso 2: `Guardar estado en etcd`

        ```bash
        kube-apiserver -> etcd
        ```
        * El YAML se guarda como **estado deseado**
        * etcd es la **fuente de la verdad**

        ### Paso 3: `Controllers detectan cambios`

        * Los **controllers** observan el API Server:

            ```bash
            Estado deseado != Estado actual
            ```
        * **Ejemplo**

            * Quiero 3 pods
            * Solo hay 1
        
        * El controller actúa

        ### Paso 4: `Sheduler asigna nodos`

        * El **scheduler**

            * Analiza recursos
            * Evalúa afinidades
            * Decide el mejor nodo
        
        ### Paso 5: `Kubelet ejecuta Pods`

        * En el nodo elegido:

            * kubelet recibe la orden
            * Crea contenedores
            * Reporta estado al API Server

        ### Paso 6: `Ciclo de reconciliación`

        * El control Plane **nunca se detiene**

            ```bash
            Observar -> Comparar -> Corregir
            ```
    
    ## Elementos que componen el Control Plane

    * Ahora veamos **cada componente**, uno por uno

        ### kube-apiserver (el corazón)

        * Es la **unica puerta de entrada** al cluster

        ### etcd (memoria del clúster)

        * Base de datos **clave-valor distribuida**

        ### kube-scheduler (el planificador)

        * Es el componente que decide **dónde corre cada pod**

        ### kube-controller-manager (los controladores)

        * Es un proceso que ejecuta **muchos controllers**

    ## Dónde vive el Control Plane

    * Depende del entorno:

        | Entorno          | Ubicación         |
        | ---------------- | ----------------- |
        | Kind             | Contenedor Docker |
        | Minikube         | VM                |
        | MicroK8s         | Host              |
        | Cloud (EKS, GKE) | Gestionado        |

    ## Que No hace el Control Plane

    * No ejecuta contenedores (apps de usuarios)
    * No expone servicios al exterior
    * No balancea tráfico
    * No maneja red de pods
