# etcd

* **etcd** es una **base de datos distribuida clave-valor**, altamente consistente, diseñada para **configuración, coordinación y estado.**

* En Kubernetes:

    > etcd es la fuente única de verdad del clúster

* **Todo el estado de Kubernetes vive en etcd**

* Definición corta

    > etcd es una base de datos key-value distribuida que almacena **el estado completo del clúster Kubernetes** de forma consistente y confiable

    ## Para qué sirve etcd en Kubernetes ?

    * etcd sirve para:

        ### Guardar el estado del clúster

        * Pods
        * Deployments
        * Services
        * Secrets
        * ConfigMaps
        * Nodes
        * RBAC
        * Custom Resources (CRDs)

        * **Todo** lo que crea Kubernetes

        ### Permitir consistencia fuerte

        * Todos los componentes ven **el mismo estado**
        * No hay estados intermendios inconsistentes

        ### Coordina componentes

        * Controllers 
        * Schedulers
        * API Server

        * Todos leen/escriben **desde un solo lugar confiable**

    ## Funcionamiento

    1. **Modelo clave valor**

        * etcd almacena datos como 

            ```bash
            /key -> value
            ```
        * Ejemplo real en Kubernetes

            ```bash
            /registry/pods/default/nginx-abc123
            ```
        * Valor:

            ```json
            {
                "spec": {...},
                "status": {...}
            }
            ```
    2. **Solo el kube-apiserver habla con etcd**

        > Ningun Otro componente accede directamente a etcd excepto kube-apiserver

        * Esto garantiza:

            * Seguridad
            * Integridad
            * Control de acceso
    
    3. **Flujo de lectura/escritura**

        ```bash
        kubectl
        ↓
        kube-apiserver
        ↓
        etcd
        ```
        * Kube-apiserver valida
        * etcd persite
        * etcd responde

    4. **Consenso con Raft**

        * etcd usa el **algoritmo Raft** para consenso distribuido

        * **Que significa ?**

            * Múltiples nodos etcd
            * Un lider
            * Votacion
            * Replicacíon
        * Garantiza **consistencia fuerte.**

    5. **Operaciones atómicas**

        * etcd garantiza:

            * Lecturas consistentes
            * Escrituras atómicas
            * Transacciones simples

    ## Como usa Kubernetes a etcd ?

    1. **Estado deseado vs estado actual**

        * Estado deseado -> guardad en etcd
        * Estado actual -> reportado por kubelet

        * Controllers comparan:

            ```bash
            etcd (deseado) != realidad -> reconciliar
            ```
    
    2. **Watchers**

        * etcd permite **watch**:

            * Notificar cambios
            * Activar controllers
            * Reaccionar en tiempo real

        * Base del modelo reactivo

    ## Riesgos y buenas prácticas

    1. **Backups obligatorios**

        * Si pierdes etcd:

            * Pierdes el clúster
            * Los pods siguen corriendo, pero **sin control**
    
    2. **No exponer etcd**

        * Nunca público
        * Solo accesible desde control plane

    3. **Recursos dedicados**

        * Disco rápido
        * Baja latencia



