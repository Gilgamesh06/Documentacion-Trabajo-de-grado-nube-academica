# Single Responsability Principle (SRP)

* El **SRP** es el **primer principio de SOLID** y dice:

    > Una clase debe tener una sola razón para cambiar

* Dicho en palabras simples:

    * **Una clase debe tener una sola responsabilidad**
    * **Debe hacer una sola cosa, y hacerla bien**

    ## Para que sirve SRP ?

    * SRP sirve para **evitar clases *Dios*** (Clases gigantes que hacen de todo)

    * Aplicar SRP te permite:

        * Código mas facil de entender
        * Cambios más seguros
        * Menos errores colaterales
        * Mejor testeo
        * Menos acoplamiento
        * Código escalable

    ## Que problema soluciona SRP ?

    * **Problema tipico (Sin SRP)**

        * Una clase que:

            * Valida datos
            * Guarda en base de datos
            * Imprime reportes
            * Envia correos

        * **Tiene muchas razones para cambiar**
        * Rompes algo al tocar otra cosa
        * El código se vuelve frágil

    ## Funcionamiento

    * SRP funciona **identificando responsabilidades** y **separandolas**

    * **Pregunta clave:**

        > *Porque motivos esta clase podria cambiar ?*

        * Si tiene **más de un motivo**, viola SRP
        * Cada motivo -> **una clase distinta**

    ## Elementos que componen SRP

    1. **Responsabilidad:** una **responsabilidad** es:

        * Una razón de cambio
        * Una función clara dentro del sistema

    2. **Separación de responsabilidades:** Cada responsabilidad debe vivir en:

        * Una clase
        * Un Módulo
        * Un servicio

    3. **Alta cohesión:** Todo lo que hace la clase:

        * Esta relacionado entre sí
        * Cumple un único proposito

    4. **Bajo acoplamiento**

        * Las clases:

            * Se conocen poco entre si
            * Colaboran, no se mezclan

    ## Ejemplo: Java Sin SRP

    * **Escenario**

        * Validar datos
        * Guardar en DB
        * Enviar email

    * **Codigo incorrecto**

        ```java
        public class UsuarioService {

            public void registrarUsuario(String nombre, String email){
                // validar datos
                if ( nombre == null || email == null){
                    throw new IllegalArgumentException("Datos invalidos");
                }

                // Guardar en base de datos
                System.out.println("Guardando usuario en la base de datos");

                // Enviar email
                System.out.println("Enviando email de bienvenida");

            }
        }
        ```
        * **Problemas**

            * Cambia si cambia la validación
            * Cambia si cambia la DB
            * Cambia si cambia el email

                * **3 responsabilidades = 3 razones para cambiar**

    ## Ejemplo: Java con SRP

    * **Paso 1: Identificar responsabilidades**

        * Validar usuario
        * Persistir Usuario
        * Enviar email

    * **Paso 2: Separar clases**

        1. **Validador**

            ```java
            public class UsuarioValidator {

                public void validar(String nombre, String email){
                    if ( nombre == null || email == null){
                        throw new IllegalArgumentException("Datos invalidos");
                    }
                }
            }
            ```

        2. **Repostorio**

            ```java
            public class UsuarioRepository {

                public void guardar(){
                    System.out.println("Guardando usuario en la base de datos");
                }
            }
            ```

        3. **Servicio de email**

            ```java
            public class EmailService {

                public void enviarBienvenida(String email){
                    System.out.println("Enviando email a: "+ email);
                }
            }
            ```

        4. **Servicio principal (Coordina)**

            ```java
            public class UsuarioService {

                private UsuarioValidator validator;
                private UsuarioRepository repository;
                private EmailService emailService;

                public UsuarioService(){
                    this.validator = new UsuarioValidator();
                    this.repository = new UsuarioRepository();
                    this.emailService = new EmailService();
                }

                public void registrarUsuario(String nombre, String email){
                    validar.validar(nombre, email);
                    repository.guardar();
                    emailService.enviarBienvenida(email);
                }
            }
            ```
            * **Beneficios**

                * Cada clase tiene **Una sola responsabilidad**
                * Cambios aislados
                * Código más limpio

        ## Ejemplo: Python sin SRP

        ```python
        class UsuarioService:

            def registrar_usuario(self, nombre:str, email:str):
                if not nombre or not email:
                    raise ValueError("Datos invalidos")

                print("Guardando usuario en la base de datos")
                print("Enviando email de bienvenida")
        ```
        * Mismo problema: demasiadas responsabilidades

        ## Ejemplo: Python con SRP

        * **Paso 1: Validador**

            ```python
            class UsuarioValidator:
                def validar(self, nombre: str, email: str):
                    if not nombre or not email:
                        raise ValueError("Datos invalidos")
            ```
        
        * **Paso 2: Repositorio**

            ```python
            class UsuarioRepository:
                def guardar(self):
                    print("Guardar usuario en la base de datos")
            ```

        * **Paso 3: Email**

            ```python
            class EmailService:
                def enviar_bienvenida(self, email):
                    print(f"Enviado email a {email}")
            ```

        * **Paso 4: Servicio principal**

            ```python
            class UsuarioService:
                
                def __init__(slef):
                    self.validator = UsuarioValidator()
                    self.repository = UsuarioRepository()
                    self.email_service = EmailService()

                def registrar_usuario(self, nombre:str, email:str):
                    self.validator.validar(nombre, email)
                    self.repository.guardar()
                    self.email_service.enviar_bienvenida(email)
            ```

        ## Resumen

        * Cuando pienses en SRP, preguntale:

            * ¿Esta clase tiene mas de una razon para cambiar?
            * ¿Hace más de una cosa?
            * ¿Podria dividirse sin perder sentido?

        * Si la respuesta es **si, rompe la clase** (en el buen sentido)

    