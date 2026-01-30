# Normalización

* La **normalización** es un **proceso del modelo relacional** que consiste en **organizar los datos en tablas correctamente estructuradas**, con el objetivo de:

    * Eliminar **redundacia**
    * Evitar **inconsistencias**
    * Prevenir **anomlias**
    * Garantizar **integridad de los datos**

* Se basa en **reglas formales** llamadas **Formas Normales (FN)**

    ## Para que sirve la normalización ?

    * Sirve para evitar problemas clásicos en bases de datos mal diseñadas.

    * **Problemas sin normalización**

        1. **Redundancia:** El mismo dato repetido muchas veces
        2. **Anomalía de inserción:** No puedes insertar un dato sin otro que no existe aún
        3. **Anomalía de actualizacion:** Actualizar un dato dato en un lugar y olvidar en otro
        4. **Anomalía de eliminación:** Eliminar un dato y perder información importante

    * **Benenficios de Normalizar**

        * Datos coherentes
        * Menor duplicación
        * Mejor mantenimiento
        * Relaciones claras
        * Integridad referencial
    
    ## Funcionamiento

    * Funciona **descomponiendo tablas grandes**, en tablas mas pequeñas

        1. Se identifican atributos
        2. Se detectan dependencias
        3. Se separan tablas
        4. Se aplican claves primarias y foráneas
        5. Se avanza forma normal por forma normal.

    * Siempre se parte de una tabla *cruda* y se mejora paso a paso

    ## Formas Normales (Tipos de Normalización)

    1. **Primera Forma Normal (1FN)**

        > **Regla:** Todos los atributos deben ser **atómicos** (no listas, no conjuntos, no valores repetidos)

        * **Tabla No normalizada**

            | pedido_id | cliente | productos      |
            | --------- | ------- | -------------- |
            | 1         | Ana     | Zapato, Camisa |
            | 2         | Luis    | Pantalón       |

            * `productos` tiene **valores múltiples**

        * **En 1FN**

            * **Pedido**

                | pedido_id | cliente |
                | --------- | ------- |
                | 1         | Ana     |
                | 2         | Luis    |

            * **Pedido_Producto**

                | pedido_id | producto |
                | --------- | -------- |
                | 1         | Zapato   |
                | 1         | Camisa   |
                | 2         | Pantalón |

            * Cada campo tiene un solo valor
            * No hay columnas repetidas

    2. **Segunda Forma Normal (2FN)**

        > **Regla:** Debe estar en 1FN **Ningun atributo no clave** depende **de una parte** de una clave compuesta

        * Solo aplica cuando la clave primaria es **compuesta**

        * **Tabla No en 2FN**

            | pedido_id                     | producto_id | producto_nombre | cantidad |
            | ----------------------------- | ----------- | --------------- | -------- |
            | PK = (pedido_id, producto_id) |             |                 |          |

            * `producto_nombre` depende solo de `producto_id`, no de toda la clave

        * **En 2FN**

            * **Pedido_Producto**

                | pedido_id | producto_id | cantidad |

            * **Producto**

                | producto_id | producto_nombre |

                * Cada atributo depende **de la clave completa**

    3. **Tercera Forma Normal (3FN)**

        > **Regla:** Debe estar en 2FN **No debe haber dependencias transitivas**

        * Un atributo no clave **no debe depender de otro atributo no clave**

        * **Tabla No en 3FN**

            | persona_id | nombre | ciudad | codigo_postal |
            | ---------- | ------ | ------ | ------------- |

            * `codigo_postal` depende de `ciudad`, no directamente de `persona_id`

        * **En 3FN**

            * **Persona**

                | persona_id | nombre | ciudad |

            * **Ciudad**

                | ciudad | codigo_postal |

            * Cada atributo depende **solo de la clave primaria**

    4. **Forma Normal de Boyce-Codd (BCNF)**

        > **Regla:** Para toda dependencia funcional X -> Y, **X debe ser una superclave**

        * Es una version **mas estricta** de la 3FN

        * **Violación BCNF**

            | profesor | curso | aula |

            * Si:

                * Un profesor puede dictar varios cursos
                * Un curso solo tiene un aula
                * `curso -> aula` pero `curso` no es clave.

        * **En BCNF**

            * **Curso**

                | curso  | aula |

            * **Profesor_Curso**

                | profesor | curso |

                * Dependencias bien definidas

    5. **Cuarta Forma Normal (4FN)**

        > **Regla:** No debe haber **dependencias multivaluadas**

        * **No en 4FN**

            | estudiante | idioma | hobby |
            | ---------- | ------ | ----- |

            * Idioma y hobbies son independientes

        * **En 4FN**

            * **Estudiante_Idioma**

                | estudiante | idioma |

            * **Estudiante_Hobby**

                | estudiante | hobby |

    6. **Quinta Forma Normal (5FN)**

        > **Regla:** Eliminar dependencias de unión innecesarias
        
        * Se usa en casos muy avanzados

        * **Ejemplo típico**

            * Relaciones ternarias complejas:

                * Proveedor
                * Producto
                * Proyecto

            * Se separan si:

                * No se pierde información
                * No se generan inconsistencias

                