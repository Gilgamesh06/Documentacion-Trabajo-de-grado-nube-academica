# Role

* Un **Role** es un **Objeto de Kubernetes que define Permisos**, es decir:

    > Que acciones están permitidas sobre que recursos dentro de un Namespace

* **Importante**

    * **Role No es una identidad**
    * **Role No se asigna solo**
    * **Role solo define reglas**

* **Definición corta**

    > Un Role describe **qué se puede hacer,** pero no **quién lo puede hacer**

    ## Para que sirve ?

    1. **Controlar acceso a recursos**

        * Permite definir cosas como:

            * Leer Pods
            * Crear ConfigMaps
            * Ver Services
            * Modificar Deployments

    2. **Aplicar el principio de minimo privilegio**

        * En lugar de:

            * Dar acceso total 

        * Se define:

            * Solo lo necesario

    3. **Seguridad por Namespace**

        * Los Roles:

            * **Solo aplican dentro de un Namespace**
            * No pueden acceder a otros Namespaces

    ## Funcionamiento

    * Flujo real

        ### Definición del Role

        ```yaml
        apiVersion:rbac.authorization.k8s.io/v1
        kind: Role
        metadata:
            name: pod-reader
            namespace: dev
        rules:
        - apiGroups: [""]
          resources: ["pods"]
          verbs: ["get", "list"]
        ```
        * Esto **No da acceso a nadie todavia**

        ### Evaluación por el API Server

        * Cuando alguien (o algo) hace una peticion:

            1. kube-apiserver recibe la request
            2. Identifica la **identidad** (User o ServiceAccount)
            3. Revisa los Roles asociados via RoleBinding
            4. Decide: `permitir o denegar`

    ## Relación con ServiceAccount 

    ```bash
    ServiceAccount = identidad
    Role = permisos
    RoleBinding = unión
    ```
    * Un Role **nunca funciona solo**

    ## Características

    | Característica | Detalle         |
    | -------------- | --------------- |
    | Ámbito         | Namespace       |
    | Define         | Permisos        |
    | Aplica a       | Recursos K8s    |
    | Autorización   | RBAC            |
    | Se asigna      | vía RoleBinding |
    | Reutilizable   | Sí              |

    ## Elementos que componen un Role

    * Un Role esta compuesto por:

        ### Metadata

        ```yaml
        metadata:
            name: pod-reader
            namespace: dev
        ```
        * Define:
            
            * Nombre
            * Namespace

        ### Rules (el núcleo del Role)

        ```yaml
        rules:
        - apiGroups: [""]
          resources: ["pods"]
          verbs: ["get", "list"]
        ```

        1. **`apiGroups`**

            * Define **a que API pertenece el Recurso**

                | Grupo             | Ejemplo        |
                | ----------------- | -------------- |
                | ""                | Pods, Services |
                | apps              | Deployments    |
                | batch             | Jobs           |
                | networking.k8s.io | Ingress        |

        2. **`resources`**

            * Que recurso se controla:

                ```yaml
                resources:
                - pods
                - services
                - configmaps
                ```

        3. **`verbs` (acciones permitidas)**

            | Verbo  | Acción            |
            | ------ | ----------------- |
            | get    | Obtener uno       |
            | list   | Listar            |
            | watch  | Observar          |
            | create | Crear             |
            | update | Actualizar        |
            | patch  | Modificar parcial |
            | delete | Eliminar          |

        4. **`resourceNames` (opcional)**

            * Limita a recursos especificos:

                ```yaml
                resourceNames:
                - my-config
                ```
        ### Ejemplo

        ```yaml
        apiVersion: rbac.authorization.k8s.io/v1
        kind: Role
        metadata:
            name: configmap-reader
            namespace: prod
        rule:
        - apiGroups: [""]
          resources: ["configmaps"]
          verbs: ["get"]
          resourceNames: ["app-config"]
        ```
        * Esto Role:

            * Solo puede leer
            * Solo un ConfigMap
            * Solo en `prod`

    ## Que No hace un Role

    * No crea usuarios
    * No autentica
    * No asigna permisos
    * No cruza namespace

    ## Buenas practicas

    * Roles pequeños y especificos
    * Un Role por responsabilidad
    * Auditar regularmente

    