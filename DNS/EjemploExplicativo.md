# Ejemplo Explicativo

* **Escenario del ejemplo**

    * Servicio DNS: **Bind9**
    * IP del DNS: `192.168.50.10`
    * Dominio: `app.lab`
    * IP del Ingress (MetalLB): `192.168.50.100`
    * Sin views (para que se vea claro el flujo)
    * Un frontend expuesto

* **Visión global**

    ```bash
    named (proceso)
    |
    └── named.conf  ← archivo raíz
            |
            ├── named.conf.options  ← comportamiento global
            |
            └── named.conf.local    ← zonas que tú defines
                        |
                        └── Zone File  ← registros DNS reales
    ```
    * **Esta diagrama es la clave de todo**

    ## Paso 1: `named.conf`

    * **El archivo raíz**
    * **Que es?**

        * `named.conf` es el **archivo principal** que **named** lee al arrancar.

        * **No define zonas**
        * **No define registros**
        * **Solo organiza el resto**

    * **Para que sirve?**

        * Punto de entrada
        * Incluye otros archivos
        * Define el orden de carga

    * **Contenido típico de `/etc/bind/named.conf`**

        ```conf
        include "/etc/bind/named.conf.options";
        include "/etc/bind/named.conf.local";
        ```
    * **Explicación**

        * `named` arranca
        * Lee `named.conf`
        * Ve dos `include`
        * Carga esos archivos **en ese orden**

    * **Aqui todavia No hay DNS real**

    ## Paso 2: `named.conf.options`

    * **Cómo se comporta el servidor DNS**

    * **Que es?**

        * Define **el comportamiento global del DNS**

    * No define dominios
    * No define registros
    * Define **reglas del servidor**

    * **Para qué sirve?**

        * ACLs
        * Recursion
        * Forwarders
        * Seguridad
        * Interfaces

    * **Ejemplo: `/etc/bind/named.conf.options`**

        ```conf
        options {
            directory "/var/cache/bind";

            recursion yes;
            allow-recursion { localnets; localhost; };

            listen-on { 192.168.50.10; };
            allow-query { any; };
        };
        ```
        | Línea             | Qué hace                        |
        | ----------------- | ------------------------------- |
        | `directory`       | Dónde guarda cache              |
        | `recursion yes`   | Puede resolver nombres externos |
        | `allow-recursion` | Solo LAN                        |
        | `listen-on`       | IP del servidor DNS             |
        | `allow-query`     | Quién puede preguntar           |

    ## Paso 3: `named.conf.local`

    * **Aqui empiezan tus dominios**

    * Es el archivo donde  **defines las zonas DNS** que **tu administras**

    * Aqui dices

        > Este dominio es mio y yo respondo por el

    * **Ejemplo: `/etc/bin/named.conf.local`**

        ```conf
        zone "app.lab" {
            type master;
            file "/etc/bind/zones/db.app.lab";
        };
        ```
        | Línea             | Qué hace                        |
        | ----------------- | ------------------------------- |
        | `directory`       | Dónde guarda cache              |
        | `recursion yes`   | Puede resolver nombres externos |
        | `allow-recursion` | Solo LAN                        |
        | `listen-on`       | IP del servidor DNS             |
        | `allow-query`     | Quién puede preguntar           |

        * **Todavía no sabemos que es app.lab**
        * Solo sabemos **dónde buscar**

    ## Paso 4: Zone File

    * **Aqui esta el DNS real**
    * Es el **archivo de zona** contiene **los registros DNS reales**
    * Es la *base de datos* del dominio

    * **Ejemplo `/etc/bind/zones/db.app.lab`**

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

        ns1 IN A 192.168.50.10
        app IN A 192.168.50.100
        ```

        * `$TTL 300` Cuanto tiempo cachean los clientes
        * `@` Representa `app.lab`
        * `SOA` Define **quien manda sobre la zona**

            ```bash
            ns1.app.lab → servidor DNS
            admin@app.lab → admin
            ```
        * `IN NS ns1.app.lab` Dice:

            > *El servidor autoritario de `app.lab` se llama `ns1.app.lab`*

        * `ns1 IN A 192.168.50.10` Resuelve ese nombre:

            ```bash
            ns1.app.lab -> 192.168.50.10
            ```
            * **Esto apunta al mismo servidor Bind9**

        * `app IN A 192.168.50.100` Este es el resultado final:

            ```bash
            app.lab -> 192.168.50.100
            ```
            * IP del INgress NGINX (MetalLB)

    ## Paso 5: Que ocurre cuando alguien consulta

    * **Cliente hace**

        ```bash
        dig app.lab
        ```
        1. Cliente -> `192.168.50.10`
        2. named recibe la consulta
        3. named.conf -> sabe que cargar
        4. named.conf.local -> sabe que maneja `app.lab`
        5. Zona file -> encuentra el registro
        6. Respuesta

            ```bash
            app.lab -> 192.168.50.10
            ```

    ## Relación final

    | Archivo              | Rol                  |
    | -------------------- | -------------------- |
    | `named.conf`         | Orquestador          |
    | `named.conf.options` | Comportamiento       |
    | `named.conf.local`   | Declaración de zonas |
    | Zone File            | Datos DNS reales     |

    ## Cómo encaja con Kubernetes

    ```bash
    DNS (Bind9)
    |
    v
    app.lab → 192.168.50.100
                |
                v
            NGINX Ingress
                |
                v
            Frontend / Backend
    ```