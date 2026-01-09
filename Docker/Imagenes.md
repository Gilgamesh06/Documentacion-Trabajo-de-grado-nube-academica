# Imagenes

* Una **imagen Docker** es una **plantilla inmutable** que contiene **todo lo necesario para ejecutar una aplicación**, incluyendo:

    * Sistema de archivos base (ej: Alpine, Ubuntu)
    * Runtime (Python, Java, Node, ect)
    * Librerias y dependencias
    * Código de la aplicación
    * Configuración por defecto
    * Comando de arranque

* **Una imagen No se ejecuta**
* **Un contenedor Si se ejecuta**

    ## Para que sirve las imagenes Docker ?

    * **Estandarizar entornos**

        * El mismo entorno en dev, test y prod

    * **Protabilidad**

        * Ejecuta la app en cualquier host con Docker Engine

    * **Reproducibilidad**

        * Mismo comportamiento siempre

    * **Versionado**

        * Cada imagen puede tener versiones (tags)

    * **Base para contenedores**

        * Todo contenedor nace de una imagen

    ## Funcionamiento

    * Docker usa un **sistema de capas (layers)** basado en **Union File System** (OverlayFS)

    * **Funcinamiento interno**

        * Cada instrucción del `Dockerfile` crea una **capa**
        * Las capas son:

            * **Inmutables**
            * **Reutilizables**
            * **Cacheables**
        
        * Ejemplo visual

            ```yaml
            Capa 4: CMD python app.py
            Capa 3: COPY app.py
            Capa 2: RUN pip install flask
            Capa 1: FROM python:3.11
            ```
        * Cuando ejecutas un contenedor:

            * Docker agrega una **capa writable**
            * El resto son solo lectura
        
    ## Elementos que componen una Imagen Docker

    1. **Imagen base (`FROM`)**

        * Punto de partida
        * Puede ser:

            * `alpine`
            * `ubuntu`
            * `python:3.11-slim`

    2. **Capas (Layers)**

        * Cada instrucción = una capa
        * Se comportan entre imágenes
    
    3. **Metadatos**

        * Variables de entorno
        * Puerto expuesto
        * Comando por defecto
        * Labels
    
    4. **Manifest**

        * Describe arquitectura y SO
        * Permite imágenes multi-arch
    
    5. **Tags**

        * Versiones de la imagen
        * Ejemplo:

            ```bash
            myapp:1.0
            myapp:latest
            ```
    ## Ejemplo paso a paso 

    * Vamos a crear una imagen **Python + Flask** desde cero

    * **Paso 1: Crear estructura del proyecto**

        ```bash
        mi_app/
        ├── app.py
        ├── requirements.txt
        └── Dockerfile
        ``` 

    * **Paso 2: Código de la aplicación**

        * `app.py`

            ```python
            from flask import Flask

            app = Flask(__name__)

            @app.route("/")
            def home():
                return "Hola desde Docker"

            if __name__ == "__main__":
                app.run(host="0.0.0.0", port=5000)
            ```

        * `requirements.txt`

            ```ini
            flask==3.0.0
            ```

    * **Paso 3: Dockerfile**

        ```Dockerfile
        # Imagen base
        FROM python:3.11-slim 
        ```
        * `FROM` indica la imagen base
        * `python:3.11-slim` Python 3.11 en Debian reducido

    
        ```Dockerfile
        WORDIR /app
        ```
        * `WORKDIR` directorio de trabajo dentro del contenedor
        * Evita usar rutas absolutas repetidas

        ```Dockerfile
        COPY requirement.txt .
        ```
        * `COPY` copia archivos del host al contenedor
        * Copiamos primero para aprovechar cache

        ```Dockerfile
        RUN pip install --no-cache-dir -r requirements.txt
        ```
        * `RUN` ejecuta comandos en tiempo de build
        * `--no-cache-dir` reduce tamaño de la imagen

        ```Dockerfile
        COPY ...
        ```
        * Copia el resto del proyecto

        ```Dockerfile
        EXPOSE 5000
        ```
        * Documenta el puerto de la app
        * No abre el puerto automaticamente

        ```Dockerfile
        CMD ["python", "app.py"]
        ```
        * Comando por defecto al iniciar el contenedor
        * Forma exec (recomendada)

    * **Paso 4: Construir la imagen**

        ```bash
        docker build -t mi-flask-app:1.0 .
        ```
        * `build` construye una imagen
        * `-t` asigna nombre y tag
        * `.` contexto de construcción

    * **Paso 5: Ver imagenes creadas**

        ```bash
        docker images
        ```
    
    * **Paso 6: Ejecutar un contenedor desde la imagen**

        ```bash
        docker run -d -p 5000:5000 --name flask-container mi-flask-app:1.0
        ```
        * `run` crea y ejecuta el contenedor
        * `-d` modo detached (background)
        * `-p 5000:5000` mapea puertos (host:contenedor)
        * `--name` nombre del contenedor

    * **Paso 7: Ver contenedore activos**

        ```bash
        docker ps
        ```

    * **Paso 8: Inspeccionar la imagen**

        ```bash
        docker history mi-flask-app:1.0
        ```
        * Muestra capas y tamaños

        ```bash
        docker inspect mi-flask-app:1.0
        ```
        * Metadatos completos (JSON)

    
    ## Comandos clave

    | Comando          | Función          |
    | ---------------- | ---------------- |
    | `docker build`   | Crear imagen     |
    | `docker images`  | Listar imágenes  |
    | `docker rmi`     | Eliminar imagen  |
    | `docker pull`    | Descargar imagen |
    | `docker push`    | Subir imagen     |
    | `docker history` | Ver capas        |
    | `docker inspect` | Ver metadatos    |

    ## Resumen

    | Concepto   | Explicación                  |
    | ---------- | ---------------------------- |
    | Imagen     | Plantilla inmutable          |
    | Capas      | Instrucciones del Dockerfile |
    | Dockerfile | Define la imagen             |
    | Tag        | Versión                      |
    | Contenedor | Imagen en ejecución          |
