# NOTAS DEL PROYECTO — EP3 ADY1104 Visualización de Datos (StreamView Analytics)

**Archivo único de traspaso entre agentes** (`notas/NOTAS.md`). Última consolidación: 05/10/2026.

**Convención:**
- Es el único archivo de notas. Cada trabajo nuevo se **agrega como entrada al final** (`# ENTRADA NN — título`); no se borran entradas, solo se corrigen con una entrada nueva.
- Cada entrada: objetivo y fecha, qué se hizo, archivos (rutas relativas a `EP3_visualizacion_datos/`), decisiones y motivo, cifras exactas, pendiente con primer paso concreto.
- Todo debe re-ejecutarse sin modificaciones (pauta, punto 5): rutas relativas a la raíz, sin rutas absolutas ni pasos manuales.

```
EP3_visualizacion_datos/
├── data/raw/         CSV originales (no se modifican)
├── data/processed/   datasets limpios, tablas largas y agregados (los genera src/)
├── notebooks/        01_integracion_y_limpieza, 02_eda_visualizaciones
├── src/              limpieza.py, kpis.py, (infografia.py)
├── dashboard/        capturas, PDF exportado y especificación del dashboard Looker
├── images/           gráficos estáticos (PNG) usados en informe y resumen
├── reports/          informe ejecutivo (PDF), resumen ejecutivo (PDF/PPTX), glosario.md
├── notas/NOTAS.md    este archivo
├── README.md
└── requirements.txt
```

**LIMPIEZA FINAL (OBLIGATORIA) antes de entregar:** eliminar `NOTAS.md`, cualquier `.md` de contexto interno para agentes y las pautas en PDF. La entrega contiene solo los entregables oficiales.

**Índice:** 01 Pauta, rúbrica y contexto · 02 Fases 0–1 integración y limpieza · 03 Fase 2 EDA (G1–G8) · 04 Fase 3 KPIs · 05 Revisión contra rúbrica y storytelling · 06 Verificación con datos reales · 07 Notebooks actualizados y decisión de género (G4/Looker) · Estado y pendientes

**Equivalencias con la versión anterior de este archivo (que tenía 8 entradas, dos de ellas numeradas "03"):**

| Antes | Ahora |
|---|---|
| Entrada 01 (análisis y plan) | Entrada 01 (pauta, rúbrica) + "Datos de contexto conservados" (hallazgos 3.1–3.4 y base EP1) |
| Entrada 02 (feedback EP1, checklist IE5/IE6) | Entrada 01 (feedback real corregido, regla "una métrica, un criterio", checklist) |
| Entrada 03 (Fases 0 y 1) | Entrada 02 |
| "Entrada 03" repetida (revisión de títulos EDA) y Entrada 07 (correcciones de mensajes) | Entrada 03 (tabla G1–G8) |
| Entrada 04 (cierre de notas 01 y 02) | Eliminada: ya se cumplió (los archivos 01 y 02 de contexto se borraron) |
| Entrada 05 (decisión Looker y Fase 3), Entrada 08 (duplicada de 05/07) | Entrada 04 |
| Entrada 06 (corrección del feedback de EP1) | Entrada 01 |
| (no existía) | Entradas 05, 06 y 07 |

---

# ESTADO ACTUAL (leer primero)

| Fase | Estado | Indicador |
|---|---|---|
| 0 Estructura de carpetas | Hecha | IE22 |
| 1 Integración y limpieza (`src/limpieza.py`, NB01) | Hecha, 15 controles OK; diccionario de variables agregado (Entrada 07) | IE16 |
| 2 EDA y gráficos estáticos (NB02, G1–G8) | Hecha y verificada con datos reales; G3 corregido; falta decidir la definición de género (Entrada 07) | IE13 |
| 3 KPIs (`src/kpis.py`, `reports/glosario.md`) | Hecha: 11 KPIs verificados 330/330 (Entrada 06); falta actualizar el glosario | IE18 |
| 4 Dashboard en **Looker Studio** | **Pendiente** | IE14, IE17 |
| 5 Informe ejecutivo PDF + resumen ejecutivo | **Pendiente** | IE13, IE17 |
| 6 Empaquetado, README, requirements, QA, limpieza de notas | **Pendiente** | IE22 |

