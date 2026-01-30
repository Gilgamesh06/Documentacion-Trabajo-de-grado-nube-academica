# options

* `/etc/bind/named.conf.options` es el **archivo donde se definen las opciones globales de funcionamiento de `named` BIND9**
* Aqui no se crean zonas; aqui se decide **cómo se comporta el servidor DNS en general**

    * Si `named.conf` es el *Orquestador*
    * `named.conf.options` es el **archivo de políticas y comportamiento global**

* Archivo de **configuración global** de BIND9
* Define reglas que aplican **a todas las zonas**
* Controla:

    * Seguridad
    * Recursión
    * Caché
    * Forwarders
    * Interfaces de escucha
    * DNSSEC
    * Logs

* Ubicación tipica:

    ```bash
    /etc/bind/named.conf.options
    ```

    ## Para que sirve ?

    * Sirve para decidir:

        * Quién puede consultar el DNS ?
        * El DNS resuelve Internet o solo zonas internas ?
        * En qué interfaces escucha ?
        * Usa DNS externos como respaldo ?
        * Como se manejan la caché ?
        * Qué nivel de seguridad aplica ?

    * En tu proyecto:

        * **Autoritativo interno**
        * **No resolver internet**
        * **Responder solo a tu LAN**

    ## Funcionamiento

    * **Flujo al iniciar `named`**

        1. `named` lee `named.conf`
        2. Encuentra

            ```conf
            include "/etc/bind/named.conf.options";
            ```
        3. Aplica las opciones **antes de cargar las zonas**
        4. Todas las zonas heredan estas reglas

    * **Estructura basica**

        ```conf
        options {
            ...
        };
        ```
        * Todo vive dentro del bloque `options {}`

    ## Elementos que componen `named.conf.options`

    1. **`directory`**

        ```conf
        directory "var/cache/bind";
        ```
        * Define dónde `named` guarda:

            * Caché
            * Archivos temporales
            * Zonas descargadas

    2. **`listen-on`**

        ```conf
        listen-on port 53 {any;};
        ```
        * En qué interfaces escucha DNS
        * `any` = todas

        * Ejemplo producción

            ```conf
            listen-on { 192.168.1.10;};
            ```
    
    3. **`allow-query`**

        ```conf
        allow-query { 192.168.1.0/24; };
        ```
        * Define quién puede hacer consultas DNS
        * Control de acceso básico

    4. **`recursión`**

        ```conf
        recursion no;
        ```
        * `yes` -> resuelve internet
        * `no` -> solo zonas locales
        * Para tu cluster -> **no**

    5. **`allow-recursion`**

        ```conf
        allow-recursion {192.168.1.0/24;};
        ```
        * Control fino sobre quién puede usar recursión

    6. **`forwarders`**

        ```conf
        forwarders {
            8.8.8.8;
            1.1.1.1;
        }
        ```
        * DNS externos de respaldo
        * Solo se usan si `recursion yes`
    
    7. **`dnssec-validation`**

        ```conf
        dnssec-validation auto;
        ```
        * Verifica autenticidad de respuestas DNS
        * Recomendado incluso en labs

    8. **`auth-nxdomain`**

        ```conf
        auth-nxdomain no;
        ```
        * Compatibilida con RFC antiguos

    9. **`listen-on-v6`**

        ```conf
        listen-on-v6 { none; };
        ```
        * Desactiva IPv6 si no lo usas
    
    10. **Seguridad avanzada**

        * **ACLs**

            ```conf
            acl trusted {
                192.168.1.0/24;
            };
            ```

            * Uso

                ```conf
                allow-query { trusted;};
                ```
        * **Rate limiting**

            ```conf
            rate-limit {
                responses-per-second 10;
            };
            ```

    ## Ejemplo

    * **Objetivo:**

        * DNS autoritativo interno
        * Responder solo LAN
        * Resolver `app.lab`
        * No resolver internet

    * `/etc/bind/named.conf.options`

        ```conf
        acl lan {
            192.168.1.0/24;
        };

        options {
            directory "/var/cache/bind";

            listen-on port 53 {192.168.1.10;};
            listen-on-v6 {none;};

            allow-query { lan;};

            recursion no;

            allow-recursion {none;};

            dnssec-validation auto;

            auth-nxdomain no;
        };
        ```
        | Línea                  | Explicación          |
        | ---------------------- | -------------------- |
        | `acl lan`              | Define red permitida |
        | `directory`            | Caché                |
        | `listen-on`            | IP donde escucha     |
        | `allow-query`          | Quién consulta       |
        | `recursion no`         | No DNS público       |
        | `allow-recursion none` | Seguridad            |
        | `dnssec-validation`    | Validación           |
        | `listen-on-v6 none`    | Sin IPv6             |
                
    ## Resultado

    ```bash
    Cliente -> app.lab
    DNS -> 192.168.1.240
    ```

    ## Errores comunes

    * `allow-query {any;};` en LAN
    * `recursion yes` sin control
    * Escuchar en `any` sin firewall
    * No usar ACLs

    ## Comprobación

    ```bash
    named-checkconf
    systemctl restart bind9
    dig @192.168.1.10 app.lab
    ```

    ## Relación con Kubernetes

    | Componente      | Rol             |
    | --------------- | --------------- |
    | Bind9 (`named`) | DNS de entrada  |
    | CoreDNS         | DNS interno K8s |
    | MetalLB         | IP externa      |
    | Ingress         | Routing HTTP    |

    ## Resumen 

    * `named.conf.options` define **cómo se comporta el DNS**
    * Aplica a todas las zonas
    * Controla seguridad, recursión y caché
    * Es crítico en entornos on-prem y bare-metal

    