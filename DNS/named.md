# named

* `named` **(Name Daemon)** es el **proceso principal de BIND9**
* Es el **demonio DNS** que realmente **escucha consultas, resulta nombres, administra zonas y responde en el puerto 53**

* Es el **servicio/demonio DNS** de BIND
* Corre en segundo plano
* Implementa el **protocolo DNS**
* Gestiona zonas, caché, seguridad y recursión

* En Linux:

    ```bash
    systemctl status named
    ```

    ## Para que sirve `named` ?

    * `named` sirve para:

        * Resolver **nombres de dominio -> IP**
        * Actuar como **DNS autoritativo**
        * Actuar como **DNS recursivo / caché**
        * Administrar **zonas DNS**
        * Responder consultas internas y externas
        * Proveer DNS para infraestructuras on-prem y clusters

    * En tu arquitectura:

        ```bash
        app.lab -> 192.168.1.240 (MetalLB / NGINX)
        ```
        * Eso lo responde directamente **named**

    ## Funcionamiento

    1. `named` **escucha la consulta**

        * Puerto **53**
        * UDP (normal)
        * TCP (zonas grandes / tranferencias)

        ```bash
        Cliente -> named
        ```
    
    2. **Analiza la consulta**

        * `named` revisa:

            1. **Tengo esta zona configurada ?**
            2. **Soy autoritativo ?**
            3. **Está en caché ?**
    
    3. **Decide cómo responder**

        * **Caso A: Zona local (autoritativo)**

            ```bash
            api.app.lab -> 192.168.1.240
            ```
            * Respuesta inmediata desde el archivo de zona
        
        * **Caso B: No es autoritativo**

            * Si recursión está activa:

                * Consutal root -> TLD -> autoritativo
                * Guardar en caché
            
            * Si no:

                * Rechaza la consulta
    
    4. **Envía la respuesta**

        * Devuelve IP
        * Incluye TTL
        * El cliente continúa la conexión

    ## Elementos que componen `named`

    1. **El proceso `named`**

        * Binario principal:

            ```bash
            /usr/sbin/named
            ```
        * Se ejecuta como servicio:

            ```bash
            systemd -> named.service
            ```
    2. **Archivos de configuración**

        * `/etc/bind/named.conf`

            * Archivo raíz de configuración

                ```conf
                include "/etc/bind/named.conf.options";
                include "/etc/bind/named.conf.local";
                include "/etc/bind/named.conf.default-zones";
                ```
        * `named.conf.options`

            * Opciones globales

                ```conf
                options {
                    recursion yes;
                    allow-query {any;};
                    listen-on port 53 {any;};
                };
                ```
                * Controla:

                    * Recursión
                    * Seguridad
                    * Caché
                    * Forwarders
        
        * `named.conf.local`

            * Definicón de **zonas propias**

                ```conf
                zone "app.lab" {
                    type master;
                    file "/etc/bind/zones/db.app.lab";
                };
                ```
    3. **Archivos de zona**

        * Ejemplo:

            ```zone
            @   IN  SOA ns.app.lab. admin.app.lab. (
                    2026011601
                    3600
                    1800
                    604800
                    86400
            )
            @   IN NS ns.app.lab.
            @   IN A  192.168.1.240
            ```
        
        * Aquí `named` obtiene:

            ```bash
            app.lab -> 192.168.1.240
            ```
    
    4. **Cache DNS**

        * `named` mantiene caché en:

            ```bash
            /var/cache/bind
            ```
            * Mejora rendimiento
            * Reduce tráfico externo
    
    5. **Módelos internos de `named`**

        * **Resolver**

            * Lógica de resolución recursiva
            * Comunicación con root/TLD

        * **Cache Manager**

            * Manaje TTL
            * Expiración de registros

        * **Zone Manager**

            * Carga archivos de zona
            * Mantiene autoridad

        * **Query Processor**

            * Procesa peticiones entrantes

    6. **Seguridad y control de acceso**

        * `named` implementa:

            * ACLs
            * Views
            * Rate limiting
            * DNSSEC
            * TSIG

        * Ejemplo

            ```conf
            acl internal {
                192.168.1.0/24;
            };

            options {
                allow-query { internal; };
            };
            ```
    
    7. **Transferencias de zona (AXFR/IXFR)**

        * `named` puede:

            * Enviar zonas a servidores secundarios
            * Recibir actualizaciones incrementales

    8. **Logs**

        * `named` genera logs:

            * Consultas
            * Errores
            * Seguridad
            
        * Configurables via:

            ```conf
            logging {...};
            ```
    
    ## Relación Bind9 <-> named

    | Elemento         | Rol                   |
    | ---------------- | --------------------- |
    | **Bind9**        | Software DNS completo |
    | **named**        | Demonio DNS           |
    | Archivos `.conf` | Configuración         |
    | Zone files       | Datos                 |
    | systemd          | Arranque              |

    ## `named` en tu proyecto MicroK8s 

    * Rol

        * Resolver **dominio principal** -> IP MetalLB
        * Punto único DNS de entrada

    * No hace:

        * Balanceo L7
        * Routing HTTP
        * Service discovery interno K8s

    * Eso lo hace **NGINX + Ingress + CoreDNS**

    ## Resumen

    * `named` es el **corazón de Bind9**
    * Escucha en el puerto 53
    * Resuelve dominios
    * Administra zonas y caché
    * Fundamental en DNS on-prem

    