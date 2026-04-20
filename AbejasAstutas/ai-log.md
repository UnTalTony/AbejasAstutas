# AI Log — Equipo AbejasAstutas
## HackODS UNAM 2026 · Proyecto: Desconectados

---

## Herramientas utilizadas

- **Claude (claude.ai)** — Anthropic, modelo Claude Sonnet 4.6
- **Modelo LLM Gemini (Google)** — modelo 3.1 Pro

---

## Filosofía de uso

Usamos Claude como acelerador técnico para tareas mecánicas y de depuración.
Las decisiones que definen el proyecto —la pregunta central, la selección
de fuentes, la narrativa de los 4 actos, y la interpretación de los datos—
fueron tomadas por el equipo. Nuestro criterio de delegación fue claro:
**si la tarea requiere conocimiento del contexto socioeconómico del suroeste
de México o criterio editorial, la hacemos nosotros. Si es código repetitivo
o depuración de errores, la delegamos.**

---

## Registro de uso

### 2026-04-13 | Gemini | corregir errores

- **Tarea:** Depurar errores de compilación de entornos virtuales (uv y kernels de Jupyter).
- **Decisión del equipo:** Ningún fragmento de código, texto o conclusión fue integrado al repositorio final 
  sin una validación de rigor técnico, lógico y bibliográfico por parte del equipo

---

### 2026-04-13 | Claude | Selección de variables en ENDUTIH 2024

- **Tarea:** Identificar qué columna del CSV de hogares mide acceso a internet
  y cómo ponderar correctamente con el factor de expansión.
- **Prompt:** *"ayudame a explorar los datos [...] son estos [subiendo archivos
  ENDUTIH]"*
- **Resultado:** Claude identificó que `P4_4 == 1` es la variable binaria de
  acceso a internet en el hogar, y que `FAC_HOG` es el factor de expansión
  para obtener porcentajes representativos a nivel estatal.
- **Decisión del equipo:** Validamos contra los resultados publicados por INEGI
  (Chiapas 50.7%, media nacional 73.6%) antes de usar los datos en el
  dashboard. La interpretación de por qué Chiapas tiene ese porcentaje —
  pobreza estructural, geografía, costos de servicio — fue análisis propio
  del equipo basado en conocimiento del contexto.

---

### 2026-04-13 | Claude | Cálculo de tasa de abandono escolar desde microdatos SEP

- **Tarea:** Los archivos de Media Superior no tienen una columna de abandono
  directa. Necesitábamos derivar la tasa.
- **Resultado:** Claude propuso `(ALUMNOS - EXISTENTES) / ALUMNOS * 100`
  como aproximación de tasa de abandono, calculada por entidad federativa
  para 5 ciclos escolares (2019-2024).
- **Decisión del equipo:** Discutimos si esta métrica es técnicamente correcta
  como proxy de abandono (vs. deserción formal). Decidimos usarla porque es
  reproducible con los datos disponibles y consistente con indicadores
  publicados por Mejoredu. Documentamos la limitación en el README.

---

### 2026-04-14 | Claude | Depuración de errores de renderizado en Quarto

Tres errores distintos durante el proceso de render:

**Error 1 — `TypeError: got multiple values for keyword argument 'margin'`**
- **Causa:** El dict `LAYOUT_BASE` ya contenía `margin`, y cada llamada a
  `update_layout()` pasaba un segundo `margin=` explícito.
- **Solución de Claude:** Convertir `LAYOUT_BASE` en una función `L(**ov)`
  que hace `base.update(ov)` antes de retornar, eliminando la duplicación.
- **Decisión:** Aceptamos sin modificación. Error puramente mecánico.

**Error 2 — `ValueError: Invalid value '#15803d1f' for fillcolor`**
- **Causa:** El código intentaba agregar transparencia concatenando `"1f"` a
  un hex de 6 dígitos. Plotly no acepta hex de 8 dígitos.
- **Solución de Claude:** Función `hex_rgba(h, a)` que convierte `#rrggbb`
  a `rgba(r,g,b,alpha)` válido para Plotly.
- **Decisión:** Aceptamos. Conversión matemática.

**Error 3 — `KeyError: 'año'`**
- **Causa:** La `ñ` en nombres de columnas CSV causa problemas de encoding
  en Windows al leer/escribir archivos.
- **Solución de Claude:** Renombrar `año` → `anio` en el CSV exportado,
  el notebook y el `index.qmd`.
- **Decisión:** Aceptamos y adoptamos como regla de estilo para todo el
  proyecto: sin caracteres especiales en nombres de columnas.

---

### 2026-04-14 | Claude | Solución a gráficas duplicadas en Quarto

- **Problema:** Cada `add_trace()` y `update_layout()` de Plotly devuelve el
  objeto figura, y Quarto con kernel Jupyter renderizaba cada retorno como un
  output separado —generando 4-6 copias de cada gráfica.
- **Resultado:** Claude identificó que Quarto activa internamente
  `ast_node_interactivity = "all"` y propuso:
  ```python
  InteractiveShell.ast_node_interactivity = "last_expr"
  pio.renderers.default = "notebook"
  ```
- **Decisión:** Aceptamos ambas líneas. El renderer `"notebook"` genera HTML
  directo que Quarto puede embeber; `"last_expr"` asegura que solo el
  resultado final de cada celda se renderice.

---

### 2026-04-13 | Claude | Diseño visual (SCSS y layout)

- **Tarea:** Crear `custom.scss` con estética editorial acorde a la gravedad
  del tema.
- **Resultado:** Claude propuso una paleta basada en verde oscuro y rojo
  (`#15803d` / `#c0392b`), value boxes compactos, callouts con borde lateral
  de color y tipografía Inter.
- **Decisión del equipo:** Aprobamos la paleta porque el contraste
  rojo-crítico / verde-alto funciona semánticamente para el tema de brecha
  digital. Ajustamos manualmente los tamaños de los value boxes después de
  ver el primer render —eran demasiado grandes y dominaban la pantalla.

---

## Lo que NO delegamos a IA

- **La pregunta central del proyecto:** surgió de una discusión del equipo
  sobre el marco legal del internet como derecho y la incapacidad del Estado
  de llevar infraestructura al suroeste.
- **La propuesta técnica del nodo solar:** es una solución que el equipo
  investigó independientemente. 
- **La selección de los 3 estados prioritarios** (Chiapas, Guerrero, Oaxaca):
  decisión basada en conocimiento de contexto regional.
- **La narrativa de los callouts:** los textos de contexto en cada acto fueron
  escritos por el equipo. Claude los revisó como editor, no como redactor.
- **La justificación del uso de CONEVAL 2020 vs 2022:** análisis propio sobre
  disponibilidad de desagregación municipal —documentado en el README.

---

*Fecha de cierre del log: 14 de abril de 2026*
*Equipo: Adán Gael Salgado Ortega · Anthony Soto Vazquez · Kimberly Ramirez Becerril*
