# Introduccion a Spring Boot

1. **Arquitectura principal: Arquitectura en Capas (Layared Architecture)**

    * Spring Boot **no inventa una arquitectura nueva**, sino que **refuerza y automatiza** una arquitectura muy usada en sistemas empresariales

    * **Capas típicas en Spring Boot**

        ```bash
        Controller (Web Layer)
        ↓
        Service (Business Layer)
        ↓
        Repository (Persistence Layer)
        ↓
        Database
        ```
        * **Controller:** Maneja HTTP, rutas, request y responses
        * **Service:** Contiene la lógica de negocio
        * **Repository:** Aceso a datos (JPA, JDBC, etc)
        * **Model/Entity:** Representacion del dominio

        * **Spring Boot obliga suavemente** a esta separación porque:

            * Las anotaciones (`@RestController`, `@Service`, `@Repository`) **marcan roles claros**
            * La inyección de dependencias conecta capas sin acoplarlas

    * ***Arquitectura subyacente: MVC (Model-View-Controller)**

        * Spring Boot hereda **Spring MVC**

        * En APIs REST:

            * **Model** -> Entidades/DTOs
            * **View** -> JSON (No HTML)
            * **Controler** -> `@RestController`
        
        * Aunque no lo veas explicito, **Spring MVC está simpre ahi**

2. **Arquitectura interna de Spring Boot (más importante)**

    * Spring Boot Se basa en **3 pilares arqutectonicos clave:**

        1. **Inversión de Control (IoC)**

            * Spring **no crea objetos directamente**
            
            * Mal diseño tradiciona:

                ```java
                UserService service = new UserService();
                ```
            * Spring:

                ```java
                @Autowired
                UserService service;
                ```
            * El Contenedor **decide cuándo, cómo y con qué dependencias** crear cada objeto.
            * Esto es la base de **Todo Spring**

        2. **Contenedor de Beans (ApplicationContext)**

            * Spring Boot arranca un **contenedor** que:

                * Escanea clases
                * Crea objetos (Beans)
                * Gestiona su ciclo de vida
                * Inyecta dependencias
            
            * Esto permite:

                * Bajo acoplamiento
                * Testeabilidad
                * Configuración flexible

        3. **Auto-configuración (AutoConfiguration)**

            * Spring Boot analiza:

                * Dependencais en el classpath
                * Configuración (`application.yml`)
                * Anotaciones

            * Y **configura todo automaticamente**

            * Ejemplo

                ```text
                spring-boot-starter-data-jpa
                ```
            * Spring configura:

                * EntityManager
                * DataSource
                * Transacciones
                * Repositorios
            * Esto reduce configuración **Sin sacrificar arquitectura**

3. **Patrones de diseño que Spring Boot implementa Internamente**

    1. **Dependency Injection (DI)**

        ```java
        @Service
        public class OrderService {
            private final OrderRepository repo;

            public OrderService(OrderRepository repo){
                this.repo = repo;
            }
        }
        ```
        * Desacopla clases
        * Facilita testing (mocks)
        * Permite cambiar implementaciones sin tocar código

    2. **Inversion of Control (IoC)**

        * El framework controla el flujo, no tú

        * Ejemplo

            * Tu escribes `@RestController`
            * Spring decide cuando llamar al método

        * Esto permite

            * Plugins
            * Extensibilidad
            * Integración transparente

    3. **Factory / Abstract Factory**

        * Spring crea objetos usando **factories internas**

            * `BeanFactory`
            * `EntityManagerFactory`
            * `DataSource`

        * Tu no usas `new`, Spring fabrica instancias según contexto.

    4. **Singleton (Controlado por contenedor)**

        * Por defecto, los beans son **Singleton**

            ```java
            @Service
            public class UserService {}
            ``` 
            * Una sola instancia por ApplicationContext

        * Ojo: **No es Singleton clásico**, sino gestionado por el contenedor

    5. **Proxy (muy importante)**

        * Spring usa **Proxies dinámicos** para:

            * `@Transactional`
            * `@Lazy`
            * `@Async`
            * `@Cacheable`

        * Ejemplo

            ```java
            @Transactional
            public void save() {
            }
            ```
        * Spring Crea

            ```bash
            Proxy -> TransactionManager -> Metodo real
            ```
            * Sin Proxy **no existirían transacciones declarativas**

    6. **Template Method**

        * Ejemplo clásico:

            * `JdbcTemplate`
            * `RestTemplate`
            * `JpaRepository`

        * Spring define el **esqueleto del algoritmo**
        * Tu solo implementas partes especificas

    7. **Strategy**

        * Spring elige estrategias en tiempo de ejecucion:

            * Serialización (Jackson, Gson)
            * Autenticación (JWT, OAuth2)
            * Persistencia (Hibernate, EclipseLink)

    8. **Observer / Event-driven**

        * Spring tiene eventos:

            ```java
            @EventListener
            public void onEvent(...) {}
            ```
        * Muy usado en:

            * Seguridad
            * Ciclo de vida
            * Microservicios

4. **Porque Spring Boot integra todos estos patrones ?**

    * **Razon principal:** Framework empresarial escalable
    * Spring está diseñado para:

        * Proyectos grandes
        * Equipos grandes
        * Cambios constantes
        * Microservicios

    * Los patrones permiten:

        * Bajo acoplamiento
        * Alta cohesion
        * Testeabilidad
        * Evolución sin reescritura

