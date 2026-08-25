# SPECS.md — AgentHub Admin Panel

## 1. Descripción del Producto

**AgentHub** es una plataforma SaaS donde las empresas pueden alquilar agentes de IA — asistentes inteligentes preconfigurados que se equipan con **skills** (capacidades modulares como navegar por la web, leer documentos o gestionar calendarios) y se despliegan para tareas de negocio específicas.

**El usuario de este panel** es el administrador interno de AgentHub. Desde este panel gestiona usuarios, agentes, skills, contrataciones y monitoriza errores de ejecución. Este prototipo es una referencia visual para el equipo de desarrollo — todos los datos están hardcodeados, sin conexión a backend ni APIs.

---

## 2. Stack Tecnológico y Restricciones

| Aspecto | Decisión |
|---|---|
| **Markup** | HTML5 con etiquetas semánticas (`<nav>`, `<header>`, `<main>`, `<section>`, `<aside>`, `<table>`, `<footer>`) |
| **Estilos** | Tailwind CSS cargado vía CDN. Sin archivos CSS externos. Sin atributos `style` en línea |
| **Interactividad** | JavaScript vanilla únicamente. Sin frameworks (React, Vue, Angular), sin jQuery, sin herramientas de build |
| **Estructura** | Un único archivo `index.html` (o varios archivos HTML enlazados, uno por sección) |
| **Modo oscuro** | Implementado con utilidades `dark:` de Tailwind y clase `dark` en `<html>` |
| **Responsivo** | Usable en viewports de desktop y tablet. Enfoque mobile-first |
| **Datos** | Todos hardcodeados. Sin conexiones a API ni backend |

---

## 3. Estructura Global del Layout

```
┌──────────────────────────────────────────────────────┐
│  <html> (clase "dark" togglable)                     │
│  ┌──────────┬───────────────────────────────────┐    │
│  │          │  <header> ─ Barra superior        │    │
│  │          │  [Título sección]    [🌙 Toggle]  │    │
│  │  <aside> ├───────────────────────────────────┤    │
│  │  Sidebar │                                   │    │
│  │          │  <main>                            │    │
│  │  Logo    │  Contenido de la sección activa    │    │
│  │  Nav ×6  │  (cambia según navegación)         │    │
│  │          │                                   │    │
│  │          │                                   │    │
│  └──────────┴───────────────────────────────────┘    │
└──────────────────────────────────────────────────────┘
```

- **Sidebar `<aside>`**: Fija a la izquierda, altura completa del viewport, ancho fijo (`w-64`). Contiene logo y 6 enlaces de navegación.
- **Barra superior `<header>`**: Fija en la parte superior del área de contenido. Layout flex con título de sección a la izquierda y toggle de modo oscuro a la derecha.
- **Contenido `<main>`**: Ocupa el espacio restante, scrollable. Muestra una `<section>` a la vez según la navegación seleccionada.

**Responsivo**: En tablet (< 1024px) la sidebar puede colapsar a iconos solamente o ser ocultable. El contenido principal ocupa más ancho.

---

## 4. Especificaciones por Sección

### 4.1 Dashboard

**Spec 4.1.1 — Tarjetas de métricas**
Cuatro tarjetas de métricas dispuestas en una cuadrícula responsiva 2×2 (`grid grid-cols-1 md:grid-cols-2 gap-6`). Cada tarjeta contiene:
- Un icono representativo del tipo de métrica.
- Una etiqueta descriptiva (texto gris/muted).
- Un valor numérico hardcodeado en tamaño grande y fuente semibold.

Cada tarjeta usa un color de acento distinto por tipo de métrica:
| Métrica | Valor | Color de acento |
|---|---|---|
| Ingresos totales (este mes) | $15,600 | Verde (`emerald`) |
| Pérdidas por descuentos | $2,340 | Rojo (`rose`) |
| Agentes activos | 2 | Azul (`blue`) |
| Agentes fallando | 1 | Naranja (`amber`) |

