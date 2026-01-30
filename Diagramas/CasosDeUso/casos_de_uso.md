# Casos De Uso

* Un **diagrama de casos de uso**, es una **representación gráfica** que muestra:

    * **Quienes interactúan con el sistema** (Actores)
    * **Qué funcionalidades ofrece el sistema** (Casos de uso)
    * **Cómo se relacionan los actores con esas funcionalidades**

    * Responde principalemente a la pregunta:

        > **Qué puede hacer el sistema y quien lo usa?**

    * No muestra:

        * Algoritmos
        * Código
        * Clases
        * Bases de datos
        
    ## Para que sirve ?

    1. Entender los requisistos del sistema
    2. Comunicar ideas con usuarios no ténicos
    3. Delimitar el alcance del sistema
    4. Identificar funcionalidades faltantes
    5. Servir como base para otros diagramas

        * Diagramas de clases
        * Diagramas de secuencia
        * Historias de usuario

    * Es uno de los **primeros diagramas que se hacen** en un proyecto.

    ## Funcionamiento

    * Funciona bajo estos principios:

        1. El sistema se representa como una **caja**
        2. Los usuarios externos (actores) estan **fuera del sistema**
        3. Cada funcionalidad del sistema es un **caso de uso**
        4. Las líneas indican **interacciones**, no flujos de datos
        5. Se modela **el comportamiento observable**, no la lógica interna

    ## Características principales

    * Enfoque **funcional**, no técnico
    * Lenguaje **simple y entendible**
    * Independiente de la tecnología
    * Centrado en el **usuario**
    * No describe orden de ejecucion
    * No muestra validaciones internas

    ## Elementos que lo componen

    1. **Actor**

        * Representa **quíen usa el sistema**
        * Puede ser:

            * Persona
            * Sistema externo
            * Dispositivo
        * No es un rol interno del sistema, **siempre es externo**

        * **Ejemplos:**
            
            * Usuario
            * Administrador
            * Sistema de pagos

        * **Símbolo:** muñeco de palo

    2. **Casos de uso**

        * Representa una **funcionalidad del sistema**
        * Debe empezar con un **verbo en infinitivo**
        * **Ejemplos:**

            * Iniciar sesión
            * Registrar pedido
            * Consultar saldo

        * **Simbolo:** óvalo

    3. **Sistema (límite del sistema)**

        * Es el **rectángulo** que encierra los caos de uso
        * Define **qué pertenece al sistema y qué no**

    4. **Asiciación**

        * Linea que conecta un actor con un caso de uso
        * Indica que el actor **participa** en ese caso de uso

    5. **Relaciones entre casos de uso**

        1. `<<include>>`

            * Un caso de uso **siempre incluye a otro**
            * Se usa para **reutilzar comportamiento común**
            * **Ejemplo:**

                > "Realizar compra" incluye "Validar pago"

        2. `<<extend>>`

            * Un caso de uso **extiende a otro opcionalmente**
            * Se ejecuta solo bajo ciertas condiciones
            * **Ejemplo**

                > "Aplicar descuento" extiende "Realizar compra"

        3. **Generalización**

            * Un actor o caos  de uso hereda de otro
            * Similar a herencia de OOP
            * Ejemplo:

                > Administrador hereda de Usuario

    ## Ejemplo

    * **Paso 1: Identificar el sistema**

        * **Sistema:** Tienda en linea

    * **Paso 2: Identificar actores**

        * Cliente
        * Administrador
        * Pasarela de pago (sistema externo)

    * **Paso 3: Identificar casos de uso**

        * **Cliente puede**

            * Registrase 
            * Iniciar sesión
            * Buscar productos
            * Agregar producto al carrito
            * Realizar compra

        * **Administrador puede**

            * Gestionar productos
            * Ver ventas

    * **Paso 4: Identificar relaciones: `include` y `extend`**

        * **Realizar compra** `<<include>>` **Validar Pago**
        * **Aplicar cupón** `<<extend>>` **Realizar compra**

    * **Paso 5: Construcción del diagrama (Conceptual)**

        ```bash
                Cliente
                    |
                    |
            -------------------------
            |     Tienda Online     |
            |                       |
            | (Registrarse)         |
            | (Iniciar sesión)      |
            | (Buscar productos)    |
            | (Agregar al carrito)  |
            | (Realizar compra) -----> (Validar pago)
            |        ^               <<include>>
            |        |
            |   (Aplicar cupón)
            |     <<extend>>
            -------------------------

                    |
                Pasarela de Pago
        ```
        1. **Cliente** es un actor externo
        2. **Tienda Online** es el sistema
        3. **Realizar compra** es el caos de uso principal
        4. **Validar pago** es obligatorio -> `<<include>>`
        5. **Aplicar cupón** es opcional -> `<<extend>>`
        6. **Pasarela de Pago** interactua solo con el pago

    ## Errores comunes

    * Usar nombres técnicos
    * Poner validaciones internas
    * Confundir actores con clases
    * Modelar flujo de pasos
    * Poner casos de uso fuera del sistema

    ## ¿Cuándo usarlo y cuándo no?

    * **Úsalo cuando:**

        * Estás levantando requisitos
        * Hablas con usuarios
        * Definís alcance funcional

    * **No lo uses para:**

        * Diseñar base de datos
        * Modelar lógica interna
        * Optimización técnica
