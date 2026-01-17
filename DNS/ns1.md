# ns1

* **Pregunta:**

    * No entiendi bien que es

        ```bash
        IN NS ns1.app.lab.
        ns1 IN A 192.168.50.10
        app IN A 192.168.50.100
        api IN A 192.168.50.100
        frontend IN A 192.168.50.100
        gateway IN A 192.168.50.100 ns1.app.lab.
        ```
        > Es el servidor autoritarios entoces ns1 la ip que le asocias que es ? la ip del servidor ? la ip de la interfaz de red ?, y porque hay tantos nombres no basta con solo app o tiene un proposito cada uno

1. **Empecemos por la idea más importante**

    * DNS No apunta a servicios
    * DNS solo responde: nombre -> IP
    
    * Nada mas
        * Kubernetes, Ingress, rutas,etc. **No existen para DNS**

2. **Que significa esta linea ?**

    ```dns
    IN NS ns1.app.lab.
    ```

    * **Traducción literal**

        > Para la zona `app.lab`. el **servidor DNS autoritativo** se llama **ns1.app.lab**

    * **Ojo**

        * Esto **No es una IP**, es un **Nombre DNS**

3. **Entonces...¿Que es `ns1.app.lab`?**

    * El **nombre del servidor DNS**
    * No es Kubernetes
    * No es MetalLB
    * No es NGINX

    * Es simplemente **tu servidor Bind9**

4. **Y esta linea entonces?**

    ```bash
    ns1 IN A 192.168.50.10
    ```
    * ***Ahora si lo entendemos**

        > El nombre `ns1.app.lab` apunta a la IP `192.168.50.10`

    * **Esa IP es la IP del Servidor DNS**

        | Pregunta                   | Respuesta                       |
        | -------------------------- | ------------------------------- |
        | ¿Es la IP del servidor?    | Si                              |
        | ¿Es la IP de una interfaz? | Si                              |
        | ¿Cuál interfaz?            | La que **recibe consultas DNS** |
        | ¿Es la IP del Ingress?     | No                              |

    * Normalmente

        * IP fija
        * IP de la LAN
        * IP accesible desde los clientes

5. **Entonces, el flujo real es este:**

    * **Cliente pregunta:** Quine es `app.lab` ?

    * **DNS responde:**
    
        * Yo soy el DNS autoritativo

            ```dns
            IN NS ns1.app.lab.
            ```
        
        * Mi nombre `ns1.app.lab` esta en esta IP

            ```dns
            ns1.app.lab -> 192.168.50.10
            ```
        
        * `app.lab` apunta a esta IP

            ```dns
            app.lab -> 192.168.50.100
            ```

6. **Ahora vamos a los registros A**

    ```bash
    app        IN A 192.168.50.100
    api        IN A 192.168.50.100
    frontend   IN A 192.168.50.100
    gateway    IN A 192.168.50.100
    ```
    * **Pregunta clave**

        > Porque tantos nombres si todos apuntan a la misma IP?

    * **Respuesta corta**

        * Porque el routing No lo hace DNS
        * Lo hace NGINX Ingress

7. **Que hace cada nombre (esto es clave)**

    Supongamos esta IP:

        ```bash
        192.168.50.100 = IP de MetalLB -> NGINX Ingress
        ```

    * **DNS solo hace esto:**

        | Nombre DNS       | IP             |
        | ---------------- | -------------- |
        | app.app.lab      | 192.168.50.100 |
        | api.app.lab      | 192.168.50.100 |
        | frontend.app.lab | 192.168.50.100 |
        | gateway.app.lab  | 192.168.50.100 |

    * **Todos entran por la misma puerta**

8. **Entonces para que tantos nombres?**

    * Porque **NGINX Ingress diferencia por HOST**

    * Ejemplo real:

        ```http
        Host: api.app.lab
        ```

        * **NGINX dice:**

            > Ah, esto es API -> servicio backend

        ```http
        Host: frontend.app.lab
        ```

        * **NGINX dice**

            > Esto es frontend -> servicio React

