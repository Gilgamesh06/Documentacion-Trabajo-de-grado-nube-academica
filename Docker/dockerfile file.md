# Dockerfile

* Un **Dockerfile** es un archivo de texto (sin extensión) que contiene una **lista ordenada de instrucciones**

* Cada instrucción

    * Se ejecuta **de arriba hacia abajo**
    * Crea una **capa (layer)** en la imagen
    * Puede reutilizarse desde caché
    
* Dockerfile -> Imagen -> Contenedor

    ## Para que sirve ?

    * **Definir el entorno de la aplicación**

        * Sistema base
        * Lenguaje
        * Dependencias
        * Variables de entorno

    * **Automatizar la construcción**

        * Sin pasos manuales
        * Sin errores humanos

    * **Reproducibilidad**

        * Misma imagen siempre

    * **Versionado**

        * Vive en Git junto al código

    * **CI/CD**

        * Base de pipelines modernos

    ## Funcionamiento

    * Cuando ejecutas 

        ```bash
        docker build .
        ```
    * Docker hace lo siguiente:

        1. Lee el Dockerfile
        2. Ejecuta cada instrucción en orden
        3. Crea una **capa** por instrucción
        4. Usa **cache** si no hubo cambios
        5. Genera una imagen final
    
    * Si una capa cambia -> las siguientes se reconstruyen

    ## Ejemplo

    * **Estructura del proyecto**

        ```bash
        api/
        ├── main.py
        ├── requirements.txt
        └── Dockerfile
        ```
    
    * **Código de la app (`main.py`)**

        ```python
        from fastapi import FastAPI

        app = FastAPI()

        @app.get("/")
        def root():
            return {"msg": "Hola desde Dockerfile"}        
        ```
    
    * **Dockerfile explicado linea por linea**

        * `FROM`

            ```dockerfile
            FROM python:3.11-slim
            ```
            * Define la **imagen base**
            * **Proporciona**

                * Sistema operativo minimo
                * Runtime (Python)

            * **Funcionamiento**

                * Docker descarga la imagen si no existe y la usa como capa inicial

        * `WORKDIR`

            ```bash
            WORKDIR /app
            ```
            * Define el directorio de trabajo dentro del contenedor

            * **Sirve para**

                * Evitar rutas largas
                * Mantiene orden
            
            * **Funcionamiento**

                * Si no existe, Docker lo crea

        * `COPY`

            ```dockerfile
            COPY requirements.txt .
            ```
            * Copia archivos del host al contenedor
            
            * **Sirve para**

                * Mover código
                * Mover configuraciones

            * **Funcionamiento**

                * Usa el **contexto de build**
                * Respeta `.dockeringnore`
            
            * Diferencias a `ADD` (mas simple y predecible)

        * `RUN`

            ```dockerfile
            RUN pip install --no-cache-dir -r requirements.txt
            ```
            * Ejecuta comandos **durante la construcción**

            * **Para que sirve**

                * Instalar dependencias
                * Compilar código
                * Configurar entorno

            * **Funcionamiento**

                * Ejectua en un contenedor temporal
                * Crea una nueva capa

        * `COPY`

            ```dockerfile
            COPY . .
            ```
            * Copia el resto del proyecto
            * Se coloca después para aprovechar cache

        * `EXPOSE`

            ```dockerfile
            EXPOSE 8000
            ```
            * Documenta el puerto que usa la app

            * **Sirve para**

                * Informativo
                * Ayuda a herramientas externas

            * **Funcionamiento**

                * No abre el puerto
                * Solo indica intención

        * `CMD`

            ```dockerfile
            CMD ["uvicorn", "main.py", "--host", "0.0.0.0", "--port", "8000"]
            ```
            * Comando por defecto del contenedor

            * **Sirve para**

                * Arrancar la aplicacion

            * **Funcionamiento**

                * Se ejecuta en `docker run`
                * Puede sobreescribirse

            * Forma **exec** recomendada

    ## Otras instrucciones importantes

    * `ENV`

        ```dockerfile
        ENV APP_ENV=produccion
        ```
        * Define variables de entorno

    * `ARG`

        ```Dockerfile
        ARG VERSION=1.0
        ```
        * Variables solo en build

    * `ENTRYPOINT`

        ```dockerfile
        ENTRYPOINT ["python"]
        ```
        * Comando fijo
        * No se sobrescribe facilmente

    * `LABEL`

        ```dockerfile
        LABEL maintainer="admin@empresa.com"
        ```
        * Metadatos

    * `USER`

        ```dockerfile
        USER appuser
        ```
        * Seguridad
        * Evita ejecutar como root

    * `VOLUME`

        ```dockerfile
        VOLUME /data
        ```
        * Define punto de montaje

    ## Construcción y ejecución

        ```bash
        docker build -t fastapi-app .
        ```
        * `-t` -> nombre y tag
        * `.` -> contexto de build

    ## Resumen

    | Instrucción | Función             |
    | ----------- | ------------------- |
    | FROM        | Imagen base         |
    | WORKDIR     | Directorio          |
    | COPY        | Copiar archivos     |
    | RUN         | Ejecutar en build   |
    | EXPOSE      | Documentar puerto   |
    | CMD         | Comando por defecto |
    | ENTRYPOINT  | Comando fijo        |

