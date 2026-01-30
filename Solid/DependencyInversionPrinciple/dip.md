# Dependency Inversion Principle (DIP)

* El **DIP** es el **quinto principio de SOLID** y dice:

    > *Los módulos de alto nivel no deben depender de módulos de bajo nivel. Ambos deben depender de abstracciones. Las abstracciones no deben depender de los detalles, los detalles deben depender de las abstracciones.*

* La lógica principal del sistema **no debe conocer clases concretas**
* Debe depender de **interfaces**
* Las implementaciones concretas se pueden cambiar sin romper nada

* En corto:

    > **Programa contra interfaces, no contra implementaciones.**

    ## Para que sirve DIP ?

    * Reducir acoplamiento 
    * Facilitar testing (mocks)
    * Cambiar tecnologias sin romper lógica
    * Aplicar OCP correctamente
    * Construir arquitecturas limpias
    * Hacer el código flexible y mantenible

    ## El problema que soluciona DIP

    * **Código sin DIP**

        ```text
        Servicio -> MySQL -> Email -> API externa
        ```

    * Problemas:

        * Cambiar DB = cambiar lógica
        * Testing dificil
        * Código rigido

    ## Funcionamiento de DIP

    * DIP funciona invirtiendo la dependencia tradicional

    * **Sin DIP**

        ```bash
        Servicio -> Implementación concreta
        ```

    * **Con DIP**

        ```bash
        Servicio -> Interfaz <- Implementación concreta
        ```
        * El **servicio no sabe** qué implementación usa.

    ## Elementos que componen DIP

    1. **Módulo de alto nivel**

        * Contiene la lógica de negocio
        * No conoce detalles técnicos

    2. **Módulo de bajo nivel**

        * DB, APIs, archivos, email
        * Implementa interfaces

    3. **Abstracciones (interfaces)**

        * Contratos
        * Punto de unión entre módulos

    4. **Inyección de dependencias**

        * Las dependencias se pasan desde fuera
        * No se crean dentro del módulo

    ## Ejemplo: Java Sin DIP

    * **Escenario**

        * Servicio de pedidos que guarda en MySQL

    ```java
    public class MySQLPedidoRepository {

        public void guardar(){
            System.out.println("Guarda pedido en MySQL");
        }
    }

    public class PedidoService {
        private MySQLPedidoRepository repository = new MySQLPedidoRepository();

        public void crearPedido(){
            repository.guardar()
        }
    }
    ```
    * **Problemas**

        * `PedidoService` depende de MySQL
        * No se puede cambiar DB fácilmente
        * Testing complicado
    
    ## Ejemplo con DIP (Java)

    * **Paso 1: Crear abstracción**

        ```java
        public interface PedidoRepository {
            void guardar();
        }
        ```
    
    * **Paso 2: Implementación concreta**

        ```java
        public class MySQLPedidoRepository implements PedidoRepository {
            public void guardar(){
                System.out.println("Guardar pedido en MySQL");
            }
        }
        ```

    * **Paso 3: Servicio depende de la abstracción**

        ```java
        public class PedidoService {

            private PedidoRepository repository;

            public PedidoService(PedidoRepository repository){
                this.repository = repository;
            }

            public void crearPedido(){
                repository.guardar();
            }
        }
        ```

    * **Paso $: Inyección desde fuera**

        ```java
        public class Main {

            public static void main(String[] args){

                PedidoRepository repo = new MySQLPedidoRepository();
                PedidoService service = new PedidoService(repo);

                service.crearPedido();
            }
        }
        ```
    * **Beneficios**

        * Servicio desacoplado
        * Facil de camviar MySQL -> PostgreSQL
        * Testing sencillo

    ## Ejemplo: Python sin DIP

    ```python
    class MySQLPedidoRepository:
        def guardar(self):
            print("Guardando pedido en MySQL")

    class PedidoService:
        def __init__(slef):
            self.repository = MySQLPedidoRepository()

        def crear_pedido(self):
            self.repository.guardar()
    ```
    * Dependencia directa

    ## Ejemplo: Python con DIP

    * **Paso 1: Abstracción**

        ```python
        from abc import ABC, abstractmethod

        class PedidoRepository(ABC):
            @abstractmethod
            def guardar(self):
                pass
        ```

    * **Paso 2: Implementación concreta**

        ```python
        class MySQLPedidoRepository(PedidoRepository):
            def guardar(self):
                print("Guardando pedido en MySQL")
        ```

    * **Paso 3: Servicio desacoplado**

        ```python
        class PedidoService:
            def __init__(self, repository: PedidoRepository):
                self.repository = repository

            def crear_pedido(self):
                self.repository.guardar()
        ```

    * **Paso 4: Inyección**

        ```python
        repo = MySQLPedidoRepository()
        service = PedidoService(repo)
        service.crear_pedido()
        ```

    ## DIP en frameworks

    * **Spring Boot**

        * `@Service` + `@Repository`
        * `@Autowired`
        * Inyección por constructor (mejor practica)

        ```java
        @Service
        public class PedidoRepository {
            private final PedidoRepository repository;

            public PedidoService(PedidoRepository repository){
                this.repository = repository;
            }
        }
        ```

    * **FastAPI**

        * `Depends()`
        * Inyección explícita

        ```python
        def get_repo():
            return MySQLPedidoRepository()

        @app.post("/pedido")
        def crear_pedido(repo: PedidoRepository = Depends(get_repo)):
            service = PedidoService()
            service.crear_pedido()
        ```

    > La lógica no debe conocer detalles técnicos

    ## Como DIP conecta todo SOLID

    * **SRP** -> Clases pequeñas
    * **OCP** -> Extensiones seguras
    * **LSP** -> sustitución correcta
    * **ISP** -> Contratos claros
    * **DIP** -> Todo desacoplado

    * **DIP es el pegamento de SOLID**