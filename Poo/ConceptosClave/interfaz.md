# Interfaz 

* Una **interfaz** es un **contrato** que define **que metodos deben tener una clase**, pero **No como se implementan**

* La interfaz dice:

    > Si tú implementas esta interfaz, **estás obligado** a implementar estos métodos.

* No tiene lógica interna (reglas generales)
* Solo define **que se puede hacer**

* Ejemplo conceptual

    > Un Pago puede **procesarse**,  
    pero **Cada forma de pago lo hace distinto**

    ## Para que sirve una intefaz ?

    * **Desacoplar el código:** Tu código depende de **contratos**, no de clases concretas
    * **Permite múltiples implementaciones:** Una intefaz -> muchas clases
    * **Aplicar polimorfismo:** Puedes usa distintos objeto sin cambiar el código cliente
    * **Facilitar mantenimiento y pruebas:** Cambiaas implementaciones sin rompar nada
    * **Base de Spring Boot, FastAPI, Clean Architecture:** Repositorios, servicios, puertos, adapatadores.

    ## Funcionamiento

    1. **Se define la interfaz (contrato):** Define métodos sin implementación
    2. **Una clase implementa la intefaz:** La clase **está obligada** a implementar todos los métodos.
    3. **Se usa la interfaz como tipo:** El código usa la interfaz, no la clase concreta.

    ## Elementos que componen una interfaz.

    1. **Métodos abastractos:** Metodos **sin cuerpo**

        ```java
        void procesarPago();
        ```
    
    2. **Firma del metodo:** Nombre + parámetros + retorno

    3. **(Java) Modificadores implícitos**

        * En Java:

            * Metodos -> `public abstract`
            * Atributos -> `public static final`

    4. **Implementaciones:** Clases que implementan la interfaz

    ## Ejemplo: Java

    *  **Paso 1: Definir el problema**

        * Queremos un sistema de **pagos**

            * Target
            * Paypal
            * Trasferencia

        * Todos **procesan un pogo**, pero diferente

    * **Paso 2: Crear la interfaz (Contrato)**

        ```java
        public interface MetodoPago(){

            void procesarPago(double monto);
        }
        ```   
        * `interface` -> define un contrato
        * `procesarPago` -> obligación
        * No hay cuerpo `{}` -> solo definición

    * **Paso 3: Implementar la interfaz (Targeta)**

        ```java
        public class PagoTargeta implements MetodoPago {

            @Override
            public void procesarPago(double monto){
                system.out.println("Porcesando pago con targeta por $" + monto):
            }
        }
        ```
        * `implements MetodoPago` -> acepta el contrato
        * `@Override` -> indica que implementa el método
        * Si no implementas el método -> error de compilación

    * **Paso 4: Otra implementacion (PayPal)**

        ```java
        public class PagoPaypal implements MetodoPago{

            @Override
            public void metodoPago(double monto){
                System.out.println("Procesando pago con PayPal por $" + monto);
            }
        }
        ```

    * **Paso 5: Usar la interfaz (Polimorfismo)**

        ```java
        public class Main {
            public static void main(String[] args) {

                MetodoPago pago1 = new PagoTarjeta();
                MetodoPago pago2 = new PagoPaypal();

                pago1.procesarPago(100);
                pago2.procesarPago(200);
            }
        }
        ```
        * El tipo es `MetodoPago`
        * La implementación cambia
        * El código No se rompe

    ## Ejemplo: Python

    * **Paso 1: Importar ABC**

        ```python
        from abs import ABC, abstractmethod
        ```

    * **Paso 2: Definir la interfaz**

        ```python
        class MetodoPago(ABC):

            @abstractMethod
            def procesar_pago(self, monto: float):
                pass
        ``` 
        * `ABC` -> clase base abstracta
        * `@abstractmethod` -> obliga a implementar
        * `pass` -> sim implementación

    * **Paso 3: Implementar la interfaz (Targeta)**

        ```python
        class PagoTargeta(MetodoPago):

            def procesar_pago(self, monto: float):
                print(f"Procesando pago con tarjeta por ${monto}")
        ```
    
    * **Paso 4: Otra implementacion (PayPal)**

        ```python
        class PagoPaypal(MetodoPago):

            def procesar_pago(self, monto: float):
                print(f"Procesando pago con PayPal por ${monto}")
        ```
         
    * **Paso 5: Usar polimorfismo**

        ```python
        def ejecutar_pago(metodo: MetodoPago, monto):
            metodo.procesar_pago(monto)

        ejecutar_pago(PagoTargeta(), 100)
        ejecutar_pago(PagoPaypal(), 200)
        ```

    ## Relación directa con Spring Boot y FastAPI 

    * **Spring Boot**

        ```java
        public interface UsuarioRepository {
            Usuario save(Usuario usuario);
        }
        ```
        ```java
        @Repository
        public class UsuarioRepositoryImpl implements UsuarioRepository { }
        ```
        * Spring intecta la implementacion automaticamente

    * **FastAPI**

        ```python
        class UsuarioRepository(ABC):
            @abstractmethod
            def guardar(self, usuario): ...
        ```
    
    ## Interfaz vs Clase abstrata

    | Interfaz                | Clase Abstracta       |
    | ----------------------- | --------------------- |
    | Solo contrato           | Puede tener lógica    |
    | Múltiple implementación | Solo una herencia     |
    | Más desacoplada         | Más acoplada          |
    | Ideal para arquitectura | Ideal para base común |

    ## Resumen final

    * Interfaz = contrato
    * Define que se hace
    * Obliga a implementar métodos
    * Pemite polimorfismo
    * Base de arquitecturas modernas

