# Interface Segregation Principle (ISP)

* El **ISP** es el **cuarto principio de SOLID** y dice:

    > Ningun cliente deberia verse obligado a depender de métodos que no utiliza

* En palabras simples:

    * Mejor muchas interfaces pequeñas y especificas, que una interfaz grande y generica

    ## Para que sirve ISP ?

    * Evitar interface *gordas*
    * Reducir acoplamiento
    * Evitar métodos vacíos o con `thorw`
    * Mejorar legibilidad del código
    * Facilitar mantenimiento
    * Hacer el sistema más flexible

    ## El problema que soluciona ISP

    * **Problema común**

        * Una sola interfaz intenta cubrir **muchos comportamientos distintos**

    * **Resultado**

        * Clases obligadas a implementar métodos que no necesitan
        * Métodos vaciós
        * Excepciones del tipo `UnsupportedOperationException`
    
    * **Diseño forzado y frágil**

    ## Funcionamiento

    * ISP funciona **dividiendo responsabilidades** (relación con SRP)

        * **Regla mental**

            > *Una interfaz debe representar un solo rol o capacidad*

        * Si una clase no puede implementar todos los métodos **sin hacer trampas,** esa interfaz esta mal diseñada

    ## Elementos que componen ISP

    1. **Interfaces pequeñas y cohesivas**

        * Cada interfaz representa una capacidad concreta
        * Métodos altamente relacionados

    2. **Cliente especificos**

        * Cada cliente depende solo de lo que necesita

    3. **Composición de interfaces**

        * Una clase puede implementar varias interfaces pequeñas

    4. **Bajo acoplamiento**

        * Cambios en una interfaz no afecta a clases que no la usan

    ## Ejemplo: Java Sin ISP

    * **Escenario**

        * Sistema de empleados

            * Programadores
            * Diseñadores
            * Conductores
        
    * **Interfaz incorrecta**

        ```java
        public interface Empleado {

            void programar();
            void diseñar();
            void conducir();
        }
        ```

    * **Implementacón forzada**

        ```java
        public class Programador implements Empleado {

            @Override 
            public void programar() {
                System.out.println("Programador");
            }

            @Override
            public void diseñar() {
                // No aplica
            }

            @Override
            public void conducir(){
                // No aplcia
            }
        }
        ```
        * Metodos vacios
        * Violacion clara de ISP

    ## Solución con ISP (Java)

    * **Paso 1: Segregar interfaces**

        ```java
        public interface Programador{
            void programar();
        }

        public inteface Diseñador {
            void diseñar();
        }

        public interface Conductor {
            void conducir();
        }
        ```

    * **Paso 2: Implementaciones limpias**

        ```java
        public class ProgramadorBackend implements Programador {
            public void programar() {
                System.out.println("Programando backend");
            }
        }

        public class DiseñadorUI implements Diseñador {
            public void diseñar() {
                System.out.println("Diseñando interfaz");
            }
        }

        public class Chofer implements Conductor {
            public void conducir() {
                System.out.println("Conduciendo vehículo");
            }
        }
        ```

    * **Paso 3: Composición si es necesario**

        ```java
        public class PorgramadorFullStack implements Programador, Diseñador {
            public void programar(){
                System.out.println("Programando");
            }

            public void diseñar(){
                System.out.println("Diseñando");
            }
        }
        ```
        * Cada clase implementa solo lo que necesita

    ## Ejemplo: Python Sin ISP

    ```python
    from abc import ABC, abstractmethod

    class Empleado(ABC):

        @abstractmethod
        def programar(self):
            pass

        @abstractmethod
        def diseñar(self):
            pass

        @abstractmethod
        def conducir(self):
            pass
    ```

    * **Implementacion forzada**

        ```python
        class Programador(Empleado):

            def programar(self):
                print("Programando")

            
            def diseñar(self):
                pass  # No aplica

            def conducir(self):
                pass  # No aplica    
        ```
    * Violación directa de ISP

    ## Solución con ISP (Python)

    * **Paso 1: interface pequeñas**

        ```python
        from abc import ABC, abstractmethod

        class Programador(ABC):

            @abstractmethod
            def programar(self):
                pass
        
        class Diseñador(ABC):

            @abstractmethod
            def diseñador(self):
                pass

            @abstractmethod
            def conducir(self):
                pass
        ```

    * **Paso 2: Implementaciones claras**

        ```python
        class ProgramadroBackend(Programador):
            def programar(self):
                print("Programando backend")

        class DiseñadorUI(Diseñador):
            def diseñar(self):
                print("Disenando UI")

        class Chofer(Conductor):
            def conducir(self):
                print("Conduciendo vehiculo")
        ```

    * **Paso 3: Implementacion múltiple**

        ```python
        class ProgramadorFullStack(Programador, Diseñador):
            def programar(self):
                print("Programando")

            def diseñar(self):
                print("Diseñando")
        ```

    ## Reglas prácticas para aplicar ISP

    * Si una clase implementa métodos vacios -> mala señal
    * Si una interfaz tiene muchos metodos -> revisala
    * Divide por **roles**, no por entidades
    * Interfaces pequeñas -> código flexible

    > *Una clase no deberia pagar el precio de métodos que no usa*

    ## Relación con otros principios

    * **SRP** -> divide responsabilidades
    * **ISP** -> divide contratos
    * **LSP** -> garantiza sustitución correcta
    * **OCP** -> permite extender sin romper