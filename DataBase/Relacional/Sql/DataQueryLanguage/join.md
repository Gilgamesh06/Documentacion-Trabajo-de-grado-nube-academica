# JOIN

* **JOIN** es una cláusula de SQL que se usa para **combinar filas de dos o más tablas** relacionadas entre sí, usando una **condicción de relación**, normalmente una **clave primaria (PK)** con una **clave foránea (FK)**

* En palabras simples:

    > ***JOIN** sirve para unir información que está separada en varias tablas*

    ## Para que sirve JOIN ?

    * Evitar duplicar datos (normalización)
    * Consultar información relacionada entre tablas
    * Reconstruir la información comleta del negocio
    * Aplicar correctamente el **modelo relacional**

    * Ejemplo:

        * Tabla `clientes` -> datos del cliente
        * Tabla `pedido` -> datos del pedido

            * JOIN permite ver **que cliente hizo que pedido**

    ## Funcionamiento

    1. Tienes **dos tablas**
    2. Existe una **relación lógica** entre ellas
    3. Usas una condición `ON` para indicar **cómo se conectan**
    4. SQL combina las filas según el tipo de `JOIN` usado

    * Sintaxis general:

        ```sql
        SELECT columnas
        FROM tabla1
        JOIN tabla2
        ON tabla1.columna = tabla2.columna;
        ```

    ## Ejemplo

    * **Tabla** `clientes`

        | id_cliente | nombre |
        | ---------- | ------ |
        | 1          | Ana    |
        | 2          | Luis   |
        | 3          | Marta  |

    * **Tabla** `pedidos`

        | id_pedido | id_cliente | total |
        | --------- | ---------- | ----- |
        | 101       | 1          | 200   |
        | 102       | 1          | 150   |
        | 103       | 2          | 300   |


    * Relación:

        * `clientes.id_cliente` (PK)
        * `pedidos.id_cliente` (FK)

    ## Tipos de JOIN

    1. **`INNER JOIN`**

        * Devuelve **solo las filas que tiene coincidencia en ambas tablas**

        * Si no hay relación, **no aparece el resultado**

        * **Ejemplo**

            ```sql
            SELECT clientes.nombre, pedidos.total
            FROM clientes
            INNER JOIN pedidos
            ON clientes.id_cliente = pedidos.id_cliente;
            ```
            1. SQL toma `clientes`
            2. SQL toma `pedidos`
            3. Compara `id_cliente`
            4. Solo devuelve filas donde **existen en ambas tablas**

        * **Resultado**

            | nombre | total |
            | ------ | ----- |
            | Ana    | 200   |
            | Ana    | 150   |
            | Luis   | 300   |

            * Marta no aparece porque **no tiene pedidos**

    2. **`LEFT JOIN`**

        * Devuelve:

            * **Todas las filas de la tabla izquierda**
            * Coincidencias de la tabla derecha
            * Si no hay coincidencia -> `NULL`

        * **Ejemplo**

            ```sql
            SELECT clientes.nombre, pedidos.total
            FROM clientes 
            LEFT JOIN pedidos
            ON clientes.id_cliente = pedidos.id_cliente;
            ```
        
        * **Resultado**

            | nombre | total |
            | ------ | ----- |
            | Ana    | 200   |
            | Ana    | 150   |
            | Luis   | 300   |
            | Marta  | NULL  |

            * Se usa cuando **quieres incluir aunque no haya relación**

    3. **`RIGHT JOIN`**

        * Lo contrario de `LEFT JOIN`

            * Devuelve **todas la filas de la tabla derecha**
            * Coincidencias de la izquierda
            
        * **Ejemplo**

            ```sql
            SELECT clientes.nombre, pedidos.total
            FROM clientes
            RIGHT JOIN pedidos
            ON clientes.id_cliente = pedidos.id_cliente;
            ```
            * Poco usado en la practica (se puede reescribir como `LEFT JOIN`)

    4. **`FULL JOIN`**

        * Devuelve:

            * Todas las filas de ambas tablas
            * Coincidencia donde existan
            * `NULL` donde no

        * **Ejemplo**

            ```sql
            SELECT clientes.nombre, pedidos.total
            FROM clientes
            FULL JOIN pedidos
            ON clientes.id_cliente = pedidos.id_cliente;
            ```
            * No todos los motores lo soportan (MySQL no)

    5. **`CROSS JOIN`**

        * Productos cartesiano:

            * Cada fila de una tabla se combina con todas las de la otra

                ```sql
                SELECT clientes.nombre, pedidos.total
                FROM clientes
                CROSS JOIN pedidos;
                ```
                * Muy peligorso si no sabes lo que haces
                * Rara vez se usa

    6. **`SELF JOIN`**

        * Una tabla se une **consigo misma**

        * Ejemplo típico: empleados y jefes

            ```sql
            SELECT e.nombre AS empleado, j.nombre AS jefe
            FROM empleados e
            JOIN empleados j
            ON e.id_jefe = j.id_empleado
            ```

    ## JOIN + WHERE

    ```sql
    SELECT c.nombre, p.total
    FROM clientes c
    JOIN pedido p
    ON c.id_cliente = p.id_cliente
    WHERE p.total > 200;
    ```
    * `ON` define la relación
    * `WHERE` filtra resultados

    ## Errores comunes

    * Usar `WHERE` en lugar de `ON`
    * No entender que tabla es izquierda o derecha
    * Usar INNER JOIN cuando necesitas LEFT JOIN
    * JOIN sin condición (explosión de datos)
