# MAC

* Una **dirección MAC** es un **identificador fisico único** asignado a una **interfaz de red (NIC)**

    > Identifica **quién es quién dentro de una LAN,** no a nivel de Internet

* **Ejemplo de MAC**

    ```bash
    00:1A:2B:3C:4D:5E
    ```
    * 48 bits (6 bytes)
    * Representada en hexadecimal
    * Asociada a una targeta de red

    ## Para que sirve ?

    1. **Identificar dispositivos en una red local**
    2. **Permitir la entrega de tramas Ethernet**
    3. **Facilitar la conmutación en switches**
    4. **Soportar protocolos como ARP**
    5. **Controlar el acceso al medio (MAC protocol)**

    * **Sin MAC no existe comunicación Ethernte ni Wi-Fi.**

    ## Funcionamiento

    * Funciona a través de **tramas** y **conmutación por direcciones MAC**

    * **Flujo básico**

        1. Un dispositivo conoce la **IP destino**
        2. Usa **ARP** para obtener la **MAC destino**
        3. Se crea una **trama Ethernet**
        4. El switch revisa la **MAC destino**
        5. Reenvia la trama solo al puerto correcto

    ## MAC != IP 

    | MAC          | IP        |
    | ------------ | --------- |
    | Física       | Lógica    |
    | LAN          | Internet  |
    | No enrutable | Enrutable |
    | Switch       | Router    |

    ## Elementos que componen la MAC

    1. **Estructura de la dirección MAC**

        * Un MAC tiene **48 bits**, divididos asi:

            ```bash
            | OUI (24 bits) | NIC (24 bits) |
            ```
        * **OUI (Organizationally Unique Identifier)**

            * Identifica al fabricante
            * Ejemplo: Intel, Cisco, Realtek

        * **NIC ID**

            * Identificador único del fabricante

    2. **Tipos de direcciones MAC**

        | Tipo      | Descripción                   |
        | --------- | ----------------------------- |
        | Unicast   | A un dispositivo              |
        | Broadcast | A todos (`FF:FF:FF:FF:FF:FF`) |
        | Multicast | A varios                      |

    3. **MAC Addressing Modes**

        * **Global (quemada en hardware)**
        * **Local (MAC spoofing)**

        * **Ejemplo:**

            ```bash
            ip link set eth0 address 02:AA:BB:CC:DD:EE
            ```
    4. **Tramas Ethernet (donde vive la MAC)**

        * Formato simplificado:

            ```bash
            | MAC destino | MAC origen | Tipo | Datos | FCS |
            ```
    
    5. **Tablas MAC (Switch)**

        * Un switch mantiene una tabla

            | MAC          | Puerto |
            | ------------ | ------ |
            | 00:1A:2B:... | eth1   |

        * Aprende MACs automáticamente
        * Expira con el tiempo
    
    6. **Protocolo MAC (control de acceso)**

        * Define **cómo varios dispositivos usan el medio**

            | Tecnología | Método  |
            | ---------- | ------- |
            | Ethernet   | CSMA/CD |
            | Wi-Fi      | CSMA/CA |

    ## MAC en tecnologías modernas

    * **Docker**

        * Cada contenedor tiene MAC virtual
        * Bridge asigna MACs

    * **Kubernetes**

        * Pods usan interfaces virtuales
        * CNI gestiona MACs

    * **Cloud**

        * MACs virtuales
        * Filtrado por seguridad
