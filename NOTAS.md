# NOTAS DEL PROYECTO — EP3 ADY1104 Visualización de Datos (StreamView Analytics)

**Archivo único de traspaso entre agentes.** Convención vigente:
- Este es el **único** archivo de notas (`notas/NOTAS.md`). No se crean archivos nuevos por fase.
- Cada vez que se haga algo nuevo se **agrega una entrada al final** (`# ENTRADA NN — título`), sin borrar las anteriores. Si algo cambia, se anota en la entrada nueva.
- Cada entrada incluye: objetivo y fecha, qué se hizo, archivos creados/modificados (rutas relativas a la raíz del proyecto), decisiones y motivo, cifras clave exactas, y pendiente con el primer paso concreto.
- Rutas relativas a `EP3_visualizacion_datos/`.
- Rulebook de carpetas (exigido por la pauta, punto 6): todo archivo va en su carpeta; nada suelto en la raíz salvo `README.md` y `requirements.txt`.

```
EP3_visualizacion_datos/
├── data/
│   ├── raw/          CSV originales (no se modifican)
│   └── processed/    datasets limpios, tablas largas y agregados (los genera código de src/)
├── notebooks/        01_integracion_y_limpieza, 02_eda_visualizaciones, ... (numerados)
├── src/              código reutilizable (limpieza.py, kpis.py, funciones de gráficos)
├── dashboard/        app/archivo del dashboard interactivo y sus capturas finales
├── images/           gráficos estáticos exportados (PNG) usados en informe y resumen
├── reports/          informe ejecutivo (PDF), resumen ejecutivo (PDF/PPTX), glosario
├── notas/NOTAS.md    este archivo
├── README.md
└── requirements.txt
```
- Todo debe poder re-ejecutarse sin modificaciones (pauta, punto 5): rutas relativas a la raíz, sin rutas absolutas ni pasos manuales.
- **LIMPIEZA FINAL DEL REPOSITORIO (OBLIGATORIO):** Al finalizar el proyecto y antes de la entrega definitiva, este archivo `NOTAS.md`, todos los archivos Markdown de contexto interno para agentes (`01_analisis_y_plan.md`, `02_feedback_ep1_y_foco_visual.md`, etc.) y las pautas en PDF deben **quitarse/eliminarse del repositorio**, de modo que la entrega final contenga exclusivamente los entregables oficiales exigidos por la pauta.

**Índice:** Entrada 01 análisis y plan · Entrada 02 feedback EP1 y foco visual · Entrada 03 Fases 0 y 1 (estructura, integración y limpieza)

---

# ENTRADA 01 — Análisis inicial y plan de trabajo (EP3 ADY1104)

**Fecha:** 05/10/2026 · **Estado:** análisis terminado, plan propuesto, aún sin construir nada.
**Para el próximo agente:** lee esta nota completa antes de tocar datos. Todo lo que se haga después se agrega como una nueva entrada al final de ESTE archivo.

---

## 1. Qué se pide (resumen de la pauta EP3)

Encargo **grupal**, caso StreamView Analytics (igual que EP1), ahora con **películas + series**. Ponderación 9 %, semana 11.
(Inconsistencia en la pauta: la tabla dice 2 h y el texto dice 3 h para la sala de proyectos. Irrelevante para el producto, pero mencionarla si alguien pregunta.)

**Entregables (6):**
1. Informe ejecutivo **PDF** con 10 secciones mínimas: problema de negocio, objetivos, audiencia y propósito comunicacional, fuentes de datos, EDA con visualizaciones, justificación de gráficos, data storytelling, diseño del dashboard, evaluación crítica (fortalezas/limitaciones/mejoras), conclusiones y recomendaciones.
2. **Dashboard interactivo** (Power BI, Tableau, Plotly, Dash, Streamlit u equivalente) con visualizaciones, KPIs, filtros y navegación.
3. **Resumen ejecutivo** PDF o PowerPoint.
4. Archivos del proyecto documentados y reproducibles.
5. Datasets y archivos complementarios, de modo que se pueda re-ejecutar **sin modificaciones**.
6. Carpeta con estructura profesional: `data/, notebooks/, dashboard/, images/, src/, README.md`.

**Rúbrica (dónde está el puntaje):**

| Indicador | Peso | Implicancia práctica |
|---|---|---|
| IE18 KPIs pertinentes y bien calculados | **26 %** | Es el indicador más pesado: definir pocos KPIs, bien justificados, con cálculo verificable |
| IE17 Dashboards e infografías claros, alineados al negocio | **21 %** | Diseño y organización por página; infografía/resumen visual |
| IE16 Integra múltiples fuentes con consistencia y calidad | **17 %** | Es el cambio central de EP3: unir movies + tv sin errores |
| IE14 Visualizaciones interactivas (controles, filtros) | 15 % | Filtros que realmente afecten todos los gráficos y KPIs |
| IE13 Visualizaciones estáticas con herramientas especializadas | 11 % | Gráficos del informe, claros y consistentes con el objetivo |
| IE22 Organización de productos finales | 10 % | Estructura de carpetas, README, reproducibilidad |

---

## 2. Qué se hizo en EP1 (base de la que partimos)

