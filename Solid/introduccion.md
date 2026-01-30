# Introducción

* **SOLID** es un **conjunto de 5 principios de diseño de software orientado a objetos,** propuestos por **Robert C.Martin (Uncle Bob)**

* No es un tecnologia, ni un framework, ni una libreria.
* Es una **guia mental** para escribir código:

    * más **mantenibilidad**
    * más **extensible**
    * menos **acoplado**
    * más **fácil de probar**

* El nombre **SOLID** es un acrónimo:

    | Letra | Principio                       |
    | ----- | ------------------------------- |
    | S     | Single Responsibility Principle |
    | O     | Open/Closed Principle           |
    | L     | Liskov Substitution Principle   |
    | I     | Interface Segregation Principle |
    | D     | Dependency Inversion Principle  |

    ## Para que sirve SOLID ?

    * SOLID sirve para **evitar el código frágil**
    * Es código que:

        * Se rompe cuando cambias algo
        * Crece como un moustro
        * Nadie quiere tocar
        * Da miedo refactorizar

    * Aplicando SOLID logras:

        * Código facil de modificar
        * Menos bugs al hacer cambios
        * Mejor trabajo en equipo
        * Código alineado con arquitectura limpia
        * Mejor uso de interfaces, herencia y polimorfismo

    > En frameworks como **Spring Boot** y **FastAPI**, SOLID está por todas partes (aunque aveces no lo notes)

    ## Funcionamiento

    * SOLID **no se *implementa***, se **aplica** cuando:

        * Diseñas clases
        * Decides responsabilidades
        * Usas interfaces
        * Inyectas dependencias
        * Piensas en *que va a cambiar* en le futuro
    
    * Cada principio ataca un problema común del mal diseño

    ## Elementos que componen SOLID 

    1. **S: Single Responsability Principle (SRP)**

        > Una clase debe tener una sola razón para cambiar

        * Una clase debe **hacer una sola cosa,** y hacerla bien.

        * Mal:

            ```bash
            Clase Usuario
            - guardar usuario en DB
            - validar usuario
            - envia emails
            . genera reportes
            ```

        * Bien:

            ```bash
            Usuario
            UsuarioRepostory
            UsuarioValidator
            EmailService
            ```
        
        * **Beneficio**

            * Menos clases *Dios*
            * Cambios más seguros
            * Código más extensible

    2. **O: Open/Closed Principle (OCP)**

        > El software debe estar **abierto para extensión**, pero **cerrado para modificación** 

        * Puedes **agregar comportamiento nuevo** sin **romper código existente**

        * Mal:

            ```java
            if (tipo.equals("PDF")) { ... }
            else if (tipo.equals("EXCEL")) {...}
            ```
        
        * Bien:

            * Interfaces
            * Polimorfismo

            ```bash
            Reporte (interface)
            ├── ReportePDF
            └── ReporteExcel
            ```
        
        * **Beneficio**

            * Menos `if/else`
            * Menos bugs
            * Código preparado para crecer

    3. **L: Liskov Substitution Principle (LSP)**

        > Una clase hija debe poder **reemplazar a su clase padre sin romper el sistema**

        * Si `B` hereda de `A`, entonces **B debe cpmportase como A espera**

        * Ejemplo clásico:

            ```bash
            Ave
            ├── AveVoladora
            └── Pingüino (no vuela)
            ```
        
        * **Solución**

            ```bash
            Ave
            ├── AveVoladora
            └── AveNoVoladora
            ```
        
        * **Beneficio**

            * Herencia correctas
            * Menos comportamientos inesperados

    4. **I: Interface Segregation Principle (ISP)**

        > Ninguna clase deberia verse obligada a implementar métodos que no usa

        * Mejor **muchas interfaces pequeñas** que una gigante

        * Mal:

            ```bash
            Interface Trabajador
            - trabajar()
            - comer()
            - programar()
            - conducir()
            ````
        
        * Bien:

            ```bash
            Interface Trabajador
            Interface Programador
            Interface Conductor
            ```

        * **Beneficio**

            * Clases más limpias
            * Interfaces más claras
            * Menos métodos inútiles

    5. **D: Depenendcy Inversion Principle (DIP)**

        > Los módulos de alto nivel no deben depender de módulos de bajo nivel, ambos debe depender de abstracciónes

        * Tu lógica principal **No debe depender de clases concretas**, sino de **interfaces**

        * Mal:

            ```bash
            ServicioPedido -> MySQLPedidoRepository
            ```
        
        * Bien:

            ```bash
            ServicioPedido -> PedidoRepository (interface)
                                    |
                            MySQLPedidoRepository
            ```
        
        * **Beneficio**

            * Fácil cambiar DB
            * Fácil testear (mocks)
            * Bajo acoplamiento

    ## Donde veras SOLID en la vida real ?

    * **Spring Boot**

        * `@Service`, `@Repository`, `@Controller` -> SRP
        * Interfaces + `Autowired` -> DIP
        * Beans intercambiables -> OCP

    * **FastAPI**

        * Services separados
        * Repositorios por interfaz
        * Dependencias inyectadas con `Depends()`

    * **Arquitectura Limpia / Hexagonal**

        * SOLID es la base