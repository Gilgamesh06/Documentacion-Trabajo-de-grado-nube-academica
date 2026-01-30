# Modelo TCP/IP

* El modelo **TCP/IP (*Transmission Control Protocol/Internet Protocol*)** es un **modelo de arquitectura de red** que organiza la comunicación en **4 capas**, cada una con responsabilidades claras.

    ## Para que sirve ?

    1. Permite la **comunicación entre redes heterogeneas**
    2. Garantiza **interoperabilidad global**
    3. Definir **cómo viajan los datos de extremo a extremo**
    4. Soporta aplicaciones modernas (web, email, streaming)
    5. Ser la base de **Docker, Kubernetes, Cloud y DevOps**

    * **Ejemplo real**

        > Cuando un microservicio en Kubernetes llama a otro (`HTTP -> TCP -> IP`), esta usando TCP/IP

    ## Funcionamiento

    * Funciona mediante un proceso de **encapsulación** en capas:

        1. La **aplicación** genera datos
        2. **TCP o UDP** los segmenta y controla
        3. **IP** enruta los paquetes
        4. La **red física** los transmite
        5. El receptor **desencapsula** los datos en orden inverso

    * Cada cap agrega su propio encabezado (header)

    ## Capas del Modelo TCP/IP

    * 4. Aplicación
    * 3. Transporte
    * 2. Internet
    * 1. Acceso a Red

        ### Capa de Aplicación

        * Proporciona servicios de red a las aplicaciones
        * Interactúa directamente con el software del usuario

        * **Protocolos principales**

            * HTTP/HTTPS
            * FTP
            * SMTP / POP3 / IMAP
            * DNS
            * SSH

        * **Ejemplo**

            * Un navegador solicitando una página web
        
        ### Capa de Transporte 

        * Comunicación **extremo a extremo**
        * Control de errores
        * Control de flujo
        * Uso de **puertos**

        * **Protocolos**

            * **TCP**

                * Confiable
                * Orientado a conexión
                * Garantiza orden y entrega

            * **UDP**

                * No confiable
                * Sin conexión
                * Más rápido

        * **Ejemplo**

            * HTTP usa TCP puerto 80 / 443

        ### Capa de Internet

        * Direccionamiento lógico
        * Enrutamiento entre redes
        * Entrega de paquetes

        * **Protocolos**

            * IP (IPv4/IPv6)
            * ICMP (ping)
            * IPsec

        * **Ejemplos**

            * Un router decide la mejor ruta 

        ### Capa de Acceso a Red

        * Comunicación con el medio fisico
        * Direcciones MAC
        * Tramas

        * **Tecnologías**

            * Ethernet
            * WI-FI
            * ARP

        * **Ejemplo**
        
            * Envio de bits por cable o WI-FI

    ## Modelo TCP/IP vs Modelo OSI

    | TCP/IP        | OSI       |
    | ------------- | --------- |
    | 4 capas       | 7 capas   |
    | Implementado  | Teórico   |
    | Internet real | Educativo |
    | Flexible      | Estricto  |

    ## Relación con tecnologías modernas

    * **Docker** redes bridge -> TCP/IP
    * **Kubernetes** Service, Pod IP, CNI
    * **Cloud** VPC, Load Balancers
    * **APIs REST** HTTP sobre TCP/IP
