# Problema clave

* **Kubernetes No tolera bine que cambine las IPs de los nodos**
* Si un nodo cambia de IP:
    
    * El **control plane** pierde contacto con el nodo.
    * Los **certificados** quedan apuntado a la IP vieja.
    * El nodo queda en estado `NotReady`
    * En MicroK8s normalmente toca **re-unir el nodo al cluster**

* **No existe una solución "automágica" 100% soporta** para IPs dinámicas en Kubernetes tradicional.

    ## Que necesita Kubernetes para que un nodo sea estable

    * Un nodo Kubernetes debe tener:

        1. **Identidad estable**
            
            * `nodeName`
            * `nodeIP` 

        2. **Canal de comunicación constante**

            * API Server <-> Kubelet

        3. **Certificados válidos**

            * Atados a IPs / SANs

    * Si la IP cambia -> todo se rompe

    ## Opciones Reales 

    * **Opción 1: (Recomendada):  Red privada virtual (VPN overlay)**

        * Crear una **red privada con IPs estáticas virtuales,** aunque la IP pública sea dinámica.

        * **Tecnologías ideales:**

            * **WireGuard** (Recomendada)
            * Tailscale
            * ZeroTier

        * **Funcionamiento**

            ```bash
                Servidor A (IP pública dinámica)
                    ↓
                    WireGuard → IP privada fija (10.10.0.1)

                Servidor B (IP pública dinámica)
                    ↓
                    WireGuard → IP privada fija (10.10.0.2)
            ```
        * **Kubernetes Solo ve las IPs privadas** (10.10.0.x)

        * **Pasos resumidos con WireGuard**

            1. Instalar WireGuard en ambos servidores

                ```bash
                    sudo apt install wireguard
                ```

            2. Creaar tunel VPN con IPs fijas

                ```bash
                    # Servidor A
                    Address = 10.10.0.1/24
                    # Servidor B
                    Address = 10.10.0.2/24
                ```
            
            3. Verficar conectividad

                ```bash
                    ping 10.10.0.2
                ``` 

            4. Instalar MicroK8s **usando la IP del túnel**

                ```bash
                    sudo snap install microk8s --classic
                ```
            
            5. Unir el custe usando la IP VPN:

                ```bash
                    microk8s add-node
                    microk8s join 10.10.0.1:25000/XXXXXXXX
                ```
            
            * **Resultado:**

                * Si cambia la IP publica, no importa
                * El cluster **No se rompe**
                * Comunicación segura y cifrada

        * **Esta es la forma usada en producción casera, homelabs y edge clusters**

    * **Opción 2: DNS dinámico (No recomedada)**

        * Usar DuckDNS / No-IP

            ```bash
                node1.duckdns.org
                node2.duckdns.org
            ```
        
        * **Problemas:**

            * Kubernetes **resuelve DNS una vez**
            * Los certificados se generan con la IP antigua
            * MicroK8s **no revalida** nodos automáticamente

            * **Terminarás haciendo**

                ```bash
                    microk8s leave
                    microk8s join ...
                ``` 
    
    * **Opción 3: Re-join automático (parche feo)**

        * Script que detecte cambio de IP
        * El nodo se sale y vuelve a unirse

        * **No recomendado**

            * Pierdes workloads
            * StatefulSets sufren
            * Downtime garantizado

        * **Porque MicroK8s es más sensible a esto ?**

            * MicroK8s:

                * Usa **dqlite** para el control plane
                * Amarra certificados a IPs
                * No soporta nodos "flotantes"
            
            * Es ideal para:

                * IPs estáticas
                * VPNs
                * Edge computing controlado

    ## Arquitectura recomendada para Tu caso

    ```bash
        [ ISP ]
            |
        [ IP dinámica ]
            |
        [ WireGuard ]
            |
        ----------------------------------
        | 10.10.0.1  | 10.10.0.2          |
        | Master     | Worker             |
        | MicroK8s   | MicroK8s           |
        ----------------------------------
    ```
    ## Verificación

    ```bash
        microk8s kubectl get nodes -o wide
    ```
    
    * **Debe mostrar:**

        ```bash
            INTERNAL-IP
            10.10.0.1
            10.10.0.2
        ```
        * Nunca IP publica

    ## Conclusión clara

        | Solución                  | Funciona  | Recomendación |
        | ------------------------- | --------- | ------------- |
        | VPN (WireGuard/Tailscale) | ✅ SI      | ⭐⭐⭐⭐⭐         |
        | DNS dinámico              | ⚠ Parcial | ⭐⭐            |
        | IP dinámica directa       | ❌ NO      | ❌             |






        
