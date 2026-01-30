# Volumenenes

* Un **Volumen Docker** es un **espacio de almacenamiento gestionado por Docker,** que vive **fuera del filesystem del contenedor** pero puede ser montado dentro del el

* El volumen:

    * No pertenece al contenedor
    * Puede ser compartido
    * Persiste en el host
    * Es independiente del ciclo de vida del contenedor

    ## Para que sirve ?

    * **Persistir datos**

        * Bases de datos (PostgreSQL, MySQL, Redis)
        * Archivos generados por la app
        * Logs
    
    * **Compartir datos**

        * Entre contenedores
        * Entre servicios

    * **Backup y migración**

        * Exportar datos fácilmente
        * Mover datos entre entornos

    * **Separación de responsabilidades**

        * Código -> imagen
        * Datos -> volumen

    ## Funcionamiento

    * Docker usa un **mount point** del host y lo conecta al contenedor

    * **Flujo interno**

        1. Docker crea o detecta un volumen
        2. El volumen vive en el host:

            ```bash
            /var/lib/docker/volumes/
            ```
        3. Docker monta ese volumen dentro del contenedor
        4. El contenedor escribe/lee datos
        5. El volumen persiste aunque el contenedor muere

    * Docker gestiona permisos, rutas y montaje

    ## Elementos que componen un volumen

    1. **Nombre del volumen**

        * Identificador lógico

        * Ejemplo:

            ```bash
            pgdata
            ```
    2. **Driver del volumen**

        * Controla cómo se almacena el volumen

        * Tipos comunes:

            * `local` (por defecto)
            * NFS
            * Cloud (EBS, Azure, Disk)

    3. **Punto de montaje (mount point)**

        * Ruta dentro del contenedor

        * Ejemplo

            ```bash
            /bar/lib/postgresql/data
            ```

    4. **Ubicación en el host**

        * Docker la administra automaticamente

    5. **Opciones (opts)**

        * Permisos
        * Tipo de FS
        * Configuracion del driver

    
    ## Tipos 

    * **Volume**

        * Docker administra
        * Portable
        * Seguro
    
    * **Bind mount**

        * Ruta especifica del host
        * Dependiente del sistema

    * **tmpfs**

        * En memoria RAM
        * Temporal

    ## Ejemplo

    * Crear: **PostgreSQL con volumen**

        ### Paso 1: Crear el volumen

        ```bash
        docker volume create pgdata
        ```
        * `docker volume` -> gestiona volúmenes
        * `create` -> crea uno nuevo
        * `pgdata` -> nombre del volumen

        ### Paso 2: Listar volúmenes

        ```bash
        docker volume ls
        ```

        ### Paso 3: Inspeccionar el volumen

        ```bash
        docker volume inspect pgdata
        ```

        * **Resultado clave**

            ```json
            "Mountpoint": "/var/lib/docker/volumes/pgdata/_data"
            ```
            * Ahi vive los datos reales

        ### Paso 4: Ejecutar PostgreSQL usando el volumen

        ```bash
        docker run -d \
          --name postgres-db \
          -e POSTGRES_USER=admin \
          -e POSTGRES_PASSWORD=secret \
          -e POSTGRES_DB=appdb \
          -v pgdata:/var/lib/postgresql/data \
          -p 5432:5432 \
          postgres:16
        ```
        * `-d`: Ejecuta en background
        * `--name postgres-db`: Nombre del contenedor
        * `-e`: Variables de entorno
        * `-v pgdata:/var/lib/postgresql/data`

            * Monta el volumen
            * `pgdata` -> volumen
            * `/var/lib/postgresql/data` -> ruta interna
        
        * `-p 5432:5432`

            * Puerto host -> contendor

        ### Paso 5: Verificar persistencia

        1. Crear datos en PostgreSQL
        2. Detener contenedor

            ```bash
            docker stop postgres-db
            ```
        3. Eliminar contenedor

            ```bash
            docker rm postgres-db
            ```
        4. Crear un nuevo usando el mismo volumen

            * Los datoa siguen hay

    ## Eliminar volumenes

        ```bash
        docker volume rm pgdata
        ```
        * Solo si no esta en uso

    ## Eliminar volúmenes huérfanos

        ```bash
        docker volume prune
        ```