**Decisiones cerradas:** dashboard en Looker Studio (notebooks solo para gráficos de análisis, sin dashboard en código); corregir los ceros de calificación de EP1 y declararlo en el informe (el equipo ya trabaja con la cifra corregida).
**Decisiones abiertas:** (1) **definición de género para G3, G4 y Looker: opción B recomendada, pendiente de confirmar (Entrada 07)**; (2) formato del resumen ejecutivo (PDF o PPTX); (3) integrantes (¿mismos de EP1: Carlos Hidalgo, Gabriel Caroca, Bruno Miranda, Keiny Navarro?).

---

# ENTRADA 01 — Pauta, rúbrica y contexto (EP3 ADY1104)

Encargo **grupal**, caso StreamView Analytics, ahora películas + series. Ponderación 9 %, semana 11. (La pauta dice 2 h en la tabla y 3 h en el texto; irrelevante para el producto.)

**Entregables (6):** (1) informe ejecutivo PDF con 10 secciones: problema de negocio, objetivos, audiencia y propósito comunicacional, fuentes de datos, EDA, justificación de gráficos, data storytelling, diseño del dashboard, evaluación crítica, conclusiones y recomendaciones; (2) dashboard interactivo con visualizaciones, KPIs, filtros y navegación; (3) resumen ejecutivo PDF/PPTX; (4) archivos documentados y reproducibles; (5) datasets y complementarios para re-ejecutar sin modificaciones; (6) carpeta profesional (`data/, notebooks/, dashboard/, images/, src/, README.md`).
La pauta pide además "indicar las variables relevantes" de las fuentes.

| Indicador | Peso | Implicancia |
|---|---|---|
| IE18 KPIs pertinentes y bien calculados | 26 % | Pocos KPIs, justificados, cálculo verificable |
| IE17 Dashboards **e infografías** claros y alineados al negocio | 21 % | Diseño por página + una infografía/resumen visual |
| IE16 Integra múltiples fuentes con consistencia y calidad | 17 % | Unir movies + tv sin errores |
| IE14 Visualizaciones interactivas | 15 % | Filtros que afecten todos los gráficos y KPIs |
| IE13 Visualizaciones estáticas con herramientas especializadas | 11 % | Gráficos claros y consistentes con el objetivo |
| IE22 Organiza los productos finales | 10 % | Estructura, README, reproducibilidad |

**Datos de contexto conservados de las notas originales:**
- Fuentes crudas: películas 16.000 × 18 columnas; series 16.000 × 16 (15.991 `show_id` únicos, sin `budget` ni `revenue`); exactamente 1.000 títulos por año en cada una. `duration` vacía en películas y constante ("1 Seasons") en series; `rating` idéntica a `vote_average`.
- Nulos originales (películas / series): `director` 0,8 % / 68,5 %; `description` 0,8 % / 20 %; `country` 2,9 % / 11,2 %; `genres` 0,7 % / 6,1 %. Series con director: 5.035 de 16.000 (el ranking de directores no es comparable entre tipos).
- 514–528 títulos tienen el mismo nombre en ambos archivos (p. ej. una película y su serie): **no son duplicados**, se mantienen separados por `tipo_contenido`. Solo 8 géneros coinciden literalmente entre las dos taxonomías.
- Países (títulos, antes de traducir): películas EE. UU. 7.762; series EE. UU. 3.194, China 1.900, Japón 1.881, Corea del Sur 1.299. Idiomas de series: en 4.437, zh 2.456, ja 1.883, ko 1.364.
- Popularidad promedio por año: en películas sube fuerte en 2024 (74,0); en series se mantiene entre 48 y 83 sin ese quiebre.
- **Artefacto de EP1 por los ceros:** el "1,71 en 2025" de calificación era en gran parte ceros sin votos (73,5 % de las películas de 2025 tienen 0 votos); sin ceros, 2025 queda en 6,45 (películas) y 7,31 (series). La calificación de series 2010 → 2024 (4,67 → 6,48) también incluía ceros. La correlación de EP1 (0,071 películas, 0,052 series) usaba esos ceros; la corregida es r = 0,246 y 0,133 (G7).
- **Base EP1:** solo películas (16.000, 2010–2025), con `popularity` como proxy de engagement y `vote_average`/`vote_count` como calificación. Entregables de EP1: `01_limpieza_y_eda_streamview.ipynb`, `informe_ejecutivo_streamview.docx` y dashboard en Looker Studio. Sus 7 hallazgos: quiebre 2021, Drama vs Adventure/Animation, correlación 0,071, directores por ingresos, ROI mediano 1,70×, EE. UU./Japón, 78 % sin datos financieros.
- **Diagnóstico de "Top géneros" (EP1, películas):** el notebook ordenaba por densidad (TV Movie 0,596; Documentary 0,547; Music 0,438; History 0,414; Drama 0,368, puesto 5 de 20) pero dibujaba la longitud con la cantidad de títulos (Drama 6.910, la barra más larga); Looker ordenaba por la métrica graficada. Además, el boxplot usaba los 5 primeros por densidad mientras el informe decía "los 5 más frecuentes".

