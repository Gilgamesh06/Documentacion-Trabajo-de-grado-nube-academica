# Headscale Instalación

* **Componentes que vamos a instalar**

    | Componente     | Dónde      | Para qué                       |
    | -------------- | ---------- | ------------------------------ |
    | **Headscale**  | 1 servidor | Control plane (coordina nodos) |
    | **tailscaled** | Cada nodo  | Cliente WireGuard              |
    | **WireGuard**  | Implícito  | Cifrado P2P                    |
    | **IP virtual** | Automática | Identidad estable              |

*  Headscale **No transporta tráfico**, solo coordina

    ## Arquitectura fina

    ```nginx

         Headscale
        (10.0.0.10)
            |
    --------------------------------
    |              |               |
    node1        node2           node3
    10.100.0.1   10.100.0.2      10.100.0.3
    ```
    * Las IPs `10.100.0.x`:
        
        * Son virtuales
        * No cambian
        * Las usará Kubernetes 
    
    ## Paso 1: Instalar Headscale (servidor)

    1. **Descargar el binario**

        ```bash
        curl -fsSL https://github.com/juanfont/headscale/releases/latest/download/headscale_linux_amd64 -o headscale
        ```   
        * **Que hace este comando ?**

            * `curl` -> descarga archivos
            * `-f` -> falla si hay error HTTP
            * `-s` -> modo silencioso
            * `-S` -> muestra error si falla
            * `-L` -> sigue redirecciones
            * `-o headscale` -> guarda el archivo con ese nombre

    2. **Instalar el binario**

        ```bash
        sudo install -m 755 headscale /usr/local/bin/headscale
        ```   
        * **Explicación**
            
            * `install` -> copia + asigna permisos
            * `-m 755`

                * **propietario:** lectura/escritura/ejecución
                * **grupos/otros:** lectura/ejecución

            * `/usr/local/bin` -> ruta estándar para binarios
        
        * **Verificación**

            ```bash
            headscale version
            ```
    
    ## Paso 2: Crear estructura de configuración

    ```bash
    sudo mkdir -p /etc/headscale
    sudo mkdir -p /var/lib/headscale
    ``` 
    * **Porque estas rutas ?**

        | Ruta                 | Uso                |
        | -------------------- | ------------------ |
        | `/etc/headscale`     | Configuración      |
        | `/var/lib/headscale` | Estado (DB, nodos) |

    * **Crear archivo de configuración**

    ```bash
    sudo nano /etc/headscale/config.yaml
    ```

    * **Configuración mínima recomendada**

        ```yaml
        server_url: http://10.0.0.10:8000

        listen_addr: 0.0.0.0:8080

        noise:
            private_key_path: /var/lib/headscale/noise_private.key
        
        ip_prefixes:
            - 10.100.0.0/24
        
        derp:
            server:
                enabled: false
        
        db_type: sqlite3
        db_path: /var/lib/headscale/db.sqlite

        log:
            level: info
        ```
    
    * **Explicación de cada campo**

        * **`server_url`**

            * Dirección que usarán los clientes
            * **Debe ser alcanzable**
            * Puede ser IP o DNS

            ```yaml
            server_url: http://10.0.0.10:8080
            ```
        
        * **`listen_addr`**

            * IP y puerto donde escucha Headscale

                ```yaml
                listen_addr: 0.0.0.0_8080
                ```
                * `0.0.0.0` **-> acepta conexiones en cualquier interfaz**

        * **`noise.private_key_path`**

            * Clave para cifrado del control plane
            * Se genera automáticamente

        * **`ip_prefixes`**

            * **Rango de IPs virtuales**
            * Cada nodo recibe una IP fija de aqui

            ```yaml
            ip_prefixes:
                - 10.100.0.0/24
            ```
        
        * **`derp`**

            * Relay de tráfico
            * Para Kubernetes **no lo necesitas**

            ```yaml
            enabled: false
            ```

        * **`db_type` y `db_path`**

            * Base de datos interna
            * Guarda nodos, claves, ACLs
        
    ## Paso 3: Inicializar Headscale

    ```bash
    sudo headscale serve
    ```
    * Esto es solo para probar

    * Si ves:

        ```nginx
        Listening on 0.0.0.0:8080
        ```
        * Funciona. Detenlo (`ctrl+C`)

    ## Paso 4: Crear servicio systemd

    ```bash
    sudo nano /etc/systemd/system/headscale.service
    ```

    * Contenido

        ```ini
        [Unit]
        Description=Headscale Control Plane
        After=network.target

        [Service]
        ExecStart=/usr/local/bin/headscale serve
        Restart=always
        User=root

        [Install]
        WantedBy=multi-user.target
        ```
    
    * Activar:

        ```bash
        sudo systemctl daemon-reexec
        sudo systemctl daemon-reload
        sudo systemctl enable --now headscale
        ```
    * Verificar:

        ```bash
        systemctl status headscale
        ```
    
    ## Paso 5: Crear un namespace (organización)

    ```bash
    sudo headscale namespace create k8s
    ```
    * **Que es namespace ?**

        * Agrupa nodos
        * Controla visibilidad
        * Similar a "organizacion"

    * Listar:

        ```bash
        sudo headscale namespace list
        ```        

    ## Paso 6: Generar clave de registro

    ```bash
    sudo headscale preauthkeys create \
        --namespace k8s \
        --reusable \
        --expiration 24h
    ```

    * **Explicación de flags**

        | Flag           | Qué hace                           |
        | -------------- | ---------------------------------- |
        | `--namespace`  | Dónde registrar nodos              |
        | `--reusable`   | Permite usar la clave varias veces |
        | `--expiration` | Tiempo válido                      |

    * Salida:

        ```bash
        tskey-xxxx
        ```
    ## Paso 7: Instalar cliente en los nodos

    * En **cada nodo Kubernetes**

        ```bash
        curl -fsSL https://tailscale.com/install.sh | sh
        ```
    * Esto instala:

        * `taislcaled`
        * WireGuard
        * Interfaz `tailscale0`
    
    ## Registrar el nodo

    ```bash
    sudo tailscale up \
        --login-server http://10.0.0.10:8080 \
        --authkey tskey-xxxxx \
        --hostname node1
    ```

    * **Flags explicados**

        | Flag             | Uso                    |
        | ---------------- | ---------------------- |
        | `--login-server` | Dirección de Headscale |
        | `--authkey`      | Clave de registro      |
        | `--hostname`     | Nombre lógico          |

    * **Ver IP asignada**

        ```bash
        tailscale ip -4
        ```
    * Ejemplo:

        ```bash
        10.100.0.2
        ```
        * **Esta IP No cambia nunca**

    ## Paso 8: Verificar desde Headscale

    ```bash
    sudo headscale node list
    ```

    * Veras:

        ```nginx
        ID | NAME  | IP
        1  | node1 | 10.100.0.2
        ```
    
    ## Paso 9: Integración con Microk8s (clave)

    * En cada nodo:

        ```bash
        sudo snap set microk8s node-ip=10.100.0.2
        ```
    * Reiniciar MicroK8s

        ```bash
        sudo microk8s stop
        sudo microk8s start
        ```
    
    ## Prueba final

    ```bash
    microk8s kubectl get nodos -o wide
    ```

    * Debe mostar:

        ```bash
        INTERNAL-IP: 10.100.0.x
        ```
        