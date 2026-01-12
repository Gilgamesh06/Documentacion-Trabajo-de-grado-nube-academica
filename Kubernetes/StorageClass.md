# StorageClass

* Un **StorageClass** es una **plantilla de aprovisionamiento de almacenamiento**

    > Un StorageClass define como y con que tipo de disco se crean los PersistenVolumes automaticamente

    ## Para que sirve ?

    * Crear **PV automaticamente**
    * Definir **tipo de disco** (ssd HDD, NFS, local, cloud)
    * Evitar crear PV a mano
    * Hace cluster **portables y escalables**

    * En Kubernetes moderno **No se crean PV manuales ->** se usan StorageClass

    ## Funcionamiento

    1. Exite un **StorageClass**
    2. Un **PVC lo referencia**
    3. Kubernetes llama al **provisioner**
    4. El provisiones crea un **PV real**
    5. PVC <-> PV queda **Bound**
    6. El Pod usa el PVC

    * El StorageClass **solo actúa cuando aparece un PVC**


    ## Elmentos que componen un StorageClass

    | Campo                  | Función                    |
    | ---------------------- | -------------------------- |
    | `provisioner`          | Driver que crea el volumen |
    | `parameters`           | Configuración del volumen  |
    | `reclaimPolicy`        | Qué pasa al borrar el PVC  |
    | `volumeBindingMode`    | Cuándo se crea el PV       |
    | `allowVolumeExpansion` | Permite crecer el volumen  |

    ## Ejemplo

    ```yaml
    apiVersion: storage.k8s.io/v1
    kind: StorageClass
    metadata:
        name: standard
    provisiones: rancher.io/local-path
    reclaimPolicy: Delete
    volumeBindingMode: WaitForFirstConsumer
    allowVolumeExpansion: True
    ```
    * Ahora vamos **linea por linea**, con profundidad

    ```yaml
    apiVersion: storage.k8s.io/v1
    ```
    * Usa la API de alamacenamiento de Kubernetes
    * Los StorageClass pertenecen al grupo `storage.k8s.io`

    ```yaml
    kind: StorageClass
    ```
    * Define que este recurso es un **StorageClass**
    * Es un recurso `cluster-wide` (No tiene namespace)

    ```yaml
    metadata:
        name: standard
    ```
    * Nombre del StorageClass
    * Este nombre se usa en el PVC: `storageClassName: standard`

    ```yaml
    provisioner: rancher.io/local-path
    ```
    * El componente mas importante
    * Define **quien crea el volumen real**

    ```yaml
    reclaimPolicy: Delete
    ```
    * Define que pasa **cuando borras el PVC**

        | Campo                  | Función                    |
        | ---------------------- | -------------------------- |
        | `provisioner`          | Driver que crea el volumen |
        | `parameters`           | Configuración del volumen  |
        | `reclaimPolicy`        | Qué pasa al borrar el PVC  |
        | `volumeBindingMode`    | Cuándo se crea el PV       |
        | `allowVolumeExpansion` | Permite crecer el volumen  |

    * En producción critica suele usarse `Retain`

    ```yaml
    volumeBindingMode: WaitForFirstConsumer
    ```
    * Cuando se crea el volumen

        | Modo                   | Significado          |
        |------------------------|----------------------|
        | `Immediate`            | Al crear el PVC      |
        | `WaitForFirstConsumer` | Cuando un Pod lo usa |

    * Evita crear discos innecesarios

    ```yaml
    allowVolumeExpansion: true
    ```
    * Permite aumentar el tamaño del PVC
    * Ejemplo

        ```bash
        kubectl edit pvc app-pvc 
        ```
    * Cambiar:

        ```yaml
        storage: 5Gi
        ```
    