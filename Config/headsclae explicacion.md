# Headscale Explicación

* **Escenario rela que vamos a explicar**

    * Tienes **2 servidores Ubuntu**

        ```bash
        Servidor A
        - Ejecuta Headscale
        - También será nodo Kubernetes

        Servidor B
        - Nodo Kubernetes
        ```
    * **Ambos:**

        * IP real dinámica (eth0)
        * Política sin IPs estaticas
        * Necesitan verse **siempre** con una IP estable

    ## Parte 7: Instalación del cliente (En todos los servidores)

    * **Regla clave (aqui estaba la confusión)**

        > Headscale server No crea la IP virtual por si solo.
        > La IP virtual la crea el cliente `tailscaled`

    * Por eso:

        * **Servidor A (Headscale) ->** Tambien instala `tailscale`
        * **Servidor B ->** instala `tailscale`

        ### 7.1 instalar cliente tailscale (ambos servidores)

        * **En Servidor A (Headscale)**
        * **En Servidor B (Worker)**

            ```bash
            curl -fsSL https://tailscale.com/install.sh | sh
            ```
        * **Que instala esto ?**

            * `tailscaled` -> daemon (servicio)
            * WireGuard kernel module
            * Herramienta `tailscale`
            
        * **Aun No hay IP virtual,** solo el software

    ## Parte 8: Unir Cada Servidor a Headscale

    * Aqui es donde **cada máquina obtiene su IP estable**

        ### 8.1 Unir el Servidor A (Headscale)

        ```bash
        sudo tailscale up \
            --login-server http://127.0.0.1:8080 \
            --authkey tskey-XXXXX \
            --hostname k8s-master
        ```

        * **Explicación de flags**

            | Flag             | Qué hace                     |
            | ---------------- | ---------------------------- |
            | `--login-server` | Dónde está Headscale         |
            | `127.0.0.1`      | Porque Headscale corre local |
            | `--authkey`      | Clave de registro            |
            | `--hostname`     | Nombre lógico del nodo       |
        
        * Aunque sea el mismo host, **el tráfico sigue siendo WireGuard**

        #### 8.2 Unir el Servidor B (Worker)

        ```bash
        sudo tailscale up \
            --login-server http://IP_SERVIDOR_A:8080 \
            --authkey tskey-XXXXX \
            --hostname k8s-worker-1
        ```

        * Aqui:

            * `IP_SERVIDOR_A` = IP real **actual** (dinamica)
            * Si cambia **-> No pasa nada**
            * El cliente se vuelve a registrar
        
        ### 8.3 Ver IP virtual en Cada Servidor

        * En cada nodo:

            ```bash
            tailscale ip -4
            ```
        * Ejemplo:

            ```nginx
            Servidor A -> 10.100.0.1
            Servidor B -> 10.100.0.2
            ```
            * **Estas IPs No cambian jamás**
        
        ### 8.4 Ver nodos desde headscale

        * En el servidor Headscale:

            ```bash
            headscale nodes list
            ```
        * Salida tipica:

            ```nginx
            ID  NAME            IP
            1   k8s-master      10.100.0.1
            2   k8s-worker-1    10.100.0.2
            ```
        * Ahora ya tienes **red privada estable**

    ## Parte 9: Integración con MicroK8s (Los dos nodos)

    * Ahora viene la parte **más importante para Kubernetes**

    * **Qué problema resolvemos aqui**

        * Por defecto, Micrk8s:

            * Usa la IP de `eth0`
            * Que en tu caso **cambia**
            * Cluster se rompe
        * Vamos a **forzar** que use la IP de Tailscale (`tailscale0`)

        ### 9.1 Forzar IP en Servidor A

        ```bash
        sudo snap set microk8s node-ip=10.100.0.1
        ```

        ### 9.2 Forzar IP en Servidor B

        ```bash
        sudo snap set microk8s node-ip=10.100.0.2
        ```

        ### 9.3 Reiniciar Microk8s (en ambos)

        ```bash
        sudo microk8s stop
        sudo microk8s start
        ```

        ### 9.4 Unir el cluster Microk8s (en ambos)

        * En Servidor A:

            ```bash
            microk8s add-node
            ```
        * Obtendras algo como:

            ```bash
            microk8s join 10.100.0.1:250000/abcdef
            ```
        * En servidor B:

            ```bash
            microk8s join 10.100.0.1:25000/abcdef
            ```
            * **Nunca uses la IP real**
        
        ### 9.5 Verificación final

        ```bash
        microk8s kubectl get nodes -o wide
        ```

        * Debe mostrar:

            ```nginx
            NAME           INTERNAL-IP
            k8s-master     10.100.0.1
            k8s-worker-1   10.100.0.2
            ```