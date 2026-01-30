# Abstraccion

* La **abstracción** es el principio de OOP que consiste en:

    > **Mostrar qué hace un objeto**  
    y **ocultar Cómo lo hace**

    * Te quedas solo con lo **esencial** y escondes los detalles internos

* Ejemplo cotidiano:

    > Para conducir un carro usa volante, freno y acelerador.  
    No necesitas saber cómo funciona el motor por dentro

    ## Para que sirve la abstracción ?

    * **Reducir complejidad:** Te concentras en lo importante
    * **Desacoplar el código:** El código depende de contratos, no de implementaciones
    * **Facilitar mantenimiento:** Cambias el cómo sin afectar el qué
    * **Permitir escalabilidad:** Agregas nuevas implementaciones sin romper nada
    * **Base de arquitectura limpía:** Controllers -> Services -> Repositories -> interfaces

    ## Funcionamiento

    1. Defines **qué operaciones existen**
    2. Ocultas la implementación real
    3. Usas **interfaces o clases abstractas**
    4. El código cliente usa la abstracción, no la clase concreta

    * Abstracción != encapsulación

        * Encapsulación -> protege datos
        * Abstracción -> simplifica el uso
    
    ## Elementos que componen la abstracción

    1. **Contratos**

        * Interfaces
        * Clases abstractas

    2. **Métodos abstractos**

        * Definen acciones sin implementación

    3. **Implemantaciones concretas**
        
        * Clases que hacen el trabajo real

    4. **Polimorfismo**

        * Permite usar distintas implementaciones sin cambiar el código
    

    ## Ejemplo: Java

    * **Paso 1: Definir el problema**

        * Tenemos un sistema de **notificaciones:**

            * Email
            * SMS
            * WhatsApp

        * Todas:

            * Envían mensajes
            * Pero lo hacen diferente

    * **Paso 2: Crear la abstracción (Interfaz)**

        ```java
        public interface Notificacion {

            void enviar(String mensaje);
        }
        ```
        * Define **qué se puede hacer**
        * No dice **cómo**
        * Es la abstracción

    * **Paso 3: Implementación concreta (Email)**

        ```java
        public class EmailNotificacion implements Notificacion {

            @Override
            public void enviar(String mensaje){
                System.out.println("Enviando email: " + mensaje);
            }
        }
        ```
    
    * **Paso 4: Otra implementación (SMS)**

        ```java
        public class SmsNotificacion implements Notificacion {

            @Override
            public void enviar(String mensaje){
                System.out.println("Enviando SMS: " + mensaje);
            }
        }
        ```

    * **Paso 5: Usar la abstracción (Clave)**

        ```java
        public class ServicioNotificacion {

            private Notificacion notificacion;
            
            public ServicioNotificacion(Notificacion notificacion){
                this.notificacion = notificacion;
            }

            public void notificar(String mensaje){
                notificacion.enviar(mensaje)
            }
        }
        ```
        * El servicio **No sabe** si es email o SMS
        * Solo conoce la **abstracción**

    * **Paso 6: Ejecución**

        ```java
        public class Main {
            public static void Main(String[] args){

                Notificacion email = new EmailNotificacion();
                ServicioNotificacion servicio = new ServicioNotificacion(email);

                servicio.notificar("Hola mundo");
            }
        }
        ```

    ## Ejemplo: Python

    * **Paso 1: Importar ABC**

        ```python
        from abc import ABC, abstractmethod
        ```

    * **Paso 2: Crear abstracción**

        ```python
        class Notificacion(ABC):

            @abstractmethod
            def enviar(self, mensaje):
                pass
        ```
    
    * **Paso 3: Implementación concreta (Email)**

        ```python
        class EmailNotificacion(Notificacion):

            def enviar(self, mensaje):
                print(f"Enviando emai: {mensaje}")
        ```
    
    * **Paso 4: Implementacion concreta (SMS)**

        ```python
        class SmsNotificacion(Notificacion):

            def enviar(self, mensaje):
                print(f"Enviado SMS: {mensaje}")
        ```

    * **Paso 5: Usar la abstracción**

        ```python
        class ServicioNotificacion:

            def __init__(self, notificacion: Notificacion):
                self.notificacion = notificacion
            
            def notificar(self, mensaje):
                self.notificacion.enviar(mensaje)
        ```
    
    * **Paso 6: Ejecutar**

        ```python
        servicio = ServicioNotificacion(EmailNotificacion())
        servicio.notificar("Hola mundo")
        ```

    ## Diferencia clara con encapsulación

    | Abstracción           | Encapsulación  |
    | --------------------- | -------------- |
    | Oculta complejidad    | Oculta datos   |
    | Define qué se hace    | Protege estado |
    | Nivel diseño          | Nivel objeto   |
    | Interfaces / abstract | Modificadores  |

    ## Abstracción en Spring Boot y FastAPI 

    * **Spring Boot**

        ```java
        public interface UsuarioRepository {
            Usuario save(Usuario usuario);
        }
        ```
        * No importa si usar JPA, Mongo, etc.
    
    * **FastAPI**
        
        ```python
        class UsuarioRepository(ABC):
            @abstractmethod
            def guardar(self, usuario): ...
        ```

    ## Resumen

    > **Abstraer = pensar en que, no en el como**

    * Reduce complejidad
    * Desacopla
    * Facilita cambios
    * Escala sistemas
    * Base de la arquitectura limpia

