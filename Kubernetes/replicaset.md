# ReplicaSet

* Un **ReplicaSet (RS)** es un **controller de Kubernetes** cuya responsabilidad es:

    > Garantizar que siempre exista un número especifico de Pods idénticos en ejecución

    * Si un Pod muere -> el ReplicaSet crea otro.
    * Si hay más Pods de los deseados -> elimina el exceso.

* **Definición corta**

    > Un **ReplicaSet** asegura que un número fijo de réplicas de un Pod estén ejecutándose en el clúster en todo momento.

    ## Para que sirve ?

    1. Mantener alta disponibilidad
    2. Recuperar Pods caídos automáticamente
    3. Escalar horizontalmente Pods
    4. Proveer tolerancia a fallos
    5. Servir como base de Deployments

    * **No se usa casi nunca directamente** en producción -> se usa **Deployment**

    ## Funcionamiento

    * Flujo completo

        ### Paso 1: Creación

        ```bash
        kubectl apply -f rs.yaml
        ```
        * kube-apiserver valida
        * Guarda en etcd

        ### Paso 2: Observación continua

        * El **kube-controller-manager** ejecuta el **ReplicaSet controller**, que:

            * Observa Pods existentes
            * Filtrar por `selector`

        ### Paso 3: Comparación

        ```bash
        desired replicas = spec.replicas
        actual replicas = pods que coinciden con el selector
        ```

        ### Paso 4: Reconciliación

        | Caso             | Acción        |
        | ---------------- | ------------- |
        | actual < desired | Crear Pods    |
        | actual > desired | Eliminar Pods |
        | actual = desired | No hace nada  |

        * Este ciclo corre **constantemente**


    ## Cómo identifica los Pods 

    * ReplicaSet usa **labels**

        ```yaml
        selector:
            matchLabels:
                app: nginx
        ```
        * Solo controla Pods con esas labels

            > Si cambias labels manualmente  -> el **RS** pierde control.

    ## Relación ReplicaSet <-> Pod

    * ReplicaSet **no ejecuta contenedores**
    * ReplicaSet **crea Pods**
    * Pods tienen `ownerReferences`

        ```yaml
        ownerReferences:
        - kind: ReplicaSet
        ```
    
    ## Componentes de un ReplicaSet

    1. **`spec.replicas`**

        * Numero deseado de Pods

            ```yaml
            replicas: 3
            ```
    
    2. **`selector`**

        * Define qué Pods controla

            ```yaml
            selector:
                matchLabels:
                    app: api
            ```

    3. **`template`**

        * Plantilla para crear Pods

            ```yaml
            template:
                metadata:
                    labels:
                        app: api
                spec:
                    containers:
                    - name: api
                      image: my-api:v1
            ```
            * Es exactamente un `PodSpec`

    4. **Status**

        * Estado actual:

            * Replicas
            * readyReplias
            * availableReplicas

    ## ReplicaSet vs Deployment

    * Esto es CLAVE

    | ReplicaSet        | Deployment        |
    | ----------------- | ----------------- |
    | Mantiene réplicas | Mantiene réplicas |
    | No versiona       | Versiona          |
    | No rollback       | Rollback          |
    | Uso directo       | Uso indirecto     |
    | Nivel bajo        | Nivel alto        |

    * **Deployment = ReplicaSet + estrategia de despliegue**

    ## Ejemplo de ReplicaSet

    ```yaml
    apiVersion: apps/v1
    kind: ReplicaSet
    metadata: 
        name: nginx-rs
    spec:
        replicas: 3
        selector:
            matchLables:
                app: nginx
        template:
            metadata:
                labels:
                    app: nginx
            spec:
                containers:
                - name: nginx
                  image: nginx:1.25
    ```        