**Retroalimentación REAL de la docente en EP1 (solo estos puntos):**
1. El enlace al dashboard de Looker quedó como placeholder: no pudo evaluar la interactividad.
2. El gráfico "Top géneros" del dashboard en vivo no coincidía con notebook e informe (Drama dominando).
3. IE5 atributos visuales 12,8/16 y IE6 carga cognitiva 16/20, ambos "Buen desempeño"; meta: "Muy buen desempeño".
Reglas como "base fija", "ordenar por densidad", "ROI por director" o "boxplot con IQR" son **decisiones del equipo, no requisitos de la docente**.

**Lección de la discrepancia de "Top géneros" (EP1):** el notebook ordenaba por densidad pero dibujaba la longitud con la cantidad de títulos; Looker ordenaba por la métrica graficada. **Regla vigente: una métrica, un criterio** (lo que ordena = lo que mide la barra = lo que dice el título). Para el dashboard: cada gráfico debe mostrar la misma métrica, orden y cifras que su equivalente en `cifras_clave_eda.csv`.

**Checklist de diseño (IE5/IE6), aplicado en gráficos y dashboard:** Película = azul `#0072B2`, Serie = naranja `#E69F00` (Okabe-Ito, aptos para daltonismo; contraste de texto mínimo 4,5:1) en todo el proyecto; gris para lo no destacado; barras desde cero; log solo con aviso; sin 3D; título = mensaje; máx. 5–7 categorías (resto en "Otros"); rotular directo; filtros en el mismo lugar en todas las páginas; KPIs arriba, detalle abajo; página breve de "cómo leer / limitaciones".

---

# ENTRADA 02 — Fases 0 y 1: estructura, integración y limpieza

**Archivos:** `src/limpieza.py` (`python src/limpieza.py` desde la raíz; falla si un control no se cumple), `notebooks/01_integracion_y_limpieza.ipynb`, y en `data/processed/`: `contenido_unificado.csv` (31.991 filas), `contenido_por_genero.csv`, `contenido_por_pais.csv`, `contenido_por_director.csv`, `mapa_generos.csv`, `control_calidad.csv` (15 controles). Las tablas largas no incluyen `description` ni `cast`.

**Decisiones y motivo:**
1. `content_id` = `M-<show_id>` / `S-<show_id>`: 397 `show_id` se solapan entre archivos con títulos distintos.
2. 9 `show_id` duplicados en series (todos 2025): se conserva la fila con más votos y, si empatan, la de mayor popularidad → 15.991 series; total 31.991.
3. **Los ceros no son notas:** `vote_average_clean` = NaN si `vote_count` = 0; `calificacion_confiable` = `vote_count` ≥ 10 (`UMBRAL_VOTOS`). EP1 promediaba esos ceros.
4. `indice_engagement` = percentil (0–100) de `popularity` dentro de cada tipo (escalas no comparables: mediana 10,9 vs 36,2).
5. `genero_unificado` en español vía `mapa_generos.csv` (Action+Adventure y Science Fiction+Fantasy se unen); sin repeticiones por título.
6. Finanzas solo en películas. Se eliminan `duration`, `rating` (idéntica a `vote_average`) y `type`.

**Cifras exactas:**

