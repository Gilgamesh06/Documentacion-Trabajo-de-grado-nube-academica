# Docker Engine

* Docker Engine es una **aplicación cliente-servidor** que se ejecuta en el host y se encarga de:

    * Crear imagenes
    * Ejecutar contenedores
    * Gestionar Redes
    * Gestionar volúmenes
    * Comunicarse con registros (Docker Hub, ECR, GCR, etc)

    ## Para que sirve Docker Engine ?

    * Docker Engine sirve para:

        * **Ejecutar contenedores**

            * Arrancar, detener y eliminar contenedores
            * Aislar procesos del sistema

        * **Construir imágenes**

            * Interpretar Dockerfiles
            * Crear imagenes por capas
        
        * **Administrar recursos**

            * CPU
            * Memoria
            * Disco
            * Red

        * **Automatizar despliegues**

            * Integrarse con CI/CD
            * Ejecutar aplicaciones reproducibles

        * **Proveer una API estándar**

            * Permite que otras herramientas (Docker Compose, Kubernetes, Portainer) interactuen con Docker

    ## Funcionamiento

    * Docker Engine funciona con una **arquitectura cliente-servidor**

        1. **El usuario ejecuta un comado**

            ```bash
            docker run nginx
            ```
        
        2. **Docker CLI** envia la orden a la API de Docker
        3. **Docker Daemon (`dockerd`)**

            * Verifica si la imagen existe localmente
            * Si no existe -> la descarga del registro
            * Crea el contenedor

        4. **Container Runtime**

            * Usa namespaces y cgroups del kernel
            * Aisla procesos, red y fliesystem
        
        5. **El contenedor se ejecuta**

            * Como un proceso del host, pero aislado

    ## Arquitectura interna

    ```bash
    +------------------+
    |  Docker CLI      |
    +------------------+
            |
            v
    +------------------+        REST API
    | Docker API       | <----------------+
    +------------------+                  |
            |                             |
            v                             |
    +------------------+                  |
    | Docker Daemon    | (dockerd)        |
    +------------------+                  |
            |                             |
            v                             |
    +------------------+                  |
    | containerd       |------------------+
    +------------------+
            |
            v
    +------------------+
    | runc             |
    +------------------+
            |
            v
    +------------------+
    | Kernel Linux     |
    | (namespaces,     |
    |  cgroups)        |
    +------------------+
    ```

    ## Elementos que componen Docker Engine

    1. **Docker CLI**

        * Interfaz de linea de comandos
        * No ejecuta contenedores directamente
        * Envia instrucciones al daemon via API

        * **Comandos comunes**

            ```bash
            docker run
            docker build
            docker ps
            docker images
            ```
    2. **Docker Daemon (`dockerd`)**

        * Es el **proceso principal** de Docker Engine

            * Gestionar contenedores
            * Gestiona imágenes
            * Gestiona redes
            * Gestiona volúmenes
            * Expone la **Docker API**

        * Se ejecuta en segundo plano como servicio del sistema

    3. **Docker API (REST)**

        * API HTTP/REST
        * Permite interacción programática
        * Usada por:

            * Docker CLI
            * Docker Compose
            * Kubernetes
            * Portainer

        * **Ejemplo conceptual**

            ```http
            POST /containers/create
            POST /containers/start
            ```
        
    4. **containerd**

        * Runtime de contenedores de bajo nivel
        * Encargado de:
            
            * Descargar imágenes
            * Gestionar snapshots
            * Controlar el ciclo de vida del contenedor
            
        * Docker **delegó** esta responsabilidad a `containerd`

    5. **runc**

        * Runtime OCI (Open Container Invitiative)
        * Ejecuta el contenedor usando:

            * namespaces
            * cgroups
            * capabilities del kernel
        
        * `runc` crea el proceso aislado real

    6. **Kernel del sistema operativo**

        * Docker Engine depende del kernel para:

            * **Namespaces**

                * PID, NET, MNT, IPC, UTS, USER

            * **cgroups**

                * Control de CPU, RAM, I/O

            * **Filesystem**

                * OverlayFS

    ## Resumen

    | Componente    | Función                 |
    | ------------- | ----------------------- |
    | Docker CLI    | Enviar comandos         |
    | Docker API    | Comunicación REST       |
    | Docker Daemon | Orquestador local       |
    | containerd    | Gestión de contenedores |
    | runc          | Ejecución real          |
    | Kernel        | Aislamiento             |