Las tarjetas tienen sombra sutil (`shadow-sm`), bordes redondeados (`rounded-lg`), padding generoso (`p-6`), y fondo blanco (`bg-white dark:bg-gray-800`).

**Spec 4.1.2 — Placeholder de gráfico de actividad semanal**
Un `<div>` de ancho completo debajo de las tarjetas de métricas. Borde discontinuo (`border-2 border-dashed border-gray-300 dark:border-gray-600`), bordes redondeados (`rounded-lg`), altura fija (`h-64`). Contiene una etiqueta centrada vertical y horizontalmente: "Gráfico de actividad semanal" en texto gris (`text-gray-400`).

**Spec 4.1.3 — Layout de la sección**
La sección está envuelta en un `<section>` con padding uniforme (`p-6`). Las tarjetas y el gráfico están separados por margen vertical (`mt-6` en el gráfico). El título "Dashboard" se refleja en el `<header>` superior.

---

### 4.2 Gestión de Usuarios

**Spec 4.2.1 — Tabla de usuarios**
Una `<table>` semántica con `<thead>` y `<tbody>`. Mínimo 5 filas de datos hardcodeados. Columnas:
| Columna | Contenido |
|---|---|
| Nombre | Nombre completo del usuario |
| Email | Dirección de correo |
| Plan | Texto: Free, Pro, o Enterprise |
| Estado | Badge con código de color |
| Acciones | Botón ⋮ (dropdown) |

Badges de estado:
- **Activo**: Fondo verde claro, texto verde (`bg-green-100 text-green-800 dark:bg-green-900 dark:text-green-300`).
- **Inactivo**: Fondo gris claro, texto gris.
- **Suspendido**: Fondo rojo claro, texto rojo.

Las filas tienen hover sutil (`hover:bg-gray-50 dark:hover:bg-gray-700/50`). La tabla tiene bordes redondeados y overflow hidden para estilizado limpio.

**Spec 4.2.2 — Dropdown de acciones ⋮**
Un botón con el carácter `⋮` (tres puntos verticales) en la columna de acciones de cada fila. Al hacer clic:
- Se abre un menú flotante posicionado con `absolute` y `z-10`.
- Opciones: **"Ver detalle"** y **"Eliminar"**.
- Las opciones tienen hover con fondo resaltado.
- Solo un dropdown puede estar abierto a la vez (abrir uno cierra los demás).
- Se cierra al hacer clic fuera del área del dropdown (event listener en `document`).

**Spec 4.2.3 — Modal "Ver detalle" del usuario**
Al seleccionar "Ver detalle" se abre un modal overlay:
- **Backdrop**: `<div>` cubriendo toda la pantalla con fondo oscuro semitransparente (`bg-black/50`), `z-40`.
- **Contenedor del modal**: Centrado vertical y horizontalmente (`fixed inset-0 flex items-center justify-center`), `z-50`.
- **Contenido**: Fondo blanco (`bg-white dark:bg-gray-800`), bordes redondeados (`rounded-xl`), sombra (`shadow-xl`), padding (`p-6`), ancho máximo (`max-w-lg w-full`).
- **Header del modal**: Nombre del usuario como título, botón × de cierre en la esquina superior derecha.
- **Body**: Registro completo del usuario mostrando todos los campos (nombre, email, plan, estado, fecha de registro, etc.).
- **Cierre**: Haciendo clic en el botón × o haciendo clic en el backdrop.

---

### 4.3 Gestión de Agentes

**Spec 4.3.1 — Listado de agentes**
Mínimo 4 agentes presentados como cards/filas expandibles dentro de un contenedor. Cada entrada muestra:
- **Nombre del agente** (texto prominente, semibold).
- **Propietario** (texto secundario/muted).
- **Badge de estado**: Activo (verde), Inactivo (gris), Fallando (rojo/amber).
- **Control expandible** para skills (chevron ▼/▲ o texto "Ver skills").
- **Botón ⋮** para dropdown de acciones.

