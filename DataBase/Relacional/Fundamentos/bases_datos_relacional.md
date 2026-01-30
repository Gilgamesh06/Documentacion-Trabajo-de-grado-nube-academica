# Bases de datos Relacional

* Una **base de datos relacional (RDB)** es un  tipo de bases de datos que **almacena la información en tablas** y **relaciona esas tablas entre sí** usando claves (keys).

* Cada tabla representa **una entidad del mundo real** y cada fila representa **un registro**

* Se llama *relacional* porque:

    * Los datos **no están aislados**
    * Se conectan mediante **relaciones lógicas**

* Ejemplo simple:

    * **Tabla Persona**

        | id | nombre | email                                 |
        | -- | ------ | ------------------------------------- |
        | 1  | Ana    | [ana@mail.com](mailto:ana@mail.com)   |
        | 2  | Luis   | [luis@mail.com](mailto:luis@mail.com) |

    * **Tabla Pedido**

        | id | persona_id | total |
        | -- | ---------- | ----- |
        | 10 | 1          | 120.0 |
        | 11 | 2          | 80.0  |

    * `persona_id` relaciona **Pedido** con **Persona**

    ## Para que sirve una base de datos relacional ?

    * Sirve para **almacenar, organizar, consultar y proteger datos estructurados.**

    * **Uso principales:**

        * Sistemas empresariales
        * Aplicaciones web y APIs
        * Sistemas bancarios
        * ERP, CRM, facturación
        * Sistemas académicos

    ## Porque se usan tanto ?

    * Garantizan **consistencia**
    * Permiten **consultas complejas**
    * Mantienen **integridad de los datos**
    * Son ideales cuando los datos tienen **relaciones claras**

    * Ejemplo real:

        * Un **cliente** tiene muchos **pedidos**
        * Un **pedido** tiene muchos **productos**
        * Un **producto** pertenece a un **proveedor**

    * Todo eso encaja perfecto en un modelo relacional

    ## Funcionamiento

    * Funciona bajo un modelo **tabla <-> relación <-> consulta**

    * **Flujo básico:**

        1. Diseñar las tablas
        2. Defines las relaciones
        3. Insertar datos
        4. Consultas usando SQL

    * **Estructura Lógica**

        * Cada tabla tiene:

            * Columnas -> atributos
            * Filas -> registros

        * Ejemplo:

            ```sql
            SELECT nombre, total
            FROM persona p
            JOIN pedido pe ON p.id = pe.persona_id;
            ```
            * Esto une dos tablas usando la relación definida

    * **Motor de base de datos**

        * El **DBMS** (Database Management Systems) se encarga de:

            * Almacenar los datos
            * Optimizar consultas
            * Aplicar reglas
            * Manejar concurrencia
            * Controlar transacciones

        * Ejemplos de DB relacionales:

            * PostgreSQL
            * MySQL
            * Oracle
            * SQL Server

    ## Elementos que componen una base de datos relacional

    1. **Tablas (Tables)**

        * Son la **unidad principal de almacenamiento**

        * Características:

            * Nombre único
            * Columnas definidas
            * Filas con datos

        * Ejemplo:

            ```sql
            CREATE TABLE persona (
                id SERIAL PRIMARY KEY,
                nombre VARCHAR(100),
                email VARCHAR(100)
            );
            ```
    
    2. **Columnas (Campos / Atributos)**

        * Define **que tipo de datos** puede almacenar una tabla

            * Tipo de dato
            * Restricciones
            * Significado del atributo

        * Ejemplo:

            * `INT`
            * `VARCHAR`
            * `DATE`
            * `BOOLEAN`

    3. **Filas (Registros / Tuplas)**

        * Cada fila representa un **objeto real**

        * Ejemplo:

            > Persona: Ana  
            Pedido: #10

    4. **Clave primaria (Primary Key)**

        * Identifica **de forma única** cada registro

        * Características:

            * No se repite
            * No puede ser NULL
            * Garantiza unicidad

        * Ejemplo:

            ```sql
            id INT PRIMARY KEY
            ``` 

    5. **Clave foránea (Foreign Key)**

        * Permite **relacionar tablas**

            > Apunta a la clave primaria de otra tabla

        * Ejemplo:

            ```sql
            persona_id INT REFERENCES persona(id)
            ```
            * Esto asegura que **no exista un pedido sin persona válida.**

    6. **Relaciones**

        * Tipos más comunes:

            * **Uno a Uno (1:1)**

                * Persona <-> Pasaporte

            * **Uno a Muchos (1:N)**

                * Cliente  -> Pedidos

            * **Muchos a Muchos (N:M)**

                * Estudiantes <-> Cursos
                
                > **Nota:** Se usa una tabla intermedia

    7. **Restricciones (Constraints)**

        * Reglas que protegen la integridad:

            * `NOT NULL`
            * `UNIQUE`
            * `CHECK`
            * `PRYMARY KEY`
            * `FOREIGN KEY`

        * Ejemplo:

            ```sql
            email VARCHAR(100) UNIQUE NOT NULL
            ```
    
    8. **Índices (Indexes)**

        * Mejoran la **velocidad de las consultas**

            > *No almacenan datos nuevos, solo optimizan búsquedas*

        * Ejemplo:

            ```sql
            CREATE INDEX idx_persona_email ON persona(email);
            ```

    9. **Consultas SQL**

        * Lenguaje estándar para interactuar con la base de datos

        * Tipos:

            * `SELECT` -> Consultar
            * `INSERT` -> Insertar
            * `UPDATE` -> Actualizar
            * `DELETE` -> Eliminar
    
    10. **Transacciones**

        * Garantizan operaciones seguras mediante **ACID**

            * **A**tomicidad
            * **C**onsistencia
            * **I**solación
            * **D**urabilidad

        * Ejemplo:

            ```sql
            BEGIN;
            INSERT INTO pedido ...;
            UPDATE stock ...;
            COMMIT;
            ```
            * Si algo falla -> `ROLLBACK`

    ## Ventajas 

    * Datos consistentes
    * Relaciones claras
    * Estándar SQL
    * Escalables
    * Seguras
    