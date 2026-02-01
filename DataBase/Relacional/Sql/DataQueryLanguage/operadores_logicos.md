# Operadores Lógicos

* Los **operadores lógicos** se usan dentro de la cláusula `WHERE` para **combinar, negar o agrupar condiciones**

* Permiten responder preguntas como:

    * *Esto y aquello*
    * *Esto o aquello*
    * *No esto*

    ## Para que sirven ?

    * Filtrar datos con múltiples condiciones
    * Crear reglas complejas
    * Simular lógica del mundo real
    * Evitar múltiples consultas
    
    * Sin operadores lógicos, `WHERE` seria muy limitado

    ## Operadores lógicos pricipales en SQL

    | Operador | Significado |
    | -------- | ----------- |
    | `AND`    | Y           |
    | `OR`     | O           |
    | `NOT`    | Negación    |

    1. **AND**

        * `AND` exige que **toda las condiciones sean verdaderas**
        * Si una falla, **la fila no aparece**

        * **Ejemplo base (Tabla `productos`)**

            | id | nombre | categoria  | precio | stock |
            | -- | ------ | ---------- | ------ | ----- |
            | 1  | Laptop | Tecnología | 1200   | 5     |
            | 2  | Mouse  | Tecnología | 20     | 0     |
            | 3  | Silla  | Muebles    | 150    | 10    |
            | 4  | Mesa   | Muebles    | 300    | 2     |

        * **Ejemplo con AND**

            * **Consulta:** productos de tecnologia con stock disponible

                ```sql
                SELECT *
                FROM productos p
                WHERE p.categoria = 'Tecnología'
                AND p.stock > 0;
                ```
                1. SQL evalua `categoria = 'Tecnologia'`
                2. SQL evalua `stock > 0`
                3. Ambas deben ser verdaderas
                4. Solo filas que cimplan ambas pasan

            * **Resultado**

                | nombre | categoria  | stock |
                | ------ | ---------- | ----- |
                | Laptop | Tecnología | 5     |

                * Mouse no aparece porque `stock = 0`

    2. **OR**

        * `OR` exige que **al menos una condición sea verdadera**
        * Si cumple una, **la fila aparece**

        * **Ejemplo**

            * **Consulta:** Productos que sean de tecnologia o que cuesten menos de 100

                ```sql
                SELECT *
                FROM productos p
                WHERE p.categoria = 'Tecnología'
                OR p.precio < 100
                ```
                1. SQL evalúa `categoria = 'Tecnologia'`
                2. SQL evalúa `precio < 100`
                3. Si una es verdadera -> pasa
                4. No necesita cumplir ambas

            * **Resultado**

                | nombre | categoria  | precio |
                | ------ | ---------- | ------ |
                | Laptop | Tecnología | 1200   |
                | Mouse  | Tecnología | 20     |
                
                * Mouse entra por **precio**, Laptop por **categoria**

    3. **NOT**

        * `NOT` **niega una condición**
        * Devuelve lo contrario

        * **Ejemplo con NOT**

            * **Consulta:** Productos que **No** sean de tecnologia

                ```sql
                SELECT *
                FROM productos
                WHERE NOT categoria = 'Tecnologia';
                ```

            * **Resultado**

                | nombre | categoria |
                | ------ | --------- |
                | Silla  | Muebles   |
                | Mesa   | Muebles   |
                        
    4. **Combinando AND, OR y paréntesis**

        > **Nota:** SQL evalua primero `NOT`, `AND` y por ultimo `OR`. Por eso se usan **paréntesis** para controlar la lógica.

        * **Ejemplo sin paréntesis**

            ```sql
            WHERE categoria = 'Tecnologia'
            OR categoria = 'Muebles'
            AND stock > 0;
            ```
            * SQL lo interpreta como:

                ```sql
                categoria = 'Tecnología'
                OR (categoria = 'Muebles' AND stock > 0)
                ```

        * **Ejemplo correcto con paréntesis**

            * **Consulta:** productos de tecnología o muebls **que tengan stock**

                ```sql
                SELECT *
                FROM productos
                WHERE (categoria = 'Tecnologia'
                OR categoria = 'Muebles')
                AND stock > 0;
                ```
                1. Evalua categorias
                2. Filtra por stock
                3. Aplica ambas condiciones combinadas

    ## Ejemplo clompleto

    * **Consulta:** Clientes activos, mayores de edad y de Colombia o Mexico

        ```sql
        SELECT *
        FROM clientes
        WHERE activo = true
        AND edad >= 15
        AND (pais = 'Colombia' OR pais = 'México');
        ```
        1. Filtra clientes activos
        2. Filtra mayores de edad
        3. Acepta Colombia o México
        4. Combina todo correctamente

    ## Errores Comunes

    * No usar paréntesis con AND y OR
    * Confundir AND con OR
    * Usar NOT sin entender el resultado
    * Pensar que SQL evalúa de izquierda a derecha

    ## Resumen

    | Operador | Función               |
    | -------- | --------------------- |
    | AND      | Todas las condiciones |
    | OR       | Al menos una          |
    | NOT      | Niega una condición   |
    | ()       | Controla prioridad    |
