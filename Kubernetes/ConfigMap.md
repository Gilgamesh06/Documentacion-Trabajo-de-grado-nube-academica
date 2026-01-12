# ConfigMap

* Un **ConfigMap** es un **objeto de Kubernetes** que sirve para **almacenar configuración no sensible** y **separarla de código de la aplicación**

    * Variables de entorno
    * Archivos `.conf`
    * Parámetros de ejecución
    * URLs, puertos, flags, modos (`dev`, `prod`)
    * **No guardar secretos** (para eso esta `secret`)

    ## Para que sirve ?

    1. Evitar **harcodear** configuración en la imagen Docker
    2. Reutilizar la **misma imagen** en distintos entornos
    3. Cambiar configuración **sin reconstruir la imagen**
    4. Inyectar configuración como:

        * Variables de entorno
        * Archivos 
        * Argumentos de ejecución

    ## Funcionamiento

    1. Creas un ConfigMap
    2. Kubernetes lo guarda en `etcd`
    3. Un Pod lo **referencia**
    4. Kubernetes inyecta la configuración al contenedor

    * El contenedor **no sabe** que viene de un ConfigMap

    ## Elementos que componen un ConfigMap

    | Campo        | Qué es                          |
    | ------------ | ------------------------------- |
    | `apiVersion` | Versión de la API               |
    | `kind`       | Tipo de recurso (`ConfigMap`)   |
    | `metadata`   | Nombre, namespace, labels       |
    | `data`       | Pares clave–valor (texto plano) |
    | `binaryData` | Datos binarios (Base64)         |

    ## Ejemplo

    * **Archivo: `configmap.yaml`**

        ```yaml
        apiVersion: v1
        kind: ConfigMap
        metadata:
            name: backend-config
            namespace: app-secure
        data: 
            APP_NAME: backend-a
            APP_ENV: production
            APP_PORT: production
            APP_PORT: "8000"
            DATABASE_URL: "postgresql://db:5432/app"
        ```

        * `appVersion: v1`

            * Usa la API base de Kubernetes
            * ConfigMap pertenece al core (`v1`)

        * `kind: ConfigMap`

            * Indica que este recurso es un **ConfigMap**

        * `metadata`

            * Información administrativa

                ```yaml
                metadata:
                    name: backend-config
                    namespace: app-secure
                ```
                
                * `name` Nombre unico del ConfigMap dentro del namespace
                * `namespace` Solo los Pods en este namespace puede usarlo

        * `data`

            * Aqui va la **configuración real**

                ```yaml
                data:
                    APP_NAME: backend-a
                ```
                * Clave `APP_NAME`
                * Valor `backend-a`
                
            * Todo es **string**, aunque parezca número.

            ```yaml
            APP_PORT: "8000"
            ```
            * El puerto se pone entre comillas
            * Kubernetes no tiene tipos

            ```yaml
            DATABASE_URL: "postgresql://db:5432/app"
            ```
            * Valor típico de configuración externa

    ## Como usar el ConfigMap en un Pod

    1. **Como variables de entorno (la mas comun)**

        ```yaml
        envFrom:
        - configMapRef:
            name: backend-config
        ```
        * Inyecta **todas** las claves como variables de entorno

        * **Deploy completo**

            ```yaml
            containers:
            - name: backend
              image: gilgamesh06/test:1.0
              envFrom:
              - configMapRef:
                name: backend-config
            ```

        * Dentro del contenedor

            ```bash
            echo $APP_NAME
            echo $DATABASE_URL
            ```
        
    2. **Como Variables individuales**

        ```yaml
        env:
        - name: APP_ENV
          valueFrom:
          configMapKeyRef:
            name: backend-config
           key: APP_ENV
        ```
        * Solo inyecta una clave especifica

    3. **Como archivo montado**

        * **ConfigMap con archivo**

            ```yaml
            data:
                app.conf: |
                    server.port=8000
                    server.name=backend-a
            ```

        * **Montaje en el Pod**

            ```yaml
            volumeMounts:
            - name: config-volume
              mountPath: /etc/config
            ```
            ```yaml
            volumes:
            - name: config-volume
              configMap:
                name: backend-congfig
            ```

            * Kubernetes crea:

                ```bash
                /etc/config/app.conf
                ```
    ## Que pasa si modifcas el ConfigMap ?

    | Método              | Resultado                      |
    | ------------------- | ------------------------------ |
    | Variable de entorno |  Requiere reiniciar Pod        |
    | Archivo montado     |  Se actualiza automáticamente  |

    ## Buenas práticas

    * No guardar contraseñas
    * Usar nombres claros
    * Un ConfigMap por app
    * Versionar cambios criticos

    ## Errores comunes

    * Usar ConfigMap para passwords
    * Pensar que es dinamico siempre
    * Mezclasr config de muchas apps

    

            