- Solo `netflix_movies_detailed_up_to_2025.csv` (16.000 películas, 2010–2025). Proxies declarados: `popularity` = engagement, `vote_average`/`vote_count` = calificación.
- Notebook `01_limpieza_y_eda_streamview.ipynb` → limpieza, EDA, tablas agregadas (`kpis_generales`, `agg_por_anio`, `agg_por_genero`, `agg_por_pais`, `agg_financiero`, `agg_por_director`) → dashboard en **Looker Studio** (el enlace quedó pendiente en el informe).
- Informe en Word con 10 secciones; storytelling en 7 hallazgos (quiebre 2021, Drama vs Adventure/Animation, correlación 0,071, directores por ingresos, ROI mediano 1,70×, EE.UU./Japón, 78 % sin datos financieros).
- **Feedback de la docente que hay que mantener en EP3:**
  - Cada gráfico declara problema de negocio y audiencia antes de construirse.
  - Análisis de ROI por director (presupuesto vs ingresos).
  - Ordenar por **densidad** = calificación promedio / popularidad promedio.
  - Porcentajes siempre sobre una **base fija** (total de registros), nunca promediar porcentajes.
  - Boxplot con tratamiento de outliers (IQR).

---

## 3. Hallazgos al analizar ambos datasets (verificados con pandas)

### 3.1 Estructura
| | Movies | TV shows |
|---|---|---|
| Filas | 16.000 | 16.000 (15.991 `show_id` únicos) |
| Columnas | 18 | 16 (no tiene `budget` ni `revenue`) |
| Títulos por año | 1.000 exactos | 1.000 exactos |
| `duration` | 100 % vacía | constante "1 Seasons" (inútil) |
| `director` nulo | 0,8 % | **68,5 %** |
| `description` nula | 0,8 % | 20 % |
| `country` nulo | 2,9 % | 11,2 % |
| `genres` nulo | 0,7 % | 6,1 % |
| `rating` | idéntica a `vote_average` | idéntica a `vote_average` |

### 3.2 Problemas de integración (críticos para IE16)
1. **Colisión de `show_id`:** 397 IDs aparecen en ambos archivos (son espacios de ID distintos de TMDb). Hay que crear una llave compuesta (`tipo_contenido` + `show_id`) → `content_id`. Unir por `show_id` solo mezclaría títulos distintos.
2. **9 `show_id` duplicados en series** (todos de 2025, con popularidad distinta) → decidir regla (conservar el de mayor `vote_count`/popularidad) y documentarla.
3. **Taxonomías de género distintas.** Películas usa Action, Adventure, Science Fiction, Fantasy, Thriller, Horror, Romance, etc. Series usa "Action & Adventure", "Sci-Fi & Fantasy", "War & Politics", Kids, Reality, Talk, News, Soap, Unknown. Solo 8 géneros coinciden literalmente. Se necesita una **tabla de homologación** (ej. Action/Adventure → "Acción y Aventura"; Science Fiction/Fantasy → "Ciencia ficción y Fantasía"; Kids → Family; "Unknown" → "No especificado").
4. **Escalas de `popularity` no comparables:** mediana 10,9 (películas) vs 36,2 (series); promedios 20,4 vs 64,9. Comparar el valor crudo entre tipos sería engañoso. Propuesta: comparar con **percentil dentro de su tipo** (índice de engagement 0–100) y declarar la limitación.
5. 514–528 títulos con el mismo nombre en ambos archivos (cosas como una película y su serie). No son duplicados; se mantienen separados por `tipo_contenido`.

### 3.3 Hallazgo importante: error heredado de EP1
`vote_average = 0` **no es una calificación real**: corresponde a `vote_count = 0` (sin votos). En EP1 esos ceros entraron en los promedios.
- Películas: 5,6 % con 0 votos. Promedio con ceros **5,96** vs sin ceros **6,31**.
- Series: **23 %** con 0 votos. Promedio con ceros **5,42** vs sin ceros **7,02**.
- El "1,71 en 2025" de EP1 es en gran parte este artefacto: 73,5 % de las películas 2025 tienen 0 votos; sin ceros el promedio 2025 es 6,45 (series: 7,31).
- Consecuencia: algunas cifras del informe EP1 (calificaciones promedio por género y país, KPI 5,96) cambiarán al corregir. Hay que **recalcular y declararlo** en el informe (es un buen punto para "evaluación crítica" y para IE16 calidad de datos).
- Regla propuesta: `vote_average` → NaN si `vote_count == 0`; bandera `calificacion_confiable` = `vote_count >= 10` (películas 14.183 cumplen; series solo 6.148, es decir 38 %). El umbral exacto se decide en la Fase 1 y se documenta.

### 3.4 Otros datos útiles para la narrativa
- Correlación popularidad–calificación: películas 0,071 · series 0,052 (el mensaje "son señales independientes" se sostiene en ambos tipos).
- Popularidad promedio por año: películas sube fuerte en 2024 (74,0); series se mantiene entre 48 y 83 sin ese quiebre. El pico de 2024 parece específico de películas.
- Calificación de series sube de 4,67 (2010) a 6,48 (2024) con ceros incluidos; hay que revisarla ya corregida.
- Países: películas dominadas por EE.UU. (7.762); en series EE.UU. 3.194, **China 1.900, Japón 1.881**, Corea del Sur 1.299. Idiomas de series: en 4.437, zh 2.456, ja 1.883, ko 1.364.
- Datos financieros: solo películas (3.540 con presupuesto e ingresos > 0). Para series no existe → el análisis de ROI y directores por ingresos **solo aplica a películas** y debe decirse explícitamente en el dashboard.
- Series con director: solo 5.035 de 16.000, por eso "ranking de directores" no se puede comparar entre tipos.

