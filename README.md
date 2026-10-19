Back Market - Simulador de entrenamiento de Live Chat
Herramienta de entrenamiento basada en navegador para agentes de atención al cliente de Back Market. Dos personas se conectan a la misma sesión de chat. Una persona actúa como Agente y la otra como Cliente.
Demo en vivo
https://aitormaa.github.io/ES-Back-Market-Live-Chat-Simulator/
Funciones
Sesiones en vivo para dos personas

    El Agente crea un código de sesión de seis caracteres.
    El Cliente se une con ese código.
    Firebase Realtime Database sincroniza las sesiones y los mensajes entre dispositivos.
    Firebase Anonymous Authentication protege el acceso a la base de datos sin exigir cuentas de usuario.
    PeerJS WebRTC puede crear una conexión directa cuando la red lo permite.
    Si WebRTC está bloqueado por la red corporativa, Firebase mantiene la sincronización.

52 escenarios de entrenamiento
Nivel	Rango	Escenarios	Temas
Principiante	B01-B11	11	Seguimiento de pedidos, facturas, cancelaciones, garantías y devoluciones
Intermedio	I01-I30	30	Defectos de dispositivos, garantías, reembolsos y resolución técnica
Avanzado	A01-A11	11	Acusaciones de fraude, temas de RGPD, clientes difíciles, amenazas legales y clientes vulnerables
Cada escenario incluye una persona de cliente, datos del dispositivo y del pedido, un mensaje inicial, una guía de resolución, pistas sobre el comportamiento del cliente y objetivos de práctica.
Temporizadores

    FRT: tiempo hasta la primera respuesta, con un objetivo de 60 segundos.
    NRT: tiempo hasta la siguiente respuesta, con un objetivo de 90 segundos.
    Temporizador de sesión: duración total de la sesión.

Herramientas del Agente

    Modos de respuesta y nota interna.
    Biblioteca de macros para respuestas frecuentes.
    Adjuntos de archivos e imágenes con compresión en el navegador.
    Campos de ticket para estado, prioridad y categoría.
    Evaluación QA y análisis de empatía.

Primeros pasos
Usar la versión en vivo
Abre la demo en dos ventanas del navegador o en dos dispositivos:
https://aitormaa.github.io/ES-Back-Market-Live-Chat-Simulator/
La versión española usa la ruta Firebase sessions/es.
Ejecutar localmente
El proyecto no necesita un paso de compilación:

git clone https://github.com/aitormaa/ES-Back-Market-Live-Chat-Simulator.git
cd ES-Back-Market-Live-Chat-Simulator
python3 -m http.server 8080

Abre esta dirección:
http://localhost:8080
Usar un servidor local es más fiable que abrir el archivo directamente con file://.
Ejecutar una sesión
Agente

    Abre el simulador.
    Selecciona el rol Agente.
    Selecciona la dificultad.
    Escribe tu nombre.
    Comparte el código de sesión de seis caracteres.
    Espera a que se una el Cliente.

Cliente

    Abre el simulador en otro dispositivo o navegador.
    Selecciona el rol Cliente.
    Introduce el código del Agente.
    Comienza la conversación.

Estructura del repositorio

ES-Back-Market-Live-Chat-Simulator/
├── index.html
└── README.md

La configuración de Firebase está incluida en index.html. No necesitas subir un archivo firebase-config.js con esta versión independiente.
Configuración de Firebase
El simulador utiliza Firebase Anonymous Authentication y Firebase Realtime Database. La configuración web de Firebase está incluida en el HTML porque forma parte de la configuración pública de una aplicación web.
La protección de los datos depende de Authentication y de las reglas de Realtime Database.
Configuración necesaria

    Anonymous Authentication activado.
    Realtime Database activada.
    aitormaa.github.io añadido como dominio autorizado en Firebase Authentication.
    Reglas de Realtime Database publicadas.

Usa estas reglas:

Rules
Sessions
$locale
$code
.read
auth != null
.write
auth != null
.validate
newData.hasChildren(['scenario', 'closed'])

No hagas que messages sea obligatorio en la validación. Una sesión nueva empieza con una lista de mensajes vacía.
Las sesiones españolas se guardan en:

sessions/es/SESSION_CODE

Arquitectura
Firebase es la fuente principal de verdad para sesiones y mensajes. El navegador mantiene una caché local. PeerJS WebRTC puede establecer una conexión directa cuando la red lo permite.
Si aparece el mensaje WebRTC: ICE failed, Firebase debería seguir sincronizando la sesión y los mensajes.
Evaluación
El simulador calcula un Empathy Score y permite completar una lista de QA con criterios como:

    Calidad del saludo.
    Identificación del problema.
    Corrección de la solución.
    Cumplimiento de las políticas.
    Claridad de la comunicación.
    Velocidad de respuesta.

Solución de problemas
Si la consola muestra:

Firebase is not configured. Local-only mode is active.

Comprueba que estás utilizando la versión Firebase de index.html.
Si aparece:

PERMISSION_DENIED

Comprueba que Anonymous Authentication está activado y que las reglas de Realtime Database están publicadas.
Roadmap

    Unificar los cuatro repositorios de idiomas.
    Crear un panel de administración con métricas de equipo.
    Exportar informes de sesión en PDF.
    Guardar archivos grandes en Firebase Storage.

Licencia
Solo para uso interno. Herramienta propietaria de Back Market. No distribuir públicamente.
