
# POO
## Concepto
La Programacion Orientada a Objetos (POO) es un paradigma de programacion que organiza el codigo en objetos, los cuales convinan datos(atributos) y comportamiento(metodos).
Su objetivo es modelar el codigo de forma mas natural mejorando asi la reutilizacion, mantenibilidad y escalabilidad del software


## Explicacion simple
Imagina que queres crear una app que maneje informacion de personas:
En lugar de tener variables sueltas (nombre1, edad1, nombre2, edad2, etc) creas una clase ``Persona`` que define como es una persona (su nombre, su edad, lo queuede hacer).
Despues con esa clase podes crear muchos objetos diferentes (Personas diferentes) como Juan, Maria, Lucas todos basados en el mismo molde.
Asi cada persona tiene sus datos y comportamientos propios pero todos comparten la misma estructura.


## Los 4 pilares de la POO
### 🧩 1. Encapsulamiento
- Capacidad para ocultar los datos internos de un objeto y controlar como se accede o modifica esa informacion.
- En vez de dejar que cualquiera cambie los datos de ``Persona`` directamente, la clase define metodos segros para hacerlo. Esto evita errores y proteje el estado del Objeto. 
- EJEMPLO:
```python
class Persona:
    def __init__(self, nombre, edad):
        self.nombre = nombre
        self.__edad = edad # atriburo privado con ( __ )

    def cumplir anios(self):
        self.__edad += 1

    def obtener_edad(self):
        return self.__edad


juan = Persona("Juan", 25)
juan.cumplir_anios()
print(juan.obtener_edad())  # 26

# print(juan.__edad)  -> Esto daría error porque está encapsulado
# 👉 Nadie puede cambiar __edad directamente, solo a través de los métodos que la clase permite.
```
---
### 🧩 2. Abstracción
- Es la capacidad de mostrar solo la informacion escencial y ocultar los detalles internos de un objeto.
- Usas los metodos de un objeto sin saber como funciona por dentro, sabes que {juan.cumplir_anios()} suma un año pero no necesitas ver el codigo que lo hace.
- EJEMPLO:
```python
juan.cumplir_anios()  

# No te importa cómo lo hace internamente
```
---
### 🧩 3. Herencia
- Es la capacidad de heredad atributos y metodos de otra clase, permitiendo reutilizar el codigo y extender funcionalidades.
- Se puede crear una nueva clase basada en otra por ejemplo una clase `Estudiante` que hereda de `Persona` y agrega cosas propias como el curso que estudia
- EJEMPLO:
```python
class   Estudiante(Persona):
    def __init__(self, nombre, edad, curso):
        super().__init__(nombre, edad) # Llama al contructor de persona
        self.curso = curso
    
    def estudiar(self):
        print(f'El estudiante {self.nombre}, esta estudiando: {self.curso})

maria = Estudiante('Maria', 20, 'Programacion')
maria.estudiar()
```
---
### 🧩 4. Polimorfismo
- Es la capacidad de que distintos objetos respondan de forma diferente al mismo metodo, segun la clase
- Dos clases pueden tener un metodo con el mismo nombre pero cada una lo ejecuta distinto. Por ejemplo tanto una persona como un estudiando pueden tener un metodo ``presentarse()`` pero dicen cosas diferentes.
- EJEMPLO:
```python
class Persona:
    def presentarse(self):
        print('Hola soy Persona')

class Estudiante(Persona):
    def presentarse(self):
        print('Hola soy un estudiante')


persona = Persona()
estudiante = Estudiante()

persona.presentarse()  # Hola, soy una persona.
eestudiante.presentarse()  # Hola, soy un estudiante de programación.
```
---
## Mini sistema de personas y estudiantes (aplicando los 4 pilares)
```python
# -----------------------------
# CLASE BASE: Persona
# -----------------------------
class Persona:
    def __init__(self, nombre, edad):
        self.nombre = nombre
        self.__edad = edad  # Encapsulamiento: atributo privado

    def obtener_edad(self): # Encapsulamiento: accedemos a edad solo con métodos
        return self.__edad
    
    def cumplir_anios(self):
        self.__edad += 1

    def presentarse(self): # Polimorfismo: este método se redefinirá en clases hijas
        print(f"Hola, soy {self.nombre} y tengo {self.__edad} años.")



# -----------------------------
# CLASE HIJA: Estudiante
# -----------------------------
class Estudiante(Persona):  # Herencia
    def __init__(self, nombre, edad, curso):
        super().__init__(nombre, edad)  # Llama al constructor de Persona
        self.curso = curso

    def estudiar(self):  # Abstracción: mostramos una acción sin exponer los detalles internos
        print(f"{self.nombre} está estudiando {self.curso}.")

    def presentarse(self):  # Polimorfismo: redefinimos cómo se presenta
        print(f"Hola, soy {self.nombre}, tengo {self.obtener_edad()} años y estudio {self.curso}.")



# -----------------------------
# CLASE HIJA: Profesor
# -----------------------------
class Profesor(Persona):  # Herencia
    def __init__(self, nombre, edad, materia):
        super().__init__(nombre, edad)
        self.materia = materia

    def presentarse(self):  # Polimorfismo: el método se comporta distinto
        print(f"Buenas, soy el profesor {self.nombre} y enseño {self.materia}.")



# -----------------------------
# DEMO: Creación y uso de objetos
# -----------------------------
persona = Persona("Laura", 40)
estudiante = Estudiante("Matías", 22, "Programación")
profesor = Profesor("Carlos", 50, "Matemática")

# Abstracción: usamos métodos sin preocuparnos por cómo están hechos
persona.presentarse()
estudiante.presentarse()
profesor.presentarse()

# Encapsulamiento: no accedemos directamente a la edad, usamos métodos
estudiante.cumplir_anios()
print(f"{estudiante.nombre} ahora tiene {estudiante.obtener_edad()} años.")

# Polimorfismo en acción
personas = [persona, estudiante, profesor]
for p in personas:
    p.presentarse()  # Todos responden distinto al mismo método

```

### que se aplica aca:
| Pilar               | Qué muestra el ejemplo                                                                     |
| ------------------- | ------------------------------------------------------------------------------------------ |
| **Encapsulamiento** | La edad (`__edad`) está protegida, solo se accede con métodos.                             |
| **Abstracción**     | Usás métodos como `cumplir_anios()` o `presentarse()` sin saber su implementación interna. |
| **Herencia**        | “Estudiante” y “Profesor” heredan de “Persona”.                                            |
| **Polimorfismo**    | Cada clase redefine `presentarse()` a su manera.                                           |


