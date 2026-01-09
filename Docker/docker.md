# Docker

* Docker es una herramienta que utiliza **contenedores** para encapsular una aplicación junto con **todas sus dependencias** (librerias, configuraciones, runtime, etc.) en una unidad portable

    > *Funciona en mi máquina* deja de ser un problema con Docker

* Un contenedor **no es una máquina virtual**, sino un proceso aislado que comparte el **kernel del sistema operativo** anfitrión

    ## Para que sirve Docker ?

    * **Desarrollo**

        * Tener el **mismo entorno** en desarrollo, pruebas y producción
        * Evitar conflictos de versiones (Java, Python, Node, etc)
        * Levantar proyectos complejos con un solo comando

    * **Despliegue**

        * Empaquetar aplicaciones listas para producción
        * Facilitar **CI/CD** (integración y despliegue continuo)
        * Desplegar microservicios de forma independiente

    * **Escalabilidad**

        * Ejecutar múltiples instancias de un servicio
        * Integrarse fácilmente con **Kubernetes**

    * **Portabilidad**

        * Ejecutar la misma app en:

            * Laptop
            * Servidor on-premise
            * Cloud (AWS, GCP, Azure)

    ## Funcionamiento

    * Docker se basa en **virtualización a nivel de sistema operativo** usando contenedores.

        1. **Escribes un `Dockerfile`**

            * Define cómo construir la imagen

        2. **Docker construye una imagen**

            * Imagen = plantilla inmutable

        3. **Ejecutar un contenedor**

            * Contenedor = imagen en ejecución

        4. **Docker aísla el proceso**

            * CPU, memoria, red y sistema de archivos controlados

    * **Ejemplo mental sencillo**

        * **Imagen -> Contenedor**

            ```yaml
            Imagen: Python + FastAPI + App
            Contenedor: App corriendo en el puerto 8000
            ```
    
    ## Diferencia entre Docker y Maquina Virtual

    | Característica | Docker     | Máquina Virtual  |
    | -------------- | ---------- | ---------------- |
    | Arranque       | Segundos   | Minutos          |
    | Consumo        | Bajo       | Alto             |
    | Kernel         | Compartido | Propio           |
    | Peso           | MB         | GB               |
    | Aislamiento    | Proceso    | Sistema completo |

    ## Características

    * **Ligero**

        * Usa el kernel del host
        * Consume menos recursos que una VM

    * **Portable**

        * Funciona igual en cualquier entorno
    
    * **Rápido**

        * Arranque casi instantáneo
        * Ideal para microservicios

    * **Aislado**

        * Cada contenedor tiene su propio entorno
        * No interfiere con otros procesos

    * **Reproducible**

        * Mismo comportamiento siempre

    * **Escalable**

        * Diseñado para trabajar con orquestadores (Kubernetes)

    ## Elementos que componen Docker

    1. **Docker Engine**

        * Es el **corazon de Docker**

            * **Docker Daemon (`dockerd`)**

                * Servicio que gestiona contenedores, imágenes, redes y volúmenes

            * **Docker CLI**

                * Permite automatización e integración con otras herramientas

    2. **Imágenes Docker**

        * Una **imagen** es una plantilla inmutable para crear contenedores

        * Caracteristicas:

            * Se construye en **capas**
            * Son **de solo lectura**
            * Se definen con un `Dockerfile`

        * **Ejemplo**

            ```bash
            python:3.11-slim
            ```
    3. **Contenedores**

        * Un contenedor es:

            > Una **imagen en ejecución**
        
        * Caracteristicas

            * Tiene su propio filesystem
            * Puede iniciarse, detenerse o eliminarse
            * Es efimero (puede perder datos si no usas volúmenes)

    4. **Dockerfile**

        * Archivo de texto que define **cómo construir una imagen**

        * Ejemplo conceptual

            ```Dockerfile
            FROM python:3.11
            WORKDIR /app
            COPY . .
            RUN pip install -r requirements.txt
            CMD ["python", "app.py"]
            ```
    
    5. **Docker Hub / Registry**

        * Repositorio de imágenes:

            * **Docker Hub** (público)
            * Registro privados (AWS ECR, GCR, Harbor)

        * Función:

            * Subir (`push`) imágenes
            * Descargar (`pull`) imágenes

    6. **Redes Docker**

        * Permite la comunicación entre contenedores

        * Tipos:

            * `bridge` (por defecto)
            * `host`
            * `overlay` (multi-host)
            * `none`

        * Ejemplo

            ```bash
            backend ↔ redis ↔ postgres
            ```
    7. **Volúmenes**

        * Permiten **persistir datos** fuera del contenedor

        * Porque son necesarios ?

            * Los contenedores son efimeros
            * Sin volúmenes, los datos se pierden

        * Ejemplo

            ```nginx
            Postgres -> volumen -> disco
            ```
    8. **Docker Compose**

        * Herramienta para definir **aplicaciones multicontendor**

            * Usa `docker-compose.yaml`
            * Ideal para microservicios locales

        * Ejemplo

            ```nginx
            web + db + redis
            ```
    
    ## Resumen 

    | Concepto   | Explicación                     |
    | ---------- | ------------------------------- |
    | Docker     | Plataforma de contenedores      |
    | Imagen     | Plantilla de la app             |
    | Contenedor | Imagen ejecutándose             |
    | Dockerfile | Instrucciones de construcción   |
    | Volumen    | Persistencia de datos           |
    | Red        | Comunicación entre contenedores |
    | Compose    | Orquestación local              |
