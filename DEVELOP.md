# Guía Técnica de PromptDJ (DEVELOP.md)

Este documento proporciona una visión técnica de la arquitectura y las tecnologías utilizadas en la aplicación PromptDJ, destinado a desarrolladores o personas interesadas en el funcionamiento interno del proyecto.

## Arquitectura General

PromptDJ es una Single Page Application (SPA) construida con tecnologías web modernas. La arquitectura se centra en la reactividad y la eficiencia para manejar un flujo de audio en tiempo real.

-   **Cliente (Frontend):** Toda la lógica reside en el lado del cliente (en el navegador). Se encarga de la interfaz de usuario, la gestión del estado, la comunicación con la API de IA y el procesamiento de audio.
-   **API de IA:** La aplicación se comunica directamente con la API de Google Gemini para utilizar el modelo de generación de música en tiempo real (actualmente, `lyria-realtime-exp`).

## Pila Tecnológica (Stack)

### Lenguaje de Programación

-   **TypeScript:** El código está escrito en TypeScript (`.tsx`). Esto proporciona tipado estático para mejorar la robustez del código, la mantenibilidad y la experiencia de desarrollo al autocompletar y detectar errores de forma temprana.

### Librería de UI

-   **Lit (LitElement):** La interfaz de usuario está construida como una serie de Web Components utilizando [Lit](https://lit.dev/). Esta librería fue elegida por su ligereza, rendimiento excepcional y su excelente encapsulación de componentes. Cada parte de la UI (un control de prompt, un botón, el panel de ajustes) es un componente autónomo y reutilizable.

### Procesamiento de Audio

-   **Web Audio API:** Es el núcleo del manejo de audio en la aplicación. Se utiliza para:
    1.  **Decodificar** los fragmentos de audio (`audioChunks`) que llegan en formato PCM desde la API de Gemini.
    2.  **Programar (`schedule`)** la reproducción de estos fragmentos de forma consecutiva y sin interrupciones (`gapless playback`) para crear un flujo musical continuo.
    3.  **Crear el sintetizador de batería** para los pads interactivos, generando sonidos de percusión proceduralmente y en tiempo real.
    4.  **Gestionar la grabación** de audio a través de un `MediaStreamAudioDestinationNode` y la API `MediaRecorder`, capturando toda la salida de audio.

### Comunicación con la API

-   **SDK de @google/genai:** Se utiliza el SDK oficial de JavaScript para interactuar con la API de Gemini. Específicamente, se establece una conexión persistente (`live.music.connect`) que mantiene un WebSocket abierto para la sesión de música en tiempo real, permitiendo una comunicación de baja latencia.

### Estructura del Proyecto

-   `index.html`: El punto de entrada de la aplicación. Define la estructura básica del DOM y configura un `importmap` para gestionar las dependencias de módulos ES6 (como `lit` y `@google/genai`) directamente en el navegador, evitando la necesidad de un paso de empaquetado (bundling) complejo como Webpack o Vite.
-   `index.tsx`: El archivo principal de la aplicación. Contiene la definición de todos los componentes de Lit, la lógica de estado principal y la orquestación de la comunicación con la API y el `AudioContext`.
-   `index.css`: Contiene los estilos globales que se aplican a la aplicación.
-   `utils.ts`: Un módulo de utilidades con funciones auxiliares, principalmente para la codificación y decodificación de los datos de audio entre Base64 y los formatos que requiere la Web Audio API.
-   `README.md`: Documentación de alto nivel orientada al usuario final, explicando qué es la aplicación y cómo usarla.
-   `DEVELOP.md`: Este mismo archivo, con documentación técnica para desarrolladores.
-   `metadata.json`: Un archivo de configuración específico para el entorno de ejecución de Google AI Studio.
