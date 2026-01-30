# Deployment

* Un **Deployment** es un **controller de alto nivel** que:

    > Gestiona el ciclo de vida de aplicaciones declarando cómo deben crearse, actualizarse y mantenerse los Pods, usando **ReplicaSet** por debajo.

    * Nunca gestiona Pods directamente
    * Siempre lo hace a través de ReplicaSets

* **Definición corta**

    > Un **Deployment** define el estado deseado de una aplicación y Kubernetes se encarga de crear, actualizar y recuperar Pods de forma controlada

    ## Para que sirve ?

    1. Desplegar aplicaciones
    2. Escalar Pods
    3. Actualizar versiones sin downtime
    4. Hacer rollbacks
    5. Mantener alta disponibilidad
    6. Declarar el estado de una app

    * **Es el objeto más usado para aplicaciones stateless**

    ## Funcionamiento

    * Flujo interno completo.

        ### Paso 1: Creación

        ```bash
        kubectl apply -f deployment.yaml
        ```
        * kube-apiserver valida
        * Guarda en etcd

        ### Paso 2: Deployment Controller

        * Dentro del **kube-controller-manager**

            * Observa Deployments
            * Compara estado deseado vs actual

        ### Paso 3: Creación de ReplicaSet

        * Crea un **ReplicaSet** *activo*
        * ReplicaSet crea Pods

            ```bash
            Deployment
            └── ReplicaSet
                └── Pods
            ```

        ### Paso 4: Monitoreo continuo

        * El Deployment controller:

            * Observa Pods
            * Si algo falla -> actúa

        ### Paso 5: Actualizaciones

        * CUnado cambia el template:

            * Crea **nuevo ReplicaSet**
            * Reduce gradualmente el anterior
            * Aplica estrategia de despliegue

    ## Elementos que compone un Deployment

    1. **`metadata`**

        ```yaml
        metadata:
            name: api-deployment
        ```
    
    2. **`spec.replicas`**

        * Cantidad deseada de Pods

            ```yaml
            replicas: 3
            ```
    3. **`spec.selector`**

        * Definer que Pods controla:

            ```yaml
            selector: 
                matchLabels:
                    app: api
            ```
            * **Debe coincidir exactamente con el template**

    4. **`spec.template`** (Pod Template)

        * Plantilla para Pods

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

    5. **`spec.strategy`**

        * Como se hacen los updates

        * **RollingUpdate (default)**

            ```yaml
            strategy:
                type: RollingUpdate
                rollingUpdate:
                    maxUnavailable: 1
                    maxSurge: 1
            ```
    
    6. **Status**

        * Estado actual del Deployment:

            * replicas
            * updateReplicas
            * readyReplicas
            * availableReplicas

    ## Estrategias de despliegue

    1. **RollingUpdate (por defecto)**

        * Crea Pods nuevos
        * Elimina antiguos gradualmente
        * Sin downtime

    2. **Recreate**

        * Mata todos los Pods
        * Luego crea nuevos
        * Downtime garanizado

            ```yaml
            strategy:
                type: Recreate
            ```

    ## Características 

    1. **Versionado automático**

        * Cada cambio crea un **nuevo ReplicaSet**

            ```bash
            kubectl get rs
            ```
    2. **Rollback**

        ```bash
        kubectl rollout undo deployment api
        ```
    3. **Pausar y reanudar**

        ```bash
        kubectl rollout pause deployment api
        kubectl rollout resume deployment api
        ```
    4. **Escalado simple**

        ```bash
        kubectl scale deployment api --replicas=5
        ```
    
    5. **Auto-healing**

        * Si un Pod muere <-> RS lo recrea

    ## Relación Deployment <-> ReplicaSet <-> Pod

    ```bash
    Deployment
    ├── ReplicaSet (v1)
    │    └── Pods
    └── ReplicaSet (v2)
        └── Pods
    ```
    * Solo uno está activo
    * Los viejos se conservan para rollback

    ## Qué No hace un Deployment

    * No expone red
    * No balancea tráfico
    * No maneja estado (stateful)
    * No gestiona volúmenes persistentes

    ## Ejemplo 

    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
        name: nginx-deployment
    spec:
        replicas: 3
        selector: 
            matchLabels:
                app: nginx
        strategy:
            type: RollingUpdate
            rollingUpdate:
                maxSurge: 1
                maxUnavailable: 1
        template:
            metadata:
                labels:
                    app: nginx
        spec:
            containers:
            - name: nginx
              image: nginx:1.25
              ports: 
              - containerPort: 80
    ```
