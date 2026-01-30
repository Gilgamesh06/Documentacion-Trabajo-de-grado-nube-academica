# Ejemplo: Aplicando los 5 Principios de SOLID

* **Escenario del sistema**

    * Un **sistema de pedidos** uqe:

        1. Crea un pedido
        2. Lo guarda en una base de datos
        3. Procesa el pago
        4. Enviar una notificación

    * Queremos que el sistema sea:
    
        * Extensible
        * Testeable
        * Desacoplado
        * Mantenible

    ## Problema inicial (Sin SOLID)

    * Una sola clase que hace todo:

        ```bash
        PedidoService
        - valida pedido
        - guarda en DB
        - procesa pago
        - envia email
        ```
        * Muchas responsabilidades
        * Dificil de cambiar
        * Imposible de testear bien

    * Ahora vamos a cosntruirlo **BIEN**, usando SOLID

    ## Soluciíon con SOLID (Arquitectura)

    ```bash
    PedidoService (orquesta)
    │
    ├── PedidoRepository (interface)
    │       └── MySQLPedidoRepository
    │
    ├── PaymentMethod (interface)
    │       ├── TarjetaPayment
    │       └── PaypalPayment
    │
    └── NotificationService (interface)
            └── EmailNotification
    ```

    ## 1. SRP -- Single Responsability Principle

    * **Cada clase una responsabilidad**

        * `PedidoService` -> Orquestar el flujo
        * `PedidoRepository` -> Persistencia
        * `PaymentMethod` -> Pago
        * `Notificación` -> Notificación

    * **Beneficio**

        * Clases pequeñas
        * Cambios aislados
        * Código fácil de entender

    ## 2. OCP -- Open/Closed Principle

    * Usamos **Interfaces** para permitir extensiones

        * Nuevos métodos de pago -> **nuevas clases**
        * Nuevas notificaciones -> **nuevas clases**
        * No se modifica `PedidoService`

    * **Beneficio**

        * Sistem extensible
        * Menos bugs al agregar funcionalidades
        
    ## 3. LSP -- Liskov Substitution Principle

    * **Todas las implementaciones pueden sustituirse sin romper nada**

    * Ejemplo:

        ```bash
        PaymentMethod
        ├── TarjetaPayment
        └── PaypalPayment
        ```
        * Ambas **se comportan como un método de pago válido**

    * **Beneficio**

        * Polimorifismo seguro
        * Sin comportamientos inesperados

    ## 4. ISP -- Interface Segregation Principle

    * Interfaces **pequeñas y especificas**

    * No existe una interfaz gigante tipo:

        ```text
        PedidoSystem
        - pagar()
        - guardar()
        - notificar()
        ```
    * **Interfaces separadas**

        * `PedidoRepository`
        * `PaymentMethod`
        * `NotificacionService`

    * **Beneficio**

        * Implementaciones limpias 
        * Sin metodos innecesarios

    ## 5. DIP -- Dependency Inversion Principle

    * `PedidoService` depende de **interfaces** y no de clases concretas

    ```bash
    PedidoService -> Interfaz <- Implementaciones
    ```

    * **Beneficio**

        * Bajo acoplamiento
        * Testing Facil
        * Cambio de tecnológia sin dolor

    
    ## Implementación en Java

    1. **Abstacciones (DIP + OCP + ISP)**

        ```java
        public interface PedidoRepository {
            void guardar()
        }

        public interface PaymentMethod {
            void pagar(double monto);
        }

        public interface NotificationService{
            void notificar(String mensaje);
        }
        ```

    2. **Implementacions concretas (LSP)**

        ```java
        public class MySQLPedidoRepository implements PedidoRepository{

            public void guardar(){
                System.out.println("Pedido guardado en MySQL");
            }
        }

        public class TargetaPayment implements PaymetMethod {
        
            public void pagar(double monto){
                System.out.println("Pagando con targeta: " + monto);
            }
        }

        public class EmailNotification implements NotificationService {

            public void notificar(String mensaje){
                System.out.println("Email enviando: " + mensaje);
            }
        }
        ```
    
    3. **Servicio principal (SRP + DIP)**

        ```java
        public class PedidoService {

            private final PedidoRepository repository;
            private final PaymentMethod payment;
            private final NotificationService notification;

            public PedidoService(
                PedidoRepository repository,
                PaymentMethod payment,
                NotificationService notification
            ){
                this.repository = repository
                this.payment = payment
                this.notification = notification;
            }

            public void procesarPedido(double monto){
                payment.pagar(monto);
                repository.guardar();
                notification.notificar("Pedido procesado correctamente");
            }
        }
        ```
    
    4. **Uso del sistema**

        ```java
        public class Main {
            public static void main(String[] args){

                PedidoRepository repo = new MySQLPedidoRepository();
                PaymentMethod pago = new TargetPayment();
                NotificationService notif = new EmailNotification();

                PedidoServie service = new PedidoService(repo, pago, notif);

                service.procesarPedido(150.0);
            }
        }
        ```

    ## Implemantación en Python

    1. **Abstracciones**

        ```python
        from abc import ABC, abstractmethod

        class PedidoRepository(ABC):

            @abstractmethod
            def guardar(self):
                pass
        
        class PaymentMethod(ABC):

            @abstractmethod
            def pagar(self, monto: float):
                pass
        
        class NotificationService(ABC):

            @abstractmethod
            def notificar(self, mensaje:str):
                pass
        ```

    2. **Implementaciones**

        ```python
        class MySQLPedidoRepository(PedidoRepository):
            def guardar(self):
                print("Pedido guardado en MySQL")

        class TarjetaPayment(PaymentMethod):
            def pagar(self, monto:float):
                print(f"Pagando con tarjeta: {monto}")

        class EmailNotification(NotificationService):
            def notificar(self, mensaje:str):
                print(f"Email enviado: {mensaje}")
        ```

    3. **Servicio Principal**

        ```python
        class PedidoService:

            def __init__(self, repository: PedidoRepository, payment: PaymentMethod, notification: NotificationService):
                self.repository = repository
                self.payment = payment
                self.notification = notification
            
            def procesar_pago(slef, monto:float):
                self.payment.pagar(monto)
                self.repository.guardar()
                self.notification.notificar("Pedido procesado correctamente")
        ```

    4. **Uso**

        ```python
        repo = MySQLPedidoRepository()
        pago = TargetaPayment()
        notif = EmailNotification()

        service = PedidoService(repo, pago, notif)
        service.procesar_pag(150.0)
        ```

    ## Resumen

    | Principio | Qué hicimos                 | Beneficio           |
    | --------- | --------------------------- | ------------------- |
    | SRP       | Clases pequeñas             | Código claro        |
    | OCP       | Interfaces                  | Sistema extensible  |
    | LSP       | Implementaciones coherentes | Polimorfismo seguro |
    | ISP       | Interfaces específicas      | Limpieza            |
    | DIP       | Inyección de dependencias   | Bajo acoplamiento   |
