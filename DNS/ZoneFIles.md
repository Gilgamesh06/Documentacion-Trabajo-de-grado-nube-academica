# Zone Files 

* Los **Zone Files** (archivos de zona) son **archivos de texto** que contiene los **registros DNS reales** de un dominio o subdominio

    * Son **la bases de datos del DNS**
    * Aqui es donde se define **qué nombre aputa a qué IP, qué servidor maneja la zona, qué subdominio existen, etc.**

    ## Para que sirve los Zona Files ?

    * Asociar **dominios <-> IPs**
    * Definir **subdominios**
    * Inidicar **quién es el DNS autoritativo**
    * Controlar **TTL, seriales, transferencias de zona**
    * Permitir **resolución interna (LAN/ cluster)** o pública

    * En tu caso:

        > app.lab -> IP de MetalLB (NGINX Ingress)  
        api.app.lab -> mismo  
        frontend.app.lab -> mismo Ingress

    ## Funcionamiento

    1. `named` arranca
    2. Lee:

        * `named.conf`
        * `named.conf.options`
        * `named.conf.local`

    3. En `named.conf.local` se declara una **zona**
    4. Esa zona apunta a un **zone file**
    5. Cuando un cliente pregunta:

        * `Quien es app.lab ?`

    6. Bind9:

        * Busca la zona `app.lab`
        * Lee el **zone file**
        * Responde con la IP configurada

    ## Tipos de Zone files

    1. **Forward Zone (Directa)**

        * Nombre -> IP
        * La mas común

            ```bash
            app.lab -> 192.168.50.100
            ```
    
    2. **Reverse Zone (Inversa)**

        * IP -> Nombre
        * Opcional pero recomendada

            ```bash
            192.168.50.100 -> app.lab
            ```

    ## Elementos que componen un Zone File

    | Elemento | Función                                   |
    | -------- | ----------------------------------------- |
    | `$TTL`   | Tiempo de vida del caché                  |
    | `SOA`    | Start of Authority (autoridad de la zona) |
    | `NS`     | Servidores DNS de la zona                 |
    | `A`      | Nombre → IPv4                             |
    | `AAAA`   | Nombre → IPv6                             |
    | `CNAME`  | Alias                                     |
    | `MX`     | Servidores de correo                      |
    | `TXT`    | Metadatos                                 |

    ## Estructura basica 

    ```dns
    @   IN  SOA ns1.app.lab. admin.app.lab. (
            2026011601 ; Serial
            3600       ; Refresh
            1800       ; Retry
            604800     ; Expire
            86400 )    ; Minimum TTL

        IN  NS  ns1.app.lab.

    ns1     IN  A   192.168.50.10
    app     IN  A   192.168.50.100
    ```

    * `$TTL 86400`

        * Tiempo (en segundos) que los clientes cachean la respuesta -> 24 horas

    * `@` 

        * Representa el **nombre de la zona**. Si la zona es `app.lab`, entonces:

            ```bash
            @ = app.lab
            ```
    
    * `SOA` **- Start of Authority**

        ```dns
        @ IN SOA ns1.app.lab. admin.app.lab. ()
        ```
        | Campo            | Significado                       |
        | ---------------- | --------------------------------- |
        | `ns1.app.lab.`   | DNS principal                     |
        | `admin.app.lab.` | Email del admin (`admin@app.lab`) |

    * **Serial**

        ```dns
        2026011601
        ```
        * Cada cambio en el archivo -> aumentar el serial:
        * Usualmente formato: `YYYYMMDDNN`

    * **Refresh/Retry/Expire**

        * Controlan **replicación entre DNS** (Si existieran secundarios)

    * **NS Record**

        ```dns
        IN NS ns1.app.lab
        ```
        * Define quién es el **DNS autoritativo**

    * **Registros A**

        ```dns
        ns1 IN A 192.168.50.10
        app IN A 192.168.50.100
        ```

        * Resultado

            ```bash
            ns1.app.lab -> 192.168.50.10
            app.app.lab -> 192.168.50.100
            ```
    
    ## Ejemplo

    * Supongamos:

        * IP MetalLB (Ingress): `192.168.50.100`
        * Dominio base: `app.lab`
        * DNS interno: `102.168.50.10`

    * `/etc/bind/named.conf.local`

        ```conf
        zone "app.lab" {
            type master;
            file "/etc/bind/zones/db.app.lab";
        };
        ```

    * `/etc/bind/zones/db.app.lab`

        ```dns
        $TTL 300
        @ IN SOA ns1.app.lab. admin.app.lab. (
            2026011601
            3600
            1800
            604800
            300
        )
        IN NS ns1.app.lab.
        
        ns1        IN A 192.168.50.10
        app        IN A 192.168.50.100
        api        IN A 192.168.50.100
        frontend   IN A 192.168.50.100
        gateway    IN A 192.168.50.100
        ```
    
    ## Qué pasa ahora cuando alguien entra a la app ?

    1. Usuarios escribe:

        ```bash
        https://api.app.lab
        ```

    2. DNS responde

        ```bash
        192.168.50.100
        ```
    
    3. NGINX Ingress recibe la petición
    4. Ingress enruta por:

        * Host
        * Path
    
    5. Kubernetes envía al servicios correcto

    * **DNS -> Ingress -> Servicios -> Pods**

    ## Relación con Kubernetes e Ingress

    * DNS **No sabe nada de Kubernetes**

        | Componente    | Responsabilidad      |
        | ------------- | -------------------- |
        | DNS           | Resolver nombre → IP |
        | MetalLB       | Asignar IP           |
        | NGINX Ingress | Routing HTTP         |
        | K8s Service   | Balancear pods       |

    ## Buenas práciticas

    * TTL bajo (300-600)
    * Un solo IP (Ingress)
    * Subdominios por Servicio
    * Serial bien versionado 
    * Zones separadas por entorno (`dev.lab`, `prod.lab`)

    ## En resumen

    * **Zone Files** = bases de datos DNS
    * Contienen **registros reales**
    * Son usados por **Bind9 / named**
    * Permiten ingresar **MetalLB + Ingress**
    * Son clave para simular **producción real**