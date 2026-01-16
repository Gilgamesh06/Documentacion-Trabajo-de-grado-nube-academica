# ACL

* Una **ACL (Access Control List)** es un **conjunto de direcciones IP, redes o palabras clave** que reprensentan **clientes permitidos o denegados**

* En Bind9

    ```conf
    acl nombre_acl {
        192.168.1.0/24;
        10.0.0.0/8;
        localhost;
    };
    ```
    * **No hacen nada por sí solas**
    * Solo **definen grupos** que luego se usan en reglas

    ## Para qué sirven las ACL ?

    * Sirven para controlar

        * Quién puede **consultar DNS**
        * Quién puede usar **recursión**
        * Quién puede **actualizar zonas**
        * Quién puede usar **forwarders**

    * En tu proyecto:

        * Solo tu LAN puede resolver `app.lab`
        * Nadie externo puede abusar del DNS

    ## Relación ACL <-> Bind9 <-> named

    | Elemento               | Rol                        |
    | ---------------------- | -------------------------- |
    | **Bind9**              | Software DNS               |
    | **named**              | Demonio que ejecuta reglas |
    | **ACL**                | Definen grupos de IP       |
    | **named.conf.options** | Aplica ACL globalmente     |
    | **named.conf.local**   | Aplica ACL por zona        |

    ## Funcionamiento

    1. Cliente envia solicitud DNS
    2. `named` recibe petición
    3. Evalúa:

        * IP del cliente pertenece a una ACL ?
    
    4. Si **si** -> permite acción
    5. Si **no** -> rechaza o ignora

    ## Donde se define las ACL

    * Las ACL se definen normalmente en:

        * `named.conf.options`
        * O directamente en `named.conf`

    * Ejemplo

        ```bash
        acl lan {
            192.168.1.0/24;
            localhost;
        };
        ```

    ## Como se usan las ACL

    * Las ACL se usan **dentro de directivas,** no solas

    1. **`allow-query` (quien puede consultar)**

        ```bash
        allow-query {lan; };
        ```
        * Solo la LAN puede preguntar

    2. **`allow-recursion` (quien puede resolver internet)**

        ```conf
        recursion yes:
        allow-recursion {lan;};
        ```
        * SOlo LAN puede usar recursión

    3. **`allow-transfer` (transferencias de zona)**

        ```conf
        allow-transfer {none;};
        ```
        * Nadie puede copiar tu zona

    4. **`allow-update` (actualizaciones dinámicas)**

        ```conf
        allow-update {none;};
        ```
        * Nadie modifica registro

    5. **`listen-on` (interfaces)**

        ```conf
        listen-on {192.168.1.10;};
        ```
    
    ## Ejemplo

    * **Objetivo**

        * DNS interno
        * Dominio `app.lab`
        * Solo LAN
        * Sin DNS abierto
    
    * `/etc/bind/named.conf.options`

        ```conf
        acl lan {
            192.168.1.0/24;
            localhost;
        };

        options {
            directory "/var/cache/bind";

            listen-on port 53 { 192.168.1.10; };
            listen-on-v6 { none; };

            allow-query { lan; };

            recursion no;
            allow-recursion { none; };

            allow-transfer { none; };

            dnssec-validation auto;
        };
        ```
        | Acción                   | ¿Permitida? |
        | ------------------------ | ----------- |
        | Consultar DNS desde LAN  | Si          |
        | Consultar desde Internet | No          |
        | Recursión                | No          |
        | Copiar zonas             | No          |
        | Modificar registros      | No          |


    ## ACL por zona (Opcional)

    ```conf
    zone "app.lab" {
        type master;
        file "/etc/bind/zones/db.app.lab";
        allow-query { lan; };
    };
    ```

    ## Tipos de elementos en un ACL

    ```conf
    acl ejemplo {
        192.168.1.10;        // IP
        192.168.1.0/24;      // Red
        localhost;           // Palabra clave
        any;                 // Cualquiera
        none;                // Nadie
    };
    ```

