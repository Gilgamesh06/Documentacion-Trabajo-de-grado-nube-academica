# Subdominios

* **Pregunta**

    > Pero entonces esto seria si quiero que todos los componente de mi app esten afuera porque normalmente aunque tenga host para backend y frontend aplicaria NetworkPolicy para limitar el acceso al backend solo permitiria acceso al frontend. entonces en este caso no necesitaria esos subdominios solo el frontend.

1. **Tu afirmación es Correcta**

    > Si aplico NetworkPolicy y solo permito acceso al backend desde el frontend, entonces no necesito exponer backend ni gateway por DNS público

    * Correcto
    * Es lo más común en producción
    * Es la mejor práctica de seguridad

2. **Arquitectura típica de producción (la que describes)**

    | Componente            | ¿Expuesto afuera? |
    | --------------------- | ----------------- |
    | Frontend (React)      |  Si               |
    | Backend (Spring Boot) |  No               |
    | API Gateway           |  No (a veces sí)  |
    | PostgreSQL            |  No               |

3. **Entonces...¿Cómo queda DNS?**

    * **DNS público / externo / usuario**

        ```dns
        app.lab -> 192.168.50.100
        ```
    
    * Solo eso.

        > El usuario **solo conoce el fronted**

4. **¿Y cómo se comunican frontend -> Backend?**

    * **Opción realista (mas usada)**

        ```bash
        Browser → app.lab
                |
                v
            Frontend (React)
                |
                v
            /api/* (MISMO HOST)
                |
                v
            Ingress → Backend
        ```
        * El backend No tiene dominio propio

5. **Ingress en este escenario (Importante)**

    ```yaml
    spec:
      rules:
      - host: app.lab
        http:
          paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: frontend
                port:
                  number: 80

          - path: /api
            pathType: Prefix
            backend:
              service:
                name: backend
                port:
                  number: 8080
    ```
    * Un solo dominio
    * Rutas internas
    * Backend no accesible directamente

6. **Entonces para que sirve los subdominios ?**

    * Se usan cuando:

        * Microservicios expuestos directamente
        * API pública 
        * Clientes externos (mobile, terceros)
        * API Gateway real
        * Múltiples app bajo un mismo cluster

    * Ejemplo:

        ```pgsql
        api.app.lab        → API pública
        auth.app.lab       → Auth service
        admin.app.lab      → Panel admin
        ```

7. **NetworkPolicy entra aqui (Tu punto clave)**

    * Tu dijiste algo Muy importante

        > Aplicaria NetworkPolicy para limitar el acceso al backend

    | Flujo                     | Permitido |
    | ------------------------- | --------- |
    | Frontend → Backend        | Si        |
    | Ingress → Backend         | Si        |
    | Usuario → Backend directo | No        |
    | Otro pod → Backend        | No        |
    
    * Eso **no depende de DNS**, depende de **Calico + NetworkPolicy**

8. **DNS vs NetworkPolicy (diferencia clave)** 

    | Capa          | Qué controla          |
    | ------------- | --------------------- |
    | DNS           | Nombre → IP           |
    | Ingress       | HTTP routing          |
    | NetworkPolicy | Quién habla con quién |
    | Service       | Balanceo interno      |

    * **DNS no es seguridad**
    * **NetworkPolicy si**

9. **Entonces tu conclusión final sería**

    * **Caso A: App monolitica moderna (React + API)**

        * 1 dominio
        * 1 Ingress
        * Rutas internas
        * NetworkPolicy estricta

        ```dns
        app.lab -> 192.168.50.100
        ```
    
    * **Caso B: Plataforma / microservicios reales**

        * Subdominios
        * API Gateway
        * Servicios públicos
        * DNS más rico

            ```dns
            frontend.app.lab → 192.168.50.100
            api.app.lab      → 192.168.50.100
            gateway.app.lab  → 192.168.50.100
            ``` 
10. **Para Tu proyecto especifico**

    > *APP que convierte docker-compose a manifest k8s*

    1. Exponer **solo frontend**
    2. Backend **solo interno**
    3. API accesible solo via `/api`
    4. NetworkPolicy estricta
    5. DNS minimo

    * **Eso demuestra madurez técnica**