Layout de cada card: flex horizontal con la información a la izquierda y los controles (expandir + dropdown) a la derecha. Fondo blanco con sombra sutil, bordes redondeados, padding interior.

**Spec 4.3.2 — Lista de skills colapsable**
La lista de skills de cada agente está **oculta por defecto**. Al hacer clic en el control expandible:
- La lista se revela con una **transición suave** (CSS transition en `max-height` de `0` al valor del contenido, o `overflow-hidden` con transición, duración ~300ms).
- El icono del control rota o cambia (▼ → ▲).
- Las skills se muestran como badges/tags pequeños en línea (`inline-flex`, fondo coloreado claro, texto pequeño, bordes redondeados).
- Hacer clic de nuevo **colapsa** la lista con la misma transición.

**Spec 4.3.3 — Dropdown y modal de configuración**
Dropdown ⋮ con dos opciones: **"Configurar"** y **"Eliminar"**.
- **"Configurar"** abre un modal con:
  - Título: Nombre del agente.
  - `<textarea>` editable con el prompt de sistema del agente (texto hardcodeado). El textarea tiene ancho completo, altura generosa (`h-48`), bordes, padding, y fuente monoespaciada (`font-mono`).
  - Botón de cierre estándar (× + backdrop).
- **"Eliminar"**: Acción visual solamente (puede mostrar alerta o simplemente cerrar el dropdown).

---

### 4.4 Skills

**Spec 4.4.1 — Texto explicativo**
En la parte superior de la sección, un bloque informativo (callout/info box) con fondo azul claro sutil (`bg-blue-50 dark:bg-blue-900/20`), borde izquierdo de acento (`border-l-4 border-blue-500`), padding interior (`p-4`), bordes redondeados. Contiene texto explicativo:

> *"Las skills son capacidades modulares que se pueden asignar a los agentes de IA. Cada skill le permite al agente realizar una tarea específica — como navegar por la web, leer documentos, gestionar calendarios o analizar datos. Un agente puede tener múltiples skills activas simultáneamente."*

**Spec 4.4.2 — Catálogo de skills (cards)**
Mínimo 4 skills presentadas en un grid responsivo (`grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6`). Cada card contiene:
- **Nombre de la skill** (texto semibold, tamaño base/lg).
- **Descripción breve** (1-2 líneas, texto muted/gris).
- **Contador**: "Usada por X agentes" (texto pequeño, con icono o badge numérico).
- **Botón ⋮** para dropdown de acciones.

Cards con fondo blanco (`bg-white dark:bg-gray-800`), sombra sutil, bordes redondeados, padding generoso.

**Spec 4.4.3 — Dropdown y modal de detalle de skill**
Dropdown ⋮ con opciones **"Ver detalle"** y **"Eliminar"**.
- **"Ver detalle"** abre un modal con la información extendida de la skill:
  - Nombre, descripción completa, lista de agentes que la tienen asignada, fecha de creación, etc.
  - Patrón de cierre estándar (× + backdrop).

---

### 4.5 Contrataciones de Agentes

**Spec 4.5.1 — Tabla de contratos**
Una `<table>` semántica con mínimo 4 filas hardcodeadas. Columnas:
| Columna | Contenido |
|---|---|
| Cliente | Nombre de la empresa |
| Agente | Nombre del agente alquilado |
| Skills contratadas | Badges/tags con los nombres de las skills |
| Fecha inicio | Formato YYYY-MM-DD |
| Fecha fin | Formato YYYY-MM-DD |
| Importe total | Valor monetario ($X,XXX) |
| Acciones | Botón ⋮ |

Las skills contratadas se muestran como badges en línea dentro de la celda (mismo estilo que en Gestión de agentes). Filas con hover sutil.

