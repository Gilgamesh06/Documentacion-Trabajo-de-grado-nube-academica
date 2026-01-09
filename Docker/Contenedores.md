# Contenedores

* Un **Contenedor Docker es**

    * Una **instancia en ejecución** de una imagen
    * Un **proceso aislado** del sistema operativo
    * Con su propio:

        * Sistemas de archivos
        * Red
        * Variables de entorno
        * PID namespaces

    * Que **comparte el kernel del host**

* **Imagen** = plantilla
* **Contenedor** = plantilla ejecutandose

    ## Para que sirve ?

    * **Ejecutar aplicaciones**

        * Backend (FastAPI, SpringBoot)
        * Frontend (React, Nginx)
        * Bases de datos (Postgres, Redis)

    * **Aislar procesos**

        * Cada app corre sin interferir con otras

    * **Escalar servicios**

        * Ejecutar múltiples instancias de la misma app

    * **Facilitar despliegues**

        * Arranque rápido 
        * Rollbacks sencillos
    
    * **Microservicios**

        * Un servicio -> un contenedor

    ## Funcionamiento

    * Docker usa **feactures del kernel de Linux**

        ### Namespaces (aislamiento)
        
        * `pid` -> procesos
        * `net` -> red
        * `mnt` -> filesystem
        * `ipc`, `uts`, `user`

        ### cgroups (control de recursos)

        * CPU
        * RAM
        * I/O

        ### Union FS (OverlayFS)

        * Capas de solo lectura (imagen)
        * Capa writable (contenedor)

    * **Flujo interno**

        1. Docker toma una imagen
        2. Agrega una capa writable
        3. Aisla el proceso
        4. Aplica límites de recursos
        5. Ejecuta el comando principal (`CMD`/`ENTRYPOINT`)

    ## Elementos que componen un contenedor

    1. **Imagen base**

        * Plantilla de donde nace el contenedor

    2. **Capa writable**

        * Donde se escribe cambios en runtime
        * Se pierde al borra el conteneor

    3. **Procesos principal (PID 1)**

        * Comando definiddo en `CMD` o `ENTRYPOINT`
        * Si muere -> el contenedor se detiene

    4. **Namespaces**

        * Aislamiento total del host

    5. **cgroups**

        * Limita recursos

    6. **Red del contenedor**

        * IP propia
        * Interfaces virtuales

    7. **Volúmenes**

        * Persisten datos

    ## Ejemplo

    * **Paso 1: Ejecutar un contenedor**

        ```bash
        docker run nginx
        ```
        1. Busca la imagen `nginx` local
        2. Si no existe -> `docker pull nginx`
        3. Crea un contenedor
        4. Ejecuta Nginx
        5. El proceso queda en foreground

    * **Paso 2: Ejecutar en modo detached**

        ```bash
        docker run -d nginx
        ```
        * **Flags**

            * `-d` -> detached (background)

        * El contenedor sigue corriendo sin bloquear la terminal

    * **Paso 3: Asignar nombre al contenedor**

        ```bash
        docker run -d --name web-nginx nginx
        ```
        * `--name` -> nombre legible

    * **Paso 4: Exponer puertos**

        ```bash
        docker run -d -p 8080:80 --name web-nginx nginx
        ```
        * `-p host:container`
        * `8080` -> puerto del host
        * `80` -> puerto interno de Nginx

    * **Paso 5: Ver contenedores activos**

        ```bash
        docker ps
        ```
        * `-a` -> muestra todos (activos + detenidos)
    
    * **Paso 6: Ver logs del contenedor**

        ```bash
        docker logs web-nginx
        ```
        * `-f` -> follow (logs en tiempo real)
        * `--tail 50` -> últimas 50 lineas

    * **Paso 7: Ejecutar comandos dentro del contenedor**

        ```bash
        docker exec -it web-nginx /bin/bash
        ```
        * `exec` -> ejecuta comando en contenedor activo
        * `-i` -> modo interactivo
        * `-t` -> pseudo-TTY
        * `/bin/bash` -> shell

    * **Paso 8: Detener el contenedor**

        ```bash
        docker stop web-nginx
        ```

    * **Paso 9: Iniciar contenedor detenido**

        ```bash
        docker start web-nginx
        ````
    * **Paso 10: Eliminar contenedor**

        ```bash
        docker rm web-nginx
        ```
        * `-f` -> fuerza eliminación

    ## Recursos y limites (cgroups)

    ```bash
    docker run -d \
        --memory=256m \
        --cpus="0.5" \
        nginx
    ```
    * `--memory` -> limite de RAM
    * `--cpu` -> nucleos disponibles

    ## Volúmenes en contenedores

    ```bash
    docker run -d \
        -v data:/var/lib/mysql \
        mysql
    ```
    * `-v volumen:ruta`
    * Persistente datos fuera del contenedor

    ## Resumen

    | Concepto      | Explicación         |
    | ------------- | ------------------- |
    | Contenedor    | Imagen en ejecución |
    | PID 1         | Proceso principal   |
    | Capa writable | Cambios en runtime  |
    | Namespaces    | Aislamiento         |
    | cgroups       | Control de recursos |
    | Volúmenes     | Persistencia        |


    