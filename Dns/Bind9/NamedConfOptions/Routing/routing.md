# Routing

* **Pregunta**

    > Una pregunta si solo admite comunicacion de lan entonces solo permiten peticiones en esa misma subred es decir si otras subredes que esten conectadoas por bgp o ospf no enrutarian y podrian ver app.lab ?

* **Respuesta corta**

    * **Si** 
    * Si en Bind9 tienen una ACL como:

        ```conf
        acl lan {
            192.168.1.0/24;
        };

        allow-query { lan; };
        ```
        * Solo los clientes cuya **IP esté en** `192.168.1.0/24` **podran resolver** `app.lab`, aunque **otras subredes estén perfectamente enrutadas vía BGP u OSPF**

        * El routing puede funcionar
        * Pero el DNS va a rechazar la consulta

    ## Punto clave: Rounting != Permisos

    | Capa                     | Qué hace                       |
    | ------------------------ | ------------------------------ |
    | **Routing (BGP / OSPF)** | Decide *cómo llegar* a una red |
    | **ACL de Bind9**         | Decide *si te dejo usar DNS*   |

    * Aunque exista camino IP, **Bind9 puede decir *No te respondo***

    ## Flujo real con BGP/OSPF

    * Supongamos

        * DNS Bind9: `192.168.1.10`
        * Dominio: `app.lab`
        * Subred LAN: `192.168.1.0/24`
        * Subred remota: `10.20.0.0/24`
        * BGP/OSPF funcionan correctamente

    1. **Cliente en otra subred**

        ```bash
        Cliente: 10.20.0.15
        DNS: 192.168.1.10
        ```
    2. **La red Si enruta**

        * BGP/OSPF anuncian rutas
        * El paquete **llega** a Bind9
        * Routing OK

    3. **Bind9 evaluá la ACL**

        ```conf
        allow-query {lan;};
        ```
        * `10.20.0.15` **No pertenece a** `192.168.1.0/24`

    4. **Resultado**

        * **Bind9 No responde**

            * `REFUSED`
            * o timeout
            * o `SERVFAIL`

        * No puede resover `app.lab`

    ## Esto es Exactamente lo que quieres en producción

    * No expones DNS innecesariamente
    * Controlas qué redes puede usarlo
    * Evitas DNS abiertos
    * Separas dominios internos por red

    ## Entonces como permitir otras subredes ?

    * **Opcion 1: Ampliar la ACL (lo más comun)**

        ```conf
        acl internal {
            192.168.1.0/24;
            10.20.0.0/24;
            10.30.0.0/24;
        };
        ```
        ```conf
        allow-query { internal; };
        ```
        * LAN
        * Redes BGP/OSPF
        * Internet

    * **Opcion 2: ACL por zona (más fino)**

        ```conf
        zone "app.lab" {
            type master;
            file "/etc/bind/zones/db.app.lab";
            allow-query {
                192.168.1.0/24;
                10.20.0.0/24;
            };
        };
        ```
        * Control por dominio
        * Util si tienes varios dominios internos

    
    * **Opcion 3: Views (nivel experto / producción)**

        * Diferente respuesta DNS según la red:

            ```conf
            view "lan" {
                match-clients { 192.168.1.0/24; };
                zone "app.lab" { ... };
            };

            view "bgp" {
                match-clients { 10.20.0.0/24; };
                zone "app.lab" { ... };
            };
            ```
            * Muy usado en ISPs y DC grandes
            * Máximo control

    ## Importante para Kubernetes + MetalLB

    ```bash
    Cliente
    ↓ DNS
    Bind9 (ACL)
    ↓ IP MetalLB
    NGINX Ingress
    ↓
    Cluster
    ```
    * Si una red **no puede resolver DNS**
    * No entra al cluster, aunque IP routing funcione

    * Esto es **seguridad por diseño,** no un problema

    ## Resumen

    * BGP/OSPF **solo enrutan**
    * ACL de Bind9 **decide quién usa DNS**
    * Routing != permiso
    * Tener ruta No implica resolver DNS
    * Puedes permitir múltiples subredes explicitamente