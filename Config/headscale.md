# Headscale

* **Headscale** es un **control plane auto-gestionado (self-hosted)** compatible con **Tailscale**, basado en **WireGuard**

* En palabras simples

    > **Headscale permite crear una red privada virtual con IPs estables, sin depender de Tailscale Cloud y sin Ips estaticas reales**

    ## Definición formal

    * Headscale es una implementación **open-source** del **control plane de Tailscale** escrita en Go, que:

        * Gestiona identidades de nodos
        * Asigna IPs virtuales estables
        * Distribuye claves WireGuard
        * Coordina conexiones P2P
    
    * **Headscale No es una VPN tradicional**
    * **No enruta tráfico como OpenVPN**

    ## Para que sirve ?

    * Crear una **red privada lógica**
    * Unir máquinas con IPs dinámicas
    * Tener **IPs virtuales fijas**
    * Operar **sin IP pública**
    * Controlar todo localmente

    * **Casos tipicos**

        * Kubernetes (MicroK8s, k3s)
        * Bases de datos distribuidas
        * Homelabs
        * Edge computing
        * Entornos con politicas estrictas de red

    * Exactamente tu escenario

    ## Problema que resuelve

    * Tu problema

        ```bash
            IPs reales (eth0) cambian
                |
            Kubernetes se rompe
        ```
    * Headscale

        ```bash
            IPs reales cambian
            ↓
            WireGuard se reconecta
            ↓
            IP virtual (tailscale0) NO cambia
            ↓
            Kubernetes sigue funcionando
        ```
    ## Arquitectura general

    ```bash
                ┌─────────────┐
                │  Headscale  │   ← Control plane
                └─────┬───────┘
                    │
            ┌─────────┼─────────┐
            │         │         │
        node1      node2      node3
        10.100.0.1 10.100.0.2 10.100.0.3
    ```
    * **Headscale no trasporta tráfico**
    * Solo **coordina** conexiones
    * El tráfico es **peer-to-peer cifrado**

    ## Funcionamiento

    1. **Cliente se registra**

        * Cada nodo ejecuta:

            ```bash
                tailscaled
            ```
            * Genera un par de claves WireGuard
            * Se registra en Headscale
            * Recibe una identidad

    2. **Asignación de IP virtual**

        * Headscale:

            * Asigna una IP fija (ej: `10.100.0.2`)
            * Esta IP:

                * No dependen de DHCP
                * No dependen del router
                * Es estable
    
    3. **Intercambio de claves**

        * Headscale:

            * Comparte claves públicas entr enodos
            * Indica:
                * IP virtual
                * Endpoint actual (IP real + Puerto)

    4. **Conexión directa (WireGuard)**

        * Los nodos:

            * Se conectan directo P2P
            * Usan NAT traversal si es necesario
            * Reintentan automaticamente

    5. **Cambio de IP real (evetno critico)**

        ```bash
            eth0 cambia la IP
        ```    

        * `tailscaled` detecta el cambio
        * Actualiza endpoint en Headscale
        * Reconecta peers
        * **IP virtual sigue igual**

        * Kubernetes **ni se entera**

    ## Elementos Principales de Headscale

    1. **Headscale Server**

        * **Que es:**

            * Proceso central
            * No cifra tráfico
            * Solo coordina
        
        * **Hace:**

            * Autenticación
            * Asignación de IPs
            * Control de ACLs
            * Gestión de nodos
    
    2. **Tailscale client (`tailscaled`)**

        * **Que es:**

            * Cliente WireGuard
            * Corre en cada nodo

        * **Crea:**

            * Interfaz `tailscale0`
            * IP virtual fija
    
    3. **WireGuard**

        * **Rol**

            * Cifrado
            * Transporte P2P
            * Alto rendimiento
    
    4. **DERP Server**

        * **Que es:**

            * Relay de ultimo recurso

        * **Se usa solo si**

            * NAT extremo
            * Firewalls estrictos
        
        * Kubernetes casi nunca lo necesita

    5. **ACLs**

        * Controlan:

            * Qué nodo habla con cuál
            * Puertos
            * Protocolos

        * **Ejemplo:**

            ```json
                {
                    "acls":[
                        {
                            "action":"accept",
                            "src":["tag:k8s"],
                            "dst":["tag:k8s:*"]
                        }
                    ]
                }
            ```

    ## Caracteristicas clave

    | Característica      | Headscale |
    | ------------------- | --------- |
    | IP virtual estable  | ✅         |
    | IP real dinámica    | ✅         |
    | Self-hosted         | ✅         |
    | WireGuard           | ✅         |
    | Zero Trust          | ✅         |
    | DNS interno         | Opcional  |
    | ACLs                | ✅         |
    | Kubernetes-friendly | ⭐⭐⭐⭐⭐     |

    ## Como encaja con Microk8s

    * Microk8s usa IP estable
    * Headscale la garantiza
    * Problema resuelto

        ```bash
            sudo snap set microk8s node-ip=10.100.0.2
        ```

    