---

## 4. Decisiones de diseño propuestas (pendientes de confirmar con el equipo)

1. **Tabla unificada** `contenido_unificado.csv` con una fila por título y columnas comunes + `tipo_contenido` (Película/Serie). `budget`/`revenue` quedan en NaN para series. Tablas largas por género, país e idioma para los filtros.
2. **Pregunta de negocio nueva para EP3:** ¿cómo debería StreamView repartir su inversión en adquisición entre películas y series, y en qué géneros/países, según engagement, calidad percibida y (solo películas) retorno financiero?
3. **Audiencia:** igual que EP1 (Gerencia de Contenido y Dirección Ejecutiva), declarada en cada gráfico.
4. **KPIs candidatos (IE18, el de mayor peso)** — todos deben responder a los filtros del dashboard y tener definición en el glosario:
   - Total de títulos y % películas / % series (base fija = total del dataset unificado).
   - Calificación promedio **solo con votos** y % de títulos con calificación confiable.
   - Índice de engagement (percentil de popularidad dentro del tipo) promedio.
   - Densidad (calificación prom / popularidad prom) por género.
   - ROI mediano y % de cobertura financiera (solo películas; base fija = 16.000 películas, declarada).
   - Mediana en vez de promedio donde haya outliers (popularidad, ROI).
5. **Herramienta del dashboard:** recomiendo **Plotly + Streamlit (o Dash)** en lugar de repetir Looker Studio, porque (a) la pauta pide archivos que se re-ejecuten sin modificaciones y una carpeta `src/`, (b) en EP1 Looker no soportó boxplot ni hexbin, (c) cruzar dos fuentes con filtros comunes es más limpio en código. Alternativa válida: Power BI. **Decisión abierta, ver sección 6.**
6. **Estructura de páginas del dashboard:** (1) Resumen ejecutivo con KPIs, (2) Catálogo: película vs serie por año, género, país, idioma, (3) Engagement vs calidad (scatter + densidad), (4) Financiero (solo películas: ROI, directores), (5) Notas metodológicas/limitaciones. Filtros globales: tipo, rango de años, género, país, idioma.

---

## 5. Plan por fases (cada fase cierra con su nota en `notas/`)

| Fase | Qué se hace | Salidas | Indicador que cubre |
|---|---|---|---|
| 0 | Crear estructura del proyecto (`data/raw`, `data/processed`, `notebooks`, `dashboard`, `images`, `src`, `reports`, `notas`, `README.md`, `requirements.txt`) y copiar los CSV a `data/raw` | Carpeta base | IE22 |
| 1 | **Integración y limpieza:** `content_id`, homologar géneros, tratar ceros de votos, resolver duplicados, normalizar nulos, crear tablas largas y unificada; validar con chequeos de conteo (32.000 → 31.991 u otra cifra justificada) | `notebooks/01_integracion_y_limpieza.ipynb`, `data/processed/*.csv`, nota de calidad de datos | IE16 |
| 2 | **EDA y gráficos estáticos** comparando película vs serie, con problema de negocio y audiencia por gráfico; boxplot con IQR; justificación de cada tipo de gráfico | `notebooks/02_eda_visualizaciones.ipynb`, `images/*.png` | IE13 |
| 3 | **Definición y cálculo de KPIs** con verificación independiente (recalcular con una segunda vía) y glosario | `src/kpis.py`, `data/processed/kpis_*.csv`, `reports/glosario.md` | IE18 |
| 4 | **Dashboard interactivo** con filtros globales, navegación entre páginas, KPIs reactivos | `dashboard/app.py` (o `.pbix`), capturas en `images/` | IE14, IE17 |
| 5 | **Informe ejecutivo (PDF)** con las 10 secciones + **resumen ejecutivo** (PDF/PPTX), con storytelling y la corrección del error de ceros explicada | `reports/informe_ejecutivo.pdf`, `reports/resumen_ejecutivo.*` | IE13, IE17 |
| 6 | **Empaquetado y QA:** README con pasos de ejecución, `requirements.txt`, correr todo desde cero en carpeta limpia, revisar contra la rúbrica | Entrega final | IE22 |

---

## 6. Preguntas abiertas para el usuario
1. ¿Herramienta del dashboard: Plotly + Streamlit/Dash (recomendado), Power BI o seguir con Looker Studio?
2. ¿Aceptan corregir las cifras de EP1 que incluían calificaciones en cero, y declararlo en el informe?
3. ¿El resumen ejecutivo va en PDF o PowerPoint?
4. ¿Integrantes del grupo: se mantienen los mismos de EP1 (Carlos Hidalgo, Gabriel Caroca, Bruno Miranda, Keiny Navarro)?

---

## 7. Convención de notas
Ver la convención vigente al inicio de este archivo (un solo archivo de notas).

---

