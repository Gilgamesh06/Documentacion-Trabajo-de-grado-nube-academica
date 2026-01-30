# local

* `/etc/bind/named.conf.local` es el **archivo donde se definen tus zonas DNS propias**, es decir, **los dominios de los que tu servidor Bind9 (`named`) es autoritativo**

    * Si `named.conf.options` define **cómo se comporta el DNS**
    * `named.conf.local` define **qué dominios administra**

* `named.conf.local` es el archivo de configuración de **zonas locales**
* Aqui declaras:

    * Dominios internos (`app.lab`)
    * Subdominios
    * Zonas directas e inversas

* No contiene opciones globales
* Es leído por `named` al arrancar

* Ubicación típica:

    ```bash
    /etc/bind/named.conf.local
    ```

    ## Para que sirve ?

    * Definir **dominios autoritativos**
    * Asociar dominios con archivos de zona
    * Controlar permisos por dominio
    * Separar configuración global de datos DNS
    
    * En tu proyecto:

        ```bash
        app.lab -> 192.168.1.240 (MetalLB / NGINX)
        ```
        * Eso se declara aquí

    ## Funcionamiento

    1. `named` arranca
    2. Lee `/etc/bind/named.conf`
    3. Encuentra:

        ```conf
        include "/etc/bind/named.conf.local";
        ```
    
    3. Cargar todas las **zonas declaradas**
    4. **Por cada zona**

        * Abre el archivo de zona
        * Valida su sintaxis
        * Se vuelve **autoritativo**
    
    * Si una zona falla -> `named` **no arranca**

    * **Estructura Basica**

        ```conf
        zone "dominio" {
            type master | slave;
            file "archivo";
            ...
        };
        ```

    ## Elementos que componen `named.conf.local`

    1. **Bloques `zone`**

        * Cada bloque define una **zona DNS**

            ```conf
            zone "app.lab" {
                type master;
                file "/etc/bind/zones/app.lab";
            };
            ```
    2. **`zone "nombre"`**

        * Nombre exacto del dominio
        * Sin punto final

            ```conf
            zone "app.lab" { ... };
            ```

    3. **`type`**

        * Define el rol del servidor para esa zona

            | Tipo     | Significado            |
            | -------- | ---------------------- |
            | `master` | Autoritativo principal |
            | `slave`  | Copia de otro DNS      |
            | `hint`   | Zona raíz              |

        * En tu caso;

            ```conf
            type master;
            ```

    4. **`file`**

        * Archivo donde viven los **registros DNS**

            ```bash
            fiile "/etc/bind/zones/db.app.lab"
            ```
    
    5. **Seguridad por zona**

        * Puedes aplicar ACL especificas:

            ```conf
            allow-query {lan;};
            allow-transfer {none;};
            ```
    
    6. **Zonas inversas (PTR)**

        ```conf
        zone "240.1.166.192.in-addr.arpa" {
            type master;
            file "/etc/bind/zones/db.192.168.1";
        };
        ```

    ## Ejemplo

    * **Objetivo**

        * Dominio: `app.lab`
        * IP Ingress: `192.168.1.240`
        * DNS interno
        * Seguro

        ### Paso 1: Editar `named.conf.local`

        ```conf
        zone "app.lab" {
            type master;
            file "/etc/bind/zones/db.app.lab";
            allow-query {lan;};
            allow-trasfers {none;};
        };
        ```

        ### Paso 2: Crewar el archivo de zona `/etc/bind/zones/db.app.lab`

        ```conf
        $TTL 300

        @   IN SOA ns.app.lab. admin.app.lab. (
                2026011701 ; Serial
                3600       ; Refresh
                1800       ; Retry
                604800     ; Expire
                300        ; Negative Cache
        )

        @   IN NS ns.app.lab.
        ns  IN A 192.168.1.10

        @       IN A 192.168.240
        api     IN A 192.168.1.240
        gateway IN A 192.168.1.240
        ```

        ### Paso 3: Validar Configuración

        ```bash
        named-checkconf
        named-checkkzone app.lab /etc/bind/zones/app.app.lab
        ```

        ### Paso 4: Reiniciar Bind9

        ```bash
        systemctl restart bind9
        ```

        ### Paso 5: Probar resolución

        ```bash
        dig @192.168.1.10 app.lab
        dig @192.168.1.10 api.app.lab
        ```

        * **Resultado**

            ```bash
            app.lab -> 192.168.1.240
            api.app.lab -> 192.168.1.240
            ```

    ## Que pasa despues ?

    * DNS **solo entrega la IP**

    * NGINX Ingress decide:

        ```bash
        /api -> backend
        /gateway -> gateway
        / -> frontend
        ```

    ## Relación con Kubernetes

    | Componente                 | Rol                 |
    | -------------------------- | ------------------- |
    | Bind9 (`named.conf.local`) | Dominios            |
    | CoreDNS                    | DNS interno cluster |
    | MetalLB                    | IP externa          |
    | NGINX Ingress              | Routing HTTP        |
    | Calico                     | Seguridad de red    |

    ## Errores Comunes

    * No incrementar el Serial
    * Ruta incorrecta del archivo
    * Permisos incorrectos
    * Syntax errro -> named no arranca

    ## Resumen

    * `named.conf.local` define **que dominios administra tu DNS**
    * Cada dominio = una zona
    * Apunta dominios a IPs (MetalLB)
    * Es calve para Kuberneste on-prem