# Middleware
## Concepto
- Un middleware es un componente intermedio que se ejecuta entre el request y el response dentro de una aplicacion.
- Su funcion es interceptar, modificar o controlar las peticiones y respuestas antes o despues de que lleguen al manejardor principal (la ruta o el endpoint)
- En resumen es una capa de codigo que envuelve la logica principal de tu aplicacion para aplicar comportamientos comunes (autenticacion, logs manejo de errores, etc)

## Explicacion Simple:
Imagina una app de un restaurante:
- El cliente (usuario) hace un pedido (la request).
- El chef (tu endpoint) cocina la comida.
- Pero antes de llegar al chef, el pedido pasa por el mozo o recepcionista (middleware), que puede:
    - Revisar si el cliente tiene reserva (autenticación),
    - Anotar la hora en que llegó (logging),
    - Filtrar si trae algo raro (seguridad),
    - Y después dejar pasar el pedido al chef.

El Middleware no reemplaza al endpoint, lo rodeo

## Para que se usa:
Los middlewares se usan para tareas transversales, o sea, que se aplican a todas o varias rutas, por ejemplo:
- 🔒 Autenticación y autorización
- 📜 Logging de peticiones/respuestas
- ⏱️ Medición de tiempos de respuesta
- 🧩 Manejo global de errores
- 🌍 CORS (Cross-Origin Resource Sharing)
- 🧹 Limpieza o modificación de headers

## Ejemplo en código (FastAPI – Python)
FastAPI permite crear middlewares fácilmente con el decorador @app.middleware("http").
### ✅ Ejemplo: Middleware de logging y tiempo de ejecución
```python
from fastapi import FastAPI, Request
import time

app = FastAPI()

@app.middleware("http")
async def log_requests(request: Request, call_next):
    start_time = time.time()

    # Antes de procesar la request
    print(f"➡️  Petición: {request.method} {request.url}")

    response = await call_next(request)  # llama al siguiente paso (la ruta)

    # Después de procesar la request
    duration = time.time() - start_time
    print(f"⬅️  Respuesta: {response.status_code} | Tiempo: {duration:.3f}s")

    # Podés modificar la respuesta si querés
    response.headers["X-Process-Time"] = str(duration)
    return response


@app.get("/hola")
async def hola():
    return {"mensaje": "Hola Mati!"}
```
### ¿Qué pasa acá?
1. Cada vez que entra una request, el middleware se ejecuta primero.
2. Mide el tiempo, imprime logs, y llama a call_next(request) para seguir el flujo.
3. Cuando el endpoint devuelve la respuesta, el middleware vuelve a tomar el control, calcula el tiempo total y puede modificar la respuesta (por ejemplo, agregar headers).


## Ejemplo común en la práctica
Un middleware de autenticación básica o token JWT:
```python
from fastapi import FastAPI, Request, Response

app = FastAPI()

@app.middleware("http")
async def verificar_token(request: Request, call_next):
    token = request.headers.get("Authorization")
    if token != "Bearer 12345":
        return Response(content="Acceso denegado", status_code=401)
    return await call_next(request)

@app.get("/datos")
async def datos():
    return {"mensaje": "Acceso concedido"}
```
👉 Antes de llegar al endpoint, el middleware chequea el header Authorization.
Si no cumple, corta el flujo y devuelve 401 sin ejecutar el endpoint.

## En Resumen:
| Concepto             | Descripción                                                        |
| -------------------- | ------------------------------------------------------------------ |
| **Qué es**           | Código que intercepta las peticiones antes o después del endpoint. |
| **Para qué sirve**   | Aplicar reglas o lógica común a todas las rutas.                   |
| **Dónde se usa**     | En frameworks web (FastAPI, Flask, Express, etc.).                 |
| **Ejemplos comunes** | Logs, auth, CORS, rate limiting, manejo de errores.                |


---

# nodeJS

## 🧩 1. Concepto real general
Un middleware en Express es una función que se ejecuta antes (o después) de que una ruta procese una petición HTTP.
👉 Puede leer, modificar o cortar la request/response antes de que llegue al controlador principal.
En Express, todos los middlewares reciben tres parámetros:
```js
(req, res, next)
```
- ``req``: la petición entrante.
- ``res``: la respuesta que se va a enviar.
- ``next()``: una función que llama al siguiente middleware o ruta.

## Explicación simple
Pensalo igual que antes:
El middleware es como un filtro o guardia en la puerta del endpoint.
Intercepta la petición, puede hacer algo (chequear token, loguear, medir tiempo, etc.), y después:
- llama a ``next()`` si todo está bien, o
- corta y responde directo si hay un error o no tiene permiso.


## Ejemplo común y claro
### Ejemplo: Middleware de logging y tiempo de ejecución
```js
const express = require("express");
const app = express();

// Middleware global
app.use((req, res, next) => {
  const start = Date.now();
  console.log(`➡️ ${req.method} ${req.url}`);

  // Cuando termina la respuesta, calculamos el tiempo
  res.on("finish", () => {
    const duration = Date.now() - start;
    console.log(`⬅️ ${res.statusCode} | ${duration}ms`);
  });

  next(); // sigue al siguiente middleware o ruta
});

app.get("/hola", (req, res) => {
  res.send({ mensaje: "Hola Mati!" });
});

app.listen(3000, () => console.log("Servidor corriendo en puerto 3000"));
```
🧠 Qué pasa acá:
1. Cada request pasa primero por el middleware (app.use(...)).
2. Se loguea el método y la URL.
3. Después se ejecuta el endpoint (/hola).
4. Cuando Express termina de enviar la respuesta, el middleware registra el tiempo total.

## Ejemplo práctico: Autenticación simple
```js
const express = require("express");
const app = express();

function verificarToken(req, res, next) {
  const token = req.headers["authorization"];

  if (token !== "Bearer 12345") {
    return res.status(401).json({ error: "Acceso denegado" });
  }

  next(); // deja pasar al endpoint
}

app.use(verificarToken); // aplica el middleware a todas las rutas

app.get("/datos", (req, res) => {
  res.json({ mensaje: "Acceso concedido" });
});

app.listen(3000, () => console.log("Servidor en puerto 3000"));
```
👉 Si no mandás el header ``Authorization: Bearer 12345``, no podés acceder a ``/datos``.
El middleware intercepta y bloquea la request antes de llegar a la ruta.


## En resumen:
| Concepto            | Descripción                                                           |
| ------------------- | --------------------------------------------------------------------- |
| **Qué es**          | Función que intercepta y procesa requests antes o después de la ruta. |
| **Cómo se define**  | Con `app.use()` o pasando la función en una ruta.                     |
| **Para qué sirve**  | Logging, seguridad, manejo de errores, CORS, etc.                     |
| **Ejemplo clásico** | Autenticación, tiempos de respuesta, rate limiting.                   |