## 8. Archivos de entrada disponibles
`EP3_Instrucciones_y_Pauta_EP3_Encargo_Estudiante.pdf`, `informe_ejecutivo_streamview.docx`, `01_limpieza_y_eda_streamview.ipynb`, `netflix_movies_detailed_up_to_2025.csv`, `netflix_tv_shows_detailed_up_to_2025.csv` (en `/mnt/user-data/uploads/`).

---

# ENTRADA 02 — Retroalimentación de EP1 y foco en atributos visuales / carga cognitiva

**Fecha:** 05/10/2026 · **Depende de:** Entrada 01 (no la reemplaza; la complementa) · **Estado:** diagnóstico hecho, nada construido todavía.

---

## 1. Retroalimentación recibida en EP1

1. El enlace al dashboard de Looker Studio quedó como placeholder en el informe; la docente no pudo evaluar la interactividad real.
2. El gráfico **"Top géneros"** del dashboard en vivo no reflejaba lo del notebook y el informe (Drama dominando).
3. Puntaje no máximo en dos indicadores:

| Indicador | Peso | Puntaje EP1 | Categoría | Comentario de la docente |
|---|---|---|---|---|
| IE5. Atributos visuales (color, tamaño, posición, contraste, forma) | 16 % | 12,8 | Buen desempeño (80 %) | Uso correcto con leves oportunidades de mejora |
| IE6. Visualizaciones que favorecen la comprensión y minimizan la carga cognitiva | 20 % | 16 | Buen desempeño (80 %) | Visualizaciones adecuadas con pequeñas dificultades |

**Decisión del usuario:** lo del enlace de Looker Studio se deja **para el final**. La decisión de herramienta del dashboard para EP3 (Plotly + Streamlit/Dash, Power BI o Looker) sigue **abierta** (ver nota 01, sección 6).

---

## 2. Diagnóstico de la discrepancia de "Top géneros" (verificado con el CSV de películas)

En el notebook de EP1 el gráfico 3.2 hace dos cosas a la vez:
- **Ordena** los 10 géneros por *densidad* (calificación prom / popularidad prom).
- **Dibuja** la longitud de las barras con la *cantidad de títulos*.

Resultado real del ranking por densidad (películas): TV Movie (0,596), Documentary (0,547), Music (0,438), History (0,414), **Drama (0,368, puesto 5 de 20)**, Western, Mystery, Comedy, Romance, War. Es decir, el notebook **no** muestra a Drama arriba, aunque sí tiene la barra más larga (6.910 títulos).

En Looker, el orden por defecto es por la métrica graficada (títulos), por eso allí Drama aparece primero. Cada versión es "correcta" a su manera, pero el lector ve resultados distintos y además el gráfico mezcla dos criterios: **el orden dice una cosa y el largo de la barra dice otra**. Eso es exactamente un problema de carga cognitiva (IE6).

**Segunda inconsistencia relacionada (hallada al revisar el código):** el boxplot (3.4) toma `top5 = top_generos_tabla.index[:5]`, o sea los 5 primeros por **densidad** (TV Movie, Documentary, Music, History, Drama). Pero el informe (7.5) dice "5 géneros más frecuentes" y comenta Drama, Comedy, Thriller y Action. Hay que decidir qué géneros se muestran realmente y que texto, gráfico y dashboard digan lo mismo.

**Regla para EP3 — "una métrica, un criterio":** lo que ordena, lo que codifica la longitud y lo que dice el título del gráfico deben ser la misma métrica. Si se quiere mostrar volumen *y* densidad, usar dos gráficos o un solo gráfico con ambas métricas visibles (ej. barras = títulos, punto/marcador = densidad, claramente rotulado).

---

## 3. Checklist de diseño para subir IE5 e IE6 (aplicar en todos los gráficos y en el dashboard)

### IE5 — Atributos visuales
- **Color con significado fijo y único en todo el proyecto:** Película = un color, Serie = otro (ej. azul y naranja), usado igual en gráficos estáticos, dashboard e informe. No reutilizar esos colores para otra cosa.
- **Paleta apta para daltonismo** (evitar rojo/verde como único diferenciador) y verificar contraste del texto sobre fondo (mínimo 4,5:1).
- **Color secuencial para magnitudes, categórico para tipos:** no mezclar.
- **Posición y longitud para comparar** (lo más legible); tamaño solo como apoyo (burbujas) y siempre con leyenda; forma solo para distinguir 2–3 categorías.
- **Resaltar lo importante:** un color de énfasis y el resto en gris neutro (ej. destacar la barra del género del mensaje).
- Escalas: barras siempre desde cero; escala logarítmica solo con aviso visible en el eje.
- Sin 3D, sin degradados decorativos, sin gráficos de torta con más de 3–4 categorías.

### IE6 — Carga cognitiva
- **Título = mensaje** (ej. "Las series tienen mayor calificación, pero el 38 % de ellas tiene votos suficientes") y subtítulo con métrica, unidad y base de cálculo.
- **Un mensaje por gráfico**, máximo 5–7 categorías visibles (top N), resto agrupado en "Otros".
- **Rotular directamente** valores clave en vez de depender de leyendas lejanas; ejes con unidades; quitar líneas de grilla y bordes innecesarios.
- **Orden consistente** (mayor a menor o cronológico) y declarado en el subtítulo.
- **Dashboard:** jerarquía visual (KPIs arriba, detalle abajo), una cuadrícula alineada, mismo orden y ubicación de filtros en todas las páginas, valores por defecto razonables, y una página de "cómo leer este dashboard / limitaciones" breve.
- **Cada gráfico del informe declara** problema de negocio y audiencia (feedback previo) **y** por qué ese tipo de gráfico minimiza el esfuerzo de lectura.

