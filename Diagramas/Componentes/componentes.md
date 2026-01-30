# Componenetes

* Un **diagrama de componentes** es un **diagrama UML estructural** que muestra:

    > *Los componentes fisicos o lógicos del sistema y cómo se relacionan entre sí*

* En palabras simples:

    * Muestra **módulos reales del software**
    * Indica **quién depende de quién**
    * Representa **artefactos desplegables**

* No habla de usuarios ni de flujos -> habla de **arquitectura**

    ## Para que sirve ?

    1. **Entender la arquitectura del sistema**

        * Qué módulos existen
        * Cómo están conectados
        * Qué responsabilidades tiene cada uno

    2. **Diseñar sistemas escalables**

        * Facilita dividir en **microservicios**
        * Permite detectar acoplamientos fuertes

    3. **Documentar el sistema**

        * Ideal para:

            * Documentación técnica
            * Onboarding de nuevos devs
            * Auditorías y revisiones de arquitectura

    4. **Apoyar desiciones técnicas**

        * ¿Dónde poner seguridad?
        * ¿Dónde cachear?
        * ¿Qué puedo escalar de forma independiente?

    ## Funcionamiento

    1. **Componentes** (bloques del sistema)
    2. **Interfaces** que exponen o consumen
    3. **Dependencias** entre componentes
    4. **Relaciones de comunicación**

    * No muestra lógica interna
    * No muestra algoritmos
    * Muestra **cómo se conecta las piezas**

    ## Elementos que lo componen

    1. **Componente:** Representa un modulo independiente del sistema.

        * Ejemplos:

            * API REST
            * Servicio de autenticación
            * Base de datos
            * Frontend web

        * ***Notación UML**

            ```bash
            +---------------------+
            |   Componente        |
            |   (nombre)           |
            +---------------------+
            ```
    
    2. **Interface:** Define **qué ofrece** o **que necesita** un componente

        * **Tipos**

            * **Interface provista** -> lo que el componente expone
            * **Interface requerida** -> lo que el componente consume

        * **Ejemplo**

            * API expone endpoints
            * Servicio consume base de datos

    3. **Dependencia**

        * Indica que un componente **usa a otro**

            * Ejemplo:

                * Backend -> Base de datos
                * Frontend -> Backend

        * **Notación**

            ```bash
            [Frontend] ---> [Backend]
            ```

    4. **Conectores:** Representa **comunicación directa** entre componentes:

        * HTTP
        * gRPC
        * Mensajeria (RabbitMQ)
        * Eventos

    5. **Paquetes (Opcional):** Sirven para **agrupar componentes** por dominio o capa

        * Ejemplo:

            * Capa de presentación
            * Capa de negocio
            * Capa de infraestructura

    
    ## Diferencia con otros diagramas

    | Diagrama     | Responde a             | Ejemplo           |
    | ------------ | ---------------------- | ----------------- |
    | Casos de Uso | ¿Qué hace el sistema?  | Crear proyecto    |
    | Clases       | ¿Cómo está modelado?   | Proyecto, Usuario |
    | Componentes  | ¿Cómo está construido? | API, DB, Auth     |
    | Despliegue   | ¿Dónde corre?          | Pod, Nodo, VM     |