**Spec 4.5.2 — Dropdown de acciones**
Botón ⋮ con opción **"Ver detalle"**. Mismo comportamiento de dropdown estándar (un solo abierto a la vez, cierre al clic fuera).

**Spec 4.5.3 — Modal de desglose del contrato**
Al seleccionar "Ver detalle", se abre un modal con el desglose completo:
- **Datos del contrato**: Cliente, agente, fechas.
- **Desglose de skills contratadas**: tabla o lista con cada skill y su **precio individual**.

| Skill | Precio mensual | Meses | Subtotal |
|---|---|---|---|
| Web Browsing | $200/mes | 6 | $1,200 |
| Document Reading | $300/mes | 6 | $1,800 |
| | | **Fee de plataforma** | $1,200 |
| | | **Total** | **$4,200** |

- Patrón de cierre estándar (× + backdrop).

---

### 4.6 Log de Errores

**Spec 4.6.1 — Listado de errores**
Mínimo 6 entradas hardcodeadas en `<table>` o lista estructurada. Columnas/campos:
| Campo | Contenido |
|---|---|
| Timestamp | Formato fecha + hora (YYYY-MM-DD HH:MM:SS) |
| Agente | Nombre del agente que generó el error |
| Tipo de error | Badge con código de color |
| Descripción | Texto breve (1 línea) |
| Acciones | Botón ⋮ |

**Spec 4.6.2 — Badges de tipo/gravedad**
Cada tipo de error tiene un badge con color distintivo:
| Tipo de error | Color del badge |
|---|---|
| Runtime Error | Rojo (`bg-red-100 text-red-800 dark:bg-red-900 dark:text-red-300`) |
| Timeout | Amarillo (`bg-yellow-100 text-yellow-800 dark:bg-yellow-900 dark:text-yellow-300`) |
| Rate Limit | Naranja (`bg-orange-100 text-orange-800 dark:bg-orange-900 dark:text-orange-300`) |
| Connection Error | Púrpura (`bg-purple-100 text-purple-800 dark:bg-purple-900 dark:text-purple-300`) |

**Spec 4.6.3 — Dropdown de acciones**
Botón ⋮ con opciones **"Ver detalle"** y **"Marcar como resuelto"**.
- **"Ver detalle"** abre un modal con la traza completa del error:
  - Información del error (timestamp, agente, tipo).
  - Bloque `<pre>` con la traza/stack trace en fuente monoespaciada (`font-mono bg-gray-900 text-green-400 p-4 rounded overflow-x-auto`).
  - Patrón de cierre estándar.
- **"Marcar como resuelto"**:
  - Cambia visualmente la fila: el badge de tipo cambia a un badge "Resuelto" de color verde.
  - Opcionalmente la fila reduce su opacidad (`opacity-60`).
  - Esta acción es puramente visual (JS vanilla, no hay persistencia).

---

## 5. Inventario de Componentes Reutilizables

### 5.1 Sidebar de navegación
- **Elemento**: `<aside>` con `<nav>` interior.
- **Aparece en**: Todas las vistas (persistente).
- **Variantes**: Ítem normal, ítem hover, ítem activo.
- **Estados**: Normal (texto gris, sin fondo), hover (fondo ligeramente resaltado), activo (fondo con color primario/acento, texto blanco o de contraste, borde izquierdo indicador).

### 5.2 Barra superior
- **Elemento**: `<header>`.
- **Aparece en**: Todas las vistas (persistente).
- **Contenido dinámico**: Título de la sección cambia según la navegación.

### 5.3 Toggle de modo oscuro
- **Elemento**: Botón o switch en el `<header>`.
- **Aparece en**: Barra superior (persistente).
- **Comportamiento**: Toggle entre clase `dark` en `<html>`. Icono cambia (sol ↔ luna). Estado guardado en `localStorage`.

### 5.4 Tarjeta de métrica
- **Elemento**: `<div>` con icono + etiqueta + valor.
- **Aparece en**: Dashboard.
- **Variantes**: 4 colores de acento distintos (emerald, rose, blue, amber).

