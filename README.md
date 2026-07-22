# Pessaro Capital Academy

Academia de trading en español, publicada como un sitio web estático de una sola página (`index.html`). Ofrece contenido educativo estructurado por niveles, con quizzes interactivos, calculadoras de trading, glosario y seguimiento de progreso guardado en el navegador del usuario.

## Qué es el proyecto

Es la plataforma educativa de **Pessaro Capital**: un curso de trading en forex organizado en 4 niveles progresivos ("grados") más una sección bonus:

| Grado (marca Pessaro)     | Equivalencia    | Módulos aprox. |
|----------------------------|-----------------|----------------|
| Fundamentos Pessaro         | Básico          | 60             |
| Operador Táctico            | Intermedio      | 75             |
| Trader Institucional        | Avanzado        | 65             |
| Mesa Pessaro                | Experto         | 42             |
| Cápsulas Pessaro            | Bonus           | 3              |

Cada módulo incluye contenido teórico, un checklist de dominio ("mastery tracker") y un quiz de 3 preguntas (opción múltiple, verdadero/falso y una de desarrollo). El sitio también incluye calculadoras de trading (tamaño de posición, valor de pip, ratio riesgo/beneficio, margen, niveles de Fibonacci, expectativa de sistema) y un glosario con buscador.

El progreso del alumno (módulos completados, checklists de dominio, logros/achievements) se guarda en `localStorage` del navegador; no hay backend ni base de datos remota.

## Stack técnico

- **HTML/CSS/JS estático puro**, sin build ni framework — proyecto tipo "Lite" (`project_kind: html_lite` en `.oma-project.json`).
- Un único `index.html` (contiene toda la estructura y el copy del curso; es un archivo grande y minificado en una sola línea por sección).
- `assets/styles.css`: variables, layout y clases de componentes reutilizables.
- `assets/main.js`: toda la lógica JS compartida (menú, acordeones de capítulos/módulos, quizzes, calculadoras, glosario, barra de progreso, mastery tracker, logros). Los scripts inline en `index.html` deben limitarse a interacciones puntuales de una sola página.
- No requiere servidor: se sirve como archivos estáticos. Ver `AGENTS.md` — se publica con el comando `build_and_deploy_html_project` (flujo de despliegue Lite).

## Estructura de carpetas

```
pessaro-academy/
├── index.html          # Toda la estructura, copy y quizzes del curso (un solo archivo)
├── quiz_answers.json   # Banco de respuestas de referencia (NO se usa en runtime, ver abajo)
├── assets/
│   ├── styles.css      # Estilos y variables del sitio
│   ├── main.js         # Lógica JS: quizzes, calculadoras, progreso, glosario, etc.
│   ├── favicon.png
│   ├── logo*.{png,webp}
│   └── *.jpeg          # Imágenes de hero, secciones de aprendizaje, gráficos y trading
├── AGENTS.md            # Guía para agentes/colaboradores sobre cómo editar el proyecto
├── .oma-project.json    # Metadatos de la plataforma (proyecto "html_lite" estático)
└── .gitignore
```

### Sobre `quiz_answers.json`

Es un banco de **243 quizzes** (uno por módulo aprox.) con preguntas, opciones y respuesta correcta/guía. Importante: **no se referencia ni se carga (`fetch`) desde `index.html` ni desde `main.js`** — el mecanismo real de corrección de quizzes vive embebido en el propio HTML mediante atributos `data-correct="1"` en cada `<input type="radio">`, evaluados por `checkQuiz()` en `assets/main.js`. Es decir, `quiz_answers.json` funciona como una base de datos de referencia/respaldo para autoría y control de calidad del contenido, no como fuente de verdad en producción.

Además, el archivo está listado en `.gitignore` ("Proteger base de datos de respuestas del Quiz de Trading") y por eso **no está versionado en git** (no aparece en `git ls-files`), aunque existe en el filesystem local.

## Cómo se edita el contenido

- **Estructura y copy del curso**: editar `index.html` directamente (módulos, preguntas de quiz visibles, textos de nivel, etc.).
- **Estilos**: reutilizar variables y clases existentes en `assets/styles.css` antes de crear estilos nuevos.
- **Comportamiento/JS compartido**: agregar en `assets/main.js`; evitar lógica compleja inline.
- **Imágenes**: agregar a `assets/` y referenciar con rutas relativas; todo asset usado debe quedar commiteado (excepto lo listado en `.gitignore`).
- **Despliegue**: publicar con el comando `build_and_deploy_html_project` indicado en `AGENTS.md` (no hay pipeline de build tradicional).

## Estado actual del proyecto

El repositorio tiene solo 2 commits hasta ahora:

1. `a0accd5` — **feat: estructura inicial de la academia**: primera versión del sitio, aparentemente basada/adaptada de contenido de **BabyPips / School of Pipsology** (referencia externa muy conocida en educación de forex).
2. `4c3aef0` — **fix(branding): eliminar referencias a BabyPips/School of Pipsology, reemplazar por naming propio Pessaro**: reemplaza toda la nomenclatura heredada por marca propia:
   - Nombres de grado: `Kindergarten/Middle School/High School/Graduation` → `Fundamentos Pessaro/Operador Táctico/Trader Institucional/Mesa Pessaro`.
   - El subtítulo del nivel Básico ya no menciona "School of Pipsology".
   - La sección bonus pasó de "Estilo Babypips" a "Cápsulas Pessaro" (y sus IDs de `modulo-babypips-{1,2,3}` a `modulo-bonus-{1,2,3}`, junto con todas las referencias cruzadas: `data-module`, mastery tracker, badges, `mfill`/`mpct`, contenedores de quiz, `onclick` de `checkQuiz`/`nextModule`).
   - Se reescribió el párrafo que atribuía el concepto de "blowing your account" a BabyPips.

**Verificación**: a día de hoy no quedan coincidencias de `babypips` ni `pipsology` (búsqueda case-insensitive) en `index.html`; el rebranding a "Pessaro" parece completo en el archivo principal.

**Pendiente / a revisar** (no confirmado por el código, pero razonable dado el historial):
- Confirmar que ninguna imagen, texto alternativo (`alt`) o metadato oculto siga referenciando BabyPips (la verificación hecha fue solo textual sobre `index.html`).
- Revisar si `quiz_answers.json` (el banco de 243 preguntas) contiene todavía menciones o estructura heredada de BabyPips/Pipsology, ya que no fue tocado por el commit de rebranding y no está versionado en git.
- Validar que el conteo de módulos con quiz (243) cuadre con el total de módulos definidos en `index.html` (~245 badges), por si faltan o sobran quizzes tras el rebranding de IDs bonus.
