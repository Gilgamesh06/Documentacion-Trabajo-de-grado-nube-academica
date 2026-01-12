# Sistemas Autonomos

* Un **Sistema Autónomo** es un **conjunto de redes IP y routers** que:

    * Están bajo **una misma administración**
    * Comparten una **política de enrutamiento común**
    * Se identifican mediante un **número único llamado ASN (Autonomous System Number)**

* Internet es, literalmente, **una red de Sistemas Autónomos interconectados**

    ## Para que sirven ?

    1. **Escalar internet**
    2. **Aplicar políticas propias de tráfico**
    3. **Interconectar redes independientes**
    4. **Interconectar redes independientes**
    5. **Optimizar costos y rutas**

    * **Ejemplo real**

        * Google -> AS15169
        * Cloudflare -> AS13335
        * AWS -> AS16509


    ## Funcionamiento

    * Funcionan mediante **enrutamiento jerárquico y politico,** usando **BGP (Border Gateway Protocol)**

    * **Flujo general**

        1. Cada AS administra sus **redes internas**
        2. Usa un protocolo **IGP** internamente (OSPF, IS-IS)
        3. Usa **BGP** para comunicarse con otros AS
        4. Publica **prefijos IP**
        5. Aplica **politicas de enrutamiento**

    > BGP decir rutas **por política**, no por distancia

    ## Elementos que componen un AS

    1. **ASN (Autonomous System Number)**

        * Identificador único
        * Asignado por **IANA / RIR**
        * 16 bits (legacy) o 32 bits

        * Ejemplos:

            ```bash
            AS13335  → Cloudflare
            AS15169  → Google
            ```

    2. **Prefijos IP**

        * Bloques de direcciones IP
        * Anunciados via BGP

        * Ejemplos

            ```bash
            8.8.8.0/24
            ```
    3. **Routers de borde (Edge Routers)**

        * Funciones:

            * Conectan el AS con otros AS
            * Ejecutan **BGP**
            * Aplican politicas

    4. **Protocolo BGP**

        * Funciones:

            * Intercambio de rutas
            * Selección basada en atributos:
                * AS_PATH
                * LOCAL_PREF
                * MED

    5. **Politicas de enrutamiento**

        * Definen:

            * Que rutas anunciar
            * Que rutas aceptar
            * Preferencias de tráfico

    6. **IGP interno**

        * Usado detro del AS:

            * OSPF
            * IS-IS
            * RIP (obsoleto)
    
    ## Tipos de Sistemas Autónomos

    1. **AS Stub**

        * Conceta a **un solo AS**
        * No transita tráfico de terceros
        * Ejemplo

            * Empresa 
            * Universidad

            ```bash
            Empresa -> ISP
            ```
    2. **AS Multihomed**

        * Conectado a **dos o más AS**
        * No enruta tráfico ajeno
        * Ventaja:

            * Alta disponibilidad
    
    3. **AS Transit**

        * Conectado a multiples AS
        * Si enruta trafico de terceros
        * Son los grandes ISP