### Control de consistencia (evita repetir la discrepancia de EP1)
- Antes de cerrar cada fase, comparar **3 cifras clave por gráfico** entre notebook, dashboard e informe (mismo valor, mismo orden). Dejar el resultado en la nota de esa fase.
- Generar las tablas del dashboard y las figuras del informe **desde el mismo código/CSV** (`src/` + `data/processed/`), nunca recalcular a mano en la herramienta.

---

## 4. Cambios al plan de la nota 01

- **Fase 2 (EDA/estáticos) y Fase 4 (dashboard):** incorporar el checklist de la sección 3 como criterio de aceptación; cada gráfico lleva su ficha (problema, audiencia, mensaje, métrica de orden = métrica visual).
- **Fase 5 (informe):** incluir una subsección en "Justificación de las representaciones gráficas" que explique las decisiones de atributos visuales y carga cognitiva (esto apunta directo a IE5/IE6 de la rúbrica de EP1 y es la evaluación crítica de EP3).
- **Fase 6 (QA):** agregar tarea "cruzar cifras notebook ↔ dashboard ↔ informe" y, al final, **completar el enlace/entrega del dashboard** (el pendiente de Looker queda aquí si se mantiene esa herramienta).
- Sigue pendiente la decisión de herramienta y las otras preguntas abiertas de la nota 01.

---

## 5. Pendiente y cómo continuar
Primer paso para el siguiente agente: esperar la decisión sobre la herramienta del dashboard y, luego, ejecutar la **Fase 0** (estructura de carpetas) y la **Fase 1** (integración y limpieza), dejando una nueva entrada al final de este archivo.

---

# ENTRADA 03 — Fases 0 y 1: estructura del proyecto, integración y limpieza

**Fecha:** 05/10/2026 · **Estado:** Fase 0 y Fase 1 **terminadas y verificadas** (15 controles de calidad en OK). La herramienta del dashboard sigue sin decidir; estas fases no dependen de ella.

## Qué se hizo
- Se creó la estructura profesional: `data/raw`, `data/processed`, `notebooks`, `dashboard`, `images`, `src`, `reports`, `notas`, `README.md`, `requirements.txt`. Los dos CSV originales están en `data/raw/`.
- `src/limpieza.py`: integra películas y series, limpia, crea tablas largas y ejecuta controles de calidad (`python src/limpieza.py` desde la raíz; falla si algún control no se cumple).
- `notebooks/01_integracion_y_limpieza.ipynb`: documenta y ejecuta la fase (ya ejecutado, con salidas).
- Notas consolidadas en este único archivo (las notas 01 y 02 anteriores se fusionaron aquí).

## Archivos generados en `data/processed/`
| Archivo | Contenido | Filas |
|---|---|---|
| `contenido_unificado.csv` | 1 fila por título; columnas comunes + `tipo_contenido`, `content_id`, `idioma`, `vote_average_clean`, `tiene_votos`, `calificacion_confiable`, `indice_engagement`, `budget_clean`, `revenue_clean`, `tiene_datos_financieros`, `roi` | 31.991 |
| `contenido_por_genero.csv` | Formato largo (título × `genero_unificado`, con `genero_original`) sin repeticiones | — |
| `contenido_por_pais.csv` | Formato largo título × país | — |
| `contenido_por_director.csv` | Formato largo título × director (excluye "No especificado") | — |
| `mapa_generos.csv` | Tabla de homologación género original → unificado | — |
| `control_calidad.csv` | 15 controles con esperado vs obtenido | 15 |

Las tablas largas no incluyen `description` ni `cast` (para que pesen poco: 2,7 a 7,4 MB en lugar de 14 a 37 MB).

## Decisiones y motivo
1. **`content_id`** = `M-<show_id>` (película) / `S-<show_id>` (serie): 397 `show_id` se solapan entre archivos con títulos distintos.
2. **Duplicados en series:** 9 `show_id` repetidos (todos 2025); se conserva la fila con más votos y, si empatan, la de mayor popularidad → 15.991 series. Total unificado: **31.991**.
3. **Calificación:** `vote_average_clean` = NaN cuando `vote_count` = 0; `calificacion_confiable` = `vote_count` ≥ 10 (umbral editable en `UMBRAL_VOTOS`).
4. **Engagement:** `indice_engagement` = percentil (0–100) de `popularity` dentro de cada tipo, porque las escalas no son comparables.
5. **Géneros:** `genero_unificado` en español vía `mapa_generos.csv`; Action + Adventure y Science Fiction + Fantasy se unen para igualar la taxonomía de series; se eliminan repeticiones del mismo título dentro del género unificado.
6. **Finanzas:** solo películas (series quedan en NaN).
7. Se eliminan `duration`, `rating` (idéntica a `vote_average`) y `type`.