| | Películas | Series |
|---|---|---|
| Títulos | 16.000 | 15.991 |
| Sin votos | 894 (5,6 %) | 3.666 (22,9 %) |
| Con ≥ 10 votos | 14.183 (88,6 %) | 6.148 (38,4 %) |
| Calificación prom. EP1 (con ceros) | 5,956 | 5,420 |
| Calificación prom. corregida | 6,309 | 7,024 |
| Calificación prom. solo confiables | 6,347 | 7,288 |
| Popularidad mediana / promedio | 10,91 / 20,38 | 36,20 / 64,88 |
| Con datos financieros | 3.540 (22,1 %) | 0 |

ROI mediano 1,70×; promedio 781,5× (outliers). Géneros: 12 en ambos tipos (11 reales + "No especificado"), 6 solo películas (Suspenso, Terror, Romance, Historia, Música, Película de TV), 4 solo series (Reality, Telenovela, Talk show, Noticias). `No especificado`: 107 películas, 1.071 series.

**Límites a declarar siempre:** ambos datasets tienen exactamente 1.000 títulos por año (la composición por año no es hallazgo); director nulo en 68,5 % de las series (no comparable entre tipos); comparar tipos solo con los 11 géneros comunes; cualquier gráfico de calidad muestra la cobertura de votos.

---

# ENTRADA 03 — Fase 2: EDA y gráficos estáticos (NB02, G1–G8)

`notebooks/02_eda_visualizaciones.ipynb` genera 8 PNG en `images/` y `data/processed/cifras_clave_eda.csv` (control cruzado notebook ↔ dashboard ↔ informe). Cada gráfico tiene ficha (problema, mensaje, métrica, por qué ese gráfico) y título dinámico calculado desde los datos.

| G | Hallazgo (título) | Cifra clave |
|---|---|---|
| G1 | Series puntúan más alto, pero pocas tienen votos suficientes | 7,02 vs 6,31; 38 % vs 89 % |
| G2 | Popularidad sesgada: la mediana representa mejor (boxplot IQR, atípicos fuera del dibujo) | medianas 10,9 / 36,2; atípicos 1.705 / 1.609 |
| G3 | Engagement y calidad por género (dispersión; reemplaza a la densidad, Entrada 05) | mayor engagement: Acción y Aventura 66,4 (películas), Telenovela 84,6 (series); mejor nota: Documental 7,14 / 7,48 |
| G4 | Oferta por género (% sobre total del tipo, top 7 de géneros comunes) | Drama 43,2 % películas / 49,1 % series |
| G5 | Los títulos de 2025 casi no tienen votos | series 49,7 % (2024) → 6,7 % (2025); películas 2025: 8,6 % |
| G6 | EE. UU. domina películas, no series | 48,5 % vs 20,0 % |
| G7 | Relación débil engagement–calificación (hexbin log) | r = 0,246 películas, 0,133 series |
| G8 | ROI agregado por director (≥ 5 películas con datos) | top1 Chris Renaud 8,8×; su película principal aporta 23,5 % de ingresos; mediana global 1,70×; cobertura 22,1 % |

**Correcciones ya aplicadas (no revertir):** G7 decía "no se correlacionan" (r real 0,25/0,13); G5 decía que la cobertura de series cae de 30 % a 7 % (en realidad sube hasta 2024 y se desploma solo en 2025); G6 con países en español (`PAISES_ES` de `src/kpis.py`); G8 con mínimo de 5 películas por director (con 3 lo dominaba la franquicia Terrifier, 46×) y mediana rotulada.

---

# ENTRADA 04 — Fase 3: KPIs

`src/kpis.py` genera `looker_contenido.csv` (una fila por título, `genero_principal`, `pais_principal`, columnas aditivas para SUM/COUNT) y calcula los KPIs por dos vías independientes (pandas vs estilo Looker). `reports/glosario.md` define cada KPI (fórmula, base, límites). También salen `kpis_resumen.csv`, `kpis_por_anio.csv`, `kpis_por_genero.csv`, `kpis_por_pais.csv`, `kpis_verificacion.csv`.

**Verificación:** 330/330 comparaciones coinciden entre las dos vías (rtol 1e-9; 11 KPIs, tras agregar `pct_estrella`); 9/9 contrastes con cifras de la Entrada 02 en OK.

