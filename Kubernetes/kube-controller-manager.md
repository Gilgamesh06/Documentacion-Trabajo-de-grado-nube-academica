# kube-controller-manager

* **kube-controller-manager** es un **componente del Control Plane** que ejecuta **múltiples controladores (controllers)**

* Cada controller es un **bucle que control** que observa el estado de clúster y lo **reconcilia** con el estado deseado

* Definición corta

    > kube-controller-manager es el proceso que ejecuta los controllers responsables de **mantener el estado desado del clúster**.

    ## Para que sirve ?

    * Sirve para:

        1. Detectar cambios en el clúster
        2. Comparar:

            * Estado deseado (etcd)
            * Estado real (nodos, pods)

        3. Corregir diferencias automáticamente
        4. Mantener estabilidad y resiliencia

    * Es el **principio de auto-healing** de Kubernetes

    ## Idea clave: El patrón de reconciliación

    * Todos los controllers siguen el mismo patrón

        * `Observar -> Comparar -> Corregir`

    * O formalmente:

        * `Estado deseado != Estado actual -> Acción`

    ## Funcionamiento

    * **Ejemplo: Deployment con 3 réplicas**

        ### Paso 1: Estado deseado

        ```yaml
        replicas: 3
        ```
        * Guardado en etcd via kube-apiserver

        ### Paso 2: Controller observa

        * El **Deployment Controller** observa el API Server

            * Detecta que solo hay 2 Pods

        ### Paso 3: Reconciliación

        ```bash
        Deseado = 3
        Actual = 2
        → Crear 1 Pod
        ```

        ### Paso 4: Acción

        * El controller crea un nuevo Pod
        * El scheduler lo asigna
        * kubelet lo ejecuta

        ### Paso 5: Loop continuo

        * El controller nunca se detiene

    ## Controllers principales que ejecuta

    * kube-controller-manager ejecuta **muchos controllers**, lo mas importantes:

        ### Node Controller

        * Detecta nodos caidos
        * Marca `NotReady`
        * Evit Pods

        ### ReplicaSet Controller

        * Mantener número de Pods
        * Crear o eliminar Pods

        ### Deployment Controller

        * Gestionar rollouts
        * Crear/Actualizar ReplicaSets

        ### Job Controller

        * Ejecutar tareas batch
        * Reintentar fallos
        * Marcar completados

        ### CronJob Controller

        * Programar Jobs periódicos

        ### Endpoint / EndpointSlice Controller

        * Mantener Service Actualizados
        * Reflejar Pods disponibles

        ### ServiceAccount Controller

        * Crear ServiceAccounts por defecto

        ### Namespace Controller

        * Limpiar recursos al borrar namespaces

    ## Arquitectura Interna

    1. **Un proceso, muchos controllers**

        ```bash
        kube-controller-manager
        ├── Node Controller
        ├── Deployment Controller
        ├── ReplicaSet Controller
        ├── Job Controller
        └── ...
        ```
    
    2. **Comunicación**

        * Todos los controllers:

            * `Controller → kube-apiserver → etcd`
            * Nunca acceden directamente a nodos

    3. **Uso de Watch**

        * Observan cambios en tiempo real
        * No hacen polling pesado



        