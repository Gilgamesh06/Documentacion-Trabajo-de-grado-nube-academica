# Clase

* Una **clase** es una **plantilla (molde)** que define:

    * **Cómo es** un objeto  -> *atributos*
    * **Qué puede hacer** un objeto -> *métodos*

    ## Para que sirve una clase ?

    * **Modelar el mundo real**

        * Representa entidades del dominio (Usuario, Pedido, Producto)

    * **Reutilizar código**

        * Puedes crear muchos objetos usando la misma clase.

    * **Aplicar OOP**

        * Permite encapsulación, herencia y polimorfismo

    * **Organizar el software**

        * Cada clase tiene una responsabilidad clara

    * **Escalar sistemas**

        * Fundamental para APIs, microservicios y arquitecturas limpias+

    ## Funcionamiento

    * **Nivel 1: Definición**

        * Defines la clase con:

            * Atributos
            * Métodos

    * **Nivel 2: Instanciación**

        * Crear objetos a partir de la clase

            ```text
            Clase -> Plantilla
            Objeto -> Instancia real creada desde la plantilla
            ```

    ## Elmentos que componen una clase

    1. **Atributos:** Representan el **estado** del objeto
    
        ```bash
        nombre, edad, email
        ```
    
    2. **Métodos:** Representan el **comportamiento** del objeto

        ```bash
        hablar()
        caminar()
        registrar()
        ```

    3. **Constructor:** Método especial que se ejecuta al crear el objeto

        ```bash
        Persona("Juan", 30)
        ```
    
    4. **Modicicadores de acceso (Java)**

        * Controlan quién puede acceder:

            * `public`
            * `private`
            * `protected`

    5. **`this/self`**

        * Referencia al objeto actual.

    ## Ejemplo: Java

    * **Paso 1: Definir el problema**

        * Necesitamos representar una **Persona** que:

            * Tiene su nombre y edad
            * Puede saludar

    * **Paso 2: Crear la clase**

        ```java
        class Persona {

            private String nombre;
            private int edad;

            public Persona(){}

            public Persona(String nombre, int edad){
                this.nombre = nombre;
                this.edad = edad;
            }

            public void saludar(){
                System.out.println("Hola, mi nombre es:" + nombre);
            }

            public void setNombre(String nombre){
                this.nombre = nombre;
            }

            public void setEdad(int Edad){
                this.edad = edad;
            }

            public String getNombre(){
                return this.nombre;
            }

            public int getEdad(){
                return this.edad;
            }
        }
        ```
        * `class Persona` -> define la clase
        * `private` -> encapsulación
        * `this.nombre` -> atributo del objeto actual
        * `saludar()` -> acción del objeto

    * **Paso 3:  Crear objetos (Instancias)**

        ```java
        public class Main {
            public static void main(String[] args){

                Persona p1 = new Persona("Juan", 30);
                Persona p2 = new Persona("Ana", 25);

                p1.saludar();
                p2.saludar();
            }
        }
        ```
        * Se crean **dos objetos distintos**
        * Ambos usan la misma clase
        * Cada uno tiene su propio estado

    ## Ejemplo: Python

    * Python es mas simple, pero el concepto es el mismo

    * **Paso 1: Definir la clase**

        ```python
        class Persona:

            __init__(self, nombre: str. edad: int):
                self.nombre = nombre
                self.edad = edad

            def saludar(){
                print(f"Hola, mi nombre es: {self.nombre}")
            }
        ```
        * `class Persona:` -> Define la clase
        * `__init__` -> constructor
        * `self` -> referencia al objeto actual
        * `self.nombre` -> atributo del objeto
        * Cada instancia tiene dados propios

    * **Paso 2: Crear objetos**

        ```python
        p1 = Persona("Juan", 30)
        p2 = Persona("Ana", 25)

        p1.saludar()
        p2.saludar()
        ```

    ## Comparacion Java vs Python

    | Concepto             | Java         | Python              |
    | -------------------- | ------------ | ------------------- |
    | Constructor          | `Persona()`  | `__init__`          |
    | Referencia al objeto | `this`       | `self`              |
    | Tipos de datos       | Obligatorios | Dinámicos           |
    | Modificadores        | Sí           | No (por convención) |
    | Verbosidad           | Alta         | Baja                |


    ## Relación con lo que estás estudiando

    * **En SpringBoot**

        ```java
        @Entity
        public class Usuario {
            private Long id;
            private String email;
        }
        ```

    * **En FastAPI**

        ```python
        class Usuario(BaseModel):
            email: str
        ```
    
    * **Todo es una clase:** controladores, servicios, entidades, DTOs

    ## Resumen
    
    * Una clase es una platilla
    * Define estado y comportamiento
    * Permite crear múltiples objetos
    * Es la base de OOP
    * Se usa en cualquier arquitectura moderna

    