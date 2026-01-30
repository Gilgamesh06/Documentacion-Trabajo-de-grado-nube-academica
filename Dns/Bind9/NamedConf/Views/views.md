# view

* Un **view** es una **vista lógica del DNS** que permite que **el mimso dominio** devuelva **respuestas diferentes** dependinedo de **quien hace la consulta**

* Mismo nombre DNS
* Distinta respuesta
* Según **origen de la consulta**

* Idea clave

    > **Views = DNS basado en identidad del cliente**

    ## Para que sirve ?

    * Separar **DNS interno vs externo**
    * Exponer servicios **solo a ciertas redes**
    * Ocultar infraestructura interna
    * Simular produccion real
    * Controlar acceso sin firewall
    * Evitar split-horizon DNS *manual*


    ## Ejemplo simple

    * **Sin views**

        ```bash
        app.lab -> 192.168.50.100
        ```
        * Todos ven lo mismo

    * **Con views**

        | Cliente    | Respuesta      |
        | ---------- | -------------- |
        | LAN        | 192.168.50.100 |
        | Kubernetes | 10.152.183.10  |
        | Internet   | NXDOMAIN       |

    ## Donde se configuran los views ?

    * Los `views` van en

        ```bash
        /etc/bind/named.conf
        ```
        * O en un archivo incluido desde ahi

    * Cuando usas views

        * **No puedes definir zonas fuera de views**
        * Todas las zonas deben estar dentro de un view

    ## Funcionamiento

    1. Cliente hace consulta DNS
    2. Bind9 evalúa **ACLs del cliente**
    3. Sleecciona el **view correcto**
    4. Busca la zona dentro de ese view
    5. Responde usando **ese zone file**

    ## Elementos que componen un view
    
    * Un `view` tiene:

        | Elemento        | Función             |
        | --------------- | ------------------- |
        | `view "nombre"` | Identificador       |
        | `match-clients` | Quién usa este view |
        | `recursion`     | Permitida o no      |
        | `zones`         | Zonas visibles      |
        | `include`       | Archivos externos   |

    ## Sintaxis basica 

    ```conf
    view "internal" {
        match-clients { internal-net; };
        recursion yes;

        zone "app.lab" {
            type master;
            file "/etc/bind/zones/internal/db.app.lab";
        };
    };
    ```

    ## Paso 1: Definir ACLs (base de todo)

    * `/etc/bind/named.conf.options`

        ```conf
        acl internal-net {
            192.168.50.0/24;
            10.0.0.0/8;
        };
        ```
    ## Paso 2: Crear views

    * `/etc/bind/named.conf`

        ```conf
        include "/etc/bind/named.conf.options";

        view "internal" {
            match-clients { internal-net; localhost; };
            recursion yes;

            include "/etc/bind/zones/internal/zones.conf";
        };

        view "external" {
            match-clients { any; };
            recursion no;

            include "/etc/bind/zones/external/zones.conf";
        };
        ```
        * **El orden importa**
        * Bind usa el **primer view que haga match**

    ## Paso 3: Definir zonas por view

    * `/etc/bind/zones/internal/zones.conf`

        ```conf
        zone "app.lab" {
            type master;
            file "/etc/bind/zones/internal/db.app.lab";
        };
        ```
    
    * `/etc/bind/zones/external/zones.conf`

        ```conf
        zone "app.lab" {
            type master;
            file "/etc/bind/zones/external/db.app.lab";
        };
        ```

    ## Paso 4: Zones files distintos

    * **Interno**

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
        api        IN A 10.152.183.20
        db         IN A 10.152.183.30
        ```
    
    * **Externo**

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

        ns1  IN A 192.168.50.10
        app  IN A 192.168.50.100
        ```
        * En el mundo exterior:

            * No ve `api`
            * No ve `db`

    ## Que pasa cuando un cliente consulta ?

    * **Cliente Interno (192.168.50.25)**

        ```bash
        dig api.app.lab
        → 10.152.183.20
        ```
    
    * **Cliente externo**

        ```bash
        dig api.app.lab
        → NXDOMAIN
        ```
