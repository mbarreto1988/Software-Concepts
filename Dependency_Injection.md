
# DI - (Dependency Injection)
## Concepto
La inyeccion de dependencia `Dependency Injection` es un patron de diseño que consiste en proveer las dependencias que un objeto necesita desde afuera, en lugar que el propio objeto las cree.
En otras palabras: Una clase no se encarga de crear los objetos que usa si no que los recibe ya preparados.



## Explicacion simple
Imagina que tenes una clase `Motor` y una clase `Auto`
Un auto necesita un motor para funcionar.
Si el auto mismo crea el motor, queda muy acoplado (no podes cambiar el tipo de motor facilmente).
Pero si el motor se lo pasas desde afuera el auto no depende directamente de como se cre el motor solo que exista uno
Eso es lo que hace el codigo:
- mas flexible (Podes cambiar las dependencias sin tocar la clase principal)
- mas facil de testear (podes pasar datos mockeados en los test)
- Menos acoplado (cada clase hace su trabajo y no se mete en el de la otra)



## Ejemplo conceptual (sin código)
- Sin inyeccion de dependencias:
    - La clase `Auto` crea su propio `Motor`.
    - Si querés cambiar de motor eléctrico a motor a combustión, tenés que modificar Auto.

- Con inyección de dependencias:
    - El ``Motor`` se crea afuera y se le pasa al ``Auto``.
    - ``Auto`` solo necesita saber que tiene un motor que puede arrancar, no le importa el tipo.


## 💻 4. Ejemplo en código (Python)
### ❌ Sin inyección de dependencias (acoplamiento alto)
```python
class Motor:
    def arrancar(self):
        print('Motor encendido')
    
class Auto:
    def __init__(self):
        self.motor = Motor() # El Auto crea su propia dependencia
    
    def encender(self):
        self.motor.arrancar()

auto = Auto()
auto.encender()
```
👉 Problema:
- Si mañana querés usar otro tipo de motor (por ejemplo, eléctrico), tenés que editar la clase ``Auto``.


### ✅ Con inyección de dependencias (acoplamiento bajo)
```python
class Motor:
    def arrancar(self):
        print("Motor encendido")

class MotorElectrico:
    def arrancar(self):
        print("Motor eléctrico encendido silenciosamente")

class Auto:
    def __init__(self, motor):  # Recibe la dependencia desde afuera
        self.motor = motor

    def encender(self):
        self.motor.arrancar()

# Inyectamos el tipo de motor que queramos
motor_normal = Motor()
motor_electrico = MotorElectrico()

auto1 = Auto(motor_normal)
auto2 = Auto(motor_electrico)

auto1.encender()  # Motor encendido
auto2.encender()  # Motor eléctrico encendido silenciosamente
```
👉 Ventajas:
- ``Auto`` no sabe ni le importa que tipo de motor tiene, solo que puede arrancar.
- Podes cambiar el comportamiento sin tocar la clase ``Auto``.
- Perfecto para testeo o extender el sistema sin romper nada.


## 💡 En resumen
| Concepto           | Descripción                                                                  |
| ------------------ | ---------------------------------------------------------------------------- |
| **Qué es**         | Patrón que permite que una clase reciba sus dependencias desde afuera.       |
| **Para qué sirve** | Reducir acoplamiento, mejorar flexibilidad y testeo.                         |
| **Beneficio**      | Código más limpio, mantenible y escalable.                                   |
| **Cómo se aplica** | Pasando los objetos necesarios como parámetros en lugar de crearlos adentro. |

---

# Ciclos de Vida:
## Concepto
Los ciclos de vida (o scopes) en inyeccion de dependencias definen cuanto tiempo vive una instancia de una clase dentro del sistema.
O sea cuando se crea, cuantas veces se usa y cuando se destruye.
Esto es clave para manejar bien los recursos. Por ejemplo: (Conexiones a base de datos, configuraciones, etc)

## Explicación simple
Cuando un sistema usa Inyeccion de dependencia en cualquier lenguaje, tiene un contenedor de dependencias que se encarga de crear y administrar los objetos.
Ese contenedor puede decidir si:
- crea una instancia cada vez ``transiendt``
- Usa la misma instancia para todos ``singleton``
- O crea una nueva por cada contexto como una peticion HTTP o sesion ``scoped``

## Los ciclos de vida más comunes
1. Singleton
- El objeto se crea una sola vez y se usa en toda la aplicacion.
- hay una unica instancia compartida, es ideal para cosas como configuraciones o servicios que no cambian.
- EJEMPLO:
```python
class Config:
    def __init__(self):
        self.db_url = "localhost"


 # Singleton manual
config_global = Config()


class Servicio:
    def __init__(self, config):
        self.config = config

servicio1 = Servicio(config_global)
servicio2 = Servicio(config_global)

print(servicio1.config is servicio2.config)  # True -> usan la misma instancia
```
- 👉 Ventaja: eficiencia y coherencia.
- 👉 Desventaja: si algo cambia en el objeto, afecta a todos.


## Transient (o “per request”)
- Cada vez que se inyecta una dependencia se crea una nueva instancia
- Cada uso tiene su propio objeto independiente, es ideal para servicios que manejan datos o estados temporal.
- EJEMPLO:
```python
class Servicio:
    def __init__(self):
        print('Nuevo servicio creado')

# Transient: cada vez se crea uno nuevo
s1 = Servicio()
s2 = Servicio()
print(s1 is s2) # False
```
- 👉 Ventaja: independencia total entre objetos.
- 👉 Desventaja: más consumo si se crean demasiados.


## Scoped
- El objeto vive durante un contexto determinado, por ejemplo ua peticion HTTP, una sesion de usuario o una transaccion.
- Se crea una instancia al iniciar una unidad de trabajo y se destruye cuando termina. Esto es usando mucho en frameworks web. por ejemplo: un servicio de base de datos que vive solo miestras dura una peticion.
- EJEMPLO:
```python
class ConexionDB:
    def __init__(self):
        print('Conexion abierta')
    
    def cerrar(self):
        print('Conexion Cerrada')

def manejar_peticion():
    conexion = ConexionDB()  # Scoped: vive solo dentro de esta función
    print('Usando Base de datos...')
    conexion.cerrar()

manejar_peticion()
manejar_peticion()
```
- 👉 Cada “petición” abre y cierra su propia conexión.
- 👉 Ideal para evitar fugas de memoria o conflictos entre usuarios.

## 🧱 4. En resumen

| Ciclo de vida | Cuándo se crea                     | Cuándo se destruye       | Ejemplo de uso                     |
| ------------- | ---------------------------------- | ------------------------ | ---------------------------------- |
| **Singleton** | Una sola vez                       | Cuando termina la app    | Configuración global, logs         |
| **Transient** | Cada vez que se usa                | Inmediatamente después   | Servicios pequeños o temporales    |
| **Scoped**    | Al iniciar una petición o contexto | Al terminar ese contexto | Conexiones a BD, sesión de usuario |


- En Frameworks grandes como FastAPI, Spring, NetsJS, etc, estos ciclos los define el contenedor de inyeccion automaticamente:
    Vos solo tenes que declarar que tipo de vida queres que tenga cada dependencia.

