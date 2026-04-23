#  Starshit Valley

![Unity](https://img.shields.io/badge/Unity-100000?style=for-the-badge&logo=unity&logoColor=white)
![C#](https://img.shields.io/badge/C%23-239120?style=for-the-badge&logo=c-sharp&logoColor=white)
![.NET](https://img.shields.io/badge/.NET_Standard_2.1-5C2D91?style=for-the-badge&logo=.net&logoColor=white)
![Rust](https://img.shields.io/badge/Rust-000000?style=for-the-badge&logo=rust&logoColor=white)
![WebSockets](https://img.shields.io/badge/WebSockets-010101?style=for-the-badge&logo=socket.io&logoColor=white)

Un videojuego 2D que combina mecánicas profundas de simulación agrícola, economía y supervivencia, respaldado por una arquitectura de red cliente-servidor construida desde cero para soportar multijugador en tiempo real. 

##  Características del Juego (Gameplay)

Este proyecto no es solo una prueba de red, es un ecosistema completo de juego:
* ** Sistema de Agricultura:** Mecánicas completas para arar la tierra, plantar semillas, regar y cosechar cultivos.
* ** Sobrevivencia:** El entorno es un reto. Incluye sistema de salud, control de temperatura (el clima afecta al jugador) y un medidor de hambre que requiere consumir alimentos para recuperar energía.
* ** Entorno Dinámico:** Implementación de un ciclo de Día y Noche que transforma la inmersión y la estética del mapa.
* ** Economía e Inventario:** Sistema modular de inventario para gestionar objetos y una Tienda funcional para comprar suministros y vender las cosechas.

##  Arquitectura Multijugador en Tiempo Real

El sistema multijugador permite a los usuarios coexistir en el mismo servidor de forma fluida.
* **Sincronización de Entidades ("Clones"):** Cuando un jugador nuevo se conecta, el servidor envía una señal JSON de `login`. Unity intercepta esta señal, instancia dinámicamente un Prefab del personaje en las pantallas de los demás y lo registra en un **Diccionario (`Dictionary<string, GameObject>`)**. Esto permite rastrear y actualizar las coordenadas `X` e `Y` de cada jugador de manera independiente.
* **Chat Global Inteligente:** Sistema de mensajería con historial optimizado (memoria de líneas para evitar desbordamiento visual). Incluye un gestor de **Foco de Input (EventSystem)** que aísla el teclado: al chatear, se bloquean automáticamente los controles de movimiento (WASD) y los atajos del menú (P, E) para una experiencia fluida.

##  Stack Tecnológico

El proyecto separa estrictamente la capa de presentación de la capa de red:

### Frontend (Cliente): Unity & C# (.NET Standard 2.1)
* Se encarga del renderizado 2D, animaciones, lectura de inputs, lógica de inventarios y UI.
* Utiliza las librerías nativas de **.NET** para la conexión por WebSockets (`System.Net.WebSockets`).

### Backend (Servidor): Rust
* Servidor multijugador asíncrono diseñado para máxima velocidad y seguridad de memoria.
* **Librerías principales:** * `tokio`: Para concurrencia y manejo asíncrono de múltiples hilos de jugadores.
  * `axum`: Para el enrutamiento de la conexión WebSocket.
  * `serde_json`: Para la serialización y deserialización de los paquetes de datos.

##  Retos de Ingeniería Resueltos

Durante el desarrollo, se resolvieron problemas críticos de concurrencia y red:
1. **Control de Saturación (Tick Rate):** Se optimizó el envío de coordenadas desde el `Update` de Unity (60 FPS) a un temporizador de red (0.05s / 20 FPS). Esto previno el colapso de la tarjeta de red virtual y solucionó errores críticos de `SocketException`.
2. **Tolerancia a Fallos (Heartbeats):** Se blindó el servidor de Rust con un patrón `match` exhaustivo para ignorar conexiones silenciosas y latidos de control (`Ping/Pong`) del WebSocket de Unity, evitando que los hilos entraran en pánico (`Crash`).
3. **Manejo de Embotellamientos (Lagged Channels):** Se escaló el buffer del canal de transmisión (Broadcast Channel) a 1024 y se implementó lógica para capturar errores de rezago (`RecvError::Lagged`), permitiendo al servidor descartar paquetes de movimiento viejos sin desconectar a los jugadores.

##  Instalación y Ejecución

### 1. Levantar el Servidor (Rust)
Asegúrate de tener [Rust y Cargo](https://www.rust-lang.org/tools/install) instalados.
```bash
cd Server
cargo run
