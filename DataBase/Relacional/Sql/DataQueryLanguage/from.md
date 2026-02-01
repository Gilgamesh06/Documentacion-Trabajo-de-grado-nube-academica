# FROM

* `FROM` es la cláusula de **DQL (SELECT)** que **define la fuente de los datos** que se van a consultar

* Indica:

    * **Qué tabla(s)** se usan
    * **Cómo se combinan**
    * **Con qué alias se referencian**

* Sin `FROM`, **no hay datos que consultar**

    ## Para que sirve FROM ?

    * Indicar el origen de los datos
    * Unir tablas relacionadas
    * Definir alias de tablas
    * Usar subconsultas como fuente
    * Construir vista lógicas de datos

    * Es la cláusula que **construye el conjunto base de filas**

    ## Funcionamiento

    * Desde el punto de vista del motor:

        1. Se identifica las tablas
        2. Se cargan sus filas (Conceptualmente)
        3. Se aplican `JOIN` si existe
        4. Se genera una **relación intermedia**
        5. Se genera una **relación intermedia**

    * `FROM` es **lo primero que se ejecuta** en un `SELECT`

    ## Que puede contener FROM

    1. **Una tabla simple**

        ```sql
        FROM persona
        ```
        * Se toma **toda las filas** de la tabla

    2. **Varias tablas (producto cartesiano)**

        ```sql
        FROM persona, pedido
        ```
        * Esto genera un **producto cartesiano** (normalmente se evita)

    3. **JOIN explicitos (forma correcta)**

        ```sql
        FROM persona p
        INNER JOIN pedido pe ON p.id = pe.persona_id;
        ```
        * Aqui `FROM`:

            * Define tablas
            * Define relaciones
            * Define alias

    4. **Subconsultas**

        ```sql
        FROM (
            SELECT persona_id, SUM(total) AS total
            FROM pedido
            GROUP BY persona_id
        ) t
        ```
        * El resultado se trata como una **tabla temporal**

    5. **Vistas**

        ```sql
        FROM vistas_ventas
        ```
        * Una vista se comporta igual que una tabla

    ## Ejemplo

    * **Escenario**

        * **persona**

            | id | nombre | ciudad   |
            | -- | ------ | -------- |
            | 1  | Ana    | Bogotá   |
            | 2  | Luis   | Medellín |

        * **pedido**

            | id | nombre | ciudad   |
            | -- | ------ | -------- |
            | 1  | Ana    | Bogotá   |
            | 2  | Luis   | Medellín |

    * **Objecto**

        > *Obtener el **nombre de la persona** y el **total de su pedido***

    * **Consulta**

        ```sql
        SELECT p.nombre, pe.total
        FROM persona p
        INNER JOIN pedido pe ON p.id = pe.persona_id;
        ```
    
    * **Ejecución**

        1. **FROM persona p**

            * Se cargan todas las filas de `persona`
            * Se asigna el alias `p`
        
        2. **INNER JOIN pedido pe**

            * Se carga filas de `pedido`
            * Alias `pe`

        3. **ON p.id = pe.persona_id**

            * Se comparan ambas tablas
            * Solo se combinan filas coincidentes

        4. **Resultado pasa al SELECT**

            * El `SELECT` **solo proyecta columnas**, pero **FROM ya construyo los datos**

    ## Error tipico

    ```sql
    FROM persona, pedido
    WHERE pesona_id = pedido.persona_id;
    ```
    * Funciona:

        * Menos claro
        * Mas propenso a errores

    * Mejor usar `JOIN`


    ## Relación con otra cláusulas

    | Cláusula | Rol                        |
    | -------- | -------------------------- |
    | FROM     | Construye el conjunto base |
    | WHERE    | Filtra filas               |
    | GROUP BY | Agrupa                     |
    | SELECT   | Proyecta columnas          |
    | ORDER BY | Ordena                     |
