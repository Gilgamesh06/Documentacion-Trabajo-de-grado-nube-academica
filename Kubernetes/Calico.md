# Calico

* **Calico** es un **plugin de red (CNI)** y **motor de politicas de red** para Kubernetes

    > Calico proporcioan red, ruteo y seguridad (NetworkPolicy) en Kubernetes

* **Definicion corta:**

    > Calico es un **CNI que conecta Pods entre sí** y **aplica politicas de red L3/L4** usando iptables o eBPF

    ## Para que sirve ?

    * Calico cubre **dos grandes funcionalidades**

    1. **Networking (conectividad entre Pods)**

        * Asigna IPs a Pods
        * Permite comunicacion Pod <-> Pod
        * Permite comunicacion Pod <-> Service

    2. **Seguridad (NetworkPolicy)**

        * Aplica **Kubernetes NetworkPolicy**
        * Aplica **politicas extendidas de Calico**
        * Pemite aislamiento real entre Pods 
        * Sin calico (u otro CNI compatible):

            * NetworkPolicy **no hace nada**

    ## Funcionamiento

    * Explicacion por capas

        ### Nivel OSI

        | Capa | Calico       |
        | ---- | ------------ |
        | L2   | Opcional     |
        | L3   | IP routing   |
        | L4   | TCP/UDP      |
        | L7   | No HTTPs     |

        ### Arquitectura basica

        * Calico trabaja con:

            * **IP routing (no overlay por defecto)**
            * **BGP o rutas locales**
            * **iptables o eBPF**

        * Esto lo hace:

            * Mas rápido 
            * Mas simple 
            * Mas observable

        ### Flujo de red

        1. Pod obtiene IP
        2. Calico anuncia rutas
        3. Trafico viaja directamente
        4. Se plica reglas de seguridad

    ## Modos de operación de Calico

    1. **Modo routing (default)**

        * No encapsula paquetes 
        * Usa rutas IP
        * Opcionalmente BGP

            ```bash
            Pod A --IP--> Pod B
            ```
    2. **Modo encapsulado (VXLAN / IPIP)**

        * Uitl en cloud o redes limitadas
        * Crea overlay

            ```bash
            Pod A --VXLAN--> Pod B
            ```
    4. **Modo eBPF (avanzado)**

        * Remplaza iptables
        * Mayor performance
        * Menor latencia

    ## Componentes principales de calico

    1. **calico-node (DaemonSet)**

        * El corazon de Calico

        * **Que hace:**
            
            * Conecta Pods a la red
            * Aplica NetworkPolicy
            * Maneja rutas

        * **Porque es DaemonSet**

            * Corre en **cada nodo**

    2. **Felix**

        * Agente de politicas
        * Programa iptables o eBPF
        * Observa cambios del API Server
        * Corre dentro de `calico-node`

    3. **BIRD (opcional)**

        * Demonio BGP
        * Anuncia rutas entre nodos
        * Usado en clusters grandes

    4. **CNI Plugin**

        * Se ejecuta cuando se crea un Pod
        * Asinga IP
        * Configura interfaces

    5. **calico-kube-controllers**

        * Controladores de Kubernetes
        * Sincorniza objetos:

            * NetworkPolicy
            * Profiles
            * IP Pools

    6. **IP Pools**

        * Rangos de IP para Pods

            ```yaml
            cidr: 192.168.0.0/16
            ```

    7. **Typha (opcional)**

        * Optimiza cluster grandes
        * Reduce carga de kube-apiserver

    ## Características

    | Característica | Detalle         |
    | -------------- | --------------- |
    | CNI            | Si              |
    | NetworkPolicy  | Si              |
    | Performance    | Alta            |
    | BGP            | Opcional        |
    | Overlay        | Opcional        |
    | eBPF           | Opcional        |
    | Cloud          | AWS, GCP, Azure |
    | On-prem        | Si              |

    