## Cifras clave (exactas)
| | Películas | Series |
|---|---|---|
| Títulos | 16.000 | 15.991 |
| Sin votos (`vote_count` = 0) | 894 (5,6 %) | 3.666 (22,9 %) |
| Con votos suficientes (≥ 10) | 14.183 (88,6 %) | 6.148 (38,4 %) |
| Calificación prom. EP1 (con ceros) | 5,956 | 5,420 |
| Calificación prom. corregida | 6,309 | 7,024 |
| Calificación prom. solo confiables | 6,347 | 7,288 |
| Popularidad mediana / promedio | 10,91 / 20,38 | 36,20 / 64,88 |
| Con datos financieros completos | 3.540 (22,1 %) | 0 |

- ROI mediano (películas con datos): **1,70×**; promedio 781,5× (distorsionado por outliers, igual que EP1).
- Géneros unificados presentes en ambos tipos: 12 (de los cuales uno es "No especificado", es decir 11 reales); solo películas: 6 (Suspenso, Terror, Romance, Historia, Música, Película de TV); solo series: 4 (Reality, Telenovela, Talk show, Noticias).
- `No especificado` en género: 107 películas y 1.071 series.

## Implicaciones para las fases siguientes
- Las cifras de EP1 que usaban calificaciones con ceros cambian (KPI de calificación promedio, calificación por género y país). **Hay que recalcular y declararlo** en el informe.
- Comparar entre tipos solo con los 11 géneros comunes y declararlo en cada gráfico; para popularidad usar `indice_engagement`, no el valor crudo.
- Cualquier gráfico de calidad debe mostrar la cobertura de votos (38,4 % en series) para no sobreinterpretar.
- Ambos datasets tienen exactamente 1.000 títulos por año: la composición por año no es un hallazgo de negocio.
- Recordar la regla de la Entrada 02: una métrica, un criterio (orden = métrica visual = título).

## Pendiente y cómo continuar
1. **Decisión abierta:** herramienta del dashboard (Plotly + Streamlit/Dash recomendado, Power BI o Looker Studio). El enlace/entrega de Looker, si se mantiene, va al final.
2. **Otras preguntas abiertas:** aceptar la corrección de los ceros en el informe, formato del resumen ejecutivo (PDF o PPTX), integrantes del grupo.
3. **Siguiente fase:** Fase 2 (`notebooks/02_eda_visualizaciones.ipynb`): EDA y gráficos estáticos película vs serie, con ficha por gráfico (problema, audiencia, mensaje, métrica de orden) y checklist de IE5/IE6.
4. **Hito de cierre (Entrega final):** Una vez completados y validados todos los entregables formales (informe PDF, dashboard, código en `src/`, notebooks y datasets en `data/processed/`), **eliminar del repositorio todas las notas internas (`NOTAS.md`, `01_analisis_y_plan.md`, `02_feedback_ep1_y_foco_visual.md`) y las pautas en PDF** antes de realizar la entrega final.

---

## Entrada 03 — Revisión de coherencia de títulos en EDA (2026-10-05)

### Objetivo
Verificar que cada título de sección en 
otebooks/02_eda_visualizaciones.ipynb comunique el **hallazgo** del gráfico, no solo la variable o el tipo de visualización.

