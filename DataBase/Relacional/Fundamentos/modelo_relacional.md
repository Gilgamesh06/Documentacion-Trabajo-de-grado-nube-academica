# Modelo Relacional

* El **modelo relacional** es un **modelo matemático y lógica** propuesto por **Edgar F. Codd (1970)** para **organizar, estructurar y manipular datos** mediante **relaciones**

* En este modelo:

    * Los datos se representan como **relaciones**
    * Una relación se representa como una **tabla**
    * Las relaciones entre datos se definen mediante **claves**

* Es la **base teórica** de las bases de datos relacionales (PostgreSQL, MySQL, Oracle, etc)

    ## Para que sirve el Modelo Relacional ?

    * Diseñar bases de datos de forma correcta
    * Representar datos del mundo real de manera estructurada
    * Evitar redundancia e inconsistencias
    * Garantizar la integridad de los datos
    * Permitir consultas eficientes y formales

    * En pocas palabras:

        > El modelo relacional define **cómo deben organizarse los datos y qué reglas debe cumplir**

    ## Funcionamiento

    * Funciona a través de **estructuras, formales, reglas de integridad y operaciones matematicas**

    * **Flujo Conceptual**

        1. Los datos se modelan como **relaciones (tablas)**
        2. Cada relación tiene **atributos (columnas)**
        3. Cada fila representa una **tupla (registro)**
        4. Las relaciones se conectan mediante **claves**
        5. Se manipulan con **operaciones relacionales**

    ## Elementos que componen el Modelo Relacional

    1. **Relación (Relation)**

        * Es el **concepto central** del modelo

        * Una relación:

            * Es un **conjunto de tuplas**
            * No permite filas duplicadas
            * El orden de filas y columnas **no importa**
        
        * En la practica: **Una tabla**

    2. **Tupla (Tuple)**

        * Es una **fila** dentro de una relación

        * Representa una instancia concreta:

            > Una persona, un pedido, un producto.

        * Ejemplo:

            ```python
            (1, "Ana", "ana@mail.com")
            ```
    
    3. **Atributo (Attribute)**

        * Es una **columna** de la relación

        * Describe una propiedad:

            * nombre
            * precio
            * fecha
            * estado

    4. **Dominio (Domain)**

        * Es el **conjunto de valores válidos** para un atributo

        * Ejemplo:

            * `edad:` numeros enteros > 0
            * `email:` texto con formato válido

        * En SQL se refleja como tipos de datos: `INT`, `VARCHAR`, `DATE`, etc

    5. **Claves (Keys)**

        * Permiten **identificar y relacionar** datos.

        * **Clave primaria (Primary Key)**

            * Identifica una tupla de forma única
            * No se repite
            * No es NULL

        * Ejemplo

            ```bash
            id_persona
            ```

        * **Clave candidata**

            * Cualquier atributo que podria ser clave primaria

            * Ejemplo:

                * email
                * número de documento

        * **Clave alterna**

            * Clave candidata que **no fue elegida** como primaria

        * ***Clave foránea (Foreign Key)**

            * Atributo que referencia la clave primaria de otra relación

            * Ejemplo:

                ```bash
                PEDIDO.id_persona -> PERSONA.id_persona
                ```
    
    ## Restricciones de Integridad

    * Reglas que aseguran la **calidad y consistencia** de los datos

    * **Integridad de entidad**

        * Toda relación debe tener clave primaria
        * La clave primaria no puede ser NULL
        
    * **Integridad referencial**

        * Una clave foránea debe apuntar a una clave válida o ser NULL

    * **Integridad de dominio**

        * Los valores deben pertenecer al dominio definido

    ## Esquema Relacional

    * Es la **estructura de la bases de datos,** no los datos

    * Ejemplo:

        ```bash
        PERSONA(id_persona, nombre, email)
        PEDIDO(id_pedido, id_persona, total)
        ```
        * Es el *diseño* lógico del sistema

    ## Instacia rRelacional

    * Son los **datos actuales** almacenados en las relaciones

        > El esquema no cambia seguido, la instancia sí

    ## Operaciones del Modelo Relacional

    * Son operaciones matemáticas para manipular relaciones

        * **Selección** -> filtrar filas
        * **Proyección** -> Seleccionar columnas
        * **Unión** 
        * **Intersección**
        * **Diferencia**
        * **Producto cartesiano**
        * **Join**

    * SQL es una **implementación práctica** de estas operaciones

