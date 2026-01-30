# named.conf

* `/etc/bind/named.conf` es el **archivo de configuración principal de `named` BIND9**. No contiene normalmente toda la configuración directa, sino que **actúa como el archivo raíz que organiza e incluye el resto de configuraciones DNS**

    * Archivo principal de configuración de BIND9
    * Es leído **al iniciar** `named`
    * Define

        * **Que archivos cargar**
        * **Que zonas existen**
        * **Como debe comportarse el servidor DNS**

    * Ubicación tipica (Debian/Ubuntu)

        ```bash
        /etc/bind/named.conf
        ```

    ## Para que sirve ?

    * Centralizar la configuración DNS
    * Incluir configuraciones separadas (modular)
    * Definir zonas autoritativas
    * Cargar zonas por defecto
    * Facilitar mantenimiento y escalabilidad
    * Sine este archivo, **named no arranca**

    ## Funcionamiento

    * **Flujo al iniciar: `named`**

        1. systemd arranca `named`
        2. `named` lee `/etc/bind/named.conf`
        3. Porcesa las directivas `include`
        4. Carga

            * Opciones globales
            * Zonas personalizadas
            * Zonas por defecto

        5. Abre el puerto 53
        6. El servidor DNS queda operativo

    
    ## Estructura típica de `/etc/bind/named.conf`

    ```conf
    include "/etc/bind/named.conf.options";
    include "/etc/bind/named.conf.local";
    include "/etc/bind/named.conf.default-zones";
    ```
    * Este archivo **no define zonas directamente** (aunque prodría), sino que **las delega a otros archivos**

    ## Elementos que componen `named.conf`

    1. **Directiva `include`**

        * Permite dividir la configuración en archivos más pequeños.

        * Sirve para:

            * Organización
            * Mantenimiento
            * Separación de responsabilidades

        * Ejemplo:

            ```conf
            include "/etc/bind/named.conf.local";
            ```
    2. ***`named.conf.options` (opciones globales)**

        * Configuración general de `named`

        * Ejemplo:

            ```conf
            options {
                directory "/var/cache/bind";

                recursion yes;
                allow-query {any;};

                forwarders {
                    8.8.8.8;
                    1.1.1.1M;
                };

                dnssec-validation auto;
            };
            ```
            | Línea               | Explicación                        |
            | ------------------- | ---------------------------------- |
            | `directory`         | Carpeta de caché                   |
            | `recursion yes`     | Permite resolver dominios externos |
            | `allow-query`       | Quién puede consultar              |
            | `forwarders`        | DNS externos                       |
            | `dnssec-validation` | Validación DNSSEC                  |

    3. **`named.conf.local` (zonas propias)**

        * Aqui defines **tus dominios**

        * Ejemplo:

            ```conf
            zone "app.lab" {
                type master;
                file "/etc/bind/zones/db.app.lab";
            };
            ```
            | Directiva     | Función               |
            | ------------- | --------------------- |
            | `zone`        | Nombre del dominio    |
            | `type master` | Servidor autoritativo |
            | `file`        | Archivo de zona       |

    4. `named.conf.default-zones`

        * Zonas esenciales del sistema

        * Ejemplo

            ```conf
            zone "." {
                type hint;
                file "/usr/share/dns/root.hints";
            };

            zone "localhost" {
                type master;
                file "/etc/bind/db.local";
            };
            ```
            * Zona raiz (`.`)
            * Loopback
            * DNS inverso local

    ## Ejemplo

    * `/etc/bind/named.conf`

        ```conf
        include "/etc/bind/named.conf.options";
        include "/etc/bind/named.conf.local";
        include "/etc/bind/named.conf.default-zones";
        ```
    * `/etc/bind/named.conf.options`

        ```conf
        options {
            directory "/var/cache/bind";

            listen-on port 53 { any; };
            allow-query { 192.168.1.0/24; };

            recursion no;

            dnssec-validation auto;
        };
        ```
        * Solo DNS autoritativo para tu red

    * `/etc/bind/named.conf.local`

        ```conf
        zone "app.lab" {
            type master;
            file "/etc/bind/zones/db.app.lab";
        };
        ```

    * `/etc/bind/zones/db.app.lab`

        ```zone
        $TTL 300

        @   IN SOA ns.app.lab. admin.app.lab. (
                2026011601 ; Serial
                3600       ; Refresh
                1800       ; Retry
                604800     ; Expire
                300        ; Negative cache
        )

        @       IN NS ns.app.lab.
        ns      IN A  192.168.1.10

        @       IN A  192.168.1.240
        gateway IN A  192.168.1.240
        api     IN A  192.168.1.240
        ```
        | Dominio           | Resuelve a    |
        | ----------------- | ------------- |
        | `app.lab`         | IP de MetalLB |
        | `api.app.lab`     | IP de MetalLB |
        | `gateway.app.lab` | IP de MetalLB |

    ## Relación con tu arquitectura

    ```bash
    DNS (named)
    |
    | app.lab → 192.168.1.240
    v
    MetalLB
    |
    NGINX Ingress
    |
    /api /gateway /
    ```

    ## Buenas practicas

    * Separar archivos
    * No meter todo en uno solo
    * Usar TTL bajos en labs
    * Versionar zonas
    * Validar antes de reiniciar

        ```bash
        named-checkconf
        named-checkzone app.lab /etc/bind/zones/db.app.lab
        ```

    ## Resumen

    * `/etc/bind/named.conf` es el **archivo raíz**
    * Orquetas toda la configuración DNS
    * Usa `include` para modularidad
    * Carga opciones, zonas y defaults
    * Es clave para entornos **on-prem + Kubernetes**