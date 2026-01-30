# Namespace

* Un **Namespace** es una **división lógica del clúster** que permite **agrupar y aislar recursos** dentro de **un mismo clúster Kubernetes**

* Importante:

    * **No es un clúster**
    * **No es una VM**
    * **No es seguridad fuerte**
    * Es una **capa lógica de organizacion**

* **Definición corta y correcta**

    > Un Namespace es un **contenedor lógico** para recursos Kubernetes que permite **organización, aislamiento y control** dentro de un clúster.

    ## Para que sirve ?

    1. **Organización**

        * Separar recursos por:

            * Equipo 
            * Proyecto
            * Entorno (dev / qa / prod)
            * Dominio funcional

        * Ejemplo:

            ```bash
            dev
            qa
            prod
            ingress-nginx
            metallb-system
            monitoring
            ```

    2. **Aislamiento (lógico)**

        * Dos Namespaces puede tener:

            * Un `Service` llamado `api`
            * Un `Deployment` llamado `api`
        
        * **No hay conflicto de nombres**

            ```bash
            dev/api
            prod/api
            ``` 

    3. **Control de acceso (RBAC)**

        * Puedes decir:

            * El equipo A **solo ve** `dev`
            * El equipo B **solo administra** `prod`

        * Sin namespaces:

            * Todo el mundo ve todo

    4. **Gestión de recursos**

        * Permite:

            * Limitar CPU y memoria por Namespace
            * Evitar que un equipo consuma todo el clúster

    ## Funcionamiento

    1. **Namespace = clave en etcd**

        * Internamente, Kubernetes guarda cada recursos asi:

            ```bash
            (namespace, kind, name)
            ```
        
        * Ejemplo:

            ```bash
            (prod, Deployment, api)
            (dev, Deployment, api)
            ```
            * Son **recursos distintos,** aunque se llamen igual

    2. **kube-apiserver es el guardian**

        * Todo pasa por:

            ```bash
            kubectl -> kube-apiserver -> etcd
            ```
        
        * El API Server:

            * Valida el Namespace
            * Aplica reglas de acceso
            * Guarda el recurso

    3. **Resolución DNS con Namespace**

        * Un Service se resuleve asi:

            ```bash
            service.namespace.svc.cluster.local
            ```
        
        * Ejemplo:

            ```bash
            api.dev.svc.cluster.local
            ```
    
    ## Que No hace un Namespace

    * No crea redes separadas automáticamente
    * No crea aislamiento de kernel
    * No es seguridad dura
    * No limita recursos por sí solo

    * Para eso necesitas:

        * NetworkPolicies
        * ResourceQuotas
        * PodSecurity
        * RBAC

    ## Características

    | Característica | Detalle                |
    | -------------- | ---------------------- |
    | Ámbito         | Lógico                 |
    | Persistencia   | Guardado en etcd       |
    | Aislamiento    | Parcial                |
    | DNS            | Sí                     |
    | RBAC           | Sí                     |
    | Recursos       | Sí (con quotas)        |
    | Red            | Compartida por defecto |


    ## Namespaces por defecto en Kubernetes

    | Namespace         | Para qué sirve                   |
    | ----------------- | -------------------------------- |
    | `default`         | Recursos sin namespace explícito |
    | `kube-system`     | Componentes internos             |
    | `kube-public`     | Recursos públicos                |
    | `kube-node-lease` | Heartbeats de nodos              |
    | `ingress-nginx`   | Ingress Controller               |
    | `metallb-system`  | MetalLB                          |
        
    ## Elementos que componene un Namespace

    * Un Namespace **No es solo un nombre,** puede contener:

        ### Workloads

        * Pods
        * Deployments
        * ReplicaSets
        * StatefulSets
        * Jobs / CronJobs

        ### Networking

        * Services
        * Ingress
        * NetworkPolicies

        ## Configuración

        * ConfigMaps
        * Secrets

        ## Seguridad

        * ServiceAccounts
        * Roles
        * RoleBindings
        * PodSecurity standards

        ## Gestión de recursos

        * ResourceQuota
        * LimitRange

    ## Recursos que No viven en Namespaces

    | Recurso            | ¿Namespace? |
    | ------------------ | ----------- |
    | Nodes              | No          |
    | PersistentVolumes  | No          |
    | StorageClass       | No          |
    | Namespace          | No          |
    | ClusterRole        | No          |
    | ClusterRoleBinding | No          |
    | CRDs               | No          |


    ## Ejemplo estrucutra (Taller-1)

    ```bash
    Cluster
    ├── ingress-nginx
    │    └── Ingress Controller
    ├── metallb-system
    │    └── MetalLB
    ├── dev
    │    ├── api
    │    ├── service
    │    └── ingress
    └── prod
        ├── api
        ├── service
        └── ingress
    ```

    