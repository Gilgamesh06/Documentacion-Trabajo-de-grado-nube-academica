# kube-apiserver

* El **kube-apiserver** es el **servidor central de la API de Kubernetes**
* Es el **único punto de entrada** al clúster y el **componente que coordina todas la comunicación entre:**

    * Usuarios (`kubectl`, CI/CD, dashboards)
    * Componentes internos (scheduler, controllers, kubelet)
    * Sistemas externos

* **Nada en Kubernetes ocurre sin pasar por kube-apiserver**

* **Definicion corta**

    > kube-apiserver es el componente del Control Plane que **expone la API REST de Kubernetes, valida las operaciones y mantiene el estado del clúster**

    ## Para que sirve ?

    * Sirve para **cuatro cosas fundamentales**

        ### Punto único de entrada (API Gateway del clúster)

        * Expone la API REST sobre HTTPS
        * Todas las operaciones (`GET`, `POST`, `PUT`, `DELETE`) pasan por él

        * **Ejemplo**

            ```bash
            kubectl get pods
            ```
            * **kubectl -> kube-apiserver**

        ### Seguridad: Autenticación y autorización

        * kube-apiserver es el **guardián de seguridad**

            * Autentica:

                * Certificados
                * Tokens
                * ServiceAccounts

            * Autoriza:

                * RBAC
                * ABAC
                * Webhooks
        
        * Nadie entra sin permiso

        ### Validación y admisión

        * Antes de aceptar un recurso:

            1. Valida el esquema del YAML
            2. Aplica políticas 
            3. Ejecuta admissions controllers

        * Ejemplo:

            * Rechazar pods privilegiados
            * Forzar labels
            * Limitar recursos

        ### Persistencia del estado

        * Escribe y lee desde **etcd**
        * Nunca modifica directamente nodos o pods
        * Solo **registra el estado deseado**

    ## Funcionamiento

    * Veamos el **camino exacto** de una solicitud real

    * **Ejemplo** `kubectl apply -f deployment.yaml`

        ### Paso 1: `Cliente -> API Server`

        ```bash
        kubectl -> HTTPS -> kube-apiserver
        ```
        * Se inicia una conexión TLS
        * Se verifica identidad

        ### Paso 2: `Autenticación`

        * Quien eres ?

            * Certificado
            * Token
            * ServiceAccount
        
        * Si falla -> deniega

        ### Paso 3: `Autorización (RBAC)`

        * Puedes hace esto ?

        * Ejemplo:

            ```bash
            Usuario X -> crear Deployment en namespace Y
            ```
            * Si no  -> deniega

        ### Paso 4: `Validación`

        * El YAML es válido ?
        * Los campos existen ?
        * El tipo es correcto ?

        ### Paso 5: `Admission Controllers`

        * Se ejecuta en orden:

            * Mutating

                * Modifica el objeto
            
            * Validating:

                * Aceptan o rechazan
        
        * Ejemplo
            
            * Añadir lables automáticamete
            * Rechazar imágenes sin tag

        ### Paso 6: `Persistencia en etcd`

        ```bash
        kube-apiserver -> etc
        ```
        * Guardar el estado deseado
        * etcd confirma

        ### Paso 7: Notificación a watchers

        * Controllers
        * Schedulers
        * Kubelets

        * Todos observan cambios via API Server

    ## Arquitectura interna 

    * Internamente es **un servidor web altamente especializado**

        ### Componentes Internos 

        ```bash
        Request
        ↓
        Authentication
        ↓
        Authorization
        ↓
        Admission
        ↓
        REST Storage
        ↓
        etcd
        ```

        ### API REST

        * Basada en HTTP/JSON
        * Versionada (`/api/v1`, `/api/apps/v1`)
        * Compatible hacia atrás

        ### Watch API

        * Permite:

            * Observar cambios en tiempo real
            * Base del modelo reactivo de Kubernetes

    ## Caracteristicas Principales

    1. **Stateless (sin estado)**

        * No guarda estado local
        * Todo vive en etcd
        * Permite escalar horizontalmente

    2. **Altamente disponible**

        * Múltiples instancias
        * Balanceadas
        * Todas leen el mismo etcd

    3. **Seguro por diseño**

        * TLS obligatorio
        * RBAC
        * Admission Controllers
        * Auditoría

    4. **Extensible**

        * CRDs
        * Admission webhooks
        * Aggregated APIs

    5. **Versionado**

        * Soporta múltiples versiones de API
        * Deprecaciones controladas


    ## Que No hace kube-apiserver

    * No ejecuta Pods
    * No decide nodos
    * No balancea tráfico
    * No crea contenedores

    * Solo **coordina y registra**

    