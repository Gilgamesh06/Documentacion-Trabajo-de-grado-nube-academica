# Kubelet

* **kubelet** es el **agente principal que corre en cada Worker Node**

* Es el componente que:

    * Recibe instrucciones del Control Plane
    * Crea y mantiene Pods
    * Supervisa contenedores
    * Reporta estado

* **Sin kubelet, un nodo no puede ser parte de Kubernetes.**

* **Definicion Corta**

    > kubelet es el agente de Kubernetes que corren en cada nodo y se encarga de **ejecutar y mantener los Pods asignados.**

    ## Para que sirve ?

    1. Ejecutar Pods asignados al nodo
    2. Mantenerlos en el estado deseado
    3. Comunicar estado al Control Plane
    4. Gestionar el ciclo de vida de contenedores
    5. Ejecutar probes de salud

    ## Funcionamiento

    * Flujo completo y real

        ### Paso 1: Registro del nodo

        * Al iniciar:

            * kubelet se registra en el API Server
            * Envía información del nodo
            
        * `Node -> Ready`

        ### Paso 2: Observa Pods asignados

        * kubelet:

            * Observa el API Server
            * Filtran Pods con:

                ```yaml
                spec:
                    nodeName: <este-nodo>
                ```

        ### Paso 3: Traduce PodSpec -> Contenedores

        * kubelet interpreta:

            * Imágenes
            * Recursos
            * Volúmenes
            * Redes 
            * Probes

        ## Paso 4: LLama el Container Runtime (CRI)

        ```bash
        kubelet -> container runtime
        ```
        * Pull de imágenes
        * Crear contenedores
        * Arrancar procesos

        ### Pasos 5: Configura red y volúmenes

        * Invoca plugins CNI
        * Monta volúmenes (CSI, hostPath)

        ### Paso 6: Monitoreo continuo

        * kubelet:

            * Ejecuta liveness probes
            * Ejecuta readiness probes
            * Reinicia contenedores si falla

        ### Paso 7: Reporta estado

        * Estado del Pod
        * Uso de recursos
        * Health del nodo

        * Todo via kube-apiserver

    ## Elementos que componen kubelet

    * kubelet es un proceso, pero internamente tiene **módulos clave**

        ### API Client

        * Comunicacíón con kube-apiserver
        * Autenticación TLS
        * Watch API

        ### Pod Manager

        * Mantiene lista de Pods activos
        * Detecta cambios
        * Aplica reconciliación local

        ### Container Runtime Interface (CRI)

        * Abstracción del runtime
        * Permite usar:

            * containerd
            * CRI-O

        ### Volume Manager

        * Monta volúmenes
        * Administra lifecycle
        * CSI plugins

        ### Network Manager

        * Invoca CNI
        * Configura IPs
        * Namespaces de red

        ### Probe Manager

        * Ejecuta:

            * Liveness
            * Readiness
            * Startup probes
        
        ### Resource Manager

        * CPU y memoria
        * cgroups
        * QoS classes

        ### Garbage Collector

        * Limpia contenedores muertos
        * Elimina imágenes no usadas

    ## Que No hace kubelet

    * No decide dónde correr Pods
    * No crea Pods por iniciativa propia
    * No balancea tráfico
    * No guarda estado del clúster

    