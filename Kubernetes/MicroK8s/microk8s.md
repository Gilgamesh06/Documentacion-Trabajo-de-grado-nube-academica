# MicroK8s 

* **MicroK8s = Kubernetes simplifica**

* Es:

    * Un **Kubernetes completo** (no es un *mini-K8s* recortado)
    * Empaquetado como un **Snap** (un solo paquete)
    * Diseñado para correr en:

        * Laptops
        * Servidores
        * VMs
        * Edge / IoT
        * CI/CD

    * Internamente usa **los mismos binarios oficiales de Kubernetes**


    ## Para que sirve ?

    * **Desarrollo local**

        * Reemplazo de Minikube o Kind
        * Probar manifests YAML reales
        * Simular un cluster casi productivo

    * **Testing / CI**

        * Crear clusters rapidamente
        * Desplegar microservicios para pruebas automatizadas
        * Ideal para pipelines CI/CD
    
    * **Producción ligera**

        * Clusters pequeños o medianos
        * Edge computing
        * Oficinas remotas
        * Laboratiorios

    * **Aprendizaje de Kubernetes**

        * Menos fricción que un cluster *full*
        * Ideal para entender networking, storage, ingress, RBAC, etc

    ## Funcionamiento

    * MicroK8s **empaqueta todos los componentes de Kubernetes** y los ejecuta como **servicios locales**, gestionados por Snap

    * **Flujo simplificado**

        ```bash
        Usuario
        ↓ kubectl / microk8s kubectl
        MicroK8s
        ↓
        API Server
        ↓
        Scheduler ──> Kubelet
        ↓             ↓
        Controller     Pods / Containers
        ```
        * Corre **directamente en el host** (no en VM por defecto)
        * Usa **containerd** como runtime (no Docker)
        * Configura automáticamente:

            * Red
            * DNS
            * Certificados
            * Almacenamiento básico

    ## Arquitectura interna de MicroK8s
    
    ```bash
    ┌──────────────────────────┐
    │        MicroK8s          │
    │                          │
    │  API Server              │
    │  Scheduler               │
    │  Controller Manager      │
    │  Kubelet                 │
    │  containerd              │
    │  CNI (Calico / Flannel)  │
    │  CoreDNS                 │
    │                          │
    └──────────────────────────┘
    ```

    * **En multiples nodos**

        ```bash
        Nodo 1 (control-plane)
        ├─ API Server
        ├─ etcd
        ├─ Scheduler
        └─ Controller Manager

        Nodo 2 (worker)
        └─ Kubelet + containerd

        Nodo 3 (worker)
        └─ Kubelet + containerd       
        ```

    ## Elementos que componen MicroK8s

    1. **Snap (sistema de empaquetado)**

        * MicroK8s se instala como un **Snap**

            * Aísla dependencias
            * Permite upgrades y rollbacks
            * Todo está contenido en:

                ```bash
                /var/snap/microk8s/
                ```
    2. **API Server**

        * El **cerebro del cluster**

        * **Para que sirve ?**

            * Recibe todos los comandos (`kubectl`)
            * Valida YAML
            * Expone la API de Kubernetes

        * **Funcionamiento**

            1. Envias un `kubectl apply`
            2. El API Server valida
            3. Guarda el estado deseado en etcd

    3. **etcd**

        * Base de datos **clave-valor distribuida**

        * **Para que sirve**

            * Guarda TODO el estado del cluster:

                * Pods 
                * Deployments
                * ConfigMaps
                * Secrets
                * Nodes

            * Si etcd se pierde -> el cluster se pierde

    4. **Scheduler**

        * El **asignador de Pods**

        * **Para que sirve**

            * Decide en que nodo se ejecuta cada Pod

        * **Considera**

            * Recursos (CPU, RAM)
            * Afinidad / anit-afinidad
            * Taints y tolerations

    5. **Controller Manager**

        * El **mantenedor del estado deseado**

        * **Para que sirve**

            * Garantiza que el cluster esté como dice el YAML

        * **Ejemplo**

            * Quiero 3 réplicas
            * Se cae una
            * El controller crea otra automaticamente

    6. **Kubelet**

        * El **agente del nodo**

        * **Para que sirve**

            * Ejecuta los Pods
            * Habla con containerd
            * Reporta estado al API Server

    7. **containerd (Container Runtime)**

        * El runtime de contenedores

        * **Para que sirve**

            * Descarga imágenes
            * Crea contenedores
            * Gestiona su ciclo de vida

        * MicroK8s **No usa Docker por defecto**

    8. **CNI (Red del cluster)**

        * MicroK8s permite varios plugins de red:

            * **Calico** (mas comun)
            * Flannel
            * Canal

        * **Para que sirve**

            * Comunicación Pod <-> Pod
            * Network Policies
            * Aislamiento de tráfico

    9. **CoreDNS**

        * DNS interno del cluster

        * **Para que sirve**

            * Resolver servicios:

                ```bash
                backend.default.svc.cluster.local
                ```       
    10. **Add-ons (caracteristica clave de MicroK8s)**

        * MicroK8s tiene **add-ons listos para activar**

            ```bash
            microk8s enable ingress
            microk8s enable dns
            microk8s enable storage
            microk8s enable metrics-server
            micrk8s enable dashboard
            ```
        * **Add-ons comunes**

            * Ingress (NGINX)
            * Storage (hostpath)
            * Metrics Server
            * RBAC
            * Istio
            * MetalLB
            * Registry LOCAL

        * Esto reduce Mucho la complejidad inicial

    ## Diferencias clave con otros Kubernetes

    | Característica     | MicroK8s  | Minikube  | Kind              |
    | ------------------ | --------  | --------- | ----------------- |
    | Kubernetes real    | Si        | Si        | Si                |
    | Producción         | Si        | No        | No                |
    | Multi-nodo         | Si        | media     | media simulados   |
    | Instalación simple | Si        | media     | media             |
    | Snap               | Si        | No        | Si                |
    | Edge               | Si        | No        | Si                |

    ## Resumen

    * **MicroK8s es:**

        * Kubernetes **completo**
        * Fácil de instalar
        * Ideal para:

            * Desarrollo
            * Testing
            * Edge
            * Producción pequeña / mediana

    * **Funciona:**

        * Ejecutando los componenes clásicos de Kubernetes
        * Con containerd
        * Con red CNI
        * Con add-ons activables
