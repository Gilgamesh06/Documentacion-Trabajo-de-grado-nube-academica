# kube-scheduler

* **Kube-scheduler** es el componente del **Control Plane** que **decide en qué nodo se ejecuta cada Pod**

    * No crea Pods
    * No ejecuta contenedores
    * **Solo asigna Pods a Nodos**

* Definición corta

    > kube-scheduler es el planfiicador de Kubernetes que selecciona el **mejor nodo disponible** para cada Pod pendiente.

    ## Para que sirve ?

    * Su función principal es:

        * **Asignar Pods a nodos de forma óptima**

    * Teniendo en cuenta:

        * Recursos disponibles
        * Restricciones
        * Reglas de afinidad
        * Balance de carga
        * Topología del clúster

    * Sin scheduler:

        * Los Pods se quedan en estado `Peding`

    ## Cuándo actúa kube-scheduler ?

    * kube-scheduler actúa cuando existe:

        ```bash
        Pod sin campo .spec.nodeName
        ```
    * Es decir:

        * Pod recién creado
        * Pod recreado
        * Pod reprogramado

    ## Funcionamiento

    * Paso a paso con un caso real

        ### Ejemplo: Crear un Deployment

        ```bash
        kubectl apply -f deployment.yaml
        ```    

        ### Paso 1: Pod entra en estado Pending

        * kube-scheduler guarda el Pod en etcd
        * El Pod **no tiene nodo asignado**

        ### Paso 2: Scheduler observa Pods Pedientes

        ```bash
        Watch API Server -> detecta Pod sin nodo
        ```

        ### Paso 3: Filtrado de Nodos 

        * El scheduler evalúa **que nodos pueden ejecutar el Pod**

        * Descarta nodos que:

            * No tengan CPU o RAM
            * No cumplan `nodeSelector`
            * No teleren taints
            * No cumplan afinidades

        * Resultado 

            ```bash
            Nodos viables
            ```
        
        ### Paso 4: Scoring (prioridades)

        * A los nodos viables se les asigna una puntuación

        * Ejemplo de criterios:

            * Menor uso de recursos
            * Distibución balanceada
            * Afinidad prefereida
            * Topología

        * Resultado:

            * Nodo con mayor score

        ### Paso 5: Binding

        * El scheduler:

            * Escribe `.spec.nodeName`
            * Lo guarda via kube-apiserver

        * Pod -> Nodo X

        ### Paso 6: kubelet ejecuta el Pod

        * En el nodo asignado:

            * kubelet crea contenedores
            * Reporta estado

    ## Criterios que usa kube-scheduler

    1. **Recursos**

        * CPU
        * Memoria
        * HugePages
        * Pods máximos

    2. **Resitricciones de nodos**

        * `nodeSelector`
        * `nodeAffinity`
        * Labels

    3. **Taints y tolerations**

        * Evitan que ciertos Pods se ejecuten en nodos especificos

        * Ejemplo:

            * `Nodo tainted -> solo Pods tolerantes`

    4. **Afinidad y anti-afinidad**

        * PodAffinity
        * PodAntiAffinity
        * Distribucion por zonas

    5. **Topología**

        * Zonas
        * Racks
        * Hosts

