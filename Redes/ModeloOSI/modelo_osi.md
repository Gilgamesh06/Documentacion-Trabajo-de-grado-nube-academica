# Modelo OSI

* El modelo OSI sirve para:

    1. **Entender como viajan los datos por una red**
    2. **Estandarizar la comunicación entre fabricantes**
    3. **Diseñar protocolos y arquitecturas de red**
    4. **Diagnosticar y aislar problemas de red**
    5. **Facilitar el aprendizaje de redes**

* **Ejemplo práctico**

    * Si una aplicación web no carga, el modelo OSI ayuda a responder **en qué capa esta el problema**

        * La app falla ? -> Capa 7
        * No hay conexión IP ? Capa 3
        * El cable esta desconectado ? -> Capa 1

    ## Funcionamiento

    * El modelo funciona como una **cadena de procesamiento en capas**

        1. **El emisor** envía datos desde la capa 7 hacia la capa 1
        2. Cada capa **agrega información** (encapsulación)
        3. Los datos viajan por la red
        4. **El receptor** procesa los datos desde la capa 1 hasta la 7
        5. Cada capa **interpreta y elimina su información**

    * Esto se llama **encapsulación y desencapsulación**

    ## Las 7 capas del modelo OSI 

    * 7. Aplicación
    * 6. Presentación
    * 5. Sesión
    * 4. Transporte
    * 3. Red
    * 2. Enlace de datos
    * 1. Fisica

        ### Capa Fisica

        * Transmite **bits (0 y 1)** como señales electricasa, opticas o de radio

        * **Elementos**

            * Cables (UTP, fibra óptica)
            * Conectores (RJ45)
            * Voltajes, frecuencias
            * Wi-Fi (señal fisica)

        * **Ejemplo**

            * Un cable Ethernet desconectado

        ### Capa de Enlace de Datos

        * Comuncicación **entre dispositivos del mismo segmento**
        * Maneja **direcciones MAC**
        * Detecta errores físicos

        * **Elementos**

            * Ethernet
            * Switches
            * ARP
            * MAC Address

        * **Ejemplo**

            * Un switch enviando tramas dentro de una LAN

        ### Capa de Red

        * **Enrutamiento** entre redes
        * Usa direcciones **IP**
        * Decide la mejor ruta

        * **Elementos**

            * IP (IPv4/IPv6)
            * Routers
            * ICMP (ping)

        * **Ejemplo**

            * Un router enviando paquetes entre redes

        ### Capa de Transporte

        * Comunicación **extremo a extremo**
        * Control de flujo
        * Control de errores
        * Puertos

        * **Protocolos**

            * **TCP** (Confiable)
            * **UDP** (rapido, no confiable)
        
        * **Ejemplo**

            * TCP garantizado que un archivo llegue completo

        ### Capa de Sesión

        * Establece, mantiene y termina sesiones
        * Control diálogos

        * **Ejemplos**

            * Sesiones en conexiones persistentes
            * RPC

        ### Capa de Presentación

        * Formato de datos
        * **Codificación**
        * **Cifrado**
        * **Compresión**

        * **Ejemplos**

            * UTF-8
            * TLS/SSL
            * JPEG, MP3

        ### Capa de Aplicación

        * Interfaz directa con el usuario o aplicación

        * **Protocolos**

            * HTTP/HTTPS
            * FTP
            * SMTP
            * DNS
        
        * **Ejemplo**

            * Un navegador solicitando una página web
