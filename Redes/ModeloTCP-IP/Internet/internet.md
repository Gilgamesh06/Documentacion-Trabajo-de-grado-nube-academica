# Internet

* La **Capa de Internet** es la **tercera capa del modelo TCP/IP** y es la responsable de **hacer posible la comunicación entre redes distintas**
* Aqui vive el **protocolo IP**, que permite que los datos **salgan de una LAN y crucen múltiples redes** hasta llegar a su destino.

    > Es la capa que **conecta Internet como red de redes.**

* Es la capa de modelo TCP/IP encargada de:

    * **Direccionamiento lógico (IP)**
    * **Enrutamiento de paquetes**
    * **Entrega de paquetes entre redes**

* Equvale a la **Capa de Red (Layer 3) del modelo OSI**

    ## Para que sirve ?

    1. **Asignar direcciones IP**
    2. **Permitir comunicación entre redes**
    3. **Determinar la mejor ruta**
    4. **Fragmentar paquetes si es necesario**
    5. **Notificar errores de red**

    * Sin esta capa:

        > Los datos **nuncan saldrian de la red local**

    ## Funcionamiento

    * Funciona mediante **paquetes IP** y **routers**

        1. Recibe **segmentos** (TCP/UDP)
        2. Los encapsula en **paquetes IP**
        3. Añade **IP origen y destino**
        4. Los routers deciden el **siguiente salto**
        5. El paquete llega a la red destino
    
    * No garantiza entrega ni orden (best effort)

    ## Principios clave de la capa de Internte

    * **Best Effort** no garantiza entrega
    * **Idenpendiente del medio** funciona igual en Ethernet o Wi-Fi
    * **Sin estado** cada paquete se trata de forma independiente

    ## Elementos que componen la capa de Internet

    1. **Protocolo IP (Internet Protocol)**

        * Direccionamiento
        * Enrutamiento
        * Fragmentación

        * **Versiones**

            | Versión | Características |
            | ------- | --------------- |
            | IPv4    | 32 bits         |
            | IPv6    | 128 bits        |

        * Ejemplo IPv4:

            ```bash
            192.168.1.10
            ```
    2. **Paquete IP**

        * Estructura Simplificada

            ```bash
            | IP origen | IP destino | TTL | Protocolo | Datos |
            ```
            * Cambios clave:

                * **TTL** evite bucles
                * **Protocolo:** TCP (6). UDP (17), ICMP (1)

    3. **Routers**

        * Leer IP destino
        * Consultar tabla de rutas
        * Reenviar al siguiente salto

        * Ejemplo

            ```bash
            ip route
            ``

    4. **Tabla de enrutamiento**

        * Contienen:

            * Red destino
            * Gateway
            * Interfaz

        * Ejemplo:

            ```bash
            0.0.0.0/0 via 192.168.1.1
            ```
    
    5. **ICMP (Internet Control Message Protocol)**

        * Sirve para: 

            * Mensajes de error
            * Diagnóstico

        * Ejemplos

            * `ping`
            * `traceroute`

    6. **IPsec (Seguridad)**

        * Cifra paquetes IP
        * Usado en VPNs

    ## Fragmnetacion IP

    * Ocurre si el paquete > MTU
    * Puede fragmentar el emisor o router
    * Reensamblado en destino

    ## Relación con Docker, Kubernetes y Cloud

    * **Pod IP**
    * **Service IP**
    * **VPC / Subnets**
    * **CNI Plugins**

    * Ejemplo

        > Un Pod en un nodo -> otro Pod en otro nod -> IP routing

