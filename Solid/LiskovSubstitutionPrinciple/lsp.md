# Liskov Substitution Principle (LSP)

* El **LSP** es el **tercer principio de SOLID** y dice:

    > *Los objetos de una clase derivada deben poder sustituir a los objetos de su clase base sin alterar el comportamiento correcto del programa.*

* En corto:

    * **Una subclase debe poder usarse donde se espera la superclase, sin romper nada**

    ## Para qué sirve LSP ?

    * Diseñar herencias correctas
    * Evitar comportamientos inesperados
    * Prevenir bugs dificiles de detectar
    * Garantizar que el polimorfismo funcione bien
    * Mejorar la calidad de diseño OO

    ## El problema que soluciona LSP

    * **Problema común**

        * Se usa herencia solo porque *parece que encaja*, pero:

            * La subclase cambia el significado del comportamiento
            * Lanza excepciones inesperadas
            * Ignora contratos de la clase base

        * Resultado: **código frágil y engañoso**

    ## Funcionamiento

    * LSP se base en el concepto de **contrato**

        * La clase base define:

            * Qué hace un método
            * Qué se espera de el
        
        * La subclase:

            * **no debe rompler ese contrato**

        * **Regla mental**

            * *Si algo es un `X`, debe comportarse como un `X`*

    
    ## Elementos que componen LSP

    1. **Clase base (Abstracción)**

        * Define el **contrato**

            * Métodos
            * Comportamiento esperado

    2. **Clases derivadas**

        * Implementan o extienden el contrato:

            * Sin cambiar su significado
            * Sin lanzar errores inesperados

    3. **Sustitución**

        * El código cliente:

            * Usa la clase base
            * recibe subclases sin saberlo

    4. **Polimorfismo correcto**

        * El comportamiento:

            * Sigue siendo válido
            * No sorprende

    ## Ejemplo Clasico que viola LSP

    * **Escenario: Rectangulo y Cuadrado**

        * Mantematicamnte:

            * Un **cuadrado Es un rectángulo**

        * En programación: **No siempre**

    * **Codigo incorrecto (Java)**

        * **Clase base**

            ```java
            public class Rectangulo {
                protected int ancho;
                protected int alto;

                public void setAncho(int ancho){
                    this.ancho = ancho;
                }

                public void setAlto(int alto){
                    this.alto = alto;
                }

                public int area(){
                    return this.alto*this.ancho
                }
            }
            ```

        * **Subclase**

            ```java
            public class Cuadrado extends Rectangulo {

                @Override
                public void setAncho(int ancho){
                    this.ancho = ancho;
                    this.alto = ancho;
                }

                @Override
                public void setAlto(int alto){
                    this.alto = alto;
                    this.ancho = alto;
                }
            }
            ```

        * **Codigo cliente**

            ```java
            public class Main {
                public static void main(String[] args){
                    Rectangulo r = new Cuadrado();
                    r.setAncho(5);
                    r.setAlto(10);

                    System.out.println(r.area()); // 50? -> Imprime 100
                }
            }
            ```

        * **Porque viola LSP**

            * El código espera comportamiento de `Rectangulo`
            * `Cuadrado` **cambia el contrato**
            * Resultado inesperado

        * **La sustitución rompe el programa**

    ## Solución correcta (Respeta LSP)

    * **Idea clave**

        * No forzar herencias que rompen el comportamiento

    * **Paso 1: Crear una abstracción común**

        ```java
        public interface Figura {
            int area();
        }
        ```

    * **Paso 2: Implementaciones correctas**

        * **Rectangulo**

            ```java
            public class Rectangulo implements Figura {
                private int ancho;
                private int alto;

                public Rectangulo(int ancho, int alto){
                    this.ancho = ancho;
                    this.alto = alto;
                }

                public int area(){
                    return ancho*alto;
                }
            }
            ```
        * **Cuadrado**

            ```java
            public class Cuadrado implements Figura {
                private int lado;

                public Cuadrado(int lado){
                    this.lado = lado;
                }

                public int area(){
                    return this.lado*this.lado;
                }
            }
            ```
        * **Resultado**

            * No herencia incorrecta
            * Sustitución segura 
            * Polimorfismo real

    ## Ejemplo: Python que viola LSP

    ```python
    class Rectangulo:

        def set_ancho(self, ancho):
            self.ancho = ancho

        def set_alto(self, alto):
            self.alto = alto

        def area(self):
            return self.ancho * self.alto

    class Cuadrado(Rectangulo):
        
        def set_ancho(self, ancho):
            self.ancho = ancho
            self.alto = ancho

        def set_alto(self, alto):
            self.alto = alto
            self.ancho = alto
    ```

    ```python
    r = Cuadrado()
    r.set_ancho(5)
    r.set_alto(10)

    print(r.area()) # Resutado inesperado
    ```
    * Violación clara de LSP

    ## Solución correcta en Python

    ```python
    from abc import ABC, abstractmethod

    class Figura(ABC):

        @abstractmethod
        def area(self):
            pass

    class Cuadrado(Figura):

        def __init__(self, lado: int):
            self.lado = lado

        def area(self)-> int:
            return self.lado*self.lado

    class Rectangulo(Figura):

        def __init__(self, ancho: int, alto: int):
            self.ancho = ancho
            self.alto = alto

        def area(self)-> int:
            return self.ancho*self.alto
    ```

    ## Reglas prácticas para No romper LSP

    * Una subclase **no debe**

        * Lanzar nuevas excepciones inesperadas
        * Cambiar el significado de un método
        * requerir condiciones más estrictas
        * devolver resultados incompatibles

    * Una subclase **debe**

        * Respetar el contrato
        * Comportarse como el padre
        * Poder reemplazarlo sin sorpresas

    > *Si tu herencia necesita *parches* para funcionar... probablemente está mal diseñanda*


    ## Relación con OCP

    * **OCP** permite extender
    * **LSP** garantiza que la extensión no rompa nada

    * *LSP es lo que hace OCP funcione correctamente*

    