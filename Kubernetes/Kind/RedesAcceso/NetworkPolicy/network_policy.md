# NetworkPolicy

* Una **NetworkPolicy** es un *recurso de Kubernetes** que define **reglas de trafico de red** entre Pods y/o hacie/desde el exterior

    > Por defecto, todos los Pods pueden comunicarse entre si. NetworkPolicy permite restringir el trafico


* **Definición corta**

    > NetworkPolicy controla **quiénpuede comunicarse con quién** dentro del clúster Kubernetes, a nivel de red (L3/L4)

    ## Para que sirve ?

    * Se usa para **seguridad y aislamiento de red**

        ### Aislar aplicaciones

        * Frontend solo puede habla con Backend
        * Backend solo con Database

        ### Zero Trust dentro del cluster

        * Nada habla con nada, execptio lo permitido explicitamente

        ### Cumplimiento de seguridad

        * PCI-DSS
        * HIPAA
        * SOC2

        ### Protección lateral (east-west traffic)

        * Evita movimientos laterales en ataques

    ## Funcionamiento

    * Aqui es clave entender **que hace Kubernetes y qué hace el CNI**

        ### Kubernetes No aplica reglas

            * Solo define la politica
            * No flitra paquetes 

        * El **CNI** (Calico, Cilium, Weave, etc) **es quien implementa la politica**
        * Sin un CNI compatible -> **Networking No funciona**

        ### Flujo interno

        1. Creas una NetworkPolicy
        2. kube-apisever guarda el objeto
        3. El CNI la detecta
        4. El CNI configura reglas:

            * iptables
            * eBPF
        
        5. Trafico permitido o bloqueado

    ## Comportamiento por defecto

    * Antes de NetworkPolicy

        ```bash
        Pod A <-> Pod B <-> Pod C
        Todo permitido
        ```
    
    * Con una **NetworkPolicy**

        * El primer NetworkPolicy activa el modo "deny by default" para esos Pods

    ## Tipos de trafico que controla 

    | Tipo    | Descripción     |
    | ------- | --------------- |
    | Ingress | Entradas al Pod |
    | Egress  | Salidas del Pod |

    ## Características

    | Característica   | Detalle        |
    | ---------------- | -------------- |
    | Nivel OSI        | L3 / L4        |
    | Controla IP      | Sí             |
    | Controla Puertos | Sí             |
    | Controla DNS     | Indirectamente |
    | TLS              | No             |
    | HTTP paths       | No             |
    
    * No entiende HTTP, headers, paths -> eso es ingress / Service Mesh

    ## Elementos que componne un NetworkPolicy

    1. **`apiVersion`**

        ```yaml
        apiVersion: networkign.k8s.io/v1
        kind: NetworkPolicy
        ```
    
    2. **`metadata`**

        ```yaml
        metadata:
            name: allow-frontend
            namespace: app
        ```
        * Las NetworkPolicy **son namespaced**

    3. **`podSelector` Obligatorio**

        ```yaml
        spec:
            podSelector:
                matchLabels:
                    app: backend
        ```
        * Define **a que Pods se aplcia la politica**

    4. **`policyTypes`**

        ```yaml
        policyTypes:
        - Ingress
        - Egress
        ```
        * Opciones:

            * Ingress
            * Egress
            * Ambos

    5. **`ingress` Reglas Ingress**

        ```yaml
        ingress:
        - from:
            - podSelector:
                matchLabels:
                    app: frontend
        ```

        * **from puede ser**

            | Tipo              | Ejemplo                  |
            | ----------------- | ------------------------ |
            | podSelector       | Pods del mismo namespace |
            | namespaceSelector | Pods de otros namespaces |
            | ipBlock           | IPs externas             |

    6. **`egress` Reglas Egrees**

        ```yaml
        egress:
        - to:
          - ipBlock:
                cidr: 10.0.0.0/0
        ```

    7. **`ports`**

        ```yaml
        ports:
        - protocol: TCP
          port: 5432
        ```

    ## Ejemplo

    * **Escenario**

        * Frontend -> Backend
        * Backend -> DB
        * Nada mas permitido

        ### Backend solo acepta frontend

        ```yaml
        apiVersion: networking.k8s.io/v1
        kind: NetworkPolicy
        metadata:
        name: backend-ingress
        namespace: app
        spec:
        podSelector:
            matchLabels:
            app: backend
        policyTypes:
        - Ingress
        ingress:
        - from:
            - podSelector:
                matchLabels:
                app: frontend
            ports:
            - protocol: TCP
            port: 8080
        ```

        ### DB solo acepta backend

        ```yaml
        apiVersion: networking.k8s.io/v1
        kind: NetworkPolicy
        metadata:
        name: db-ingress
        namespace: app
        spec:
        podSelector:
            matchLabels:
            app: db
        policyTypes:
        - Ingress
        ingress:
        - from:
            - podSelector:
                matchLabels:
                app: backend
            ports:
            - protocol: TCP
            port: 5432
        ```
        
        ### Aislamiento total (deny all)

        ```yaml
        apiVersion: networking.k8s.io/v1
        kind: NetworkPolicy
        metadata:
        name: deny-all
        namespace: app
        spec:
        podSelector: {}
        policyTypes:
        - Ingress
        - Egress
        ```
        * `{}` = todos los Pods del namespace

    ## NetworkPolicy vs Ingress

    | Aspecto   | NetworkPolicy | Ingress    |
    | --------- | ------------- | ---------- |
    | Nivel     | L3/L4         | L7         |
    | Controla  | IP/puertos    | HTTP/HTTPS |
    | Seguridad | Interna       | Externa    |
    | TLS       | No            | Si         |

    ## Relacion con Kind, MetalLB e ingress

    * **En Kind**

        * Kind usa CNI básico
        * **NetworkPolicy No funciona por defecto**
        * Necesitas:
            
            * Calico
            * Cilium
    
    * **MetalLB**

        * No aplica NetworkPolicy
        * Opera a nivel Service
        
    * **Ingress NGINX**

        * Puede ser protegido con NetworkPolicy
        * Ejemplo: Solo aceptar tráfico del ingress-controller

    
