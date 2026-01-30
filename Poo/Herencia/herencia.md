# Herencia

* La **herencia** es el mecanismo de OOP que permite que:

    > Una clase **herede atributos y métodos** de otra clase

* Se establece una relación **ES UN**

* Ejemplo mental

    * Un Perro es un Animal
    * Un EmpleadoFijo es un Empleado

    ## Para que sirve la herencia ?

    * **Reutilizar código:** Evita duplicar lógica común
    * **Modelar jerarquías reales:** Representa relaciones naturales del dominio
    * **Facilitar mantenimiento:** Cambios en la clase padre impactan a las hijas
    * **Aplicar polimorfismo:** Usar clases hijas como si fueran el padre
    * **Base de frameworks (Spring, Django):** Controllers, Services, Entities

    ## Funcionamiento

    1. Se define una **clase base (padre)**
    2. Se define una **clase derivada (hija)**
    3. La clase hija **hereda** atributos y métodos
    4. La hija puede:

        * Usar lo heredado
        * Sobrescribir métodos
        * Agregar comportamiento propio

    * **No todo debe heredarse** (principio de buen diseño)

    ## Elementos que componen la herencia

    1. **Clase padre (superclase):** Contiene comportamiento común
    2. **Clase hija (subclase):** Hereda del padre
    3. **Palabras clave**

        * Java -> `extends`
        * Python -> `class Hija(Padre)`

    4. **Método sobrescrito:** Cambia comportamiento heredado
    5. `super` Llama el contructor o métodos del padre

    ## Ejemplo: Java

    * **Paso 1: Definir el problema**

        * Tenemos animales:

            * Perro
            * Gato

        * Todos:

            * Tienen nombre
            * Hacen sonido (pero diferente)

    * **Paso 2: Crear la clase padre**

        ```java
        public class Animal {

            protected String nombre;

            public Animal(String nombre){
                this.nombre = nombre;
            }

            public void hacerSonido(){
                System.out.println("El animal hace un sonido");
            }
        }
        ```
        * `protected` -> accesible por hijas
        * Método común
        * Constructor base

    * **Paso 3: Crear clase hija (Perro)**

        ```java
        public class Perro extends Animal {

            public Perro(String nombre){
                super(nombre);
            }

            @Override
            public void hacerSonido() {
                System.out.println("El perro ladra");
            }
        }
        ```

    * **Paso 4: Otra clase hija (Gato)**

        ```java
        public class Gato extends Animal {

            public Gato(String perro){
                super(nombre);
            }

            @Override
            public void hacerSonido(){
                System.out.println("El gato maulla");
            }
        }
        ```

    * **Paso 5: Usar herencia + polimorfismo**

        ```java
        public class Main {
            
            public static void main(String[] args){

                Animal a1 = new Perro("Firulais");
                Animal a2 = new Gato("Michi");

                a1.hacerSonido();
                a2.hacerSonido();
            }
        }
        ```
        * El tipo es `Animal`
        * El comportamiento es el de la clase hija

    ## Ejemplo: Python

    * **Paso 1: Clase Padre**

        ```python
        class Animal:

            def __init__(self, nombre:str):
                self.nombre = nombre

            def hacer_sonido(self):
                print("El animal hace un sonido")
        ```

    * **Paso 2: Clase hija (Perro)**

        ```python
        class Perro(Animal):

            def __init__(self, nombre:str):
                super().__init__(nombre)

            def hacer_sonido(self):
                print("El perro ladra")
        ```

    * **Paso 3: Clase hija (Gato)**

        ```python
        class Gato(Animal):

            def __init__(self, nombre):
                super().__init__(nombre)

            def hacer_sonido(self):
                print("El gato maulla")
        ```

    * **Paso 4: Uso**

        ```python
        animales = [
            Perro("Firulais"),
            Gato("Michi")
        ]

        for animal in animales:
            animal.hacer_sonido()

        ```
    
    ## Tipos de herencia

    * **Java**

        * Simple (una clase padre)
        * Múltiple -> (solo con interfaces)

    * **Python**

        * Simple 
        * Multiple

    ## Errores comunes con herencia

    * Usar herencia solo para reutilzar codigo
    * Jerarquias profundas
    * Romper encapsulación
    * No usar polimorfismo

    * Regla de oro:

        > Si no cumple *Es un*, no heredes


    ## Herencia en arquitectura real

    * **Spring Boot**

        ```java
        public class BaseEntity {
            Long id;
            LocalDateTime createAt;
        }
        ```
    
        ```java
        public class Usuario extends BaseEntity {}
        ```
    
    * **FastAPI**

        ```python
        class BaseModel:
            id: int
        ```

    ## Resumen

    > Herencia = Es un

    * Reutiliza código
    * Modela jerarquias
    * Permite polimorifismo
    * Facilita mantenimiento

    