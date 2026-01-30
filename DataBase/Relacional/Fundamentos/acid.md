# ACID

* **ACID** es un conjunto de **propiedades** que garantizan que las **transacciones** en una base de datos se ejecuten de forma **segura, consistente y confiable**

    * El nombre viene de:

        * **A**tomicity
        * **C**onsistency
        * **I**solation
        * **D**urability

    * ACID se aplica **a las transacciones**, no a tablas ni consultas individuales.

    ## Para que sirve ACID ?

    * Sirve para asegurar que:

        * Los datos no se corrompan
        * Las operaciones incompletas no queden a medias
        * Múltiples usuarios no se interfieran
        * Los datos sobrevivan a fallos del sistema
    
    * Sin ACID:

        * Transferencias bancarias fallan
        * Inventarios quedan incorrectos
        * Pedidos duplicados o incompletos

    ## Funcionamiento

    * Funciona mediante:

        * **Transacciones**
        * **Control de Concurrencia**
        * **Logs de recuperación**
        * **Mecanismos de bloqueo o versiones**

    * Flujo tipico:

        ```sql
        BEGIN;
        UPDATE cuenta SET saldo = saldo - 100 WHERE id = 1;
        UPDATE cuenta SET saldo = saldo + 100 WHERE id = 2;
        COMMIT;
        ```
        * Si algo falla -> `ROLLBACK`

    ## Las 4 propiedades ACID (Explicadas a fondo)

    1. **Atomicidad (Atomicity)**

        > *Una transacción se ejecuta **completa o no se ejecuta***

        * Todo o nada

        * **Ejemplo**

            * Transferencias bancaria:

                1. Restar dinero de cuenta A
                2. Sumar dinero a cuenta B

                * Si falla paso 2:

                    * El paso 1 se **revierte**
                    * No hay pérdida de dinero

        * **Como se implementa**

            * Logs de transacciones
            * Rollback automático
            * Control de errores

    2. **Consistencia (Consistency)**

        > *La bases de datos pasan de un **estado válido a otro estado válido***

        * Se respeta:

            * Reglas
            * Restricciones
            * Claves
            * Checks

        * **Ejemplo**

            * **Regla:** El sado no puede ser negativo

            * Si una transaccion viola **esa regla**

                * Se cancela

        * **Como se implementa**

            * Constrains (`PRIMARY KEY`, `FOREIGN KEY`)
            * Reglas de negocio
            * Triggers
            * Validaciones

    3. **Aislamiento (Isolation)**

        > *Las transacciones concurrentes **no se afectan entre si***

        * Para cada transacción:

            * *Parece que estoy solo en la base de datos*

        * **Problemas que evita**

            * Lecturas sucias
            * Lecturas no repetibles
            * Lecturas fatasma

        * **Niveles de aislamiento**

            * `READ UNCOMMITTED`
            * `READ COMMITTED`
            * `REPETABLE READ`
            * `SERIALIZABLE`

            * A mayor aislamiento -> menor concurrencia

        * **Como se implementa**

            * Locks (bloqueos)
            * MVCC (Versiones de datos)
            * Control de concurrencia

    4. **Durabilidad (Durability)**

        > *Una vez confirmado (`COMMIT`), los datos **no se pierden***

        * Incluso si:

            * Se cae el sistema
            * Se va la luz
            * El servidor se reinicia

        * **Ejemplo:**

            * Pedido confirmado -> queda guardado **para siempre**

        * **COmo se implementa**

            * Write-Ahead Logging (WAL)
            * Escritura en disco
            * Replicación
            * Backups

    ## Relación entre ACID y BDR (Bases de datos Relacionales)

    * **Relacion Directa**

        > *Las bases de datos relacionales están disenadas para cumplir **ACID** por defecto*

    * El modelo relacional necesita ACID para:

        * Mantener integridad
        * Proteger relaciones
        * Asegurar consistencia entre tablas
    
    * **Ejemplo Relacional**

        * Tablas:

            * cliente
            * pedido
            * producto
            * stock

        * Transacción:

            1. Insertar pedido
            2. Insertar detalle
            3. Descontar stock

            * Si falla un paso:

                * Se deshace todo
                * No hay inconsistencias

            * Esto **solo es posible con ACID**

    ## ACID + Normalización + Claves

    | Concepto          | Función          |
    | ----------------- | ---------------- |
    | Modelo Relacional | Estructura       |
    | Normalización     | Diseño correcto  |
    | Claves            | Relaciones       |
    | ACID              | Ejecución segura |

    * Si falta ACID:

        * La normalización no sirve
        * Las relaciones se rompen
        * Los datos se dañan

    