### 5.5 Dropdown de acciones ⋮
- **Elemento**: Botón `⋮` + menú flotante `<div>`.
- **Aparece en**: Todas las tablas y listados (Usuarios, Agentes, Skills, Contrataciones, Log de errores).
- **Comportamiento**: Abre al clic, cierra al clic fuera. Solo un dropdown abierto a la vez.
- **Variantes de opciones**:
  - Usuarios: "Ver detalle", "Eliminar"
  - Agentes: "Configurar", "Eliminar"
  - Skills: "Ver detalle", "Eliminar"
  - Contrataciones: "Ver detalle"
  - Log de errores: "Ver detalle", "Marcar como resuelto"

### 5.6 Modal overlay
- **Elemento**: Backdrop (`<div>` fixed, bg-black/50) + contenedor centrado.
- **Aparece en**: Usuarios (detalle), Agentes (configuración), Skills (detalle), Contrataciones (desglose), Log de errores (traza).
- **Comportamiento**: Se abre al seleccionar acción del dropdown. Se cierra con botón × y con clic en el backdrop.
- **Variantes de contenido**: Registro de usuario, textarea de configuración, información de skill, desglose de contrato, traza de error.

### 5.7 Badge de estado
- **Elemento**: `<span>` inline con fondo de color y texto.
- **Aparece en**: Usuarios (estado), Agentes (estado), Log de errores (tipo de error).
- **Variantes**:
  - Verde: Activo
  - Gris: Inactivo
  - Rojo: Suspendido / Fallando / Runtime Error
  - Amarillo: Timeout
  - Naranja: Rate Limit
  - Púrpura: Connection Error
  - Verde (alternativo): Resuelto

### 5.8 Lista de skills colapsable
- **Elemento**: `<div>` con `overflow-hidden` y transición en `max-height`.
- **Aparece en**: Gestión de agentes.
- **Comportamiento**: Oculta por defecto. Expande/colapsa con clic en control. Transición suave (~300ms). Icono rota con la transición.

### 5.9 Tabla de datos
- **Elemento**: `<table>` con `<thead>` y `<tbody>`.
- **Aparece en**: Usuarios, Contrataciones, Log de errores.
- **Estilos comunes**: Header con fondo gris claro (`bg-gray-50 dark:bg-gray-700`), filas con hover sutil, bordes redondeados en el contenedor, texto alineado a la izquierda.

---

## 6. Datos Hardcodeados (Set Maestro)

> Los mismos nombres de agentes, skills y empresas deben ser consistentes en todas las secciones donde aparezcan.

### 6.1 Usuarios

| Nombre | Email | Plan | Estado |
|---|---|---|---|
| María García | maria@acmecorp.com | Enterprise | Activo |
| James Wilson | james@byteworks.io | Pro | Activo |
| Laura Chen | laura@dataflow.com | Pro | Inactivo |
| Carlos Méndez | carlos@startup.dev | Free | Activo |
| Sarah Johnson | sarah@techlab.co | Enterprise | Suspendido |

### 6.2 Agentes

| Nombre | Propietario | Estado | Skills |
|---|---|---|---|
| Atlas | Acme Corp | Activo | Web Browsing, Document Reading |
| Hermes | ByteWorks | Activo | Calendar Management, Email Drafting |
| Nova | Acme Corp | Fallando | Web Browsing, Data Analysis |
| Sentinel | DataFlow Inc | Inactivo | Document Reading, Calendar Management |

### 6.3 Skills

| Nombre | Descripción | Agentes |
|---|---|---|
| Web Browsing | Navega por sitios web y extrae información estructurada | Atlas, Nova (2) |
| Document Reading | Lee y analiza documentos PDF, Word y texto plano | Atlas, Sentinel (2) |
| Calendar Management | Gestiona eventos, citas y recordatorios de calendario | Hermes, Sentinel (2) |
| Email Drafting | Redacta y envía correos electrónicos profesionales | Hermes (1) |
| Data Analysis | Procesa y analiza conjuntos de datos tabulares | Nova (1) |

