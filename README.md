# PosturaPro TM

Monitor de postura en tiempo real que usa un modelo entrenado por el propio
usuario en **Google Teachable Machine (Pose Project)**. 100% client-side,
sin servidor, sin que el video salga del navegador.

> Proyecto académico — Programación Avanzada
> Institución Universitaria Pascual Bravo · 2026

---

## Demo

- **App live:** [https://jeffersonparedes353-netizen.github.io/posturapro-tm/](https://jeffersonparedes353-netizen.github.io/posturapro-tm/) (activar GitHub Pages — ver abajo)
- **Modo demo** incluido: no necesita cámara ni modelo, útil para exponer si falla el internet.

![screenshot](docs/screenshot.png)

---

## ¿Qué problema resuelve?

Estudiantes y teletrabajadores pasan 6+ horas frente a la pantalla con malas
posturas; cuando notan el dolor, el daño cervical o lumbar ya está instalado.
Las soluciones existentes requieren hardware caro (Upright GO ~$100) o
mandan el video a servidores externos (riesgo de privacidad).

**PosturaPro TM** permite a cualquiera entrenar su propio modelo de postura
con 10 minutos de grabación en Teachable Machine y usar la cámara del
portátil para recibir alertas en tiempo real cuando mantiene una mala
postura por más de 5 segundos.

---

## Características

- Carga modelos TM Pose desde URL pública (TensorFlow.js).
- Clasificación en vivo con barras de probabilidad por clase.
- Alertas visuales + sonoras cuando se sostiene mala postura >5s.
- Registro de sesión con segmentos continuos por clase.
- Exportación de reporte en JSON (nombres de clase, timestamps, duraciones, resumen).
- **Modo demo** con predicciones simuladas (sin modelo, sin cámara).
- Un solo archivo HTML, sin dependencias de build.

---

## Cómo usar

### 1. Entrenar tu modelo (una vez)

Ver guía completa en [`docs/guion-exposicion.md`](docs/guion-exposicion.md).
Resumen:

1. Ve a [teachablemachine.withgoogle.com](https://teachablemachine.withgoogle.com) → New Project → **Pose Project**.
2. Crea 2–4 clases: **Postura correcta**, **Encorvado**, **Cabeza adelante**, **Hombros caídos**.
3. Graba **mínimo 3 tomas separadas por clase** (importante: no una toma larga — reduce data leakage).
4. Click **Train Model** → espera 1–2 min.
5. **Export Model** → tab **Tensorflow.js** → **Upload (shareable link)** → **Upload my model** → copia la URL.

### 2. Usar el monitor

1. Abre `index.html` localmente o visita la URL de GitHub Pages.
2. Pega la URL de tu modelo en el campo.
3. Click **Iniciar monitoreo** y acepta el permiso de cámara.
4. Al terminar, click **Exportar JSON** para guardar el reporte.

---

## Arquitectura

```
Teachable Machine (entrenamiento, una vez)
    └── Transfer learning sobre PoseNet + MLP
            └── Export TensorFlow.js
                    └── URL pública del modelo (model.json + weights.bin + metadata.json)

Navegador del usuario (ejecución)
    ├── index.html carga TF.js + @teachablemachine/pose desde CDN
    ├── getUserMedia → captura webcam
    ├── Loop (requestAnimationFrame):
    │     PoseNet estima 17 keypoints → modelo.predict() → probabilidades por clase
    ├── Segmentación por clase + debounce
    ├── Detección de alertas (mala postura >5s)
    └── Export JSON de sesión (via claude.use("downloads") o <a download>)
```

**Stack:** HTML + CSS + JavaScript vanilla · TensorFlow.js 1.3 · `@teachablemachine/pose` 0.8 · PoseNet · Web APIs (MediaDevices, Canvas, AudioContext).

---

## Privacidad

- **Video nunca sale del dispositivo.** El stream vive en el `HTMLVideoElement`; no hay backend.
- Solo viaja por red la descarga inicial del modelo desde el CDN de Teachable Machine.
- El JSON del reporte contiene únicamente etiquetas y timestamps, nunca imágenes.
- Verificable abriendo **DevTools → Network** durante el uso: después de cargar el modelo, no hay más peticiones.

Compatible con la **Ley 1581 de 2012 (Habeas Data)** colombiana por diseño:
no hay tratamiento de datos personales en el sentido legal porque nada se
transmite ni almacena externamente.

---

## Limitaciones

- Requiere buena iluminación y persona completa en cuadro.
- Precisión depende de la calidad del entrenamiento del usuario (número y diversidad de muestras).
- No distingue micro-movimientos (apoyar cabeza en mano, inclinación lateral leve).
- Si la pestaña pierde foco, `requestAnimationFrame` se ralentiza (limitación del navegador).
- Un solo modelo activo a la vez.
- La URL pública del modelo en Teachable Machine caduca si no se usa por meses.

---

## Mejoras futuras

- Persistir historial de sesiones en IndexedDB (ver evolución semanal).
- Integración con Google Calendar (sugerir pausa activa cada 50 min).
- Modo "ergonomía de escritorio" combinando pose + distancia al monitor.
- Convertir a PWA instalable + notificaciones push.
- Exportar reporte como PDF.
- Permitir cargar un modelo descargado localmente (sin dependencia del CDN de TM).

---

## Estructura del repositorio

```
posturapro-tm/
├── index.html                    MVP completo (HTML + CSS + JS en un solo archivo)
├── README.md                     Este archivo
├── LICENSE                       MIT
├── .gitignore
└── docs/
    └── guion-exposicion.md       Guion completo + respuestas a preguntas del docente
```

---

## Activar GitHub Pages

Para que `index.html` quede servido como URL pública:

1. En GitHub, ve a **Settings** del repositorio.
2. Barra lateral izquierda → **Pages**.
3. **Source:** Deploy from a branch → **Branch:** `main` / `(root)` → **Save**.
4. En 1–2 minutos estará disponible en `https://<tu-usuario>.github.io/posturapro-tm/`.

---

## Licencia

MIT — ver [LICENSE](LICENSE).

## Autor

**Jefferson Paredes - Jhon Deyby HIGUITA - Jorman Santiago** — Ingeniería de Software, Pascual Bravo (2026)
