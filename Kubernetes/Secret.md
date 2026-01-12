# Secret

* Un **Secret** es un **objeto de Kubernetes** diseñado para **almacenar informacion sensible** que **No debe ir en texto plano dentro de la imagen Docker ni en el código**

    * Contraseñas
    * Tokens JWT
    * API Keys
    * Certificados
    * Credenciales de bases de datos

* **Técnicamente** Kubernetes los guarda en **Base64, No cifrados por defecto** (esto es importante)

    ## Para que sirve un Secret ?

    1. Separar **credenciales** del codigo
    2. Evitar exponer secretos en:

        * Git
        * Dockerfiles
        * Logs
    
    3. Inyectar secretos de forma controlada a Pods
    4. Rotar secretos sin reconstruir imágenes

    ## Funcionamiento

    1. Creas un Secret
    2. Kubernetes lo guarda en `etcd`
    3. Un Pod lo referencia
    4. Kubernetes lo inyecta como:
        
        * Variables de entorno
        * Archivos montados
        * Datos TLS
    
    * El contenedor **no sabe** si viene de Secret o ConfigMap

    ## Tipos de Secret

    | Tipo                             | Uso                     |
    | -------------------------------- | ----------------------- |
    | `Opaque`                         | Genérico (el más común) |
    | `kubernetes.io/dockerconfigjson` | Login a registries      |
    | `kubernetes.io/tls`              | Certificados TLS        |
    | `kubernetes.io/basic-auth`       | Usuario/contraseña      |
    | `kubernetes.io/ssh-auth`         | Claves SSH              |

    ## Elementos que componen un Secret

    | Campo        | Qué es                |
    | ------------ | --------------------- |
    | `apiVersion` | Versión API           |
    | `kind`       | Secret                |
    | `metadata`   | Nombre, namespace     |
    | `type`       | Tipo de Secret        |
    | `data`       | Claves Base64         |
    | `stringData` | Claves en texto plano |

    ## Ejemplo

    * **Archivo: `Secret.yaml`**

        ```yaml
        apiVersion: v1
        kind: Secret
        metadata:
            name: backend-secret
            namespace: app-secure
        type: Opaque
        data:
            DB_USER: YWRtaW4=
            DB_PASSWORD: c3VwZXJzZWNyZXQ=
        ```

        * `apiVersion: v1`

            * Los Secrets pertenencen a la API core de Kubernetes

        * `kind: Secret`

            * Indica que este recurso es un Secret

        * `metadata`

            ```yaml
            metadata:
                name: backend-secret
                namespace: app-secure
            ```
            * `name` Identificador único
            * `namespace` Ambito de visibilidad

        * `type: Opaque`

            * Secret generico
            * No impone estructura

        * `data`

            * Aqui va los **valores sensibles en Base64**
            
                ```yaml
                data:
                    DB_USER: YWRtaW4=
                ```
                * `YWRtaW4=` -> `admin`

                ```yaml
                DB_PASSWORD: c3VwZXJzZWNyZXQ=
                ```
                * `c3VwZXJzZWNyZXQ=` -> `supersecret`

    ## Porque Base64

    * Permite transportar binarios
    * No es seguirdad

    * La seguridad real viene de:

        * RBAC
        * Encrytion at rest
        * KMS (Vault, AWS KMS)

    ## Alternativa mas comoda: `stringData`

    ```yaml
    stringData:
        DB_USER: admin
        DB_PASSWORD: supersecret
    ```
    * Kubernetes los convierte a Base64 automaticamente

    ## Como usar un Secret en un Pod

    1. **Como variable de entorno**

        ```yaml
        envFrom:
        - secretRef:
            name: backend-secret
        ```
        
        * Dentro del contenedor:

            ```bash
            echo $DB_USER
            ```
    2. **Clave Específica**

        ```yaml
        env:
        - name: DB_PASSWORD
          valueFrom:
            secretKeyRef:
                name: backend-secret
                key: DB_PASSWORD
        ```

    3. **Como archivos montandos (Recomendado)**

        * **Volume**

            ```yaml
            volumes:
                - name: secret-volume
                  secret:
                    secretName: backend-secret
            ```

        * **Mount**

            ```yaml
            volumeMounts:
            - name: secret-volume
              mountPath: /etc/secrets
              realOnly: true
            ```
            
            * Kubernetes crea

                ```bash
                /etc/secrets/DB_USER
                /etc/secrets/DB_PASSWORD
                ```

    ## Que pasa si cambia un Secret ?

    | Método              | Actualiza          |
    | ------------------- | ------------------ |
    | Variable de entorno |  Requiere restart |
    | Archivo montado     |  Automático       |

    ## Seguridad Real

    * Encriptar etcd
    * Usar KMS
    * Rotación de secretos
    * No usar `kubectl get secret -o yaml`

    ## Errores comunes

    * Pensar que Base64 es cifrado
    * Subir secretos a Git
    * Usar Secrets para config no sensible
