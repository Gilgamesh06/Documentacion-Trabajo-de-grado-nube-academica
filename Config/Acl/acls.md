# ACL

* Una **ACL** define:

    * Quién puede comunicarse con quién 
    * Porque puertos 
    * Porque protocolos

* En Headscale/Tailscale:

    * Se aplica **ANTES de que el trafico pase**
    * Es **Zero Trust**

* **Ejemplo simple**

    ```json
    {
        "acls":[
            {
                "action": "accept",
                "src": ["tag:k8s"],
                "dst": ["tag:k8s:*"]
            }
        ]
    }
    ```

    * Significa:

        > Solo nodos Kubernetes puede hablar entre si


* **Relacion directa con Kubernetes**

    * Kubernetes necesita:

        * API server <-> kubelet
        * kubelet <-> kubelet
        * etcd/dqlite <-> peers
        * CNI <-> nodos
    
    * **Si una ACL bloquea esto, el cluster no funciona**

    * Por eso:

        * ACLs deben permitir **full mesh entre nodos K8s**
        * Opcionalmente bloquear otros nodos
    
    ## Comparacion mental correcta

    | Nivel           | Control                |
    | --------------- | ---------------------- |
    | Headscale ACL   | Quién puede hablar     |
    | Kubernetes RBAC | Qué puede hacer        |
    | NetworkPolicy   | Qué pods pueden hablar |

