# Job

* Un **Job**es un **controlador de Kubernetes** que se encarga de **ejecutar uno o varios Pods hasta que una tarea termine exitosamente**

    > Un Job se ejecuta, hace su trabajo y termina

* **Definicion corta**

    > Un Job garantiza que **un proceso batch se ejecute correctamente**, reintentándolo si falla, hasta completar el trabajo.

    ## Para que sirve ?

    * Se usa para **tareas finitas**, no para servicios de larga duración

        ### Proceso batch

        * Procesar archivos
        * Migraciones de bases de datos
        * Importar datos

        ### Tareas administrativas

        * Backups
        * Restauraciones
        * Limpieza (cleanup)

        ### Jobs de CI/CD

        * Build
        * Test
        * Deploy Scripts

        ### Tareas programadas (base de CronJob)

        * Jobs manuales
        * Jobs lanzados por pipelines

    ## Funcionamiento

    * Flujo interno

        ### Creación del Job

        ```bash
        kubectl apply -f job.yaml
        ```

        * Kubernetes crea:

            * Objeto Job
            * Pod(s) asociados

        ### Job Controller 

        * El **Job Controller** se encarga de:

            1. Crear Pods
            2. Monitorear su estado
            3. Reintentar si fallan
            4. Marcar el Job como **Completed** o **Failed**

        * A diferencia de Deployment

            * **No mantiene Pods vivos**
            * Solo espera que terminen

        ### Estados de un Job

        | Estado del Pod | Acción             |
        | -------------- | ------------------ |
        | Completed      | Cuenta como éxito  |
        | Failed         | Puede reintentarse |
        | Running        | Espera             |
        | CrashLoop      | Reintenta          |

        ### Finalización

        * Un Job termina cuando:

            * Se alcanza un numero de **completions**
            * O se supera el numero de **backloffLimit**

    ## Características

    | Característica | Detalle     |
    | -------------- | ----------- |
    | Ejecución      | Finita      |
    | Reintentos     | Automáticos |
    | Paralelismo    | Soportado   |
    | Escalado       | No          |
    | Balanceo       | No          |
    | Servicio       | No          |
    | Persistencia   | Opcional    |

    ## Tipos de Jobs

    1. **Job Simple**

        ```yaml
        completions: 1
        parallelism: 1
        ```
    
    2. **Job con reintentos**

        ```yaml
        backoffLimit: 3
        ```
    
    3. **Job paralelo**

        ```yaml
        parallelism: 5
        completions: 10
        ```
        * Ejecuta 10 tareas, maximo 5 al mismo tiempo



