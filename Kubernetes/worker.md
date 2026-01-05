# Worker

* Un **Worker Node** (o simplement **worker**) es un **nodo de Kubernetes encargado de ejecutar las cargas de trabajo** (Pods y contenedores)

    * Aquí **corren tus aplicaciones**
    * Aquí **vive el runtime de contenedores**
    * Aquí **se ejecutan los Pods**

* **Definicion corta**

    > Un Worker Node es el nodo de Kubernetes que **ejecuta los Pods** y reporta su estado al Control Plane

    ## Para que sirve ?

    1. Ejecutar contenedores
    2. Alojar Pods
    3. Proveer CPU, memoria y red
    4. Comunicar estado al Control Plane
    5. Aplicar las decisiones del scheduler

    * Sin workers. Kubernetes no ejecuta nada.

    ## Funcionamiento

    * **Ejemplo: Crear un Pod**

        ### Paso 1: Scheduler asigna el Pod

        ```yaml
        spec:
            nodeName: worker-1
        ```

        ### Paso 2: Kubelet recibe la orden

        * El **kubelet** del worker:

            * Observa el API Server
            * Detecta un Pod asignado a él

        ### Paso 3: kubelet crea el Pod

        * kubelet
            
            * Descarga imágenes
            * Crea contenedores vía runtime
            * Configura red y volúmenes

        ### Paso 4: Contenedores corren

        * Tu app empieza a ejecutarse
        * kubelet monitorea salud

        ### Paso 5: Reporte de estado

        * kubelet reporta:

            * Running
            * Crashed
            * Ready / NotReady
        
        * Todo vía kube-apiserver

    ## Elementos que componen un Worker Node

    * Un worker no es solo "una máquina", es un conjunto de **componentes criticos**

        ### kubelet (el agente del nodo)

        * Agente que corre en cada worker

            * Ejecutar Pods
            * Mantenerlos vivos
            * Reportar estado

        ### Container Runtime

        * El motor que ejecuta contenedores

            * containerd
            * CRI-O
            * (Docker legacy)

        ### kube-proxy (red del nodo)

        * Componente de red que implementa **Service**

            * Balancear tráfico
            * Implementar ClusterIP
            * Redirigir tráfico a Pods

        ### CNI Plugin (red de Pods)

        * Plugin de red

            * Asignar IP a Pods
            * Conectar Pods entre nodos
            * Manejar rutas


    ## Que No hace un Worker

    * No decide dónde correr Pods
    * No guardar estado del clúster
    * No expone APIs
    * No hace scheduling

    ## Worker vs Control Plane

    | Control Plane   | Worker       |
    | --------------- | ------------ |
    | Decide          | Ejecuta      |
    | Orquesta        | Corre apps   |
    | Mantiene estado | Usa recursos |
    | No corre apps   | Corre apps   |

    ## Flujo completo

    ```bash
    kubectl
    ↓
    kube-apiserver
    ↓
    etcd
    ↓
    controller
    ↓
    scheduler
    ↓
    kubelet (worker)
    ↓
    container runtime
    ```