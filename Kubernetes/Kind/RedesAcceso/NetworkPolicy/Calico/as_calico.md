# AS Calico

* **Pregunta**

    > Osea que toda el cluster de kubernetes esta en el mismo sistema autonomo, si es asi quien lo gestion, cual sistema autonomo es, como funciona , caracteriticas, componenetes.

    ## Respuesta corta 

    * Si, cuando Calico usa BGP, todo el clúster de Kubernetes normalmente se comporta como un solo Sistema Autonomo (AS)
    * Ese AS No es un AS de internet
    * Es un AS privado, lógico, interno

    ## Ese AS es un *Sistema Autónomo real* ?

    * **En redes clasicas**

        * Un **AS real:**

            * Está registrado en IANA / RIR
            * Tiene un ASN publico
            * Publica rutas a internet

    * **En Kubernetes con Calico:**

        * **No**

            * No se registra
            * No publica rutas globales
            * No participa en internet routing

        * Es un **AS lógico / privado**, solo para control interno de rutas

    ## Que AS usa Calico ?

    * Por defecto, Calico usa:

        ```bash
        ASN: 64512
        ```

        * Este pertenece al rango

            ```bash
            64512 - 65534 -> ASN privados
            ```
    * Equivalente a:

        * 10.0.0.0/8
        * 192.168.0.0/16

    ## Quíen gestiona ese AS ?

    * **Calico**

        * Define el ASN
        * Levanta sesiones BGP
        * Anuncia prefijos
        * Mantiene convergencia

    ## Que representa ese AS en Kubernetes ?

    * Mentalmente, piensa asi:

        > **El clúster Kubernetes es un dominio de ruteo controlado por Calico.**

    * Ese dominio:

        * Tiene un ASN
        * Tiene nodos que anuncian rutas
        * Tiene politicas simples
        * Es cerrado

    ## Componentes que forma el *AS* en Calico

    1. **Nodos = Routers**

        * Cada nodo Kubernetes:

            * Tiene una IP de nodo
            * Ejecuta `calico-node`
            * Anuncia rutas de Pods
            
        * Funciona como un **router edge interno**

    2. **calico-node (BGP speaker)**

        * Dentro de `calico-node` vive:

            * **BIRD** (deamon BGP)
            * O un BGP engine propio

        * Funciones:

            * Establecer sesiones BGP
            * Anunciar rutas
            * Retirar rutas

    3. **ASN (Autonomous System Number)**

        * Configurado en:

            ```yaml
            apiVersion: projectcalico.org/v3
            kind: BGPConfiguration
            spec:
                asNumber: 64512
            ```   
    4. **IP Pools (Prefijos anunciados)**

        * Ejemplo:

            ```bash
            10.244.0.0/16
            ```

        * Se subdivide por nodo:

            ```bash
            Node A -> 10.244.1.0/24
            Node B -> 10.244.2.0/24
            ```
            * Cada nodo anunica **su prefijo**

    5. **BGP Sessions (iBGP)**

        * Node <-> Node
        * Misma ASN
        * Sin politicas complejas
        * No hay:

            * MED
            * Local-pref
            * AS-PATH manipulation

    ## Funcionamiento

    * **Escenario: Pod A -> Pod B**

        1. Pod A envia trafico a `10.244.2.5`
        2. Node A consulta su tabla de rutas
        3. Ve: `10.244.2.0/24 via 192.168.1.11`
        4. Envia tráfico directo al Node B
        5. Node B lo entrega al Pod
        * Sin encapsulación
        * Sin NAT
        * Sin overlays

    ## Hay Multiples AS en Kubernetes ?

    * **Por defecto**

        * No
        * Todo el cluster = 1 AS

    * **Casos avanzados**

        * Multi-cluster
        * Peering con routers fisicos
        * MetalLB en modo BGP

    * Ahi puedes tener:

        ```bash
        AS 64512 (K8s)
        ↕
        AS 65001 (Router DC)
        ```

    ## Que pasa si No usas BGP ?

    * Entonces Calico:

        * Usa VXLAN / IPIP
        * No hay AS
        * No hay ruteo real
        * Todo es overlay

    ## Características

    | Característica | Valor           |
    | -------------- | --------------- |
    | Tipo           | Privado         |
    | Alcance        | Interno         |
    | Protocolo      | iBGP            |
    | ASN            | 64512 (default) |
    | Políticas      | Simples         |
    | Convergencia   | Rápida          |
    | Escala         | Alta            |


    ## Diferencia clave con redes clásicas

    > En redes tradicionales, el **AS** representa una organización. En `Calico`, el **AS** representa un dominio lógico de ruteo de *Pods*

