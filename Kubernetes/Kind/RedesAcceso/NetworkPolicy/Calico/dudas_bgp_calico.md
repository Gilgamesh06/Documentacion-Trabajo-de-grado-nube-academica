# Dudas BGP Calico

* **Pregunta:**

    > No entiendo lo del BGP en calico yo se que BGP es un protocolo en L3 que permite conectar sistemas autonomos tipo OSPF, RIP o eigrp este es un protocolo que conecta redes con diferentes protocolos o con el mismo protocolo AS pero que tiene esto que ver con kubernetes la red interna de los pods se configura como AS?


    ## Primero: Ponagos a BGP en su sitio (Redes clasicas)

    * Tu lo entiendes asi (y es correcto):

        * **BGP** -> Protocolo de ruteo **L3**
        * Se usa entre **Sistemas Autonomos (AS)**
        * OSPF / RIP / EIGRP -> **IGP**
        * BGP -> **EGP**
        * Intercambia **prefijos IP**, no estados de enlace

    ## Entonces... ¿Porque aparece BGP en Kubernetes?

    * Aqui esta la clave mental:

        > **Calico No usa BGP para *conectar Kubernetes a internet* Usa BGP como mecanismo para anunciar rutas de Pods**

    * **Kubernetes tinee un problema de red real**

        * Cada Pod tiene:

            * Una IP **real**
            * Ruteable dentro del cluster

        * **Ejemplo**

            ```bash
            Node A -> Pods: 10.244.1.0/24
            Node B -> Pods: 10.244.2.0/24
            ```
            * Pregunta clave: **¿Cómo sabe el Node A que 10.224.2.0/24 esta en Node B?**

    ## Opciones para resolver esto

    1. **Overlay (VXLAN)**

        * Encapsular paquetes
        * No necesitas ruteo real
        * Más simple, menos performance

        * Flannel, Weave

    2. **Routing real (Calico)**

        * Cada nod anuncia:

            * Yo se llegar a estas IPs

        * No hay encapsulacion
        * Tráfico directo

        * **Aqui entra BGP**

    ## ¿Cómo usa Calico BGP?

    * **Calico usa BGP** como un protocolo de distribución de rutas, no como arquitectura internet

        * **Ejemplo**

            | Nodo   | IP nodo      | Pods          |
            | ------ | ------------ | ------------- |
            | node-1 | 192.168.1.10 | 10.244.1.0/24 |
            | node-2 | 192.168.1.11 | 10.244.2.0/24 |

        * **Calico hace esto:**

            * Node-1 anuncia:

                ```bash
                10.244.1.0/24 -> 192.168.1.10
                ```
            
            * Node-2 anuncia:

                ```bash
                10.244.2.0/24 -> 192.168.1.11
                ```
            
            * Como se anuncian ?
                * **Con BGP**

    ## Esto significa que cada nodo es un AS ?

    * No necesariamente

    * **En Calico:**

        * Normalmente:

            * **Todos los noods usan el mismo AS**

        * **Se llama:**

            * **iBGP**, no eBGP

        ```bash
        AS 64512
        ├── node-1
        ├── node-2
        └── node-3
        ```
        * BGP **No es solo inter-AS**

            * Tambien existe **iBGP**

    ## Porque no usar OSPF ?

    * Calico **podria** haber usado OSPF, pero:

        | Razón                    | BGP       | OSPF     |
        | ------------------------ | --------- | -------- |
        | Simplicidad              | Si        | No       |
        | Escalabilidad            | Muy alta  | Media    |
        | Control de prefijos      | Excelente | Limitado |
        | Integración DC           | Excelente | Media    |
        | Implementación userspace | Fácil     | Difícil  |

    * BGP es:

        * Más simple de implementar
        * Más predecible
        * Más común en data centers

    ## BGP en Calico != BGP de Internet ?

    | BGP Internet        | BGP Calico    |
    | ------------------- | ------------- |
    | Políticas complejas | Rutas simples |
    | Multihoming         | No            |
    | AS públicos         | AS privados   |
    | Peering WAN         | Peering local |

    ## Es obligatorio usar BGP en Calico?

    * No, Calico tiene modos:

        | Modo          | BGP          |
        | ------------- | ------------ |
        | VXLAN         | No           |
        | IPIP          | No           |
        | Routing + BGP | Si           |
        | eBPF          | No (opcional)|

    ## Que gana Kubernetes usando BGP ?

    * Trafico directo
    * Menor latencia
    * Menor MTU issues
    * Debugging sencillo (`ip route`)

    ## Relación con MetalLB

    * **MetalLB en modo BGP**

        * MetalLB anuncia:

            * IPs de Services (LoadBalancer)
        
        * A router fisicos

    * **Calico + MetalLB**

        | Componente | Anuncia         |
        | ---------- | --------------- |
        | Calico     | IPs de Pods     |
        | MetalLB    | IPs de Services |

        * Ambos pueden usar **BGP**, pero **para cosas distintas**


> **Calico** usa `BGP` internamente para que los nodos del clúster se anuncien entre si las rutas de los Pods 
