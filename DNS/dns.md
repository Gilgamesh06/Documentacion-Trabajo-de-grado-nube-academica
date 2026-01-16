# DNS

* DNS (**Domain Name System**) es un **sistema de nombres jerárquico y distribuido** que traduce **nombres de dominio legibles por humanos** (como `www.google.com`) en **direcciones IP** (como `142.250.190.36`), que son las que realmente usan las computadoras para comunicarse en la red.

    ## Para qué sirve DNS ?

    * DNS sirve para **localizar recursos en una red**, principalmente en internet

    * Sin DNS:

        * Tendrías que recordar direcciones IP numéricas.
        * El cambio de IP de un servidor rompería todos los accesos

    * Con DNS:

        * Usas nombres fáciles de recordar
        * Los servicios puede cambiar de IP sin afectar a los usuarios
        * Permite balanceo de carga, alta disponibilidad y tolerancia a fallos

    ## Funcionamiento

    * Ejemplo: accesdes a `www.ejemplo.com`

    1. **Consulta local (DNS cache)**

        * Tu computadora revisa:

            * Caché del navegador
            * Caché del sistema operativo
            * Archivo `hosts`
            * Si encuentra la IP -> **fin del proceso**
    
    2. **Consulta al resolver DNS**

        * Si no la encuentra_

            * Envía la consulta a un **DNS recursivo**

                * Normalmente del ISP
                * O uno público (`8.8.8.8`, `1.1.1.1`)
    
    3. **Consulta al servidor raíz (Root Server)**

        * El DNS recursivo pregunta

            > Quien sabe sobre `.com` ?

        * El servidor raíz responde

            > Pregunta a los servidores TLD de `.com`

    4. **Consulta al servidor TLD**

        * El DNS recursivo pregunta al TLD `.com`

            > Quien administra `ejemplo.com`

        * El TLD responde

            > Estos son los **servidores autoritativos** de `ejemplo.com`

    5. **Consulta al servidor autoritativo**

        * El DNS recursivo pregunta:

            > Cual es la IP de `www.ejemplo.com` ?

        * El autoritativo responde

            > `192.0.2.10`

    * **Flujo resumido**

        ```bash
        Cliente -> DNS Recursivo -> Root -> TLD -> Autoritativo -> Cliente
        ```
    
    ## Caracteristicas

    * **Jerárquico**

        * Organizado en niveles:

            ```bash
            . (root)
            └── com
                └── ejemplo
                    └── www
            ```    
    * **Distribuido**

        * No existe un único servidor DNS
        * Millones de servidores en todo el mundo

    * **Cacheable**

        * La respuesta se almacena temporalmente
        * Reduce latencia y tráfico

    * **Escalable**

        * Funciona para miles de millones de dispositivos
        * Soporta crecimiento continuo de Internet

    * **Redundante y tolerante a fallos**

        * Múltiples servidores por dominio
        * Si uno cae, otro responde

    * **Independiente de IP**

        * Un dominio puede cambiar de IP sin afectar al usuario

    ## Elementos que componen DNS

    1. **Dominio**

        * Nombre legible por humanos

            ```bash
            www.api.ejemplo.com
            ```
            * Se compone de:

                * `.` -> Root
                * `com` -> TLD
                * `ejemplo` -> Dominio
                * `www` / `api` -> Subdominio

    2. **Servidores DNS**

        * **Servidores raíz (Root Servers)**

            * 13 conjuntos lógicos (A-M)
            * Punto inicial de todas las búsquedas

        * **Servidores TLD**

            * Administran dominios como:

                * `.com`
                * `.org`
                * `.net`
                * `.co`

        * **Servidores autoritativos**

            * Contienen los registros reales del dominio
            * Fuente oficial de la información

        * **Servidores recursivos**

            * Hacen el trabajo de buscar la respuesta completa
            * Son los que usan los clientes

    3. **Registros DNS**

        | Registro  | Función                                         |
        | --------- | ----------------------------------------------- |
        | **A**     | Dominio → IPv4                                  |
        | **AAAA**  | Dominio → IPv6                                  |
        | **CNAME** | Alias de otro dominio                           |
        | **MX**    | Servidores de correo                            |
        | **NS**    | Servidores autoritativos                        |
        | **TXT**   | Información adicional (SPF, DKIM, verificación) |
        | **SRV**   | Servicios específicos (puerto, protocolo)       |
        | **PTR**   | IP → Dominio (DNS inverso)                      |

    4. **Zonas DNS**

        * Conjunto de registros administrados por un servidor autoritativo
        * Ejemplo: zona `ejemplo.com`

    5. **TTL (Time To Live)**

        * Timepo que una respuesta permanece en cache
        * Controla que tan rapido se reflejan cambios

        * Ejemplo:

            ```ini
            TTL = 300 segundos
            ```

    6. **Protocolos y puertos**

        * **UDP 53** -> consultas normales
        * **TCP 53** > respuesta grandes / traferencias de zona
        * **DoH / DoT** -> DNS cifrado (HTTPS / TLS)

    ## Resumen

    * **DNS** traduce nombres a direccions IP
    * Funciones mediante consultas jerarquicas y recursivas
    * Es distribuido, cacheable y altamente disponible
    * Se compone de dominios, servidores DNS, registros y zonas
