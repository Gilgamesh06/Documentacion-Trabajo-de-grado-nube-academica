# Ejemplo: SpringBoot

* Un sistema de pedidos que:

    1. Procesa un pago
    2. Guarda el pedido
    3. Envia una notificación

* Usaremos:

    * `@Service`
    * `@Repository`
    * Interfaces
    * Inyección de depndencias por constructor

    ## Estructura del proyecto

    ```bash
    com.example.pedidos
    │
    ├── controller
    │   └── PedidoController
    │
    ├── service
    │   ├── PedidoService
    │   └── impl
    │       └── PedidoServiceImpl
    │
    ├── repository
    │   ├── PedidoRepository
    │   └── impl
    │       └── MySQLPedidoRepository
    │
    ├── payment
    │   ├── PaymentMethod
    │   └── impl
    │       ├── TarjetaPayment
    │       └── PaypalPayment
    │
    └── notification
        ├── NotificationService
        └── impl
            └── EmailNotificationService
    ```
    * **Esta estructura ya aplica SOLID**, incluso antes de escribri codigo


    ## 1. SRP -- Single Responsability Principle

    * Spring Boot **empuja SRP de forma natural** con sus estereotipos

        * `@Controller` -> Manejar HTTP
        * `@Service` -> Lógica de negocio
        * `@Repository` -> Persistencia

    * **Codigo**

        * **Controlador (Solo HTTP)**

            ```java
            @RestController
            @RequestMapping("/pedidos")
            public class PedidoController {

                private final PedidoService pedidoService;

                public PedidoController(PedidoService pedidoService){
                    this.pedidoService = pedidoService;
                }

                @PostMapping
                public String crearPedido(@RequestParam double monto){
                    pedidoService.procesarPedido(monto);
                    return "Pedido Creado";
                }
            }
            ```
            * **SRP aplicado**

                * El controlador No sabe cómo se paga
                * No guarda en DB
                * Solo maneja HTTP

            * **Beneficio:** Cambios en neogico no rompen la API

    ## 2. OCP -- Open/Closed Priciple

    * Spring Boot:

        * Trabaja con **interfaces**
        * Permite **múltiples implementaciones**
        * Cambias comportamiento **sin tocar código existente**

    * **Intefaz de pago**

        ```java
        public interface PaymentMethod {
            void pagar(double monto);
        }
        ```

    * **Implementacion 1**

        ```java
        @Component
        public class TargetPayment implements PaymentMethod {
            public void pagar(double monto){
                System.out.println("Pagando con targeta: " + monto);
            }
        }
        ```

    * **Implementación 2 (Nueva funcionalidad)**

        ```java
        @Component 
        public class PaypalPayment implements PaymentMethod {
            public void pagar(double monto){
                System.out.println("Pagando con PayPal: " + monto)
            }
        }
        ```
        * **OCP aplicado**

            * Agregamos PayPal
            * No tocamos `PedidoService`

    * **Beneficio:** Sistema extensible sin riesgo

    ## 3. LSP -- Liskov Substitution Principle

    * Spring:

        * Inyecta beans por **tipo**
        * Confia en que ***todas las implementaciones cumple el contrato**

    * Si una implementación romple el contrato -> el sistema falla

    * **Uso Polimórfico**

        ```java
        @Service
        public class PedidoServiceImpl implements PedidoService {

            private final PaymentMethod paymentMethod;

            public PedidoServiceImpl(PaymentMethod paymentMethod){
                this.paymentMethod = paymentMethod;
            }

            public void procesarPedido(double monto){
                this.paymentMethod.pagar(monto);
            }
        }
        ```
        * **LSP aplicado**

            * `PedidoServiceImpl` **no sabe** si es targeta o paypal
            * Ambas implementaciones **se comportan correctamente**

        * **Beneficio:** polimorfismo seguro y predecible

    ## 4. ISP -- Interface Segregation Principle

    * Spring **fomenta interfaces pequeñas**

        * Mala practica

            ```bash
            PedidoSystem {
                pagar()
                guardar()
                notificar()
            }
            ```
        * Buena practica (Spring-style)

    * **Repository**

        ```java
        public interface PedidoRepository {
            void guardar();
        }
        ```

    * **Notificación**

        ```java
        public interface NotificationService {
            void notificar(String mensaje);
        }
        ```
        * **ISP aplicado**

            * Cada interfaz representa **un rol**
            * Ninguna clase implementa métodos inútiles 

        * **Beneficio:** Implementaciones limpias y fáciles de mantener

    ## 5. DIP -- Dependency Inversion Principle

    * Spring Boot **esta construido sobre DIP**

        * Inyección de dependencias
        * IoC Container
        * Beans gestionados por interfaces

    * **Servicio principal**

        ```java
        @Service
        public class PedidoServiceImpl implements PedidoService {

            private final PedidoRepository repository;
            private final PaymentMethod paymentMethod;
            private final NotificationService notificationService;

            public PedidoServiceImpl(
                    PedidoRepository repository,
                    PaymentMethod paymentMethod,
                    NotificationService notificationService
            ) {
                this.repository = repository;
                this.paymentMethod = paymentMethod;
                this.notificationService = notificationService;
            }

            @Override
            public void procesarPedido(double monto) {
                paymentMethod.pagar(monto);
                repository.guardar();
                notificationService.notificar("Pedido procesado");
            }
        }
        ```
        * **DIP aplicado**

            * El servicio depende de **interfaces**
            * Spring inyecta implementaciones concretas
        
        > Spring invierte las dependencias por ti

        * **Beneficio**
            
            * Bajo acoplamiento
            * Testing fácil
            * Cambio de BD, pagos o notificaciones sin tocar negocio

        ### Repositorio (persistencia)

        ```java
        @Repository
        public class MySQLPedidoRepository implements PedidoRepository {
            public void guardar(){
                System.out.println("Pedido guardado en MySQL");
            }
        }
        ```

        ### Notificación

        ```java
        @Component
        public class EmailNotificationService implements NotificationService {
            public void notificar(String mensaje) {
                System.out.println("Email enviado: " + mensaje);
            }
        }
        ```

    ## Resumen

    | Principio | Cómo Spring lo aplica                    |
    | --------- | ---------------------------------------- |
    | SRP       | `@Controller`, `@Service`, `@Repository` |
    | OCP       | Interfaces + múltiples beans             |
    | LSP       | Inyección polimórfica                    |
    | ISP       | Interfaces pequeñas                      |
    | DIP       | IoC Container + DI                       |
