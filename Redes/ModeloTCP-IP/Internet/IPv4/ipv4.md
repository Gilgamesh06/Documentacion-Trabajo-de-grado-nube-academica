# IPv4

* **IPv4** es un **protocolo de direccionamiento y enrutamiento** que define:

    * **Cómo se identifican los dispositivos** (direcciones IP)
    * **Cómo se envían paquetes** entre redes
    * **Cómo los routers encaminan esos paquetes**

* Pertenece a la **capa de Internet del Modelo TCP/IP**
* Equvale a la **capa de red OSI**

    ## Para que sirve ?

    1. Identificar dispositivos en una red
    2. Permite comunicación entre redes
    3. Enrutar paquetes a través de internet
    4. Soportar protocolos superiores (TCP/UDP)
    5. Interconectar LAN, WAN, Cloud, Data Centers

    * Sin IPv4 no existiria:

        * Internet tradicional
        * APIs REST
        * Microservicios distribuidos

    ## Funcionamiento

    * IPv4 funciona bajo el principio **best-effort**

        * No garantiza entrega
        * No garantiza orden
        * No garanitza confiabilidad

    * Estas tareas las delega a **TCP o a la aplicación**


    ## Flujo basico

    1. La aplicación genera datos
    2. TCP/UDP crea segmentos
    3. IPv4 encapsula en **paquetes IP**
    4. Añade IP origen y destino
    5. Routers encaminan el paquete
    6. El host destino lo recibe

    ## Elementos que componen IPv4

    1. **Direccion IPv4**

        * Formato:  

            * 32 bits
            * Representacion decimal con puntos

                ```bash
                192.168.1.10
                ```
        * Estructura lógica

            ```bash
            | Red | Host |
            ``
        * Ejemplo

            ```bash
            192.168.1.0/24
            ```
            * Red: `192.168.1`
            * Host: `10`

    2. **Mascara de subred**

        * Define **que parte es red y que parte es host**

            | CIDR | Máscara       |
            | ---- | ------------- |
            | /8   | 255.0.0.0     |
            | /16  | 255.255.0.0   |
            | /24  | 255.255.255.0 |
                    
    3. **Paquete IPv4 (estructura)**

        * Cabesera simplificada:

            ```bash
            | Version | IHL | TTL | Protocolo | IP Origen | IP Destino | Datos |
            ```
        * **Campos importantes**

            * **TTL** evita bucles
            * **Protocolo** TCP (6), UDP (17), ICMP (1)
            * **Checksum** verificación de cabecera

    4. **Enrutamiento**

        * Usa **routers**
        * Cada router consulta su **tabla de rutas**
        * Decide el **siguiente salto**

        * Ejemplo

            ```bash
            ip route
            ```
    5. **Fragmentación**

        * Ocurre si el paquete > MTU
        * Puede fragmentarse
        * Reensamblaje en destino


    6. **Clases de IPv4**

        | Clase | Rango                     |
        | ----- | ------------------------- |
        | A     | 1.0.0.0 – 126.0.0.0       |
        | B     | 128.0.0.0 – 191.255.0.0   |
        | C     | 192.0.0.0 – 223.255.255.0 |

        * Hoy se usa **CIDR**, no clases

    7. **Direcciones especiales**

        | Dirección       | Uso       |
        | --------------- | --------- |
        | 127.0.0.1       | Loopback  |
        | 0.0.0.0         | Default   |
        | 255.255.255.255 | Broadcast |
        | 192.168.x.x     | Privadas  |

    8. **NAT (Network Address Translation)**

        * Traduce IP privadas <-> publicas
        * Permite ahorrar direcciones IPv4
        * Usado en routers y cloud

    
    ## Ejemplo

    * **Cliente -> Servidor Web**

        1. Cliente: `192.168.1.10`
        2. Servidor: `34.120.50.10`
        3. TCP crea segmentos
        4. IPv4 encapsula
        5. Router NAT traduce IP
        6. Paquete cruza Internet
        7. Servidor responde

    ## IPv4 en Docker y Kubernetes

    * Pod IP (IPv4)
    * Service IP
    * Cluster IP
    * Overlay networks

    