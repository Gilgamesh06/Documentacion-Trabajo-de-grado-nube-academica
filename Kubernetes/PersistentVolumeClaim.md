# PersistentVolumeClaim (PVC)

* Un **PersistentVolumeClaim (PVC)** es una **solicitud de almacenamiento** hecha por un **pod**

    > **Un PVC es cuando una aplicación dice: *Necesito disco de X tamaño, con estas caracteristicas***
    * El **PVC No es el disco,** es la petición del disco

    ## Para que sirve un PVC ?

    * Un PVC sirve para:

        * Pedir almacenamiento persistente
        * Desacoplar la app del tipo de disco
        * Permitir que los Pods se reinicien sin perder datos
        * Facilitar portabilidad entre cluster

    ## Funcionamiento

    1. El **PVC solicita** almacenamiento (size, access mode, storageClass)
    2. Kubernetes busca un **PV compatible**
    3. Si existe -> **los enlaza (Bound)**
    4. El Pod usa el PVC como volumen
    5. Los datoas sobreviven al Pod
    * El Pod **Nunca** usa un PV directamente, **siempre usa un PVC**

    ## Elementos que componen un PVC

    | Elemento                          | Función                                   |
    | --------------------------------- | ----------------------------------------- |
    | `apiVersion`                      | Versión de la API                         |
    | `kind`                            | Tipo de recurso (`PersistentVolumeClaim`) |
    | `metadata`                        | Nombre, namespace                         |
    | `spec.accessModes`                | Cómo se puede montar                      |
    | `spec.resources.requests.storage` | Tamaño solicitado                         |
    | `spec.storageClassName`           | Tipo de almacenamiento                    |

    ## Ejemplo

    ```yaml
    apiVersion: v1
    kind: PersistenVolumeClaim
    metadata:
        name: app-pvc
        namespace: app-secure
    spec:
        accessModes:
            - ReadWriteOnce
        resources:
            requests:
                storage: 1Gi
        storageClassName: standard
    ```

    * **Explicación**

        ### `apiVersion: v1`

        * Indica la version de la API de Kubernetes usada para PVC
        * PVC es un recurso **core**, por eso es `v1`

        ### `kind: PersistentVolumeClaim`

        * Define que este recurso es una **solicitud de volumen persistente**

        ### `metadata: name: app-pvc`

        * Nombre del PVC
        * Este nombre sera usado luego por el ***Pod**

        ### `namespace: app-secure`

        * El PVC es **namespaced**

            * Solo Pods del mismo namespace puede usarlo

        ### `spec:`

        * Aquí empiexa la **especificacion de la solicitud**

        ### `accessModes: - ReadWriteOnce`

        * Define **como puede montarse el volumen**

            | Modo                  | Significado                    |
            | --------------------- | ------------------------------ |
            | `ReadWriteOnce (RWO)` | 1 nodo puede leer/escribir     |
            | `ReadOnlyMany (ROX)`  | Muchos nodos solo lectura      |
            | `ReadWriteMany (RWX)` | Muchos nodos lectura/escritura |
                
        ### `resources: request: storage: 1Gi`

        * Cantidad de almacenamiento solicitada
        * Kubernetes buscara un **PV >= 1Gi**
        * No puede ser menor

        ### `storageClassName: standard`

        * Tipo de almacenamiento deseado

            * `standard`
            * `fast`
            * `local-path`
            * `nfs`

        * Si existe un StorageClass -> **se puede crear el PV automaticaménte**

    ## Como se conecta el PVC con un Pod ?

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
        name: app
        namespace: app-secure
    spec:
        containers:
        - name: app
          image: nginx
          volumeMounts:
          - name: data
            mountPath: /data
        volumes:
        - name: data
          persistentVolumeClaim:
            claimName: app-pvc
    ``` 
    *  El Pod **No sabe nada del PV**
    * Solo dice: 

        * *Quiero usar el PVC `app-pvc`*

    ## Comandos utiles

    ```bash
    kubectl get pvc -n app-secure
    kubectl describe pvc app-pvc -n app-secure
    ```