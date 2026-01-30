# DaemonSet

* Un **DaemonSet** es un **controlador de Kubernetes** que garantiza que **un Pod especifico se ejecute en cada nodo** (o en un subconjunto de nodos) del clúster

    > Por cada nodo que cumpla las condiciones -> habra exactamente un Pod del DaemonSet

* **Definición corta**

    > Un DaemonSet asegura que **todos (o algunos) nodos del clúster ejecuten una copia del mismo Pod**

    ## Para que sirve ?

    * Se usa cuando necesitas **funcionalidad a nivel de nodo,** no a nivel de aplicación

    * **Casos de uso más comunes**

        ### Networking

        * CNI (Calico, Flannel, Cilium)
        * kube-proxy
        * MetalLB speaker (modo L2 / BGP)

        * Cada Nodo debe participar en la red

        ### Logging

        * Fluentd
        * Fluent Bit
        * Filebeat

        * Cada nodo recolecta logs de los Pods que corren en el

        ### Monitoring

        * Node Exporter (Pormetheus)
        * Datadog Agend

        * Métricas por nodo.

        ### Seguridad

        * Falco
        * Agentes de seguridad

    ## Funcionamiento

    * Veamos el flujo interno

        ### Creación del DaemonSet

        ```yaml
        apiVersion: apps/v1
        kind: DaemonSet
        metadata:
            name: fluentbit
            namespace: logging
        ```
        * Cuando lo aplicas:

            ```bash
            kubectl apply -f daemonset.yaml
            ```
        ### Reconciliación por el controlador

        * El **DaemonSet Controller** hace lo siguiente:

            1. Lista todos los nodos del clúster
            2. Evalúa

                * nodeSelector
                * affinity
                * tolerations
            3. Por cada nodo válido:

                * Crea un Pod
            4. Si un nodo nuevo aparece -> crea Pod
            5. Si un nodo se elimina -> elimina el Pod
        
        * **No usa ReplicaSet**

        ### Scheduling especial

        * El Pod **ya viene asignado a un nodo**
        * El scheduler solo valida
        * No compite con otros Pods normales

    ## Que pasa cuando ...?

    | Evento          | Resultado           |
    | --------------- | ------------------- |
    | Nuevo nodo      | Nuevo Pod           |
    | Nodo cae        | Pod desaparece      |
    | Nodo vuelve     | Pod vuelve          |
    | Escalas cluster | DaemonSet se adapta |

    ## Características

    | Característica | Detalle              |
    | -------------- | -------------------- |
    | Réplicas       | 1 por nodo           |
    | Autoscaling    | Automático por nodos |
    | Scheduler      | Especial             |
    | ReplicaSet     | No                   |
    | Rolling Update | Si                   |
    | Scope          | Cluster              |
    | Afinidad       | Soportada            |


    ## Diferencais con Deployment y StatefulSet

    | Característica     | Deployment | StatefulSet | DaemonSet  |
    | ------------------ | ---------- | ----------- | ---------- |
    | Réplicas           | Variable   | Fijas       | 1 por nodo |
    | Identidad          | No         | Si          | No         |
    | Volúmenes estables | No         | Si          | No         |
    | Caso de uso        | Apps       | DB          | Agentes    |

    ## Elementos que componen un DaemonSet

    * Un DaemonSet tiene una estrctura similar a un Deployment, pero con diferencias clave.

        ### `metadata`

        ```yaml
        metadata:
            name: node-exporter
            namespace: monitoring
        ```

        ### `spec`

        ```yaml
        spec:
            selector:
                matchLabels:
                    app: node-exporter
        ```
        * El selector **debe coincidir exactamente** con las labels del Pod

        ### `updateStrategy`

        ```yaml
        updateStrategy:
            type: RollingUpdate
        ```
        * Opciones:
            
            * RollingUpdate (default)
            * OnDelete

        ### `template` Pod Template (el nucleo)

        ```yaml
        template:
            metadata:
                labels:
                    app: node-exporter
        ```
        * Aqui se define **el Pod que correrá en cada nodo.**

        ### `containers`

        ```yaml
        containers:
        - name: exporter
          image: prom/node-exporter
        ```

        ### `nodeSelector`

        ```yaml
        nodeSelector:
            kubernetes.io/os: linux
        ```

        ### `nodeAffinity`

        ```yaml
        affinity:
        nodeAffinity:
            requiredDuringSchedulingIgnoredDuringExecution:  
        ```

        ### `tolerations`

        * Permite correr en nodos taintados

            ```yaml
            tolerations:
            - key: node-role.kubernetes.io/control-plane
              operator: Exists
              effect: NoSchedule
            ```
            * Sin esto, **no corre en control-plane**

        ### `volumes`

        ```yaml
        volumes:
        - name: varlog
          hostPath:
            path: /var/log
        ```
        * Acceso directo al nodo


    ## Ejemplo

    ```yaml
    apiVersion: apps/v1
    kind: DaemonSet
    metadata:
    name: fluentbit
    namespace: logging
    spec:
    selector:
        matchLabels:
        app: fluentbit
    updateStrategy:
        type: RollingUpdate
    template:
        metadata:
        labels:
            app: fluentbit
        spec:
        tolerations:
        - operator: Exists
        containers:
        - name: fluentbit
            image: fluent/fluent-bit
            volumeMounts:
            - name: varlog
            mountPath: /var/log
        volumes:
        - name: varlog
            hostPath:
            path: /var/log
    ```

    ## Que No hace un DaemonSet

    * No escala por replicas
    * No balancea carga
    * No es para apps web
    * No maneja estado persistente

    ## Relación con lo que ya viste

    | Componente                  | Tipo                 |
    | --------------------------- | -------------------- |
    | MetalLB Speaker             | DaemonSet            |
    | CNI                         | DaemonSet            |
    | Node Exporter               | DaemonSet            |
    | Ingress NGINX (hostNetwork) | DaemonSet (opcional) |
