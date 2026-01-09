# ARP

* **ARP (Address Resolution Protocol)** es un **protocolo fundamental de red** que permite **traducir una dirección IP en una dirección MAC** dentro de una **red local (LAN)**
* Si ARP, **IP no podria funcionar sobre Ethernet o Wi-Fi**

* Su función principal es:

    > Resolver direcciones IP -> direcciones MAC

    ## Para qué sirve ARP ?

    1. **Encontrar la MAC asociada a una IP**
    2. **Permite el envio de tramas Ethernet**
    3. **Habilitar comunicación dentro de la LAN**
    4. **Conectar IP (Capa Internet) con Ethernet/Wi-Fi**
    5. **Optimizar tráfico usando caché ARP**

    * Sin ARP, un host **sabria la IP destino pero no sabria a quién eviar la trama**

    ## Funcionamiento

    * Funciona mediante **mensajes broadcast y respuesta unicast**, usando una **tabla ARP (caché)**

    ## Componentes principales de ARP

    1. **Mensajes ARP**

        * **ARP Request** (broadcast)
        * **ARP Reply** (unicast)

    2. **Table ARP (ARP Cache)**

        ```bash
        IP              MAC
        192.168.1.1     00:AA:BB:CC:DD:01
        192.168.1.20    00:11:22:33:44:55
        ```
        * Temporal 
        * Expira automaticamente
        * Se actualiza dinámicamente

    3. **Direcciones involucradas**

        * IP origen / destino
        * MAC origen / destino
    
    ## Ejemplo paso a paso 

    * **PC A quiere comunicarse con PC B en la misma red**

    * **Datos iniciales**

        ```bash
        PC A:
        IP  = 192.168.1.10
        MAC = AA:AA:AA:AA:AA:AA

        PC B:
        IP  = 192.168.1.20
        MAC = BB:BB:BB:BB:BB:BB
        ```

        ### Paso 1: PC A revisa su tabla ARP

        * PC A pregunta:

            > Ya conozco la MAC de 192.168.1.20 ?

        * No esta en la cache

        ### Paso 2: ARP Request (broadcast)

        * PC A envia:

            ```bash
            ARP Request:
            "¿Quién tiene la IP 192.168.1.20?"
            "Respóndele a 192.168.1.10"
            ```
            * MAC destino: `FF:FF:FF:FF:FF`
            * Todos los dispositivos lo recibe
        
        ### Paso 3: Todos reciben, solo uno responde

        * PC C -> Ignora
        * Router -> Ignora
        * **PC B reconoce su IP**

        ### Paso 4: ARP Reply (unicast)

        * PC B responde directamente a PC A:

            ```bash
            ARP Reply:
            "La IP 192.168.1.20 es BB:BB:BB:BB:BB:BB"
            ```
            * MAC destino: `AA:AA:AA:AA:AA:AA`

        ### Paso 5: Actualizacion de la Tabla ARP

        * PC A guarda:

            ```bash
            192.168.1.20 → BB:BB:BB:BB:BB:BB
            ```

        ### Paso 6: Envio de datos

        * Ahora PC A puede:

            * Crear una **trama Ethernet**
            * Enviar el **paquete IP**
            * Usar la MAC correcta

    ## Ejemplo cuando el destino esta en otra red

    * PC A -> Servidor en Internet

        1. PC A detecta que la IP **no esta en su red**
        2. Usa ARP para obtener la **MAC del gateway**
        3. Envia el paquete al router
        4. El router continua el proceso
    
    * **ARP nunca cruza routers**

    ## Características principales de ARP

    * **Ventajas**

        * Simple 
        * Automático
        * Rápido
        * Trasnparente para la aplicación

    * **Limitaciones**

        * Solo funciona en LAN
        * No tiene autenticación
        * Vulnerable a ataques

    * **Problemas de seguridad**

        * ARP Spoofing
        * ARP Poisoning
        * Man-in-the-middle
    
    ## Comandos utiles (Linux)

    ```bash
    ip neigh
    arp -n
    ```

    * Limpiar cache:

        ```bash
        ip neigh flush all
        ```

    ## Relación con Docker y Kubernetes

    * Contenedores usan ARP virtual
    * Bridges y veth
    * Pods resuleven MAC via ARP
    * CNI gestiona resolución

    