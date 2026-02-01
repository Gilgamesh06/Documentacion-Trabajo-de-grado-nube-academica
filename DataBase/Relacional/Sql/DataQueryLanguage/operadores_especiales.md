# Operadores Especiales

* Los **operadores especiales** son operadores de SQL que permiten **expresar condiciones avanzadas** que no se pueden (o no conviene) hacer solo con `=`, `<`, `>` y operadores lógicos

* Simplifican consultas
* Hacen el SQL más legible
* Evitan combinaciones largas de `AND`/`OR`

    ## Para qué sirven ?

    * Comparar rangos de valores
    * Evaluar listas de valores
    * Buscar patrones de texto
    * Trabajar correctamente con `NUll`
    * Evaluar existencia de datos

    ## Operadores especiales más usados en WHERE

    | Operador                  | Uso principal        |
    | ------------------------- | -------------------- |
    | `IN`                      | Lista de valores     |
    | `BETWEEN`                 | Rango                |
    | `LIKE`                    | Patrones de texto    |
    | `IS NULL` / `IS NOT NULL` | Valores nulos        |
    | `EXISTS`                  | Subconsultas         |
    | `ANY` / `ALL`             | Comparación avanzada |

    1. **IN**

        * `IN` verifica si un valor **pertenece a una lista**
        * Reemplaza multiples `OR`

        * **Sin IN (mala practica)**

            ```sql
            WHERE ciudad = 'Bogota'
            OR ciudad = 'Medellin'
            OR ciudad = 'Cali'
            ```
        
        * **Con IN (correcto)**

            ```sql
            WHERE ciudad IN ('Bogota', 'Medellin', 'Cali')
            ```
        
        * **Ejemplo**

            * **Tabla `clientes`**

                | id | nombre | ciudad       |
                | -- | ------ | ------------ |
                | 1  | Ana    | Bogotá       |
                | 2  | Luis   | Cali         |
                | 3  | Marta  | Barranquilla |

            * **Consulta**

                ```sql
                SELECT * 
                FROM clientes
                WHERE ciudad IN ('Bogota', `Cali`);
                ```
                * Ana -> true
                * Luis -> true
                * Marta -> false

    1. **BETWEEN**

        * `BETWEEN` evaluas si un valor está **dentro de un rango** (incluyente)
        * Incluye valores externos es decir `[ ]` y no `( )`

        * **Ejemplo**

            ```sql
            WHERE edad BETWEEN 18 AND 30;
            ```
            * Equivale a:

                ```sql
                edad >= 18 AND edad <= 30
                ```
        * **Ejemplo**

            **Tabla `empleados`**

                | nombre | edad |
                | ------ | ---- |
                | Ana    | 22   |
                | Luis   | 35   |
                | Marta  | 30   |

            * **Consulta**

                ```sql
                SELECT *
                FROM empleados
                WHERE edad BETWEEN 18 AND 30;
                ```
            * **Resultado**

                * Ana y Marta

    3. **LIKE**

        * `LIKE` permite **buscar patrones de texto**
        * Se usa con comodines

        * **Comodines**

            | Símbolo | Significado                    |
            | ------- | ------------------------------ |
            | `%`     | Cualquier número de caracteres |
            | `_`     | Un solo carácter               |

        * **Ejemplos**

            ```sql
            WHERE nombre LIKE 'A%'
            ```
            * Empieza con A

            ```sql
            WHERE nombre LIKE '%a'
            ```
            * Termina con a

            ```sql
            WHERE nombre LIKE '_u%'
            ```
            * Segundo caracter es *u*

        * **Ejemplo**

            * **Tabla `usuarios`**

                | nombre |
                | ------ |
                | Ana    |
                | Luis   |
                | Laura  |

            * **Consulta**

                ```sql
                SELECT * 
                FROM usuarios
                WHERE nombre LIKE 'L%';
                ```
            
            * Resultado:

                * Luis, Laura

    4. **IS NULL / IS NOT NULL**

        * SQL **No permite comparar NULL con =**
        * NULL significa *desconocido*

        * **Incorrecto**

            ```sql
            WHERE telefono = NULL;
            ```
        
        * **Correcto**

            ```sql
            WHERE telefono = IS NULL;
            ```

        * **Ejemplo**

            * **Tabla `clientes`**

                | nombre | telefono |
                | ------ | -------- |
                | Ana    | 555      |
                | Luis   | NULL     |

            * **Consulta**

                ```sql
                SELECT *
                FROM clientes
                WHERE telefono IS NULL;
                ```
            * Resultado

                * Luis

    5. **EXISTS**

        * `EXISTS` verifica si **una subconsulta devuelve al menos una fila**
        * No evalúa valores, solo existencia

        * **Ejemplo**

            * Clientes con pedidos

                ```sql
                SELECT *
                FROM clientes c
                WHERE EXIST (
                    SELECT 1
                    FROM pedidos p
                    WHERE p.cliente_id = c.id
                )
                ```
                1. Se evalúa cliente por cliente
                2. Si tiene pedidos -> TRUE
                3. Si no -> FALSE

    6. **ANY / ALL**

        * **ANY**

            * Cumple si es verdadero para **almenos uno**

                ```sql
                WHERE salario > ANY (SELECT salario FROM empleados);
                ```
        
        * **ALL**

            * Cumple si es verdadero para **todos**

                ```sql
                WHERE salario > ALL (SELECT salario FROM empleados);
                ```

    ## Ejemplo Completa  combinando operadores

    * **Consulta:** Porductos disponibles, precio entre 100 y 500, categoria válida y nombre que empiece por M

        ```sql
        SELECT *
        FROM productos
        WHERE stock > 0
        AND precio BETWEEN 100 AND 500
        AND categoria IN ('Tecnologia', 'Muebles')
        AND nombre LIKE 'M%';
        ```

    ## Errores Comunes

    * Usar = NULL
    * No enteneder BETWEEN (incluye extremos)
    * Abusar de LIKE con `%` al inicio (rompe índices)
    * No usar IN cuando corresponde

    ## Resumen

    | Operador  | Uso                     |
    | --------- | ----------------------- |
    | IN        | Lista                   |
    | BETWEEN   | Rango                   |
    | LIKE      | Texto                   |
    | IS NULL   | Nulos                   |
    | EXISTS    | Existencia              |
    | ANY / ALL | Comparaciones avanzadas |