### Qué se hizo
Se auditaron los 8 títulos (## G1 a ## G8) contra su campo **Mensaje**. Se identificaron 6 títulos genéricos o descriptivos del método que no anticipaban el hallazgo.

### Archivos modificados
- 
otebooks/02_eda_visualizaciones.ipynb — 6 celdas markdown editadas.

### Decisiones tomadas
- Un título debe responder a la pregunta: *¿qué descubre este gráfico?*, no *¿qué muestra?*
- Formato adoptado: ## Gn — [Hallazgo conciso]: [detalle del mensaje]

### Cambios realizados
| Gráfico | Antes | Después |
|---------|-------|---------|
| G2 | Distribución de popularidad (boxplot con IQR) | Popularidad sesgada: la mediana es más representativa que el promedio |
| G3 | Densidad por género | Eficiencia por género: calidad percibida por unidad de popularidad |
| G4 | Volumen por género (solo géneros presentes en ambos tipos) | Concentración de la oferta: géneros dominantes en cada catálogo |
| G5 | Evolución por año de estreno | Confiabilidad decreciente: la nota de los títulos recientes es menos fiable |
| G6 | Países de producción | Diversidad geográfica: las series están menos concentradas que las películas |
| G7 | Engagement vs calificación | Señales independientes: popularidad y calificación no se correlacionan |

G1 y G8 se mantuvieron sin cambio (ya eran coherentes).

### Pendiente
- Fase 2 completada (EDA + gráficos). Siguiente: **Fase 3** — Definición y cálculo de KPIs (src/kpis.py, data/processed/kpis_*.csv, 
eports/glosario.md).

---

## Entrada 04 — Cierre de notas de contexto 01 y 02 (2026-10-05)

### Objetivo
Verificar que el contenido de  1_analisis_y_plan.md y  2_feedback_ep1_y_foco_visual.md quedó implementado en los notebooks antes de eliminarlos del repositorio.

### Resultado de la verificación

#### 01_analisis_y_plan.md → implementado en su totalidad
| Ítem del plan | Dónde quedó |
|---|---|
| Estructura de carpetas (Fase 0) | Completada; README y requirements en raíz |
| content_id = tipo + show_id (llave compuesta) | NB01 — Decisión 1 |
| ote_average_clean (ceros → NaN) y bandera calificacion_confiable | NB01 — Sección 2 y Decisión 3 |
| indice_engagement (percentil dentro del tipo) | NB01 — Decisión 4; usado en NB02 G7 |
| Homologación de géneros con mapa_generos.csv | NB01 — Sección 4 y Decisión 5 |
| Duplicados en series (9 repetidos 2025) | NB01 — Decisión 2 |
| 8 gráficos del EDA (G1–G8) con ficha problema/mensaje/métrica | NB02 — completo |
| Preguntas abiertas (herramienta dashboard, formato resumen ejecutivo, integrantes) | **Siguen abiertas — ver "Pendiente" abajo** |

#### 02_feedback_ep1_y_foco_visual.md → implementado en su totalidad
| Ítem del feedback | Dónde quedó |
|---|---|
| Regla "una métrica, un criterio" (orden = largo = título) | Encabezado de NB02; aplicada en G1–G8 |
| Colores fijos: Película = azul, Serie = naranja | Reglas NB02 encabezado |
| Título = mensaje (hallazgo, no nombre de variable) | Revisión Entrada 03; G2–G7 ajustados |
| Máx. 5–7 categorías, resto en "Otros" | Aplicado en G3, G4, G6, G8 (top 7) |
| Barras desde cero; escala log solo con aviso | Aplicado en G2 (boxplot) y G7 (hexbin log) |
| Control de consistencia: cifras desde mismo CSV | src/ + data/processed/ como única fuente |
| Enlace/entrega Looker Studio pendiente para el final | Se mantiene como pendiente en Fase 6 |

### Archivos eliminados
-  1_analisis_y_plan.md
-  2_feedback_ep1_y_foco_visual.md

### Pendiente y cómo continuar
1. **Decisión abierta:** herramienta del dashboard (Plotly + Streamlit/Dash recomendado, Power BI o Looker Studio).
2. **Decisión abierta:** aceptar la corrección de ceros en el informe y declararlo.
3. **Decisión abierta:** formato del resumen ejecutivo (PDF o PPTX).
4. **Decisión abierta:** integrantes del grupo (¿mismos de EP1?).
5. **Siguiente fase:** Fase 3 — Definición y cálculo de KPIs (src/kpis.py, data/processed/kpis_*.csv, 
eports/glosario.md, IE18).

---

## Entrada 05 — Decisión Looker Studio y Fase 3 (KPIs) (2026-10-05)

### Decisiones del usuario
1. **Dashboard en Looker Studio.** Cierra la decisión abierta de las Entradas 01–04. El enlace/entrega queda para la Fase 6.
2. **Los notebooks se usan solo para los gráficos de análisis.** No se crea dashboard en código.

### Qué se hizo
- `src/kpis.py`: genera `looker_contenido.csv` (una fila por título, con `genero_principal`, `pais_principal` y columnas aditivas para SUM/COUNT), calcula 10 KPIs por dos vías independientes (pandas y estilo Looker) y las compara en total, por tipo y en 30 selecciones aleatorias. Contrasta además con cifras de la Entrada 03 (falla si no coinciden).
- `reports/glosario.md`: 9 KPIs con definición, cálculo, base y límites.

### Archivos
- `src/kpis.py`, `reports/glosario.md`
- `data/processed/`: `looker_contenido.csv`, `kpis_resumen.csv`, `kpis_por_anio.csv`, `kpis_por_genero.csv`, `kpis_por_pais.csv`, `kpis_verificacion.csv`

### Decisiones y motivo
1. Una sola tabla alimenta todos los KPIs de Looker para que cada filtro (incluido género y país) los afecte. Para eso se usan `genero_principal` y `pais_principal` (primer género/país de cada título). Las tablas largas se usan solo en gráficos de desglose y por eso pueden diferir levemente del filtro principal.
2. Porcentajes de KPIs bajo filtro: suma(numerador) / cuenta(registros de la selección). Los porcentajes de G4 y G6 usan base fija = total del tipo.
3. ROI agregado usa solo filas con presupuesto e ingresos (`presupuesto_roi`, `ingresos_roi`) para que numerador y denominador sean consistentes.
4. Densidad y popularidad cruda solo se comparan dentro de un mismo tipo.

### Pendiente
1. Correr `python src/kpis.py` con los datos reales; si el contraste con la Entrada 03 falla, revisar `kpis_verificacion.csv`.
2. Fase 4: construir el dashboard en Looker (conectar `looker_contenido.csv`, páginas y filtros).
3. Siguen abiertas: corregir cifras de EP1 en el informe, formato del resumen (PDF/PPTX), integrantes.

---

## Entrada 06 — Corrección de la retroalimentación de EP1 (2026-10-05)

La retroalimentación real de la docente en EP1 tiene solo dos puntos:
1. El enlace al dashboard de Looker Studio quedó como placeholder (debe completarse para evaluar la interactividad real).
2. El gráfico "Top géneros" del dashboard en vivo no refleja el mismo resultado que el notebook y el informe (Drama dominando).

Rúbrica EP1 a mejorar (ambas en "Buen desempeño", meta: "Muy buen desempeño"): IE5 atributos visuales, 12,8/16; IE6 carga cognitiva, 16/20.

**Corrección:** las Entradas 01–04 atribuyen a la docente otras reglas (porcentajes sobre base fija, ROI por director, ordenar por densidad, boxplot con IQR). Eso NO está en su retroalimentación. Son decisiones de diseño del equipo, no requisitos. En particular, los porcentajes de los KPIs usan como denominador la selección filtrada.

---

## Entrada 07 — Verificación con datos reales y correcciones de mensajes (2026-10-05)

### Verificación
- `python src/limpieza.py` regenera exactamente los 6 CSV de `data/processed/`; 15 controles de calidad en OK.
- `python src/kpis.py`: KPIs coinciden con la Entrada 03 (6,309 / 7,024 de calificación; 88,6 % / 38,4 % con votos suficientes; 3.540 películas con datos financieros; ROI mediano 1,70×). Las dos vías de cálculo coinciden en 300 de 300 comparaciones.

### Errores corregidos en NB02
1. G7: el encabezado decía "no se correlacionan" pero r = 0,25 (películas) y 0,13 (series). Ahora: relación débil.
2. G5: el título decía que la cobertura de votos de las series cae de 30 % a 7 %. En realidad sube de 30 % a 50 % hasta 2024 y se desploma solo en 2025 (títulos recientes: series 7 %, películas 9 %).
3. G6: países en español (`PAISES_ES` en `src/kpis.py`, usado también por Looker) y título con el hallazgo (EE. UU.: 49 % de las películas, 20 % de las series).
4. G8: mínimo de 5 películas por director (antes 3, el ranking lo dominaba la franquicia Terrifier con 46×) y se muestra la mediana. Mediana global 1,70× (tabla unificada).

### Cambios a KPIs
- "Engagement promedio" se reemplaza por "% de alto engagement" (percentil ≥ 75 dentro del tipo): el promedio valía 50 por construcción.
- Densidad y popularidad promedio mezclan escalas de popularidad si se muestran con ambos tipos: en Looker, solo con un tipo seleccionado.
- La regla "base fija" no viene de la docente (ver Entrada 06).

### Pendiente
1. Re-ejecutar `src/kpis.py` y el notebook 02 con los cambios.
2. Fase 4: dashboard en Looker. Cada gráfico debe mostrar la misma métrica, orden y cifras que su equivalente del notebook (`cifras_clave_eda.csv`), sobre todo Top géneros.
3. README y `requirements.txt` (no los he visto), informe PDF y resumen ejecutivo.
---

# ENTRADA 08 — Fase 3 completada: KPIs calculados, verificados y glosario (2026-10-05)

**Estado:** Fase 3 **terminada**. src/kpis.py y eports/glosario.md creados y validados.

## Qué se hizo
- src/kpis.py: calcula KPIs globales y segmentados (por año, género, país), genera looker_contenido.csv como fuente única para el dashboard, y verifica los resultados con dos vías independientes (pandas vs numpy/estilo-Looker).
- eports/glosario.md: define cada KPI con su fórmula, base de cálculo y límites.
- Notebook 02 re-ejecutado con las correcciones de Entrada 07 (mensajes G5, G6, G7, G8); todos los outputs actualizados.

## Archivos generados / modificados
| Archivo | Estado |
|---|---|
| src/kpis.py | Creado (nuevo) |
| eports/glosario.md | Creado (nuevo) |
| data/processed/looker_contenido.csv | Generado — 1 fila por título, columnas para Looker |
| data/processed/kpis_resumen.csv | Generado — KPIs globales + por tipo |
| data/processed/kpis_por_anio.csv | Generado |
| data/processed/kpis_por_genero.csv | Generado |
| data/processed/kpis_por_pais.csv | Generado |
| data/processed/kpis_verificacion.csv | Generado — 300 comparaciones vía pandas vs numpy |
| 
otebooks/02_eda_visualizaciones.ipynb | Re-ejecutado con correcciones |

## Cifras reales obtenidas (python src/kpis.py)
| KPI | Total | Película | Serie |
|---|---|---|---|
| Títulos | 31.991 | 16.000 | 15.991 |
| Calificación promedio | 6,630 | 6,309 | 7,024 |
| % con votos suficientes | 63,6 % | 88,6 % | 38,4 % |
| % alto engagement (p ≥ 75) | 25,0 % | 25,0 % | 25,0 % |
| Popularidad promedio | 42,6 | 20,4 | 64,9 |
| Densidad | 0,156 | 0,309 | 0,108 |
| ROI agregado | 2,917× | 2,917× | N/A |
| ROI mediano | 1,700× | 1,700× | N/A |
| Películas con datos financieros | 3.540 (22,1 %) | — | — |
| % del catálogo | 100 % | 50,0 % | 50,0 % |

## Verificación
- **300/300** comparaciones vía pandas vs numpy coinciden (tolerancia rtol=1e-9).
- **9/9** contrastes con cifras históricas de NOTAS.md en OK (títulos, calificaciones, cobertura de votos, datos financieros, ROI mediano).

## Pendiente y cómo continuar
- **Siguiente fase:** Fase 4 — Dashboard interactivo en Looker Studio. Fuente: data/processed/looker_contenido.csv. Cada gráfico debe mostrar la misma métrica, orden y cifras que su equivalente en el notebook (cifras_clave_eda.csv si existe, o tabla directa del CSV).
- Sigue abierta: corrección de cifras de EP1 en el informe, formato del resumen (PDF/PPTX), integrantes.