| KPI | Total | Película | Serie |
|---|---|---|---|
| Títulos | 31.991 | 16.000 | 15.991 |
| Calificación promedio | 6,630 | 6,309 | 7,024 |
| % con votos suficientes | 63,6 % | 88,6 % | 38,4 % |
| % alto engagement (p ≥ 75) | 25,0 % | 25,0 % | 25,0 % |
| % títulos estrella (p ≥ 75, nota ≥ 7, ≥ 10 votos) | 8,5 % | 8,1 % | 8,9 % |
| Popularidad promedio | 42,6 | 20,4 | 64,9 |
| Densidad | 0,156 | 0,309 | 0,108 |
| ROI agregado / mediano | 2,917× / 1,700× | igual | N/A |
| Películas con datos financieros | 3.540 (22,1 %) | — | — |

**Decisiones:**
1. Una sola tabla (`looker_contenido.csv`) alimenta todos los KPIs de Looker para que cada filtro los afecte; por eso el filtro de género/país usa `genero_principal`/`pais_principal` (primero de cada título). Las tablas largas solo sirven para gráficos de desglose y pueden diferir levemente.
2. Porcentajes de KPIs bajo filtro = Σ numerador ÷ conteo de la selección. Los porcentajes de G4 y G6 (estáticos) usan base fija = total del tipo.
3. ROI agregado solo con filas con presupuesto e ingresos.
4. Densidad y popularidad cruda solo se comparan dentro de un mismo tipo (en Looker, solo con un tipo seleccionado).
5. "Engagement promedio" se reemplazó por "% alto engagement" porque el promedio del percentil vale 50 por construcción.

---

# ENTRADA 05 — Revisión contra rúbrica y storytelling (05/10/2026)

**Veredicto:** NB02 mantiene storytelling por gráfico (fichas + títulos dinámicos), pero no hay hilo narrativo entre gráficos ni respuesta a la pregunta de negocio. IE16 e IE13 bien cubiertos; IE18, IE17 e IE14 (62 % del puntaje) dependen del dashboard, aún no construido.

**Hallazgos y qué hacer (el código de los parches se entregó aparte en `parches_ep3.md` y `src/infografia.py`):**
1. **Sin síntesis final en NB02:** agregar celda de recomendaciones que cite G1–G8 y responda "¿cómo repartir la inversión entre películas y series?".
2. **G3 engañoso:** la densidad = calificación ÷ popularidad está dominada por el denominador (la calificación varía 6–7, la popularidad ~10×); el "líder" probablemente es el género menos popular, no el de mejor calidad. Además usa popularidad *promedio* mientras G2 concluye que hay que usar la mediana. Reemplazo propuesto: dispersión engagement mediano (eje x) vs calificación promedio solo confiables (eje y), tamaño = n.
3. **G4:** el título de la sección es descriptivo; el hallazgo es "Drama domina ambos catálogos (43 % / 49 %)".
4. **G8:** el título afirma "el ranking depende de pocas películas" sin cifra; agregar el % de ingresos que aporta la película más taquillera del director líder.
5. **NB01 sin diccionario de variables** (la pauta lo pide) ni documentación de las tablas de país, director e idioma.
6. **KPI "% alto engagement" es 25 % por construcción** en cada tipo: solo informa bajo filtros de género/país/año. Propuesta: KPI "% de títulos estrella" (alto engagement y calificación ≥ 7 con votos confiables). Se agrega en `preparar_looker`, `kpis_pandas` y `kpis_looker` de `src/kpis.py` (columna `es_estrella`, KPI `pct_estrella`); hay que refrescar la fuente de datos en Looker.
7. **Riesgo de repetir el problema de EP1:** el filtro de género de Looker usa `genero_principal` y G4 cuenta todos los géneros. Generar `control_g4_vs_looker.csv` y declarar la diferencia en la página de limitaciones.
8. **IE17 pide infografías:** no estaba planificada; se agrega `src/infografia.py` (una página, `images/infografia_resumen.png`).
9. **IE22 / reproducibilidad:** Looker no se reproduce con archivos → entregar capturas, PDF exportado, enlace público probado en ventana de incógnito y especificación de páginas/filtros en README. `kpis.py` y `limpieza.py` ya usan `if __name__ == "__main__":` (revisado): el `from kpis import PAISES_ES` de NB02 es seguro, no hay que cambiar nada.
10. Glosario: `kpis.py` calcula ahora 11 KPIs y `glosario.md` define 9 (falta `pct_estrella` y reconciliar el resto).

