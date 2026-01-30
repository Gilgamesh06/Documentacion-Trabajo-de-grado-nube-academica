# Relacion PV PVC SC

* Ahora vamso a **cerrar el circulo mental** y dejar esto **grabado para siempre**, porque **PV, PVC, StorageClass no se entienden por separado**, sino **como un sistema**

    ## Idea general 

    > StorageClass define cómo se crea el almacenamiento, PVC pide cuantó alamacenamiento necesita, PV es el almacenamiento real que Kubernetes entrega

    ## Que es cada uno dentro del sistema ?

    | Recurso          | Rol                              |
    | ---------------- | -------------------------------- |
    | **StorageClass** | Plantilla / política de creación |
    | **PVC**          | Solicitud de almacenamiento      |
    | **PV**           | Volumen físico/logico creado     |

    * **StorageClass** Define las reglas de juego

        * Tipo de disco
        * Donde se crea
        * Politica de borrado
        * Rendimiento
        * Aprovisionamiento automático

        * **No almacena datos**
        * **No pertenece a un namespace**

    * **PVC (PersistentVolumeClaim)** Es la petición del usuario / app

        * Tamaño solicitado
        * Modo de acceso
        * StorageClass deseado

        * Vive en un **namespace**
        * No sabe donde esta el disco

    * **PV (PersitentVolume)** Es el volumen real

        * Representa un disco
        * Tiene capacidad real
        * Esta ligado a un PVC

        * Es **cluster-wide**
        * Lo crea Kubernetes (o tu, manualmente)


    ## Flujo real 

    * **Paso 1: Existe un StorageClass**

        ```yaml
        kind: StorageClass
        metadata:
            name: standard
        provisioner: rancher.io/local-path
        ```
        * Kubernetes ya sabe **como crear discos**

    * **Paso 2: Crea un PVC**

        ```yaml
        kind: PersistentVolumeClaim
        spec:
            storageClassName: standard
            resources:
                requests:
                    storage: 1Gi
        ```
        * El PVC dice:

            * *Quiero **1Gi**, usando el Storageclass `standard`*

    * **Paso 3: Kubernetes actúa**

        * Internamente Kubernetes:

            1. Busca el StorageClass: `standard`
            2. Llama al **provisioner**
            3. Crea un **PV automaticamente**
            4. Hace el **binding** PVC <-> PV

            * El usuario **No crea el PV**

    * **Paso 4: El PV aparece**

        ```yaml
        kind: PersistentVolume
        spec:
            capacity:
                storage: 1Gi
            storageClassName: standard
            claimRef:
                name: app-pvc
        ```
        * El PV queda **exclusivamente ligado** a ese PVC

    * **Paso 5: Un Pod usa el PVC**

        ```yaml
        volumes:
        - name: data
          persistentVolumeClaim:
            claimName: app-pvc
        ```
        * El Pod **nunca monta un PV directamente**

    ## Relacion Exacta

    * **PVC <-> PV**

        * 1 PVC <-> 1 PV
        * Relación **exclusiva**
        * Binding automatico

    * **StorageClass <-> PV**

        * Un StorageClass puede crear **muchos PV**
        * El PV *recuerda* que StorageClass lo creo

    * **Pod <-> PVC**

        * Un Pod usa **PVC**
        * Nunca usa PV directamente

    ## Esquema mental

    ```bash
    [ StorageClass ]
        ↓
    define cómo crear
        ↓
    [ PersistentVolume ]
        ↑
    creado automáticamente
        ↑
    [ PersistentVolumeClaim ]
        ↓
    usado por
        ↓
    [ Pod ]
    ```

    ## Tabla comaprativa

    | Aspecto         | StorageClass  | PVC          | PV         |
    | --------------- | ------------  | -----------  | ---------- |
    | Namespace       | No            | Si           | No         |
    | Guarda datos    | No            | No           | Si         |
    | Lo crea         | Admin         | Usuario/App  | Kubernetes |
    | Vive sin Pod    | Si            | Si           | Si         |
    | Se monta en Pod | No            | Si           | No         |
