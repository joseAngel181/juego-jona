# 🌟 Starshit Valley

![Unity](https://img.shields.io/badge/Unity-100000?style=for-the-badge&logo=unity&logoColor=white)
![C#](https://img.shields.io/badge/C%23-239120?style=for-the-badge&logo=c-sharp&logoColor=white)
![.NET](https://img.shields.io/badge/.NET_Standard_2.1-5C2D91?style=for-the-badge&logo=.net&logoColor=white)
![Rust](https://img.shields.io/badge/Rust-000000?style=for-the-badge&logo=rust&logoColor=white)
![WebSockets](https://img.shields.io/badge/WebSockets-010101?style=for-the-badge&logo=socket.io&logoColor=white)

Un videojuego 2D que combina mecánicas profundas de simulación agrícola, economía y supervivencia, respaldado por una arquitectura de red cliente-servidor construida desde cero para soportar multijugador en tiempo real. 

---

## 🚀 ¿Cómo jugar? (Instrucciones para Jugadores)

Si solo vienes a divertirte en la granja, sigue estos pasos para abrir el juego correctamente:

### 1. Instalación y Ejecución
1. **¡NO LO JUEGUES DESDE EL ZIP!** Es súper importante que extraigas (descomprimas) toda la carpeta en tu computadora (por ejemplo, en tu Escritorio).
2. Entra a la carpeta extraída.
3. Haz doble clic en el archivo **`Starshit Valley.exe`**.
4. ¡Ingresa tu nombre, dale a Entrar y conéctate al servidor!

*(**Nota de Windows:** Si te sale una pantalla azul que dice "Windows protegió su PC", haz clic en **"Más información"** y luego en **"Ejecutar de todas formas"**. Si te pide permisos de Firewall, acéptalos para que el multijugador pueda conectarse a internet).*

### 2. Controles Básicos
* **W, A, S, D** - Mover a tu personaje por el mapa.
* **Clic Izquierdo** - Usar la herramienta seleccionada (Arar la tierra, regar, talar árboles, etc.).
* **E** - Abrir / Cerrar el Inventario para gestionar tus objetos.
* **Enter** - Abrir el Chat Global para hablar con otros. *(Escribe tu mensaje y presiona Enter de nuevo para enviarlo).*
* **P / Escape** - Abrir el menú de pausa.

### 3. Guía Rápida de Supervivencia
* **Las Herramientas:** Busca el cofre inicial en el mapa para obtener tu hacha, pala, azada y regadera.
* **La Comida es Vida:** Usa la azada en la tierra, compra semillas (hablando con el loro), plántalas y riégalas. Cómetelas para recuperar energía y salud.
* **Cuidado con la noche:** Vigila tu medidor de temperatura cuando se haga de noche. ¡Tala árboles (5 golpes con el hacha) para hacer una fogata y no morir de frío!

---

## 🌾 Características del Juego (Gameplay)

Este proyecto no es solo una prueba de red, es un ecosistema completo de juego:
* **Sistema de Agricultura:** Mecánicas completas para arar, plantar, regar y cosechar.
* **Sobrevivencia:** El entorno es un reto. Incluye salud, temperatura y hambre.
* **Entorno Dinámico:** Ciclo de Día y Noche que transforma la inmersión visual.
* **Economía:** Sistema modular de inventario y una Tienda funcional para comerciar.

---

## 🛠️ Detrás de Cámaras: Arquitectura y Tecnología

Para los curiosos, desarrolladores o reclutadores, el proyecto separa estrictamente la capa de presentación de la capa de red:

### Frontend (Cliente): Unity & C# (.NET Standard 2.1)
* Se encarga del renderizado 2D, animaciones, lectura de inputs, lógica de inventarios y la interfaz gráfica (UI).
* Utiliza las librerías nativas de **.NET** para la conexión por WebSockets (`System.Net.WebSockets`).
* **Sincronización de "Clones":** Unity instancia dinámicamente Prefabs de otros jugadores al recibir la señal de `login` del servidor, registrándolos en un `Dictionary<string, GameObject>` para rastrear sus posiciones en tiempo real.
* **Gestor de Foco (UI):** Un sistema inteligente aísla el teclado al chatear, bloqueando automáticamente los controles de movimiento (WASD) y los atajos del menú.

### Backend (Servidor): Rust
* Servidor multijugador asíncrono diseñado para máxima velocidad y evitar caídas (Zero Panics).
* **Librerías principales:** * `tokio`: Para concurrencia y manejo asíncrono de múltiples hilos.
  * `axum`: Para el enrutamiento de la conexión WebSocket.
  * `serde_json`: Para serializar y deserializar paquetes de datos.

## 🚧 Retos de Ingeniería Resueltos

Durante el desarrollo, superamos problemas críticos de concurrencia:
1. **Control de Saturación (Tick Rate):** Se optimizó el envío de coordenadas desde el `Update` de Unity (60 FPS) a un temporizador de red (0.05s / 20 FPS), previniendo `SocketExceptions`.
2. **Tolerancia a Fallos (Heartbeats):** Se blindó el servidor de Rust con un patrón `match` exhaustivo para ignorar conexiones silenciosas y latidos de control (`Ping/Pong`), evitando que los hilos colapsaran.
3. **Manejo de Embotellamientos:** Se escaló el buffer del *Broadcast Channel* a 1024 y se manejó el error `RecvError::Lagged` para descartar paquetes viejos sin desconectar a los jugadores bajo alta latencia.

## 👨‍💻 Para Desarrolladores: Levantar el Servidor Local

Si descargaste el código fuente y quieres correr tu propio servidor:
Asegúrate de tener [Rust y Cargo](https://www.rust-lang.org/tools/install) instalados.
```bash
cd Server
cargo run
