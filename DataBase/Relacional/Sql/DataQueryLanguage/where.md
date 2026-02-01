# WHERE

* `WHERE` es una cláusula de **SQL (DQL)** que se utiliza para **filtrar filas** de una tabla o del resultado de un `FROM/JOIN`, segun una **condición lógica**

* `WHERE`

    * Se aplica **a filas**
    * Se ejecuta **antes de GROUP BY**
    * Reduce la cantidad de datos procesados
    
    * Sin `WHERE`, el `SELECT` devuelve **todo**

    ## Para qué sirve WHERE ?

    * Filtrar información especifica
    * Evitar traer datos innecesarios
    * Mejorar rendimiento
    * Aplicar reglas de búsqueda
    * Implementar lógica de negocio

    * Ejemplos reales:

        * Usuarios activos
        * Pedidos del mes
        * Productos con stock > 0

    ## Funcionamiento

    * Desde el punto de vista del motor:

        1. `FROM` construye el conjunto base
        2. `JOIN` combinan tablas
        3. `WHERE` **evalúa cada fila**
        4. Si la condición es `TRUE` -> la fila pasa
        5. Si es `FALSE` o `NULL` -> se descarta

    * `WHERE` **no agrupa, no calcula totales**

    ## Que puede contener `WHERE`

    1. **Operadores de comparación**

        ```sql
        = <> > < >= <=
        ```
    
    2. **Operadores lógicos**

        ```sql
        AND OR NOT
        ```
    
    3. **Operadores especiales**

        ```sql
        IN
        BETWEEN
        LIKE
        IS NULL
        ```
    
    4. **Expresiones y funciones**

        ```sql
        YEAR(fecha) = 2024
        ```
    
    ## Ejemplo

    * **Escenario**

    * Tablas **persona**

        | id | nombre | edad | ciudad   |
        | -- | ------ | ---- | -------- |
        | 1  | Ana    | 25   | Bogotá   |
        | 2  | Luis   | 17   | Medellín |
        | 3  | Marta  | 30   | Bogotá   |
        | 4  | Carlos | NULL | Cali     |

    * **Objetivo**

        > Obtener **personas mayores de edad**, que viven en **Bogota**

    * **Consulta**

        ```sql
        SELECT nombre, edad, ciudad
        FROM persona AS p
        WHERE p.edad >= 18 AND p.ciudad = 'Bogotá';
        ```
    
    * **Ejecución**

        1. **FROM persona**

            * Se cargan **todas las filas**

                | id | nombre | edad | ciudad   |
                | -- | ------ | ---- | -------- |
                | 1  | Ana    | 25   | Bogotá   |
                | 2  | Luis   | 17   | Medellín |
                | 3  | Marta  | 30   | Bogotá   |
                | 4  | Carlos | NULL | Cali     |

        2. **WHERE edad >=18**

            * Evaluación fila por fila:

                * Ana -> `TRUE`
                * Luis -> `FALSE`
                * Marta -> `TRUE`
                * Carlos -> `NULL`

            * Resultados parcial:

                | nombre | edad | ciudad |
                | ------ | ---- | ------ |
                | Ana    | 25   | Bogotá |
                | Marta  | 30   | Bogotá |

        3. **WHERE ciudad = 'Bogotá'**

            * Evaluación fina:

                * Ana -> `TRUE`
                * Marta -> `TRUE`

        4. **SELECT**

            * Se muestra solo las columnas pedidas

    ## Errores comunes como WHERE

    * **Usar WHERE con funciones agregadas**

        ```sql
        WHERE COUNT(*) > 5
        ```
        * Incorrecto: Usa `HAVING`

    * **Comparar con NULL usando =**

        ```sql
        WHERE edad = NULL
        ```
        * Incorecto
        * Correcto: `WHERE edad IS NULL`

    ## WHERE vs HAVING 

    | WHERE             | HAVING              |
    | ----------------- | ------------------- |
    | Filtra filas      | Filtra grupos       |
    | Antes de GROUP BY | Después de GROUP BY |
    | No usa agregados  | Usa agregados       |

    ## Relación con índices

    * `WHERE` + Indices = **consultas rápidas**

    * Ejemplo:

        ```sql
        WHERE email = 'ana@mail.com'
        ```

    