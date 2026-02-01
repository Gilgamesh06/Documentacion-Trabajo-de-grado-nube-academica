# DQL: Data Query Languange

* **DQL (Data Query Language)** es el **sub-lenguage de SQL** encargado **exclusivamente de consultar datos** en una base de datos relacional

* DQL:

    * No modifica datos
    * No cambia estructuras
    * Solo lee información

* Su objetivo es **obtener datso de forma precisa y eficiente**

    ## Para que sirve DQL ?

    * Consultar datos almacenados
    * Filtrar información
    * Ordenar resultados
    * Agrupar datos
    * Realizar cálculos
    * Combinar información de varias tablas

    * Toda pantalla, reporte o API que muestre datos usa DQL

    ## Funcionamiento

    * DQL funciona a través de comando `SELECT`, que el motor de la base de datos interpreta y ejecuta siguiendo un **orden lógico**

    * **Orden lógico de ejecución**

        * Anque escribimos el `SELECT` asi:

            ```sql
            SELECT columna
            FROM tabla
            WHERE condicion
            GROUP BY columna
            HAVING condicion
            ORDER BY columna
            LIMIT 10;
            ```
        * **El motor lo ejecuta en este orden lógico**

            1. `FROM`
            2. `JOIN`
            3. `WHERE`
            4. `GROUP BY`
            5. `HAVING`
            6. `SELECT`
            7. `ORDER BY`
            8. `LIMIT / OFFSET`

            * Esto explica muchos "errores raros" al escribir consultas

    ## Elementos que componen DQL

    * Aunque solo tienes un comando principal **DQL está compuesto por multiples cláusulas**

        ### `SELECT`

        * Indica **que columnas** quieres obtener

            ```sql
            SELECT nombre, email
            FROM persona;
            ```
            * Puede usar:

                * `*` (Todas)
                * Alias `AS`
                * Funciones
            
        ### `FROM`

        * Indica **de dónde viene los datos**

            ```sql
            FROM persona
            ```
            * Puede incluir:

                * Subconsultas
                * Alias de tablas

        ### `WHERE`
        
        * Filtra filas **antes de agrupar**

            ```sql
            WHERE edad > 18
            ```
            * Usa operadores:

                * `=`, `<>`, `>`, `<`
                * `AND`, `OR`, `NOT`
                * `IN`,  `BETWEEN`, `LIKE`
                * `IS NULL`

        ### `JOIN`

        * Permite **combinar tablas relacionadas**

        * **Tipos principales**

            * **INNER JOIN**

                * Solo coincidencias

                    ```sql
                    SELECT p.nombre, pe.total
                    FROM persona p
                    INNER JOIN pedido pe ON p.id = pe.persona_id;
                    ```
            * **LEFT JOIN**

                * Incluye todos los de la izquierda

            * **RIGHT JOIN**

                * Incluye todos los de la derecha

            * **FULL JOIN**

                * Incluye todos

        ### `GROUP BY`

        * Agrupa filas para funciones agregadas

            ```sql
            GROUP BY categoria
            ```
        
        * Funciones comunes:

            * `COUNT`
            * `SUM`
            * `AVG`
            * `MIN`
            * `MAX`

        ### `HAVING`

        * Filtra **grupos**, no filas

            ```sql
            HAVING SUM(total) > 1000
            ```
            * `WHERE` != `HAVING`

        ### `ORDER BY`

        * Ordena resultados

            ```sql
            ORDER BY fecha DESC
            ```
            *  `ASC`/`DESC`

        ### `LIMIT/OFFSET`

        * Limita la cantidad de resultados

            ```sql
            LIMIT 10 OFFSET 20
            ```
            * Muy usado en **paginación**

        ### `Subconsultas`

        * Consultas dentro de consultas

            ```sql
            SELECT nombre
            FROM persona
            WHERE id IN (
                SELECT persona_id
                FROM pedido
            );
            ```

        ### Funcionaes y expresiones

        * DQL permite:

            * Funciones agregadas
            * Funciones escalares
            * Expresiones matemáticas

        * Ejemplo:

            ```sql
            SELECT nombre, UPPER(nombre)
            FROM persona;
            ```
    
    ## Relación de DQL con otros componentes SQL

    | Lenguaje | Función            |
    | -------- | ------------------ |
    | DDL      | Define estructura  |
    | DML      | Inserta / modifica |
    | **DQL**  | Consulta datos     |
    | DCL      | Permisos           |
    | TCL      | Transacciones      |

    ## DQL en el mundo real

    * API REST -> `GET /users`
    * Dashboard -> tablas y gráficos
    * ORM -> genera DQL como SQL
    * Reportes -> consultas complejas
    * Cada `findAll()` o `SELECT` detrás es DQL

    