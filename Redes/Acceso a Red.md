# Acceso a Red

* Es la capa del Modelo TCP/IP que **define cómo un dispositivo accede físicamente a la red** y **cómo se entregan los datos dentro de la red local**

    > Equivale a la **Capa Fisíca + Capa de Enlace de datos del Modelo OSI**
 
    ## Para que sirve ?

    1. **Transmitir datos por medio físico**
    2. **Usar direcciones MAC**
    3. **Encapsular paquetes IP en tramas**
    4. **Detectar errores fisicos**
    5. **Permitir la comunicación dentro de una LAN**

    * Sin esta capa, **IP no puede viajar**

    ## Funcionamiento

    * Funciona en **tres pasos principales**

        ### Encapsulación
        
        * Recibe un **paquete IP** (capa internet)
        * Lo encapsula dentro de una **trama** (Ethernet, WI-FI)

        ### Resolución de direcciones

        * Convierte una **IP destino** en una **MAC destino**
        * Usa **ARP (Address Resolution Protocol)**

        ### Transmisión física

        * La trama se convierte en:

            * Señales eléctricas (Ethernet)
            * Ondas de radio (WI-FI)
            * Pulsos de luz (fibra)

    ## Que datos maneja '

    | Tipo        | Ejemplo        |
    | ----------- | -------------- |
    | Tramas      | Ethernet Frame |
    | Direcciones | MAC            |
    | Bits        | 0 y 1          |
    | Medios      | Cable, radio   |

    ## Elementos que componen la capa de Acceso a la Red

    1. **Direcciones MAC**

        * Identifican **interfaces de red**
        * Son únicas (48 bits)
        * Ejemplo: `00:1A:2B:3C:4D:5E`

    2. **Tramas (Frames)**

        * Ejemplo de trama Ethernet:

            ```bash
            | MAC destino | MAC origen | Tipo | Datos (IP) | CRC |
            ```
            * Contiene el **paquete IP**
            * Incluye verificación de errores

    3. **Protocolos y tecnologias**

        | Protocolo / Tecnología | Función               |
        | ---------------------- | --------------------- |
        | Ethernet (802.3)       | LAN cableada          |
        | Wi-Fi (802.11)         | LAN inalámbrica       |
        | ARP                    | IP → MAC              |
        | VLAN (802.1Q)          | Segmentación          |
        | PPP                    | Enlaces punto a punto |

    4. **Dispositivos**

        | Dispositivo          | Rol                    |
        | -------------------- | ---------------------- |
        | NIC (tarjeta de red) | Envía/recibe tramas    |
        | Switch               | Conmuta tramas por MAC |
        | Access Point         | Conecta Wi-Fi          |
        | Bridge               | Une segmentos          |

    5. **Medio físico**

        * Cable UTP
        * Fibra óptica
        * Aire (WI-FI)


    ## Ejemplo paso a paso 

    * Un contenedor Docker quiere comunicarse con otro servicio:

        1. IP destino conocida (capa internet)
        2. ARP busca la MAC
        3. Se crea una trama Ethernet
        4. El switch reenvia por MAC
        5. La NIC recibe los bits
        6. Se entrega el paquete IP

    ## Relación con Kubenetes y Docker

    * **CNI** trabaja en esta capa
    * **Bridge networks**
    * **Flannel / Calico**
    * **VXLAN**

    * **Ejemplo**

        > Un Pod -> veth -> brige -> NIC -> switch

    