# Clase Abstracta

* Una **clase abastracta** es una clase que:

    * **No se puede instanciar**
    * Puede tener:

        * **Metodos abstractos** (sin implementación)
        * **Metodos concretos** (con implementación)

    * Sirve como **clase base** para otras clases

* Es una clase **incompleta a proposito**

    > Define **qué debe hacer** las clases hijas  
    y **que comportamiento común ya está resuelto**

    ## Para que sirve una clase abstracta ?

    * **Compartir código común:** Evita duplicación entre clases relacionadas
    * **Forzsar estructura:** Las clases hijas debe inplementar ciertos métodos
    * **Modelar jerarquías reales:** Todo empleado calcual salario, pero cada tipo lo hace distinto.
    * **Aplicar OOP correctamente:**  Encapsulación + herencia + polimorfismo

    ## Funcionamiento

    1. Se define la **clase abastracta**
    2. Se daclara **métodos abstractos** (sin lógica)
    3. Se implementa **métodos concretos** (lógica común)
    4. Las clases hijas **extienden** la clase abstracta y completan lo que falta

    * **Nunca creas objetos de la clase abstracta,** solo de las hijas

    ## Elementos que componen una clase abstracta

    1. **Palabra clave: `abstract`** Marca la clase o el método como abstracto.
    2. **Método abstracto:** Métodos **sin cuerpo ->** obligan a implementar
    3. **Métodos concretos:** Métodos **con lógica compartida**
    4. **Atributos:** Estado común a todas las clase hijas
    5. **Herencia:** Las clases hijas **extienden** la clas abstracta

    ## Ejemplo: Java

    * **Paso 1: Definir el problema**

        * Tenemos empleados:

            * Empleado fijo
            * Empleado por horas

        * Todos:

            * Tienen nombre
            * Calculan salario (pero distinto)
            * Tienen lógica comun

    * **Paso 2: Crear la clase abstracta**

        ```java
        public abstract class Empleado {

            protected String nombre;

            // Constructor
            public Empleado(String nombre){
                this.nombre = nombre;
            }

            // Metodo abstracto (sin implementacion)
            public abstract double calcularSalario();

            // Método concreto (con implementación)
            public void mostrarNombre() {
                System.out.println("Empleado "+ this.nombre);
            }
        }
        ```
        * `abstract class` -> clase incompleta
        * `protected` -> accesible por hijas
        * `calcularSalario()` -> obligación
        * `mostrarNombre()` -> código reutilizable

    * **Paso 3: Implementar una clase hija (EmpleadoFijo)**

        ```java
        public class EmpleadoFijo extends Empleado {

            private double salarioMensual

            public EmpleadoFijo(String nombre, double salarioMensual){
                super(nombre);
                this.salarioMensual = salarioMensual;
            }

            @Override
            public double calcularSalario(){
                return salarioMensual;
            }
        }
        ```
        * `extends Empleado` -> herencia
        * `super(nombre)` -> constructor del padre
        * implementa método abstractop -> obligatorio

    * **Paso 4: Otra clase hija (EmpleadoPorHoras)**

        ```java
        public class EmpleadoPorHoras extends Empleado {
            
            private double valorHora;
            private int horas;

            public EmpleadoPorHoras(String nombre, double valorHora, int horas){
                super(nombre);
                this.valorHora = valorHora;
                this.horas = horas;
            }

            @Override
            public double calcularSalario(){
                return this.valorHora*this.horas;
            }
        }
        ```
    * **Paso 5: Usar Polimorfismo**

        ```java
        public class Main {
            public static void main(String[] args) {

                Empleado e1 = new EmpleadoFijo("Juan", 3000);
                Empleado e2 = new EmpleadoPorHoras("Ana", 160, 20);

                e1.mostrarNombre();
                System.out.println(e1.calcularSalario());

                e2.mostrarNombre();
                System.out.println(e2.calcularSalario());
            }
        }
        ```
        * Usamos el tipo `Empleado`
        * Ejecuta implementacion correcta

    ## Ejemplo: Python

    * **Paso 1: Importar ABC**

        ```python
        from abc import ABC, abstractmethod
        ```

    * **Paso 2: Crear clase Abstracta**

        ```python
        class Empleado(ABC):

            def __init__(self,nombre: str):
                self.nombre = nombre

            @abstractmethod
            def calcular_salario():
                pass

            def mostrar_nombre():
                print(f"Empleado: {self.nombre}")
        ```
        * `ABC` -> clase abstracta
        * `@abstractmethod` -> obliga a implementar
        * Puede tener métodos concretos

    * **Paso 3: Clase hija (EmpleadoFijo)**

        ```python
        class EmpleadoFijo(Empleado):

            def __init__(self,nombre: str, salario_mensual: float):
                super()__init__(nombre)
                self.salario_mensual = salario_mensual

            def calcular_salario()-> float:
                return self.salario_mensual
        ```
    
    * **Paso 4: Clase hija (EmpleadoPorHoras)**

        ```python
        class EmpleadoPorHoras(Empleado):

            def __init__(self, nombre: str, horas: int, valor_hora: float):
                super().__init__(nombre)
                self.horas = horas
                self.valor_hora = valor_hora

            def calcular_salario()-> float:

                return self.valor_hora*self.horas
        ```
    
    * **Paso 5: Uso**

        ```python
        e1 = EmpleadoFijo("Juan", 3000)
        e2 = EmpleadoPorHoras("Ana", 160, 20)

        e1.mostrar_nombre()
        print(e1.calcular_salario())

        e2.mostrar_nombre()
        print(e2.calcular_salario())
        ```

    ## Clase Abstracta vs Interfaz

    | Clase abstracta   | Interfaz             |
    | ----------------- | -------------------- |
    | Tiene lógica      | No tiene lógica      |
    | Tiene atributos   | No tiene estado      |
    | Una sola herencia | Múltiples interfaces |
    | Base común        | Contrato puro        |

    * **Regla práctica**

        * **Interfaz** -> *que se hace*
        * **Abstracta** -> *que se hace + parte de como*
    
    ## Relación directa con Spring Boot y FastAPI

    * **Spring Boot**

        * `AbstractController`
        * `AbstractService`
        * `JpaRepository` (mezcla interfaz + base abstracta)

    * **FastAPI**

        * Servicios base abstractos
        * Repositorios abstractos
        * Casos de uso (Clean Architure)

    ## Resumen

    * Clase abstracta = clase incompleta
    * No se pude instanciar
    * Puede tener lógica y contratos
    * Obliga a implementar métodos
    * Facilita reutilización y polimorfismo

    