---

# ENTRADA 06 — Verificación con datos reales de los parches de la Entrada 05 (05/10/2026)

**Procedimiento:** se copió el proyecto (CSV originales + `src/` + notebooks ya parchados) a una carpeta limpia y se ejecutó en orden `src/limpieza.py`, `src/kpis.py` y todas las celdas de código de NB01 y NB02 (fuera de Jupyter, con matplotlib sin pantalla); también `src/infografia.py`.

**Resultado:**
- `limpieza.py`: 15/15 controles OK (31.991 filas, 397 `show_id` solapados, 9 duplicados, 3.540 películas con datos financieros).
- `kpis.py`: 330/330 comparaciones entre vías y 9/9 contrastes en OK. `pct_estrella` = 8,5 % total (8,1 % películas, 8,9 % series); `pct_alto_engagement` sigue en 25,0 % por construcción.
- NB01: sin errores; el diccionario cubre todas las variables (ninguna "(completar)").
- NB02: sin errores; 8 PNG en `images/`; `cifras_clave_eda.csv` con 25 cifras (las de G3 cambiaron: `eng_max_*`, `calif_max_*`; G8 suma `dependencia_top1_pct` = 23,5).
- `infografia.py`: genera `images/infografia_resumen.png` con los datos reales.
- Los notebooks subidos ya traen salidas de las celdas nuevas.

**Hallazgos y ajustes:**
1. **Discrepancia G4 vs Looker (riesgo para IE14/IE17; es el mismo tipo de problema del feedback de EP1).** `control_g4_vs_looker.csv`: Drama 43,2 % películas y 49,1 % series (G4 cuenta todos los géneros del título) frente a 22,7 % y 31,5 % (Looker, solo `genero_principal`); Acción y Aventura en películas 26,0 % vs 14,2 %. Si el gráfico de géneros de Looker usa la tabla principal, mostrará cifras distintas a las del notebook y del informe. **Decisión abierta:** (A) conectar `contenido_por_genero.csv` como segunda fuente en Looker y mezclarla por `content_id`; (B) usar el género principal en G4 y en Looker, rotulado como "género principal (primero listado)". Análisis de rigor y alcance en la Entrada 07 (recomendado: B, aplicado también a G3).
2. **G3:** en el panel de series las etiquetas se pisaban y el título repetía "Documental y Documental". **Corregido y ya aplicado en NB02:** se rotulan solo los 6 géneros más alejados del valor típico y el más grande, y el título dice "en ambos tipos" cuando el líder coincide.
3. **G8:** la película principal aporta entre 23 % y 49 % de los ingresos de los 10 directores del ranking (ninguno ≥ 50 %), por eso el título dice "ingresos repartidos". **Joe Russo y Anthony Russo** aparecen como dos filas idénticas (codirigen las mismas películas): unificarlos o anotarlo.
4. **Diccionario (NB01):** la columna "% nulos" muestra 0 % en director, país, géneros y descripción porque `limpieza.py` rellena con "No especificado" (en series, el director realmente falta en 68,5 %). Cambiar la columna a "% sin dato" (nulos + "No especificado"). **Aplicado** (Entrada 07).
5. **NB01:** las celdas del diccionario (5b) quedaron después de la sección 6; moverlas antes.

---

# ENTRADA 07 — Notebooks actualizados y decisión de género para G3, G4 y Looker (05/10/2026)

**Notebooks recibidos y verificados:** NB02 es idéntico a la versión ya verificada (incluye el G3 corregido). NB01 solo cambió en la celda 21: el diccionario ahora usa la columna "% sin dato" (nulos + "No especificado"); se ejecutó sin errores. Esa columna se calcula sobre el dataset completo, así que mezcla ambos tipos (director 34,7 %; en series solo, 68,5 %): si se quiere el detalle, calcularla por tipo.

