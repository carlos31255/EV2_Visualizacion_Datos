# NOTA 01 — Análisis inicial y plan de trabajo (EP3 ADY1104)

**Fecha:** 05/10/2026 · **Estado:** análisis terminado, plan propuesto, aún sin construir nada.
**Para el próximo agente:** lee esta nota completa antes de tocar datos. Todo lo que se haga después debe dejar su propia nota `notas/NN_titulo.md` (ver convención al final).

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

## 7. Convención de notas entre agentes
Cada vez que se haga algo nuevo, crear `notas/NN_titulo_corto.md` (NN correlativo, empezando en 02) con estas secciones:
- **Objetivo** de la fase y fecha.
- **Qué se hizo** (breve).
- **Archivos creados o modificados** (rutas relativas).
- **Decisiones tomadas** y por qué.
- **Problemas encontrados / cifras clave** (con valores exactos).
- **Pendiente y cómo continuar** (primer paso concreto para el siguiente agente).

Nunca sobrescribir notas anteriores; si algo cambia, se anota en la nota nueva.

---

## 8. Archivos de entrada disponibles
`EP3_Instrucciones_y_Pauta_EP3_Encargo_Estudiante.pdf`, `informe_ejecutivo_streamview.docx`, `01_limpieza_y_eda_streamview.ipynb`, `netflix_movies_detailed_up_to_2025.csv`, `netflix_tv_shows_detailed_up_to_2025.csv` (en `/mnt/user-data/uploads/`).