### 6.4 Contrataciones

| Cliente | Agente | Skills | Inicio | Fin | Importe |
|---|---|---|---|---|---|
| Acme Corp | Atlas | Web Browsing, Document Reading | 2025-01-15 | 2025-07-15 | $4,200 |
| ByteWorks | Hermes | Calendar Management, Email Drafting | 2025-03-01 | 2025-09-01 | $3,600 |
| Acme Corp | Nova | Web Browsing, Data Analysis | 2025-02-10 | 2025-08-10 | $3,800 |
| DataFlow Inc | Sentinel | Document Reading, Calendar Management | 2024-11-01 | 2025-05-01 | $4,000 |

### 6.5 Errores

| Timestamp | Agente | Tipo | Descripción |
|---|---|---|---|
| 2025-06-15 14:32:07 | Nova | Runtime Error | Null reference in data processing pipeline |
| 2025-06-15 11:15:23 | Atlas | Timeout | Web request exceeded 30s timeout limit |
| 2025-06-14 09:47:51 | Hermes | Rate Limit | Calendar API rate limit exceeded |
| 2025-06-14 08:22:10 | Nova | Connection Error | Failed to establish database connection |
| 2025-06-13 16:05:33 | Atlas | Runtime Error | Unexpected response format from target URL |
| 2025-06-13 10:41:18 | Sentinel | Timeout | Document parsing exceeded time limit |

### 6.6 Métricas del Dashboard

| Métrica | Valor | Color |
|---|---|---|
| Ingresos totales (este mes) | $15,600 | Emerald |
| Pérdidas por descuentos | $2,340 | Rose |
| Agentes activos | 2 | Blue |
| Agentes fallando | 1 | Amber |

---

## 7. Criterios de Aceptación

1. Las seis secciones del panel están presentes y son accesibles desde la barra lateral de navegación.
2. La barra lateral muestra un indicador visual claro de qué sección está activa.
3. Las cuatro tarjetas de métricas del Dashboard se muestran en una cuadrícula responsiva 2×2 con colores de acento distintos por tipo.
4. El placeholder de gráfico de actividad semanal está visible debajo de las tarjetas con borde discontinuo.
5. Todas las filas de tablas y listados tienen un dropdown de acciones ⋮ funcional que se abre al hacer clic, se cierra al hacer clic nuevamente, y se cierra al hacer clic fuera de su área.
6. Solo un dropdown puede estar abierto a la vez en toda la interfaz.
7. "Ver detalle" abre un modal en al menos cuatro secciones diferentes (Usuarios, Skills, Contrataciones, Log de errores).
8. Todos los modales se cierran con el botón de cierre (×) y haciendo clic en el backdrop.
9. En Gestión de agentes, las listas de skills están colapsadas por defecto y se expanden/colapsan al hacer clic con una transición suave visible.
10. "Configurar" en Gestión de agentes abre un modal con un `<textarea>` editable que contiene el prompt de sistema del agente.
11. En Log de errores, los tipos de error se muestran como badges con código de color diferenciado.
12. "Marcar como resuelto" en Log de errores cambia visualmente el badge o la fila para indicar que el error fue resuelto.
13. El toggle de modo oscuro/claro en la barra superior cambia todo el panel entre esquemas de color usando las utilidades `dark:` de Tailwind. El modo elegido se conserva al navegar entre secciones (almacenado en `localStorage`).
14. Los datos hardcodeados son consistentes entre secciones: el mismo nombre de agente aparece en Gestión de agentes, Contrataciones y Log de errores.
15. El HTML usa etiquetas semánticas correctamente — `<section>`, `<table>`, `<nav>`, `<header>`, `<main>`, `<aside>` y similares. El layout es usable en viewports de escritorio y tablet.
