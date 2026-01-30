# SQL 

* **SQL (Structured Query Language)** es un **lenguaje estandar** utilizado para **definir, consultar, manipular y controlar** datos alamcenados en **base de datos relacionales**

    * SQL **no es un motor**, es un lenguaje
    * Se basa en el **modelo relacional**
    * Es declarativo: dices que quieres, no como hacerlo

    ## Para que sirve SQL ?

    * Crear estructuras de datos (Tablas, relaciones)
    * Consultar informacion
    * Insertar, actualizar y eliminar datos
    * Definir reglas e integridad
    * Controlar accesos y permisos
    * Manejar transacciones

    * **Sin SQL:**

        * No puedes hablar con la base de datos
        * No puedes explorar el modelo relacional

    ## Funcionamiento

    * SQL funciona mediante **instrucciones (sentencias)** que el **motor de la base de datos** interpreta y ejecuta

    * **Flujo simplificado**

        1. El usuario envia una sentencia SQL
        2. El DBMS la analiza (parser)
        3. El optimizador genera un plan
        4. El motor ejecuta la operación
        5. Se devuelve resultados

    * Ejemplo:

        ```sql
        SELECT nombre FROM persona WHERE id = 1;
        ```

        * El motor decide:

            * Que índices usar
            * En qué orden acceder a las tablas
            * Cómo optimizar la consulta

    ## Elementos que componen SQL

    * SQL se divide en **sub-lenguajes**, cada uno con una responsabilidad clara

        ### DDL - Data Definition Language

        * Define la **estructura** de la base de datos

        * **Comandos principales**

            * `CREATE`
            * `ALTER`
            * `DROP`
            * `TRUNCATE`

        * **Ejemplo**

            ```sql
            CREATE TABLE persona (
                id SERIAL PRIMARY KEY,
                nombre VARCHAR(100)
            );
            ```
            * Define **que existe** en la base de datos

        ### DML - Data Manipulation Language

        * Manipula los **datos** dentro de las tablas

        * **Comandos principales**

            * `INSERT`
            * `UPDATE`
            * `DELETE`
            * `SELECT` ( a veces se separa)

        * **Ejemplo:**

            ```sql
            INSERT INTO persona (nombre) VALUES ('Ana');
            ```
            * Cambia **el contenido** de la base de datos

        ### DQL - Data Query Language

        * Se centra exclusivamente en **consultar datos**

        * **Comando Principal**

            * `SELECT`

        * **Ejemplo**

            ```sql
            SELECT nombre FROM persona WHERE nombre LIKE 'A%';
            ```
            * No modifica datos, solo los lee

        ### DCL - Data Control Language

        * Controla **seguridad y permisos**

        * **Comandos Principales**

            * `GRANT`
            * `REVOKE`

        * **Ejemplo**

            ```sql
            CREATE SELECT ON persona TO usuario_app;
            ```
            * Define **quien puede hacer que**

        ### TCL - Transaction Control Language

        * Maneja **transacciones (ACID)**

        * **Comandos Principales**

            * `BEGIN`/ `START TRANSACTION`
            * `COMMIT`
            * `ROLLBACK`
            * `SAVEPOINT`

        * **Ejemplo**

            ```sql
            BEGIN;
            UPDATE stock SET cantidad = cantidad - 1;
            COMMIT;
            ```
            * Grantiza **seguridad y consistencia**

    ## Componentes internos que interactúan con SQL

    * Aunque no son parte del leguaje, son clave para enteneder como SQL funciona:

        * **Parser SQL**

            * Analiza sintaxis
            * Detecta errores
        
        * **Optimizador de consultas**

            * Decide el mejor plan de ejecución
            * Usa estadísticas e índices

        * **Motor de ejecución**

            * Accede a disco/memoria
            * Aplica locks o MVCC

        * **Catalogo del sistema**

            * Metadatos (tablas, columnas, indices)

    ## Relación de SQL con DBR y ACID

    * **SQL + Modelo Relacional**

        * SQL implementa **álgebra relacional**
        * Usa tablas, joins, proyecciones

    * **SQL + ACID**

        * SQL controla transacciones
        * Asegura integridad y consistencia

    * **SQL + Normalización**

        * SQL implementa claves y restricciones
        * Evita anomalias
        