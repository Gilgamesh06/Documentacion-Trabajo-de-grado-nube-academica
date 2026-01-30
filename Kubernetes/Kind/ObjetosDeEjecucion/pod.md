# Pod

* Un **Pod** es la **unidad mínima de ejecución en Kubernetes**

* Es un **wrapper lógico** que agrupa:

    * Uno o más contenedores
    * Red compartida
    * Volúmenes compartidos
    * Ciclo de vida común
* **Kubernetes no ejecuta contenedores directamente, ejecuta Pods**

* **Definición corta**

    > Un Pod es la unidad básica de Kubernetes que encapsula uno o varios contenedores que **se ejecutan juntos, comparten red y almancenamiento,** y tiene un ciclo de vida común

    ## Para que sirve ?

    1. Ejecutar una aplicación
    2. Agrupar contenedores estrechamente acoplados
    3. Compartir red y volúmenes
    4. Ser la unidad que el scheduler asigna a nodos
    5. Ser gestionado por controllers

    * **Ejemplo**
        
        * 1 Pod = 1 contenedor (lo mas comun)
        * 1 Pod = app + sidecar (logs, proxy, metrics)
    
    ## Funcionamiento

    * Que pasa realmente cuando creas un **Pod**

        ### Paso 1: Creación del Pod

        ```bash
        kubectl apply -f pod.yaml
        ```
        * kube-apiserver valida
        * Guarda en etcd

        ### Paso 2: Scheduler asigna nodo

        * Pod está en `Pending`
        * kube-scheduler asigna `nodeName`

        ### Paso 3: kubelet crea el Pod

        * En el worker:

            * kubelet observa Pod asignado
            * Prepara entorno

        ### Paso 4: Namespace y red

        * Crear un namespace de red
        * Asigna IP única al Pod

        * **Tods los contenedores del Pod comparten esta IP**

        ### Paso 5: Volúmenes

        * Monta volúmenes 
        * Compartidos entre contenedores

        ### Paso 6: Arranque de contenedores

        * Primero `initContainers`
        * Luego contenedores principales
        * Aplica probes

        ### Paso 7: Estado y monitoreo

        * Running
        * Failed
        * Succeeded

        * kubelet reporta estado continuamente

    ## Características 

    1. **Unidad atómica**

        * El Scheduler asigna Pods, no contenedores
        * Se mueven juntos

    2. **IP única**

        * Un Pod = una IP
        * Contenedores se comunican por `localhost`

    3. **Volúmenes compartidos**

        * Todos los contenedores ven los mismo volúmenes

    4. **Ciclo de vida común**

        * Si el Pod muere -> mueren todos
        * No se reinicia automáticamente (el controller lo hace)

    5. **Efímero**

        * Los Pods no están pensados para vivir para siempre 
        * Se recrean, no se *arreglan*

    ## Componentes 

    1. **Contenedores**

        * App principal
        * Sidecars
        * Init containers

    2. **Init Containers**

        * Corren antes
        * Preparan entorno
        * Deben terminar con éxito

    3. **Sidecars**

        * Soporte a la app
        * Logs, proxies, métricas

    4. **Probes**

        * Liveness
        * Readiness
        * Startup

    5. **Volúmenes**

        * emptyDir
        * configMap
        * secret
        * PVC

    ## Estados de un Pod

    | Estado    | Significado      |
    | --------- | ---------------- |
    | Pending   | Esperando nodo   |
    | Running   | Ejecutándose     |
    | Succeeded | Completado       |
    | Failed    | Error            |
    | Unknown   | Nodo inaccesible |

    ## Que No es un Pod

    * No es una VM
    * No es persistente
    * No escala Solo
    * No se balancea solo

    ## Pod vs contenedor 

    | Contenedor       | Pod           |
    | ---------------- | ------------- |
    | Proceso          | Unidad lógica |
    | IP propia        | IP compartida |
    | Ciclo individual | Ciclo común   |
    | Docker           | Kubernetes    |

