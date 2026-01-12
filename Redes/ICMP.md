# ICMP

* **ICMP (Internet Control Message Protocol)** es un **protocolo de control y diagnóstico** de la **capa de Internet (TCP/IP)**
* No sirve para transportar datos de aplicaciones, sino para **informar errores, estados y ayudar a diagnosticar problemas de red**

    > Es el protocolo detrás d **ping** y **traceroute**

* ICMP es un protocolo usado por **host** y **routers** para **comunicarse información sobre el estado de la red**

    * No es TCP ni UDP
    * No usa puertos
    * Se ecapsula directamente dentro de **IP**

    ## Para que sirve ?

    1. Detectar errores de red
    2. Diagnosticar conectividad
    3. Notificar problemas de enrutamiento
    4. Evitar bucles infinitos
    5. Medir latencia y alcannce

    ## Funcionamiento

    * Funciona mediante **mensajes ICMP** enviados como respuestas a eventos de red

        1. Un host envia un paquete IP
        2. Ocurre un evento (error o control)
        3. Un router o host genera un **mensaje ICMP**
        4. ICMP viaja dentro de un paquete IP
        5. El host origen recibe el mensaje

    ## Elementos que componen ICMP

    1. **Mensaje ICMP**

        * Cada mensaje tiene:

            * **Type** (tipo)
            * **Code** (subtipo)
            * **Checksum**
            * **Datos**

        * Estructura simplificada

            ```bash
            | Type | Code | Checksum | Data |
            ```
    2. **Typos de mensajes ICMP más importantes**

        | Type | Nombre                  | Uso                 |
        | ---- | ----------------------- | ------------------- |
        | 0    | Echo Reply              | Respuesta ping      |
        | 8    | Echo Request            | Solicitud ping      |
        | 3    | Destination Unreachable | Destino inaccesible |
        | 11   | Time Exceeded           | TTL agotado         |
        | 5    | Redirect                | Redirección         |
        | 12   | Parameter Problem       | Error de cabecera   |

    3. **Encapsulación**

        ```bash
        ICMP -> IP -> Ethernet/Wi-Fi
        ```
        * ICMP **no tiene puertos,** usa solo IP

    ## Ejemplo: `ICMP con ping`

    * **PC A hace ping a PC B**

    * **Datos**

        ```bash
        PC A: 192.168.1.10
        PC B: 192.168.1.20
        ```

        ### Paso 1: Echo Request (Type 8)

        * PC A envia:

            ```bash
            ICMP Echo Request
            Type: 8
            Code: 0
            ```
        * Encapsulado en IP

            ```bash
            IP origen: 192.168.1.10
            IP destino: 192.168.1.20
            ```
        ### Paso 2: PC B recibe el mensaje

        * Verifica checksum
        * Reconoce Type 8
        * Prepara respuesta

        ### Paso 3: Echo Reply (Type 0)

        * PC B responde:

            ```bash
            ICMP Echo Reply
            Type: 0
            Code: 0
            ```

        ### Paso 4: PC A recibe respuesta

        * Mide **latencia**
        * Confirma **conectividad**

        * **Resultado**

            ```bash
            64 bytes from 192.168.1.20: time=2ms
            ```
