# PersistentVolume (PV)

* Un **PersistentVolume (PV)** es:

    > Un recurso de Kubernetes que representa almacenamiento fisico o lógico disponible en el clúster, gestionado por el administrador del clúster. idenpendiente de los Pods

* Idea clave:

    * Un **Pod es efimero**
    * Un **PV es persistente**
    * El PV **vive más que el Pod**

    ## Para que sirve ?

    * Un PV sirve para:

        * Persistir datos cuando un Pod muere
        * Compartir almacenamiento entre Pods (segun modo)
        * Separar:

            * **Quién administra el storage** (admin)
            * **Quién lo usa** (aplicación)

        *  Casos tipicos

            * Bases de datos
            * Archivos subidos por usuarios
            * Logs
            * Cache persistente
            * Modelos ML

    ## Funcionamiento

    ```bash
    [ Storage real ]
        ↓
    [ PersistentVolume ]
        ↓ (binding)
    [ PersistentVolumeClaim ]
        ↓ (mount)
    [ Pod ]
    ```
    1. El **admin crea un PV**
    2. El **developer crea un PVC**
    3. Kubernetes **hace el binding**
    4. El Pod **monta el PVC**
    5. El Pod muere -> **los datos permanecen**

    ## Elementos que componen un PersistentVolume

    * Un PV está compuesto por:

        | Elemento                      | Qué es                    |
        | ----------------------------- | ------------------------- |
        | capacity                      | Tamaño del volumen        |
        | accessModes                   | Cómo se puede montar      |
        | storageClassName              | Clase de almacenamiento   |
        | persistentVolumeReclaimPolicy | Qué pasa al borrar el PVC |
        | volumeType                    | Tipo real de storage      |
        | nodeAffinity                  | En qué nodos puede usarse |
                
    ## Tipos de alamcenamiento (volume types)

    * Ejemplos comunes

        * `hostPath` (local al nodo - solo labs)
        * `nfs`
        * `awsElasticBlockStore`
        * `gcePersistentDisk`
        * `azureDisk`
        * `csi` (moderno y recomendado)

    * En **Kind / labs**, usamos **hostPath**

    ## Ejemplo 

    1. **PersistentVolume (PV)**

        ```yaml
        apiVersion: v1
        kind: PersistentVolume
        metadata:
            name: pv-demo
        spec:
            capacity:
                storage: 1Gi
        ```

        ### Explicacion

        ```yaml
        apiVersion: v1
        ```
        * API core de Kubernetes
        * Los PV pertenecen al core

        ```yaml
        kind: PersistentVolume
        ```
        * Define que este recurso es un **PV**
        * Es **cluster-wide** (no pertenece a un namespace)

        ```yaml
        metadata:
            name: pv-demo
        ```
        * Nombre único del volumen en el clúster

        ```yaml
        spec:
        ```
        * Configuración real del volumen

        ```yaml
        capacity:
            storage: 1Gi
        ```
        * Tamaño disponible
        * Kubernetes **no lo hace cumplir fisicamente**, es declarativo

    2. **accessModes**

        ```yaml
        accessModes:
            - ReadWriteOnce
        ```
        * Define **cómo puede ser montado el volumen**

            | Modo                | Significado                |
            | ------------------- | -------------------------- |
            | ReadWriteOnce (RWO) | 1 nodo puede leer/escribir |
            | ReadOnlyMany (ROX)  | Muchos nodos solo lectura  |
            | ReadWriteMany (RWX) | Muchos nodos leer/escribir |
                    
        * `hostPath` **solo soporta RWO**

    3. **storageClassName**

        ```yaml
        storageClassName: manual
        ```
        * Etiqueta lógica para agrupar volúmenes
        * El PVC debe pedir **la misma clase**

    4. **reclaimPolicy**

        ```yaml
        persistentVolumeReclaimPolicy: Retain
        ```
        * **Qué pasa cuando se borra el PVC ?**

            | Política | Comportamiento             |
            | -------- | -------------------------- |
            | Retain   | El volumen queda con datos |
            | Delete   | Se elimina el volumen      |
            | Recycle  | Obsoleto                   |

    5. **volume real (hostPath)**

        ```yaml
        hostPath:
            path: /data/pv-demo
        ```
        * El volumen es un **directorio del nodo**
        * Solo para labs / pruebas
        * No usar en producción

    * **PV completo**

        ```yaml
        apiVersion: v1
        kind: PersistentVolume
        metadata:
            name: pv-demo
        spec:
            capacity:
                storage: 1Gi
            accessModes:
                - ReadWriteOnce
            storageClassName: manual
            persistentVolumeReclaimPolicy: Retain
            hostPath:
                path: /data/pv-demo
        ```
    
    ## Como se usa este PV

    * Un Pod No usa un PV directamente

    * Necesita un **PersistentVolumeClaim (PVC)**

    * **PersistentVolumeClaim (PVC)**

        ```yaml
        apiVersion: v1
        kind: PersistentVolumeClaim
        metadata:
            name: pvc-demo
            namespace: app-secure
        spec:
            accessModeS:
                - ReadWriteOnce
            resources:
                requests:
                    storage: 1Gi
            storageClassName: manual
        ``` 
        * Kubernetes hace:

            * Compara tamaño
            * accessModes
            * storageClassName
            * Si coinciden -> **BIND**

    * **Pod usando el PVC**

        ```yaml
        apiVersion: v1
        kind: Pod
        metadata:
            name: pod-demo
            namespace: app-secure
        spec:
            containers:
            - name: app
              image: busybux
              command: ["sh", "-c", "echo hello > /data/hello.txt && sleep 3600"]
              volumeMounts:
              - name: data
                mountPath: /data
            volumes:
            - name: data
              persistentVolumeClaim:
                claimName: pvc-demo     
        ```
        