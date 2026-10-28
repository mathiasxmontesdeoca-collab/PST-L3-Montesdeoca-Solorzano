# Lab 3 Estructura de un backend

Código base del curso para leer, ejecutar y modificar a mano, sin IA.
Hardware de referencia: NodeMCU ESP8266, DHT11 y LED RGB de cátodo común.
Este proyecto es una propuesta de material base para acompañar la guía.

## Backend en Windows
Desde backend, en una terminal Command Prompt de VS Code:
```
py -m venv .venv
.venv\Scripts\python.exe -m pip install -r requirements.txt
.venv\Scripts\python.exe -m uvicorn app.main:app --host 0.0.0.0 --port 8000 --reload
```
Swagger: http://127.0.0.1:8000/docs
En macOS/Linux: python3 -m venv .venv y usar .venv/bin/python.
Los cinco endpoints base están en app/routes.py. La lista guarda como máximo
100 lecturas en memoria y se pierde al reiniciar. Ejecutar un solo proceso.
POST /lecturas retorna HTTP 201 con {"lectura": {...}, "led": "apagado"}.
PUT /led acepta rojo, verde, azul o apagado. No hay autenticación:
utilizar únicamente en la red de laboratorio autorizada.

## Firmware
Abrir firmware con PlatformIO IDE en VS Code. Copiar include/config.example.h
a include/config.h y completar WiFi, URL con IPv4 del PC e identificador.
Build, Upload y Monitor a 115200 baudios. El ESP envía cada 5 segundos y aplica
el color devuelto por POST /lecturas; PUT /led se refleja en el próximo envío.
Conexiones y pruebas: ver guía. Las dependencias se descargan la primera vez.
No publicar config.h, contraseñas ni tokens.

## Reto
Implementar GET /lecturas/promedio?n=3 a mano; no se incluye la solución.
Devolver cantidad, temperatura_promedio y humedad_promedio (dos decimales).
Validar n de 1 a 100; sin lecturas responder 404; si hay menos de n, usar todas.

## Antes de la sesión
El docente debe probar el firmware en el hardware real y la red del aula,
precargar las herramientas y validar las dependencias en los equipos.
La guía destina 105 minutos a trabajar con el entorno ya preparado.


## 1
Método: get
Ruta: http://127.0.0.1:8000/salud
Código esperado: 200; {"estado":"ok"}
Código obtenido: 200; {"estado": "ok"}

## 2
Método: get
Ruta: http://127.0.0.1:8000/lecturas
Código esperado: 200; [] al inicio
Código obtenido: 200; []

## 3
Método: get
Ruta: http://127.0.0.1:8000/led
Código esperado: 200; color apagado
Código obtenido: 200; color apagado

## 4
Método: put
Ruta: http://127.0.0.1:8000/led
Código esperado: 200; {"color":"verde"}
Código obtenido: 200; {"estado": "verde"}

## 5
Método: post
Ruta: http://127.0.0.1:8000/lecturas
Código esperado: 201; lectura y led verde
Código obtenido: 201; lectura y {"color": "rojo"}

## 6
Método: get
Ruta: http://127.0.0.1:8000/lecturas
Código esperado: 200; contiene la lectura enviada
Código obtenido: 200; [
  {
    "dispositivo": "equipo01",
    "temperatura": 25,
    "humedad": 60
  },
  {
    "dispositivo": "equipo01",
    "temperatura": 25,
    "humedad": 60
  },
  {
    "dispositivo": "equipo01",
    "temperatura": 25,
    "humedad": 60
  },
  {
    "dispositivo": "string",
    "temperatura": 50,
    "humedad": 100
  }
]

## 7
Método: get
Ruta: http://127.0.0.1:8000/led
Código esperado: 200; {"color":"verde"}
Código obtenido: 200; {"color": "rojo"}

## 8
Método: post - get
Ruta: http://127.0.0.1:8000/lecturas
Código esperado: 422, no registra nada
Código obtenido: 422, no registró nada}

## Matías Sebastián Montesdeoca Solórzano
Objetivos:
●	Identificar la entrada de la aplicación, las rutas, los modelos, la lógica y el almacenamiento.
●	Ejecutar la API localmente y probar sus cinco endpoints desde Swagger.
●	Conectar el ESP8266 con el backend y comprobar el cambio del LED.
●	Agregar un endpoint y publicar su trabajo en su repositorio de GitHub.

## Pregunta 1
Recibe post y se guarda en services.py
## Pregunta 2
El archivo reoutes.py es el que sirve para agregar URLs adicionales o nuevas dentro de la app: @route.metodo_a_usar(la:ruta): la función que tenga relación con el método
## Pregunta 3
Se borran todos los registros y el LED vuelve a su estado inicial porque store.py guarda el estado únicamente en memoria RAM
## Pregunta 4
A pesar de que la petición estaba bien escrita el valor amarillo para el LED no esta definido por lo que se genera un error 4xx de usuario a pesar de que la sintaxis del JSON sea correcta
## Pregunta 5
El recorrido es que el ESP8266 realiza la lectura correspondiente sobre la humedad a través del sensor y emite una petición HTTP de tipo POST en formato JSON a la URL del servidor, service.py guarda la lectura y store.py contiene los datos en memoria.
El LED cambia en cada POST de lecturas.