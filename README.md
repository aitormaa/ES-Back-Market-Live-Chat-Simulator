# Back Market – Simulador de Formación en Live Chat

Una herramienta de formación interactiva basada en el navegador, diseñada para los agentes del servicio de atención al cliente de Back Market. Dos usuarios se conectan en tiempo real: uno interpreta al **Agente** y el otro al **Cliente**, para practicar la gestión del chat en directo a través de escenarios de soporte realistas.

> **Demo en línea:** [https://aitormaa.github.io/ES-Back-Market-Live-Chat-Simulator/](https://aitormaa.github.io/ES-Back-Market-Live-Chat-Simulator/)

---

## Funcionalidades

### Multijugador para dos usuarios

- Dos participantes se conectan mediante un **Código de Sesión** compartido
- Un usuario elige el nivel de dificultad y se convierte en el **Agente**
- El otro se une con el código y se convierte en el **Cliente**
- Sincronización en tiempo real vía **PeerJS (WebRTC)** con fallback de localStorage + BroadcastChannel
- Funciona en dos pestañas del mismo navegador o en dos dispositivos distintos

### 52 Escenarios de Formación – 3 Niveles de Dificultad

| Nivel | Rango de códigos | Escenarios | Áreas clave |
|---|---|---|---|
| Principiante | B01–B11 | 11 | Seguimiento de pedidos, facturas, cancelaciones, garantías, política de devoluciones |
| Intermedio | I01–I30 | 30 | Defectos del dispositivo, reclamaciones de garantía, disputas de reembolso, resolución técnica |
| Experto | A01–A11 | 11 | Acusaciones de fraude, infracciones RGPD, clientes hostiles, amenazas legales, usuarios vulnerables |

Cada escenario incluye:

- **Perfil del cliente** (nombre, dispositivo, detalles del pedido, estado de ánimo)
- **Mensaje inicial** del cliente
- **Guía de resolución** (visible únicamente para el Agente)
- **Pistas** sobre el comportamiento del cliente
- **Desafíos clave** a trabajar

### Temporizadores de Rendimiento

- **FRT** – Tiempo de Primera Respuesta (objetivo: menos de 60 s), cuenta atrás automática desde el inicio de la sesión
- **NRT** – Tiempo de Respuesta Siguiente (objetivo: menos de 90 s), se reinicia con cada mensaje del cliente
- **Temporizador de Sesión** – tiempo total transcurrido

### Comportamiento Automatizado del Cliente

- Sin respuesta del agente tras **60 segundos**: se envía un mensaje automático de cola de espera
- Sin respuesta tras **120 segundos**: el chat se transfiere automáticamente al modo asíncrono

### Herramientas del Agente

- Alternancia **RESPUESTA / NOTA** (las notas internas no son visibles para el cliente)
- **Biblioteca de macros** (8 respuestas predefinidas: saludo, espera, escalada, cierre, nota de resumen, etc.)
- **Carga de archivos e imágenes** con compresión en el lado del cliente (máx. 600px, JPEG 65 %)
- Campos del ticket: **Estado**, **Prioridad**, **Categoría**

### Puntuación QA y Análisis de Empatía

- **Puntuación de Empatía** automática calculada a partir de los mensajes del agente (detección de palabras clave, uso del nombre del cliente, frecuencia)
- **Lista de verificación QA** manual con puntuación ponderada
- **Informe de evaluación** completo al cerrar el chat

---

## Primeros Pasos

### Opción 1 – Usar la versión en línea

Sin instalación. Abrir [https://aitormaa.github.io/ES-Back-Market-Live-Chat-Simulator/](https://aitormaa.github.io/ES-Back-Market-Live-Chat-Simulator/) en dos ventanas del navegador o en dos dispositivos.

### Opción 2 – Ejecutar en local

Al tratarse de un único archivo HTML sin proceso de compilación:

```bash
git clone https://github.com/aitormaa/backmarket-chat-simulator.git
cd backmarket-chat-simulator
open index.html
```

> **Nota:** Algunos navegadores restringen localStorage y BroadcastChannel en URL `file://`. Para mejores resultados, usar un servidor local:

```bash
npx serve .
# o
python3 -m http.server 8080
```

---

## Cómo Iniciar una Sesión

### Agente (Formador o Aprendiz)

1. Abrir el simulador
2. Elegir un nivel de dificultad: **Principiante**, **Intermedio** o **Experto**
3. Introducir el nombre
4. Se genera un **Código de Sesión de 6 caracteres** – compartirlo con el compañero
5. Esperar a que el cliente se una y el chat comenzará

### Cliente (Jugador de Rol)

1. Abrir el simulador en otra pestaña o dispositivo
2. Hacer clic en **«Unirse como Cliente»**
3. Introducir el Código de Sesión compartido por el Agente
4. El escenario se carga automáticamente – el chat puede comenzar

---

## Estructura de Archivos

```
backmarket-chat-simulator/
└── index.html        # Aplicación completa – autónoma, sin dependencias que instalar
```

La aplicación utiliza bibliotecas cargadas vía CDN (sin npm):

- [React 18](https://react.dev/) – framework de UI
- [Tailwind CSS](https://tailwindcss.com/) – estilos
- [Babel Standalone](https://babeljs.io/) – transpilación JSX en el navegador
- [PeerJS 1.5](https://peerjs.com/) – conexión WebRTC entre pares

---

## Arquitectura

```
AGENTE (Pestaña / Dispositivo A)         CLIENTE (Pestaña / Dispositivo B)

Crea el código de sesión                 Se une con el código de sesión
Recibe el escenario + las pistas         Recibe el escenario (sin pistas)
Ve la puntuación QA                      Ve únicamente el chat del lado del cliente

          |                                        |
          +------------ PeerJS WebRTC -------------+
               BroadcastChannel + localStorage (fallback)
```

**Flujo de datos de sesión:**

1. El Agente crea la sesión – el escenario se almacena en `localStorage` bajo el código de sesión
2. PeerJS envía el payload `INIT` al Cliente al establecer la conexión
3. Los mensajes se transmiten vía WebRTC DataChannel + escritura en `localStorage`
4. `BroadcastChannel` mantiene sincronizadas las pestañas del mismo dispositivo (polling de 400 ms como fallback)

---

## Configuración

Todos los escenarios, macros y mensajes automatizados están definidos como arrays de JavaScript simples al inicio de `index.html` – sin backend ni base de datos.

| Constante | Descripción |
|---|---|
| `SC` | Array con los 52 escenarios de formación |
| `MACROS` | Plantillas de macros para el Agente (soporta `{CUSTOMER_FNAME}`, `{CURRENT_USER_FNAME}`) |
| `AUTO` | Mensajes automáticos enviados por inactividad del Agente a los 60 s y 120 s |
| `PCFG` | Configuración de PeerJS (servidores STUN) |

---

## Detalles de Puntuación

### Puntuación de Empatía (calculada automáticamente, máx. 25 pts)

Analiza todos los mensajes del Agente en busca de:

- Palabras clave de empatía (`entiendo`, `lo siento`, `disculpa`, `con mucho gusto`, etc.)
- Uso del **nombre del cliente**
- Frecuencia y consistencia a lo largo de la conversación

### Puntuación QA (lista de verificación manual)

Los evaluadores revisan elementos en las siguientes categorías:

- Calidad del saludo
- Identificación del problema
- Corrección de la solución
- Cumplimiento de políticas
- Claridad de la comunicación
- Velocidad de respuesta (objetivos FRT y NRT)

---

## Hoja de Ruta

- [ ] Backend Firebase / Supabase para historial persistente de sesiones
- [ ] Panel de administración con análisis a nivel de equipo
- [ ] Versión en español (`index-es.html`)
- [ ] Inyección de escenarios por el formador durante la sesión
- [ ] Informe de evaluación exportable en PDF

---

## Colaboradores

Desarrollado por el equipo de Learning & Development / Atención al Cliente de Back Market.

---

## Licencia

Solo para uso interno – herramienta propietaria de Back Market. No está destinada a distribución pública.
