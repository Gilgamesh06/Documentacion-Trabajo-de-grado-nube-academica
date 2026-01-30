# Open/Closed Principle (OCP)

* El **OCP** es el **segundo principio de SOLID** y dice:

    > Las entidades de software deben estar abiertas para extensión, pero cerradas para modificación

* **Abierta para extensión** -> puede agregar nuevo comportamiento
* **Cerrada para modificación** -> no tienes que cambiar el código que ya funciona 

    > Agrega nuevas funcionalidades nuevas Sin tocar código existente

    ## Para que sirve OCP?

    * Evitar romper código estable
    * Reducir bugs al agregar nuevas funcinalidades
    * Facilitar mantenimiento
    * Hacer el sistema escalable
    * Eliminar grandes bloques de `if / else`

    ## El problea que soluciona OCP

    * **Codigo sin OCP**

        * Cada vez que aparece algo nuevo:

            * Modificas la misma clase
            * Agregas otro `if`
            * El código crece sin control

        * Esto es **alto riesgo de errores**

    ## Funcionamiento

    * OCP se logra usando:

        * **Abstracciones (interfaces / clases abstractas)**
        * **Polimorfismo**
        * **Composición**
        * (a veces) **inyección de dependencias**

    * La lógica principal:

        > *Programar contra interfaces, no contra implementaciones*

    ## Elementos que componen OCP

    1. **Abstracción**

        * Inteface o clase abstracta
        * Define **que se hace**, no **como**

    2. **Implementaciones concretas**

        * Clases que extienden o implementan la abstracción
        * Contiene el comportamiento especifico

    3. **Polimorfismo**

        * El código usa la abstracción
        * No le importa la clase concreta

    4. **Extensión (no modificación)**

        * Para agregar algo nuevo:

            * Se crea una nueva clase
            * No se toca el código existente

    ## Ejemplo: Java sin OCP

    * **Escenario**

        * Sistema de pagos:

            * Targeta
            * PayPal
            * Transferencia (nueva)

    * **Código incorrecto**

        ```java
        public class PaymentService {

            public void pagar(String tipo, double monto){
                if (tipo.equals("TARGETA")){
                    System.out.println("Pagando con targeta:" + monto);
                }else if (tipo.equals("Paypal")) {
                    System.out.println("Pagando con Paypal:" + monto);
                }
            }
        }
        ```
        * **Problemas**

            * Para agregar transferencia -> modifcar la clase
            * Cada cambio es riesgoso
            * Violación directa de OCP

    ## Ejemplo: Java con OCP

    * **Paso 1: Crear la abstracción**

        ```java
        public interface PaymentMethod {

            public void pagar(double monto);
        }
        ```
    
    * **Paso 2: Implementaciones concretas**

        * **Targeta**

            ```java
            public class TargetaPayment implements PaymentMethod {

                @Override
                public void pagar(double monto){
                    System.out.println("Pagando con targeta: "+ monto);
                }
            }
            ```
        
        * **PayPal**

            ```java
            public class PaypalPayment implements PaymentMethod {

                @Override
                public void pagar(double monto){
                    System.out.println("Pagando con PayPal: " + monto);
                }
            }
            ```

    * **Paso 3: Servicio principal (cerrado a cambios)**

        ```java
        public class PaymentService {
            public void procesarPago(PaymentMethod metodo, double monto){
                metodo.pagar(monto);
            }
        }
        ```
    
    * **Paso 4: Agregar Nuevo metodo (Extensión)**

        ```java
        public class TransferenciaPayment implements PaymentMethod {

            @Override 
            public void pagar(double monto){
                System.out.println("Pagando con transferencia: " + monto);
            }
        }
        ```
        * **Resultado**

            * `PaymentService` no cambia
            * Solo se **extiende** el sistema

    ## Ejemplo: Python Sin OCP

    ```python
    class PaymentService:
        def pagar(self, tipo: str, monto: float):
            if tipo == "targeta":
                print(f"Pagando con targeta: {monto}")
            elif tipo  == "paypal":
                print(f"Pagando con PayPal: {monto}")
    ```
    * Cada método nuevo = modificar la clase

    ## Ejemplo: Python con OCP

    * **Paso 1: Abstracción**

        ```python
        from abc import ABC, abstractmethod

        class PaymentMethod(ABC):

            @abstractmethod
            def pagar(self, monto):
                pass
        ```
    
    * **Paso 2: Implementaciones**

        ```python
        class TargetaPayment(PaymentMethod):
            def pagar(self, monto: float):
                print(f"Pagando con trajeta: {monto}")
        
        class PaypalPayment(PaymentMethod):
            def pagar(self, monto: float):
                print(f"Pagando con PayPal: {monto}")
        ```

    * **Paso 3: Servicio principal**

        ```python
        class PaymentService:

            def procesar_pago(self, metodo: PaymentMethod, monto: float):
                metodo.pagar(monto)
        ```

    * **Paso 4: Nueva extensión**

        ```python
        class TransferenciaPayment(PaymentMethod):
            def pagar(self, monto):
                print(f"Pagando por transferencia: {monto}")
        ```

    ## Relación con frameworks

    * **Spring Boot**

        * Interfaces + `@Service`
        * Beans intercambiables
        * Strategy Pattern (muy comun)

    * **FastAPI**

        * Clases de servicio intercambiables
        * Dependencias inyectadas
        * Estrategias por implementación

    ## Resumen
    
    * Piensa OCP asi:

        * Elimina `if/else` por tipo
        * Utiliza polimorfismo
        * No modifica código estable
        * Agrega nuevas clases

    > *Si para agregar algo nuevo tienes que tocas código viejo... probablemente estás violando OCP*

    