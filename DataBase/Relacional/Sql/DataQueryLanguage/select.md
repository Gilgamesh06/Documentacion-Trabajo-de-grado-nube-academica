# SELECT

* **`SELECT`** es la instrucción principal de **DQL (DATA Query Language)** que se utiliza para **consultar datos** almacenados en una base de datos relacional

* `SELECT`

    * No modifica datos
    * Solo los lee
    * Devuelve un **conjunto de resultados**

    ## Para que sirve SELECT ?

    * Obtener información específica
    * Filtrar registros
    * Combinar datos de varias tablas
    * Agrupar y resumir datos
    * Ordenar resultados
    * Construir reportes y APIs

    * Cada:

        * `GET` en una API
        * Lista en una web
        * Reporta en un sistema

    * Termina siendo un `SELECT`

    ## Funcionamiento

    * `SELECT` funciona mediante **clausulas** que el motor de la base de datos **procesa en orden lógico**

    * **Orden lógico de ejecución (Clave)**

        ```sql
        SELECT ...
        FROM ...
        WHERE ...
        GROUP BY ...
        HAVING ...
        ORDER BY ...
        LIMIT ...
        ```
        * El motor ejecuta asi:

            1. `FROM`
            2. `JOIN`
            3. `WHERE`
            4. `GROUP BY`
            5. `HAVING`
            6. `SELECT`
            7. `ORDER BY`
            8. `LIMIT / OFFSET`
        
        * Esto explica porque:

            * No puede usar alias en `WHERE`
            * Si puede usarlo en `ORDER BY`

    ## Clausualas

    1. **FROM - Origen de los datos**

        * Indica **de dónde salen los datos**

            ```sql
            FROM persona
            ```
            * El motor carga la tabla en memoria (Conceptualmente)

    2. **SELECT - Columnas a devolver**

        * Indica **que columnas** quieres ver:

            ```sql
            SELECT nombre, email
            ```
        * Opciones

            * `*` (todas)
            * Alias (`AS`)
            * Funciones (`COUNT`, `SUM`, etc)

    3. **WHERE - Filtro de filas**

        * Filtra registros **antes de agrupar**

            ```sql
            WHERE edad >= 18
            ```
        
        * Operadores:

            * Comparación
            * Lógicos (`AND`, `OR`)
            * `IN`, `LIKE`, `BETWEEN`
            * `IS NULL`

    4. **JOIN - Unión de tablas**

        * Permite consultar **datos relacionados**

            ```sql
            INNER JOIN pedido pe ON p.id = pe.persona_id
            ```
        * Tipos:

            * `INNER`
            * `LEFT`
            * `RIGHT`
            * `FULL`

    5. **GROUP BY - Agrupación**

        * Agrupa filas para funciones agregadas

            ```sql
            GROUP BY categoria
            ```

        * Funciones comunes:

            * `COUNT`
            * `SUM`
            * `AVG`

    6. **HAVING - Filtro de grupos**

        * Filtra **después del GROUP BY**

            ```sql
            HAVING SUM(total) > 1000
            ```
    
    7. **ORDER BY - Ordenamiento**

        * Ordena resultados finales

            ```sql
            ORDER BY fecha DESC
            ```
    
    8. **LIMIT/OFFSET - Paginación**

        * Limita filas devueltaas

            ```sql
            LIMIT 10 OFFSET 0
            ```

    ## Ejemplo 

    * **Escenario**

        * Tenemos:

            * persona
            * pedido

        * Tablas:

            | id | nombre | ciudad   |
            | -- | ------ | -------- |
            | 1  | Ana    | Bogotá   |
            | 2  | Luis   | Medellín |
            | 3  | Marta  | Bogotá   |

            | id | persona_id | total |
            | -- | ---------- | ----- |
            | 10 | 1          | 200   |
            | 11 | 1          | 150   |
            | 12 | 2          | 300   |
                    
        * **Objetivo**

            * Obtener el **nombre** de las personas de **Bogota** y el **total gastado**, solo si gastaron mas de **300** ordenado de mayor a menor

        * **Consulta**

            ```sql
            SELECT p.nombre, SUM(pe.total) AS tota_gastado 
            FROM persona p
            INNER JOIN pedido pe ON p.id = pe.persona_id
            WHERE p.ciudad = 'Bogota'
            GROUP BY p.nombre
            HAVING SUM(pe.total) > 300
            ORDER BY total_gastado DESC;
            ```
        