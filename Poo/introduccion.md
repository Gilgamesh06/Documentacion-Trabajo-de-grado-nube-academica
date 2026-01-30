# Introduccion

* La **Programación Orientada a Objetos** es una forma de diseñar y construir software donde:

    * Todo se modela como **objetos**
    * Los objetos son instancias de **clases**
    * Cada objeto:

        * **Tienes datos** (atributos)
        * **Puede hacer cosas** (métodos)

* La idea central es **modelar el problema antes de programar,** pensando en entidades reales y cómo interactúan

* Ejemplo conceptual:

    > Un Usuario tiene nombre y correo, y puede iniciar sesión o cerrar sesión

    ## Para qué sirve OOP ?

    * OOP sirve para crear sistemas:

        * **Mas faciles de entender**
        * **Mas mantenibles**
        * **Reutilizables**
        * **Escalables**
        * **Colaborativos**

    ## Funcionamiento

    1. **Analizas el problema**
    2. **Crear clases**
    3. **Creas objetos**
    4. **Los objetos interactúan**

    * **Ejemplo**

        ```bash
        Clase: Persona
        - nombre
        - edad
        - hablar()

        Objeto:
        persona1 = Persona("Juan", 30)
        persona2 = Persona("Ana", 25)
        ```
        * Cada objeto tiene **sus propios datos**, pero **comparte el mismo comportamiento**

    ## Elementos que componen OOP

    1. **Clases**

        * **Plantillas** para crear objetos

            ```java
            class Persona {
                String nombre;
                int edad;

                void hablar(){
                    System.out.println("Hola");
                }
            }
            ```
    
    2. **Objetos**

        * **Instancias de una clase**

            ```java
            Persona p = new Persona();
            ```
    
    3. **Atributos**

        * Variables que representan el **estado** del objeto

            ```java
            String nombre;
            int edad;
            ```
    
    4. **Métodos**

        * Funciones que representan el **comportamiento** del objeto

            ```java
            void hablar() {}
            ```
    
    ## Los 4 pilares de OOP

    1. **Encapsulación**

        * Ocultar los detalles internos del objeto y exponer solo lo necesario.

            ```java
            class Cuenta {
                private double saldo;

                public void depositar(double monto){
                    saldo += monto;
                }
            }
            ```
            * Protege los datos
            * Controla el acceso
            * Reduce errores

    2. **Abstracción**

        * Mostrar **que hace** un objeto, no **cómo lo hace**

            ```java
            interface Vehiculo {
                void mover();
            }
            ```            
            * Simplifica el diseño
            * Permite enfocarse en lo importante

    3. **Herencia**

        * Una clase puede **heredar** de otra

            ```java
            class Empleado extends Persona {
                double salario;
            }
            ```
            * Reutiliza código
            * Crea jerarquías

    4. **Polimorfismo**

        * Un mismo método puede comportarse diferente según el objeto

            ```java
            Vehiculo v = new Auto();
            v.mover();
            ```
            * Flexibilidad
            * Código desacoplado

    ## Relación con arqutectura

    * Como tu trabajas con **Spring Boot, FastAPI y Microservicios,** OOP se refleja en:

        * **En Spring Boot**

            * Controllers -> Clases
            * Services -> Clases
            * Repositories -> Interfaces
            * Entities -> Objetos de dominio

        * **En FastAPI**

            * Modelos Pydantic -> Clases
            * Servicios -> Clases
            * Dependencias -> Inyección (OOP + DI)

    ## Resumen rápido

    | Concepto      | Descripción                 |
    | ------------- | --------------------------- |
    | OOP           | Paradigma basado en objetos |
    | Clase         | Plantilla                   |
    | Objeto        | Instancia                   |
    | Atributos     | Estado                      |
    | Métodos       | Comportamiento              |
    | Encapsulación | Protección                  |
    | Abstracción   | Simplificación              |
    | Herencia      | Reutilización               |
    | Polimorfismo  | Flexibilidad                |

