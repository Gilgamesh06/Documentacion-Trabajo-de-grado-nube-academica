# Relación

* **Idea Central: Kubernetes es un sistema basado en estado**

    * Kubernetes **No funciona por órdenes directas** tipo:

        > Crea este pod ahora mismo en este nodo

    * Funciona asi:

        > Este es el estado que quiero -> Kubernetes se encarga de que ocurra

    * Ese modelo se llama **control loop + estado deseado**

    ## Rol de cada componente 

    | Componente              | Rol principal                         |
    | ----------------------- | ------------------------------------- |
    | kube-apiserver          | Puerta de entrada y coordinador       |
    | etcd                    | Fuente única de verdad (estado)       |
    | kube-scheduler          | Decide **dónde** corre un Pod         |
    | kube-controller-manager | Garantiza **que** el estado se cumpla |

    * **Ninguno funciona solo**

    ## Relación fundamental entre ellos

    * La relación se puede resumir asi:

        ```bash
        kube-apiserver
        ↓
        etcd  -> kube-controller-manager
        ↑                 ↓
        kube-scheduler ───┘
        ```
    * **Todos hablan con kube-apiserver**
    * **Solo kube-apiserver habla con etcd**

    ## kube-apiserver: el punto de unión

    1. **Centro de comunicación**

        * Todos los componentes:

            * Leen del API Server
            * Escriben al API Server

        * Nadie se comunica directamente entre sí

            ```bash
            Scheduler -> API Server
            Controller -> API Server
            kubectl -> API Server
            ```
            * kube-apiserver es el **bus central**

    2. **Seguridad y validación**

        * Antes de que algo exista:

            * Se valida
            * Se autoriza
            * Se persiste

        * Nada entra *en bruto* al sistema

    ## etcd: el contrato entre todos

    1. **Fuente única de verdad**

        * El estado **no vive en memoria**
        * Vive en **etcd**

        * Ejemplo

            ```yaml
            replicas: 3
            ```
            * Esto es un **contrato**
                > Kubernetes debe mantener 3 Pods, pase lo que pase

    2. **Separación de responsabilidades**

        * etcd **no ejecuta lógica**
        * Solo guarda estado
        * Los demás **leen y reaccionan**

    ## kube-controller-manager: el corrector del sistema

    1. **Observa el estado**

        * Los controllers observan el API Server

            * `Estado deseado (etcd) vs Estado actual (reportado)`

    2. **Corrig desviaciones**

        * Ejemplo:

            * 3 Pods deseados
            * 2 reales

        * `Controller -> crear Pod`
        * No le importa **dónde** correrá, solo **que exista.**

    ## kube-scheduler: el decisor tático

    1. **Solo actúa en Pods pendientes**

        * Un Pod sin nodo asignado

            ```yaml
            spec:
                nodeName: null
            ```
    2. **Decide el mejor nodo**

        * Usa:

            * Recursos
            * Reglas
            * Afinidades

        * Luego

            * `Scheduler -> escribe nodeName`
            * No crea Pods, solo los asigna

    ## Flujo completo real

    * Flujo paso a paso con ejemplo real

        ### Paso 1: Usuario aplica un Deployment

        ```bash
        kubectl apply -f deployment.yaml
        ```
        * kube-apiserver:

            * Autentica
            * Autoriza
            * Valida
            * Guarda en etcd

        ### Paso 2: Estado deseado guardado

        * etcd ahora dice:
        
            * `Quiero 3 Pods nginx`

        ### Paso 3: Controller detecta diferencia

        * kube-controller-manager observa:

            * 0 Pods reales
            * 3 deseados
        
        * Crear 3 Pods (sin nodo)

        ### Paso 4: Scheduler asigna nodos

        * kube-scheduler detecta:

            * Pods sin `nodeName`
        
        * Evalúa nodos
        * Asigna el mejor

        ### Paso 5: kubelet ejecuta

        * En cada nodo:

            * kubelet crea contenedores
            * Reporta estado

        ### Paso 6: Control loop infinito

        * Si:
        
            * Un Pod muere
            * Un nodo falla
            * Cambia el YAML
        
        * Controllers + Scheduler corrigen

    ## Que pasaria si falta uno

    | Falta              | Resultado           |
    | ------------------ | ------------------- |
    | kube-apiserver     | Kubernetes muere    |
    | etcd               | Clúster sin memoria |
    | controller-manager | No hay auto-healing |
    | scheduler          | Pods en `Pending`   |
    