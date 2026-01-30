# Polimorfismo

* **Polimorfismo** significa literalmente:

    > Muchas formas

* En OOP es la capacidad de:

    > Usar **un mismo método o referencia**  
    para ejecutar **comportamientos distintos,**  
    dependiendo del **objeto real**

* Mismo mensaje -> diferente comportamiento

    ## Para que sirve el polimorifismo ?

    * **Escribir código flexible:** No depende de clases concretas
    * **Reducir `if / else`:** Evita condicionales gigantes
    * **Facilitar mantenimiento** Agregas nuevas clases sin tocar el código existente
    * **Aplicar Open/Closed (Solid):** Abierto a extensión, cerrado a modificación
    * **Base de frameworks modernos:** Spring, FastAPI, microservicios

    ## Funcionamiento

    1. **Abstracción** (interfaces, o clases abstractas)
    2. **Herencia / implementacion**
    3. **Sobreescritura de métodos**

    * El código usa el **tipo padre,** pero se ejcuta el **método de la clase hija**

    ## Elementos que componen el polimorfismo

    1. **Tipo base**

        * Interfaz
        * Clase abstracta
        * Clase padre

    2. **Clases concretas:** Implementan o exienden el tipo base
    3. **Métodos sobrescritos:** Cada clase define su comportamiento
    4. **Enlace dinámico:** La decisión del método se hace **en tiempo de ejecución**

    ## Ejemplo: Java

    * **Paso 1: Definir el problema**

        * Tenemos distintos **Tipo de pago**

            * Targeta 
            * PayPal

        * Todos: 

            * Procesa pago
            * Pero diferente

    * **Paso 2: Crear la abstracción**

        ```java
        public interface MetodoPago {
            void pagar(double monto);
        }
        ```

    * **Paso 3: Implementación concreta (Targeta)**

        ```java
        public class PagoTargeta implements MetodoPago {

            public PagoTargeta(){
            }

            @Override
            public void pagar(double monto){
                System.out.println("Pago con targeta: $" + monto);
            }
        } 
        ```

    * **Paso 4: Implementación concreta (PayPal)**

        ```java
        public class PagoPaypal implements MetodoPago {

            @Override
            public void pagar(double monto) {
                System.out.println("Pago con PayPal: $" + monto);
            }
        }
        ```

    * **Paso 5: Usar polimorifsmo**

        ```java
        public class ProcesadorPago {

            public void procesar(MetodoPago metodo, double monto) {
                metodo.pagar(monto);
            }
        }
        ```
    * **Paso 6: Ejecución**

        ```java
        public class Main {
            public static void main(String[] args) {

                ProcesadorPago procesador = new ProcesadorPago();

                MetodoPago pago1 = new PagoTarjeta();
                MetodoPago pago2 = new PagoPaypal();

                procesador.procesar(pago1, 100);
                procesador.procesar(pago2, 200);
            }
        }
        ```
        * **Aqui ocurre el polimorfismo**

            * El tipo es `MetodoPago`
            * El comportamiento cambia

    ## Ejemplo: Python

    * **Paso 1: Crear abstracción**

        ```python
        from abc import ABC, abstractmethod

        class MetodoPago(ABC):

            def __init__(self):

            @abstractmethod
            def pagar(self, monto):
                pass
        ```

    * **Paso 2: Implementacion concreta (Targeta)**

        ```python
        class PagoTarjeta(MetodoPago):

            def pagar(self, monto):
                print(f"Pago con tarjeta: ${monto}")
        ```

    * **Paso 3: Implementación concreta (PayPal)**

        ```python
        class PagoPaypal(MetodoPago):

        def pagar(self, monto):
            print(f"Pago con PayPal: ${monto}")
        ```

    * **Paso 4: Uso polimórfico**

        ```python
        def procesar_pago(metodo: MetodoPago, monto):
            metodo.pagar(monto)

        procesar_pago(PagoTarjeta(), 100)
        procesar_pago(PagoPaypal(), 200)
        ```
    
    ## Polimorifsmo en tiempo de compilación vs ejecución

    * **Java**

        * **Compilación** -> overloading
        * **Ejecución** -> overriding (el importante)

    * **Python**

        * Todo es **dinámico** (duck typing)

    ## Polimorifismo real en frameworks

    * **Spring Boot**

        ```java
        @Service
        public class EmailService implements NotificacionService {}
        ```
        * Spring inyecta la implementación concreta

    * **FastAPI**

        ```python
        def servicio(repo: UsuarioRepository = Dependes()):
            repo.guardar(usuario)
        ```
    
    ## Error común

    * Usar `if tipo == ...`
    * No usar interfaces
    * Romper Open/Closed

    * EL polimorfismo **elimina condicionales**

    ## Resumen 

    > **Polimorfismo = mismo mensaje, distinto comportamiento**

    * Flexibilidad
    * Escalabilidad
    * Código limpio
    * Arquitectura sólida

    