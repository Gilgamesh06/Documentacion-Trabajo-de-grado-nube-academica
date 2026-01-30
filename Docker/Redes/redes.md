# Redes Docker

* Una **red Docker** es una **red virtual** gestionada por Docker Engine que:

    * Asigna **IPs virtuales** a contenedores
    * Permite **resolución DNS por nombre**
    * Controla **quién puede hablar con quién**
    * Aisla tráfico entre aplicaciones

* Docker crea redes automáticamente o bajo demanda

    ## Para que sirve ?
    
    * **Comunicación entre contenedores**

        * Backend <-> Bases de datos
        * Backend <-> Redis
        * Frontend <-> Backend
    
    * **Aislamiento**

        * Evitar que contenedores no relacionados se comuniquen

    * **Exposición controlada**

        * Exponer solo servicios necesaios al host

    * **Arquitecturas de microservicios**

        * Simular redes reales

    ## Funcionamiento

    * Docker usa **Network namespace** y **drivers de red**

    * **Flujo interno simplificado**

        1. Docker crea una red virtual
        2. Asigna un rango IP (subnet)
        3. Cada contenedor obtiene:

            * IP
            * Interface virtual (`eth0`)

        4. Docker configura.

            * DNS interno
            * Reglas iptables

        5. El trafico se enruta según el driver

    * Cada red tiene su propio **namespace de red**


    ## Elementos que componen una red Docker

    1. **Network Driver**

        * Define el **tipo de red**

            | Driver    | Uso                          |
            | --------- | ---------------------------- |
            | `bridge`  | Comunicación local (default) |
            | `host`    | Usa red del host             |
            | `none`    | Sin red                      |
            | `overlay` | Multi-host (Swarm/K8s)       |
            | `macvlan` | IP real en LAN               |

    2. **Subnet**

        * Rango de IPs virtuales

        * Ejemplo:

            ```bash
            172.18.0.0/16
            ```
    
    3. **Gateway**

        * Puerta de salida de la red

        * Ejemplo:

            ```bash
            172.18.0.1
            ```
    4. **Interfaces virtuales**

        * `veth`
        * Conectan contenedor <-> bridge

    5. **DNS interno**

        * Resolución por nombre del contenedor

        * Ejemplo:

            ```bash
            db -> 172.18.0.2
            ```
    6. **Reglas iptables**

        * Control tráfico
        * NAT
        * Port forwarding

    ## Tipos de redes Docker

    1. **Bridge (Por defecto)**

        * Red privada virtual local

        * Características

            * Contenedores se ven entre si
            * Acceso al exterior
            * NAT con el host

        * **Ejemplo**

        ### Paso 1: Crear red bridge

        ```bash
        docker network create app-network
        ```

        ### Paso 2: Listar redes

        ```bash
        docker network ls
        ```

        ### Paso 3: Ejecutar contenedor DB

        ```bash
        docker run -d \
            --name db \
            --network app-network \
            -e POSTGRES_PASSWORD=secret \
            postgres
        ```

        ### Paso 4: Ejecutar backend

        ```bash
        docker run -d \
            --name backend \
            --network app-network \
            backend-app
        ```    

        ### Paso 5: Comunicación por nombre

        ```bash
        psql -h db -U postgres
        ```
        * `db` es resuelto por DNS interno

        ### Paso 6: Exponer serviciso al host

        ```bash
        docker run -d \
            -p 8080:80 \
            --network app-network \
            nginx
        ```
        * `--network`

            * Asigna red al contenedor
            * Evita usar red default
        
        * `-p`
            
            * Redirecciona trafico
            * `host:container`

    2. **Red host**

        ```bash
        docker run --network host nginx
        ```
        * Usa red del host
        * SIn aislamiento

    3. **Red none**

        ```bash
        docker run --network none alpine
        ```
        * Contenedor aislado

    ## Inspeccionar red

    ```bash
    docker network inspect app-network
    ```
    * Muestra:

        * IPs
        * Contenedores conectados
        * Subnet
        * Driver

    ## Conectar contendores a otra red

    ```bash
    docker network connect otra-red backend
    ```

    ## Eliminar red

    ```bash
    docker network rm app-network
    ```