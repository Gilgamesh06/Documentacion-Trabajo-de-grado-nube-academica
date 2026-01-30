# Ejemplo: FastAPI

* Vamos a modelar algo simple pero real:

    * Crear pedidos
    * Guardarlos
    * Enviar notificación
    * Mantener el sistema extensible

    ## Arquitectura propuesta (muy FastAPI-Style)

    ```bash
    app/
    ├── main.py
    ├── api/
    │   └── order_controller.py
    ├── domain/
    │   ├── models.py
    │   ├── repositories.py
    │   ├── services.py
    │   └── notifications.py
    ├── infrastructure/
    │   ├── memory_repository.py
    │   └── email_notification.py
    └── dependencies.py
    ```
    * Esto **no es casualidad:** FastAPI *empuja* a usar SOLID


    ## 1. SRP -- Single Responsability Principle

    > *Una clase debe tener una sola razón para cambiar*

    * **Violación (lo que No hacemos)**

        ```python
        class OrderManager:

            def create_order(self, data):
                ...

            def save_to_db(self, order):
                ---

            def send_email(self, order):
                ....
        ```
        * Demasiadas responsabilidades

    * **Aplicacion correcta en FastAPI**

        * `domain/models.py`

            ```python
            from pydantic import BaseModel

            class Order(BaseModel):

                id: int
                product: str
                quantity: int
            ```
            * **Responsabilidad:** Solo representar datos
            * SRP aplicado

        * `domain/repositories.py`

            ```python
            from abc import ABC, abstractmethod
            from domain.models import Order

            class OrderRepository(ABC):

                @abstractmethod
                def save(self, order: Order):
                    pass
            ```
            * Solo persistencia
            * SRP aplicado

        * `domain/notifications.py`

            ```python
            from abc import ABC, abstractmethod
            from domain.models import Order

            class  NotificationService(ABC):

                @abstractmethod
                def notify(self, order: Order):
                    pass
            ```
            * Solo notificar
            * SRP aplicado

        * `domain/services.py`

            ```python
            from domain.models import Order
            from domain.repositories import OrderRepository
            from domain.notifications import NotificationService

            class OrderService:

                def __init__(
                    self,
                    repository: OrderRepository,
                    notifier: NotificationService
                ):
                    self.repository = repository
                    self.notifier = notifier

                def create_order(self, order: Order):
                    self.repository.save(order)
                    self.notifier.notify(order)
            ```
            * Orquesta la lógica de negocio
            * SRP aplicado

    ## 2. OCP -- Open/Closed Principle

    > Abierto a extensión, cerrado a modificación

    * **Violación tipica**

        ```python
        if type == "email":
            send_email()
        elif type == "sms":
            send_sms()
        ```
        * Cada nuevo método -> modificar código

    * **Aplicación con polimorfismo**

        * `infraestructure/emai_notification.py`

            ```python
            from domain.notifications import NotificationService
            form domain.models import Order

            class EmailNotification(NotificationService):

                def notify(self, order: Order):
                    print(f"Email enviado para pedido {order.id}")
            ```
        * Si mañana quieres WhatsApp

            ```python
            class WhatsAppNotification(NotificationService):

                def notify(self, order: Order):
                    print(f"WhatsApp Enviado")
            ```
        * **No tocamos** `OrderService`
        * OCP aplicado

    ## 3. LSP -- Liskov Substitution Principle

    > *Las subclases deben poder reemplazar a la clase base sin romper el sistema*

    * `infraestructure/memory_repository.py`

        ```python
        from domain.repositories import OrderRepostory
        from domain.models import Order
        
        class InMemoryOrderRepository(OrderRepository):

            def __init__(self):
                self.db = []

            def save(self, order: Order):
                self.db.append(order)
        ```

    * **Reamplazable por otro repo**

        ```python
        class PostgreSQLOrderRepository(OrderRepository):
            def save(self, order: Order):
                print("Guardando en PostgreSQL")
        ```
        * `OrderService` **no nota la diferencia**
        * LSP aplicado correctamente


    ## 4. ISP -- Interface Segregation principle

    > *No forzar a una clase a depender de métodos que no usa*

    * **Mala interfaz**

        ```python
        class NotificationService:
            def send_email(self): ...
            def send_sms(self): ...
            def send_push(self): ...
        ```
    
    * **Interfaz pequeña y especifica**

        ```python
        class NotificationService(ABC):

            @abstractmethod
            def notify(self, order: Order):
                pass
        ```
        * Cada implementación hace solo lo que necesita
        * ISP aplicado

    ## 5. DIP -- Dependency Inversion Principle

    > *Los módulos de alto nivel no deben depender de módulos concretos*

    * **Violación**

        ```python
        class OrderService:
            
            def __init__(self):
                self.repo = InMemoryOrderRepository()
        ```
        * Acoplamiento fuerte

    * **FastAPI lo ace Naturalmente con Depends**

        * `dependencies.py`

            ```python
            from domain.services import OrderService
            from infrastructure.memory_repository import InMemoryOrderRepository
            from infraestruture.email_notification import EmailNotification

            def get_order_service():

                repo = InMemoryOrderRepository()
                notifier = EmailNotification()
                return OrderService(repo, notifier)
            ```

        * `api/order_controller.py`

            ```python
            from fastapi import APIRouter, Depends
            from domain.models import Order
            from domain.services import OrderService
            from dependencies import get_order_service

            router = APIRouter()

            @router.post("/orders")
            def create_order(
                order: Order,
                service: OrderService = Depends(get_order_service)
            ):
                service.create_order(order)
                return {"message": "Pedido Creado"}
            ```
            * **DIP aplicado 100%**
            * FastAPI **fomenta la inversión de dependecias**
        
    ## Como FastAPI implementa SOLID indirectamente

    | Principio | FastAPI                                       |
    | --------- | --------------------------------------------- |
    | SRP       | Routers, servicios, schemas separados         |
    | OCP       | Dependencias intercambiables                  |
    | LSP       | Interfaces con ABC                            |
    | ISP       | Depends pequeños y específicos                |
    | DIP       | `Depends()` es inversión de dependencias pura |