**Pregunta: ¿A o B es más rigurosa, dado que Looker aún no se ha tocado? Respuesta: B.**
Razones:
1. **Aditividad y KPIs correctos (IE18, 26 %).** Con género principal cada título cae en un solo género: los porcentajes suman 100 % y los promedios no repiten títulos. Con la tabla larga (A) un título cuenta en cada uno de sus géneros: promedios ponderados de más y porcentajes que no suman 100 %. Además, un cruce uno-a-muchos por `content_id` en Looker multiplica las filas de la tabla principal y puede inflar sus SUM/COUNT.
2. **Una sola definición de "género" en todo el dashboard.** Con A, los KPIs responderían al filtro por género principal y el gráfico de géneros a todos los géneros: dos criterios en la misma pantalla, que rompe "una métrica, un criterio".
3. **Notebook = informe = dashboard con las mismas cifras** (lo que falló en EP1), con menos piezas que mantener.
4. Evita el costo cognitivo de G4 ("no suma 100 %", IE6).

**Costo de B (declararlo):** 76 % de las películas y 57 % de las series tienen 2 o más géneros (promedio 2,28 y 1,82 por título), así que el género principal descarta información. El "primero listado" es una convención de la fuente; no está verificado que sea el género dominante. La historia sigue en pie, pero con cifras menores:
- G4 con género principal: Drama 22,7 % películas / 31,5 % series (antes 43,2 / 49,1); Comedia 14,7 / 14,5; Animación 7,0 / 12,5; Acción y Aventura 14,2 / 4,2; Documental 5,1 / 4,4; Crimen 3,0 / 4,1; Ciencia ficción y Fantasía 4,1 / 2,3. Cambia el top 7 (entran Animación y Documental).
- G3 con género principal: mayor engagement Ciencia ficción y Fantasía 67,6 (películas; antes Acción y Aventura 66,4) y Telenovela 83,6 (series); mejor nota Documental 7,11 / 7,51.

**Alcance si se confirma B (no solo G4):**
- G3 y G4 pasan a usar `genero_principal` de `looker_contenido.csv`.
- `kpis_por_genero.csv` (hoy sale de la tabla larga) se recalcula desde `looker_contenido.csv` o se elimina.
- Reescribir el título y el mensaje de G4, y el punto 4 de la síntesis.
- Rotular "género principal (primero listado)" en gráficos, glosario y página de limitaciones del dashboard.
- Si se quiere mostrar la imagen completa por géneros, ponerla solo en el informe, como análisis aparte y con otro nombre ("todos los géneros del título"), nunca en el dashboard.
- Mantener `control_g4_vs_looker.csv` como evidencia del por qué de la decisión.

**Estado:** B es una recomendación; la decisión sigue abierta hasta que el equipo la confirme.

---

---

# ENTRADA 08 — Ajustes finales EDA y limpieza (05/10/2026)

**Ajustes realizados y verificados:**
1. **NB01:** Se reordenó la sección 5 (exportación y QA) y 5b (diccionario de variables) para que queden secuencialmente antes de la sección 6 (resumen de decisiones).
2. **G8 (NB02):** Se unificó a "Anthony Russo" y "Joe Russo" en "Anthony y Joe Russo", evitando filas duplicadas en el ranking de ROI. El top 1 sigue siendo Chris Renaud con 8,8×.
3. **G2 (NB02):** Se ajustó el layout del boxplot (*Popularidad sesgada*) desplazando el gráfico hacia la izquierda y colocando las estadísticas de texto a la derecha, fuera del área de trazado, para evitar superposiciones.
4. **Glosario (`reports/glosario.md`):** Se agregaron las definiciones formales para `% títulos estrella` (`pct_estrella`) y `Popularidad promedio`, reconciliando los 11 KPIs calculados por `src/kpis.py`.

---

# PENDIENTE Y PRIMER PASO

1. **Confirmar la opción B (Entrada 07)** y aplicarla en G3, G4, `kpis_por_genero.csv` y la síntesis antes de tocar Looker.
2. **Fase 4:** Construir el dashboard en Looker conectando `looker_contenido.csv`. Páginas sugeridas: (1) resumen con KPIs, (2) catálogo película vs serie, (3) engagement vs calidad, (4) financiero solo películas, (5) cómo leer / limitaciones.
3. **Fase 5:** Informe PDF (10 secciones) y resumen ejecutivo; incluir `images/infografia_resumen.png`.
4. **Fase 6:** README, `requirements.txt`, correr todo desde cero, y limpieza final de notas y pautas.
