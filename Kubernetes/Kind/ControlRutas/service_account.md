# ServiceAccount

* Un **ServiceAccount** es una **identidad para procesos que se ejecutan dentro del clúster,** principalmente **Pods**

* **Definición corta**

    > Un ServiceAccount proporciona una **identidad autenticable** a los Pods para que pueden **interactuar con el kube-apiserver** de forma segura.

    ## Diferencias clave

    | Tipo               | ¿Para quién es?          |
    | ------------------ | ------------------------ |
    | User Account       | Humanos (kubectl, CI/CD) |
    | **ServiceAccount** | **Pods / aplicaciones**  |

    ## Para que sirve ?

    1. **Autenticación contra Kubernetes API**

        * Permite que un Pod haga cossas como:

            * Listar Pods
            * Leer ConfigMaps
            * Crear Jobs
            * Observar Services
        
        * Todo esto **siempre pasa por el kube-apiserver**

    2. **Seguridad (principio de minimo privilegio)**

        * Sin ServiceAccount personalizado:

            * El Pod usa `default`
            * Tiene más permisos de los que debería

        * Con ServiceAccount 

            * Das **solo los permisos necesarios**

    3. **Integraciíon con contraladores**

        * Componentes como:

            * Ingress Controller
            * MetalLB
            * ExternalDNS
            * Cert-Manager

        * Todos usan **ServiceAccounts** para operar

    ## Funcionamiento

    * Este es el flujo **real**

        ### Creacion del ServiceAccount

        ```yaml
        apiVersion: v1
        kind: ServiceAccount
        metadata:
            name: app-sa
            namespace: dev
        ```
        * Esto crea:
            
            * Una **identidad**
            * Un objeto en etcd

        ### Token automático (JWT)

        * Kubernetes genera:

            * Un **token JWT**
            * Firmado por el clúster

        * Este token:

            * Identifica al Pod
            * Se usa para autenticación

        ### Inyección automática en el Pod

        * Cuando un Pod usa un ServiceAccount

            ```yaml
            spec:
                serviceAccountName: app-sa
            ```
        
        * Kubernetes monta automáticamente:

            ```bash
            /var/run/secrets/kubernetes.io/serviceaccount/
            ├── token
            ├── ca.crt
            └── namespace
            ```
            * El Pod **no necesita credenciales externas**

        ### Autenticación y autorización

        * Cuando el Pod llama al API:

            1. Usa el token JWT
            2. kube-apiserver valida el token
            3. RBAC decice si puede o no hacer la acción

    ## Cómo se relaciuona con RBAC ?

    * ServiceAccount **No define permisos**
    * Permisos se definen con:

        | Recurso            | Función          |
        | ------------------ | ---------------- |
        | Role               | Qué puede hacer  |
        | RoleBinding        | A quién se le da |
        | **ServiceAccount** | **Identidad**    |

    ## ServiceAccount: `default`

    * Cada Namespace tiene uno

        ```bash
        kubectl get sa -n dev
        ```
    
    * Resultado

        ```bash
        default
        ``` 
        * **Mala práctica** usarlo en producción

    ## Elementos que componen un ServiceAccount

    * Un ServiceAccount incluye o se realcion con:

        ### Objeto ServiceAccount

        ```yaml
        kind: ServiceAccount
        ```
        * Contiene

            * Nombre
            * Namespace
            * Annotations
        
        ### Token (Secret)

        * Tipo: `kubernetes.io/service-account-token`
        * Contiene:

            * `token` (JWT)
            * `ca.crt`
            * `namespace`

        * Desde K8s 1.24+ se generan **tokens proyectados**, no Secrets permanentes

        ### Vinculación RBAC

        * Role / ClusterRole
        * RoleBinding / ClusterRoleBinding

        * Ejemplo:

            ```yaml
            subjects
            - kind: ServiceAccount
              name: app-sa
              namespace: dev
            ```

        ### Uso por el Pod

        ```yaml
        spec:
            serviceAccountName: app-sa
        ```

    ## Tipos de permisos comunes

    * Un ServiceAccount puede:

        * No acceder a nanada
        * Leer recursos
        * Escribir recursos
        * Administrar componentes

    * Todo depenede del RBAC

    ## Buenas prácticas

    * Un ServiceAccount por aplicación
    * Permisos minimos
    * No usar `default`
    * No usar ClusterRole si no es necesario
    * Rotación automatica de tokens

    
        
