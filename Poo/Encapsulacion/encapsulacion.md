# Encapsulación

* La **encapsulación** es el principio de OOP que consiste en:

    > **Ocultar los detalles internos de un objeto**  
    **y controlar cómo se accede o modifica su estado**

* El objeto **protege sus datos** y solo permite interactuar con ellos a través de **métodos controlados**

* Ejemplo mental

    * No abres un cajero automatico por detro, usas botones que controlan lo que puedes hacer.

    ## Para que sirve la encapsulación ?

    * **Protege los datos:** Evita que se modifique de forma incorrecta
    * **Controlar reglas del negocio:** Valida datos antes de guardarlos
    * **Reducir acoplamiento:** Los cambios internos no afectan a otros módulos
    * **Facilitar mantenimiento:** Puedes cambiar la implementación sin romper el sistema
    * **Aumentar seguirdad:** Especialmente importante en APIs y sistemas críticos
    
    ## Funcionamiento

    1. Los **atributos se ocultan** (no acceso directa)
    2. Se usan **modificadores de acceso**
    3. Se exponen **métodos públicos controlados**
    4. El objeto es responsable de su propio estado


    ## Elementos que componen la encapsulación

    1. **Atributos privados:** No accesibles desde fuera de la clase
    2. **Métodos públicos:** Única forma de interactuar con los datos
    3. **Getters:** Métodos para **leer** valores
    4. **Setters:** Métodos para **modificar** valores con validación
    5. **Modificadores de acceso (Java)**

        * `private`
        * `protected`
        * `public`

    ## Ejemplo: Java

    * **Paso 1: Problema**

        * Tiene saldo
        * El saldo No debe modificarse directamente
        * Solo con reglas claras

    * **Paso 2: Sin encapsulación (Mal)**

        ```java
        public class Cuenta {
            public double saldo;
        }
        ```
        * Problema

            ```java
            cuenta.saldo = -10000;
            ```
    
    * **Paso 3: Con encapsulación (Correcto)**

        ```java
        public class CuentaBancaria {

            // Atributo privado
            private double saldo;

            // Constructor
            public CuentaBancaria(double saldoInicial){
                this.saldo = saldoInicial;
            }

            // Getter
            public double getSaldo(){
                return this.saldo;
            }

            // Metodo controlado
            public void depositar(double monto){
                if (monto > 0){
                    this.saldo += monto;
                }
            }

            // Metodo controlado

            public boolean retirar(double monto){
                if (monto > 0 && monto <= saldo){
                    saldo -= monto;
                    return true;
                }
                return false;
            }
        }
        ```
    * **Paso 4: Uso correcto**

        ```java
        public class Main {
            public static void main(String[] args){

                CuentaBancaria cuenta = new CuentaBancaria(1000);

                cuenta.depositar(500);
                cuenta.retirar(300);

                System.out.println(cuenta.getSaldo());
            }
        }
        ```
        * El saldo está protegido
        * Las reglas viven dentro de la clase
        * Nadie rompe el estado

    ## Ejemplo: Python

    * Python no tiene `private` real, pero usa **convención**

    * **Paso 1: Clase encapsulada**

        ```python
        class CuentaBancaria:

            def __init__(self, saldo_inicial: float):
                self._saldo = saldo_inicial

            def obtener_saldo(self)-> float:
                return self._saldo

            def depositar(self, monto: float):
                if(monto > 0){
                    self._saldo += monto
                }
            
            def retirar(self, monto: float):
                if (monto > 0 && slef._saldo >= monto):
                    self._saldo -= monto
                    return True
                return False
            
        ```
    
    * **Paso 2: Uso correcto**

        ```python
        cuenta = CuentaBancaria(1000);

        cuenta.depositar(500)
        cuenta.retirar(300)

        print(cuenta.obtener_saldo())
        ```
    
    * **Lo que No se debe hacer**

        ```python
        cuenta._saldo = -99999
        ```
        * Se puede, pero **No se debe**

    ## Encapsulación en APIs 

    * **Spring Boot**

        ```java
        @Entity
        public class Usuario {
            private String email;

            public void cambiarEmail(String email){
                if ( email.contains("@")){
                    this.email = email;
                }
            }
        }
        ```
    
    * **FastAPI**

        ```python
        class Usuario:
            def cambiar_email(self, email:str):
                if "@" in email:
                    self._email = email
        ```
        * La logica **vive dentro del objeto**
    
    ## Resumen

    > **Encapsular = proteger + controlar**
    
    * Oculta datos
    * Expones métodos
    * Aplicas reglas
    * Evitas errores

    