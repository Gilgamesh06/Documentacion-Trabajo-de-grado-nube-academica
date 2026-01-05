# Kind

* **Kind** significa **Kubernetes IN Docker**

* Es una herramienta que permite **crear clústeres de Kubernetes locales**, donde **cada nodo del clúster corre dentro de un contenedor Docker**

    * No es un Kubernetes *especial*
    * No es un simulador 
    * Es **Kubernetes real,** ejecutándose en contenedores

* Kind fue creado por el **equipo oficial de Kubernetes (SIGs)** para:

    * Probar Kubernetes
    * Ejecutar test end-to-end
    * Facilitar desarrollo y CI/CD


    ## Para qué sirve Kind ?

    * Kind se usa principalmente para:

        ### Desarrollo local

        * Probar microservicios
        * Validar YAMLs (Deployments, Services, Ingress)
        * Iterar rápido sin cloud

        ### Testing

        * Test de integración
        * Test end-to-end
        * Reproducir entornos Kubernetes reales

        ### CI/CD

        * Levantar clústeres efimeros en pipelines
        * Ejecutar pruebas
        * Destruir el clúster al finalizar

        ### Aprendizaje

        * Aprender Kubernetes real
        * Entender networking, Services, Ingress
        * Practicar sin costos
    
    * **Kind no es para producción**

    ## Funcionamiento

    * Kind funciona usando **Docker como capa de infraestructura**

        ### Principio clave

        > **1 Contenedor Docker = 1 nodo Kubernetes**

        * Cada Nodo:

            * Corre Linux
            * Tiene kubelet
            * Tiene container runtime
            * Se comporta como un nodo real

        ### Flujo de creación de un clúster

        * Cuando ejecutas:
        
            ```bash
            kind create cluster
            ```
        * Kind hace lo siguiente:

            1. Descarga una imagen especial (`kindest/node`)
            2. Crea contenedores Docker
            3. Inicializa Kubernetes con `kubeadm`
            4. Configura red interna
            5. Genera `kubeconfig`
            6. Conencta `kubectl` al clúster

        ### Arquitectura Interna

        ```bash
        Host (Ubuntu)
        └── Docker
            ├── kind-control-plane (contenedor)
            │    ├── kube-apiserver
            │    ├── etcd
            │    ├── scheduler
            │    └── controller-manager
            │
            └── kind-worker (contenedor)
                ├── kubelet
                └── container runtime
        ```

        ### Networking en Kind

        * Docker crea una red bridge
        * Todos los nods:
            
            * Estan en la misma subred
            * Comparten dominio L2

        * El tráfico entre pods usa CNI (kindnet)

        * No hay **LoadBalancer cloud**

    ## Elementos que componen kind

    * Que piezas conforma kind

        ### Docker (Infraestructura)

        * Docker es:

            * El *hardware virtual*
            * Proporciona:

                * Red 
                * Volúmenes
                * Aislamiento
                * Procesos
        
        * Sin Docker, Kind no existe

        ### Imagen: `kindtest/node`

        * Es una imagen Docker que incluye:

            * Linux
            * kubadm
            * kubelet
            * kubectl
            * containerd
            * herramientas de red
        
        * Es el "SO" de cada nodo

        ### kubeadm

        * Herramienta que:

            * Inicializa el cluster
            * Une nodos
            * Configura certificados
            * Arranca componentes base
        
        * Kind la usa internamente

        ### kubelet

        * Agente que corre en cada nodo
        * Se encarga de:

            * Crear Pods
            * Monitorear contenedores
            * Reportar estado al API Server
        
        ### Control Plane

        * Solo en el nodo control-plane:

            * kube-apiserver
            * etcd
            * scheduler
            * controller-manager

        * Son porcesos reales de Kubernetes

        ### Container Runtime (containerd)

        * Ejecuta los contenedores de los pods
        * Maneja imágenes
        * Comunica con kubelet

        ### CNI (kindnet)

        * Maneja networking de pods
        * Asigna IPs
        * Permite comunicación pod <-> pod

        ### kubectl (cliente externo)

        * No vivve dentro de Kind, pero es esencial:

            * Se conecta al API Server
            * Usa el `kubeconfig` generado

    ## Que No incluye Kind ?

    * Cloud Provider
    * LoadBalancer
    * Storage dinamico cloud
    * Alta disponibilidad real
    * Seguridad avanzada por defecto

    ## Diferencias conceptual: Kind vs Otros 

    | Herramienta | Cómo crea nodos     |
    | ----------- | ------------------- |
    | Kind        | Contenedores Docker |
    | Minikube    | VM o Docker         |
    | MicroK8s    | Binarios nativos    |
    | k3s         | Kubernetes ligero   |
