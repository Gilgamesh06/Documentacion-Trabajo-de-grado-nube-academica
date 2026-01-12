# StatefulSet

* Un **SatefulSet** es un **controller de Kubernetes** diseñado para ejecutar **aplicaciones stateful**, es decir, aplicaciones que:

    * Mantienen estado
    * Necesitan identidad estable
    * Usan almacenamiento persistente
    * Requieren orden de arranque/parada


* **Definición corta**

    > Un StatefulSet administra Pods con **identidad estable, nombres predecibles, almacenamiento persistente dedicado** y **orden garantizado** de creación y eliminacion.

* A diferencia del Deployment:

    * **Los Pods No son intercambiables**
    * Cada Pod es único

    ## Para que sirve ?

    * Bases de datos (PostgreSQL, MySQL, MongoDB)
    * Sistemas distribuidos (Kafka, ZooKeeper, etcd)
    * Colas (RabbitMQ)
    * Storage distribuido

    * Cualquie sistema que **necesite saber *quien es quien***

    ## Funcionamiento

    * Flujo interno completo

        ### Paso 1: Creación

        ```bash
        kubectl apply -f statefulset.yaml
        ```
        * kube-apiserver valida
        * Guarda en etcd

        ### Paso 2: Pod Management Policy

        * Por defecto;

            * Los Pods se crean **uno por uno**
            * En orden

                * `app-0 -> app-1 -> app-2`
        
        ### Paso 3: Identidad estable

        * Cada Pod tiene:

            * Nombre fijo 
            * Hostname fijo
            * DNS estable

                * `db-0.db.default.svc.cluster.local`

        ### Paso 4: Almacenamiento persistente

        * Cada Pod obtiene su **propio PVC**
        * El PVC **no se borrra** al eliminar el Pod

        ### Paso 5: Scheduler + Kubelet

        * Scheduler asigna nodo
        * kubelet monta volumen correcto
        * Pod arranca con su data intacta

    ## Características

    1. **Identidad estable**

        | Elemento | Estabilidad |
        | -------- | ----------- |
        | Nombre   | ✅          |
        | Hostname | ✅          |
        | DNS      | ✅          |
        | Volumen  | ✅          |

    2. **Orden garantizado**

        * Creación secuencial
        * Eliminación inversa

    3. **Almacenamiento persistente**

        * Un volumen por Pod
        * No compartido
        * Sobrevive a reinicios

    4. **No ReplicaSet**

        * StatefulSet **No usa ReplicaSet**

            * Gestiona Pods **directamente**

    5. **Escalado controlado**

        * Escala uno a uno
        * Mantiene identidad

    ## Elementos que componen un StatefulSet

    1. **`metadata`**

        ```yaml
        metadata:
            name: db
        ```
    
    2. **`spec.serviceName`** Headless Service

        * Obligatiorio

            ```yaml
            serviceName: db
            ```
            * Service sin ClusterIP
            * Usado para DNS estable

    3. **`spec.replicas`**

        ```yaml
        replicas: 3
        ```

    4. **`spec.selector`**

        ```yaml
        selector:
            matchLabels:
                app: db
        ```

    5. **`spec.template`** Pod Template

        ```yaml
        template:
            metadata:
                labels:
                    app: db
            spec:
                containers:
                - name: postgres
                  image: postgres:16
        ```
    
    6. **`volumeClaimTemplates`**

        * La pieza mas importante

            ```yaml
            volumeClaimTemplates:
            - metadata:
                name: data
              spec:
                accessModes: ["ReadWriteOnce"]
                storageClassName: standard
                resources:
                    requests:
                        storage: 10Gi
            ```

        * Kubernetes crea:

            ```bash
            data-db-0
            data-db-1
            data-db-2
            ```    

    7. **`podManagementPolicy`**

        ```yaml
        podManagementPolicy: OrderedReady | Parallel
        ```
        * OrderedReady (default)
        * Parallel (Casos especiales)

    ## Networking en StatefulSet

    1. **Headless Service**

        ```yaml
        clusterIP: None
        ```
        * Permite DNS individual por Pod

    2. **DNS generado**

        ```bash
        <stateful-pod>.<service>.<namespace>.svc.cluster.local
        ```

    ## StatefulSet vs Deployment

    | Deployment           | StatefulSet       |
    | -------------------- | ----------------- |
    | Pods intercambiables | Pods únicos       |
    | IP variable          | DNS estable       |
    | Sin orden            | Orden garantizado |
    | Sin volumen dedicado | PVC por Pod       |
    | Stateless            | Stateful          |

    ## Que No hacen StatefulSet

    * No balancea tráfico
    * No hace rolling update agresivo
    * No replica datos automáticamente
    * No reemplaza backups

    ## Ejemplo

    ```yaml
    apiVersion: apps/v1
    kind: StatefulSet
    metadata:
        name: postgres
    spec:
        serviceName: postgres
        replicas: 3
        selector:
            matchLabels:
                app: postgres
        template:
            metadata:
                labels:
                    app: postgres
            spec:
                containers:
                - name: postgres
                  image: postgres:16
                  volumeMounts:
                  - name: data
                    mountPath: /var/lib/postgresql/data
    volumeClaimTemplates:
    - metadata:
        name: data
      spec:
        accesModes: ["ReadWriteOnce"]
        resources:
            requests:
                storage: 10Gi
    ```

    