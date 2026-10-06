# NOTAS DEL PROYECTO — EP3 ADY1104 Visualización de Datos (StreamView Analytics)

Archivo único de traspaso entre agentes: `notas/NOTAS.md`. Última consolidación: 06/10/2026.

---

## 0. REGLAS PARA AGENTES (leer y respetar siempre)

**Estructura obligatoria.** Este archivo tiene SIEMPRE las secciones 0 a 10, con esa numeración y ese orden. Cada sección de fase (3 a 8) usa SIEMPRE los mismos bloques, en este orden: **Estado · Archivos · Decisiones · Cifras · Límites y riesgos · Pendiente**. No inventes secciones nuevas ni cambies la numeración; si un bloque no aplica, escribe "—".

**Cómo actualizar (al terminar cualquier trabajo):**
1. Edita la sección afectada en su lugar. No agregues entradas al final ni dupliques texto: la información vive en un solo lugar.
2. Actualiza la tabla 1.1 (estado de fases), la 1.2 (decisiones) y la 1.3 (pendientes) si cambiaron.
3. Agrega UNA línea al registro de la sección 10 (fecha, qué cambió, secciones tocadas). Lo más reciente va arriba.
4. Si cambia una decisión, modifica su fila en 1.2 (mismo ID); no crees una decisión nueva para la misma pregunta.
5. Las cifras llevan su valor exacto y la fuente (archivo o celda). Si una cifra cambia, corrígela donde aparece; nunca dejes versiones viejas.
6. Antes de dar algo por hecho, verifica con los datos reales (re-ejecutar `src/` y los notebooks). Registra lo verificado y lo NO verificado.
7. No atribuyas a la docente reglas que no estén en su retroalimentación (sección 2). Las decisiones de diseño son del equipo.
8. Todo debe re-ejecutarse sin modificaciones: rutas relativas a la raíz, sin rutas absolutas ni pasos manuales.
9. Si tu trabajo contradice una decisión registrada en 1.2, no la cambies en silencio: anótalo como riesgo y pide confirmación al usuario.

**Entrega de contenido al usuario:** texto plano para pegar a mano, no modificaciones directas de sus archivos (preferencia del usuario). Pedir confirmación antes de avanzar de fase.

**Estructura de carpetas (raíz `EP3_visualizacion_datos/`):**

```
data/raw/         CSV originales (no se modifican)
data/processed/   datasets limpios, tablas largas y agregados (los genera src/)
notebooks/        01_integracion_y_limpieza, 02_eda_visualizaciones
src/              limpieza.py, kpis.py, infografia.py
dashboard/        capturas, PDF exportado y especificación del dashboard Looker
images/           gráficos estáticos (PNG) usados en informe y resumen
reports/          informe ejecutivo (PDF), resumen ejecutivo (PDF/PPTX), glosario.md
notas/NOTAS.md    este archivo
README.md
requirements.txt
```

**LIMPIEZA FINAL OBLIGATORIA antes de entregar:** eliminar `NOTAS.md`, cualquier `.md` de contexto interno para agentes y las pautas en PDF. La entrega contiene solo los entregables oficiales.

---

## 1. ESTADO ACTUAL (leer primero)

### 1.1 Fases

| Fase | Estado | Indicador |
|---|---|---|
| 0 Estructura de carpetas | Hecha | IE22 |
| 1 Integración y limpieza (`src/limpieza.py`, NB01) | Hecha. 15 controles OK (verificado 06/10) | IE16 |
| 2 EDA y gráficos estáticos (NB02, G1–G8) | Hecha. NB02 corre sin errores, 8 PNG (verificado 06/10) | IE13 |
| 3 KPIs (`src/kpis.py`, `reports/glosario.md`) | Código hecho: 301/301 comparaciones entre vías (verificado 06/10). Falta actualizar el glosario | IE18 |
| 4 Dashboard en Looker Studio | PENDIENTE | IE14, IE17 |
| 5 Informe PDF + resumen ejecutivo + infografía | PENDIENTE | IE13, IE17 |
| 6 Empaquetado, README, requirements, QA, limpieza de notas | PENDIENTE | IE22 |

### 1.2 Decisiones

| ID | Decisión | Estado |
|---|---|---|
| D1 | Dashboard en Looker Studio | Cerrada |
| D2 | Notebooks solo para gráficos de análisis; el dashboard no se construye en código | Cerrada |
| D3 | Los ceros de calificación de EP1 no son notas: se corrigen y se declara en el informe | Cerrada |
| D4 | Género en Looker: dos fuentes SIN mezclarlas. `looker_contenido.csv` (KPIs de portada, usa `genero_principal`) y `looker_genero.csv` (todos los gráficos de género, tabla larga, igual base que G3 y G4). Una entrada anterior recomendaba la opción B (género principal en todo, también G3 y G4) | Aplicada en el código; falta confirmación explícita del usuario de que descarta B (ver sección 6) |
| D5 | Porcentajes de KPIs bajo filtro = Σ numerador ÷ registros de la selección. G4 y G6 (estáticos) usan base fija = total del tipo. Ambas son decisiones del equipo, no requisitos de la docente | Cerrada |
| D6 | KPIs: se elimina "engagement promedio" (vale 50 por construcción), "densidad" y "popularidad promedio" (ya no se usan en ningún gráfico). Se agrega "% alto engagement" y "% estrella" | Cerrada |
| D7 | G3 usa la mediana del índice de engagement por género. Verificar si Looker permite mediana; si no, precalcular tabla por género como fuente de ese gráfico | Abierta (verificar en Fase 4) |
| D8 | Formato del resumen ejecutivo: PDF o PPTX | Abierta |
| D9 | Integrantes: ¿los mismos de EP1 (Carlos Hidalgo, Gabriel Caroca, Bruno Miranda, Keiny Navarro)? | Abierta |
| D10 | Gráficos de categorías en Looker (género, país, idioma): solo categorías reales, Top N explícito en el título, sin barra "Otros", sin "No especificado" (su cantidad se declara en la página de limitaciones). El título del gráfico y el texto deben coincidir con lo que se ve | Cerrada (responde a los comentarios IE5, IE6 e IE9 de EP1) |

### 1.3 Pendientes (en orden)
1. Actualizar `reports/glosario.md` (sección 5, Pendiente).
2. Confirmar D4 con el usuario.
3. Fase 4: construir el dashboard en Looker (sección 6).
4. Fase 5: informe PDF (10 secciones), resumen ejecutivo e infografía (sección 7).
5. Fase 6: README, `requirements.txt`, correr todo desde cero, limpieza final (sección 8).

---

## 2. CONTEXTO Y REQUISITOS

**Encargo:** grupal, caso StreamView Analytics, ahora películas + series. Ponderación 9 %, semana 11. La pauta dice 2 h en la tabla y 3 h en el texto (irrelevante para el producto).

**Entregables (6):** (1) informe ejecutivo PDF con 10 secciones (problema de negocio, objetivos, audiencia y propósito comunicacional, fuentes de datos, EDA con visualizaciones, justificación de gráficos, data storytelling, diseño del dashboard, evaluación crítica, conclusiones y recomendaciones); (2) dashboard interactivo con visualizaciones, KPIs, filtros y navegación; (3) resumen ejecutivo PDF/PPTX; (4) archivos documentados y reproducibles; (5) datasets y complementarios para re-ejecutar sin modificaciones; (6) carpeta profesional (`data/, notebooks/, dashboard/, images/, src/, README.md`). La pauta pide además indicar las variables relevantes de las fuentes.

**Rúbrica:**

| Indicador | Peso | Implicancia |
|---|---|---|
| IE18 KPIs pertinentes y bien calculados | 26 % | Pocos KPIs, justificados, cálculo verificable |
| IE17 Dashboards e infografías claros y alineados al negocio | 21 % | Diseño por página + una infografía/resumen visual |
| IE16 Integra múltiples fuentes con consistencia y calidad | 17 % | Unir movies + tv sin errores |
| IE14 Visualizaciones interactivas | 15 % | Filtros que afecten todos los gráficos y KPIs |
| IE13 Visualizaciones estáticas con herramientas especializadas | 11 % | Gráficos claros y consistentes con el objetivo |
| IE22 Organiza los productos finales | 10 % | Estructura, README, reproducibilidad |

**Retroalimentación REAL de la docente en EP1:**
1. **IE9 — enlace pendiente:** el enlace al dashboard quedó como placeholder ("[pegar aquí la URL...]"); no se pudo verificar la experiencia completa para las audiencias declaradas.
2. **IE9 — coherencia dashboard–informe:** el gráfico de géneros del dashboard en vivo no coincidía con notebook e informe (Drama dominando). Rompe la coherencia entre lo que vería Dirección Ejecutiva y la conclusión que el informe le pide creer.
3. **IE6 — carga cognitiva:** una barra "Otros" en el dashboard en vivo contradice lo que dice el texto y lo que muestra el gráfico; genera confusión para quien solo mira el dashboard.
4. **IE5 — codificación visual:** el mismo gráfico incluía categorías que no son géneros; afecta la limpieza de la codificación visual.
5. Puntajes: IE5 atributos visuales 12,8/16 e IE6 carga cognitiva 16/20, ambos "Buen desempeño". Meta en EP3: "Muy buen desempeño".

Reglas como "base fija", "ordenar por densidad", "ROI por director" o "boxplot con IQR" son decisiones del equipo, no requisitos de la docente.

**Lección de "Top géneros" (EP1):** el notebook ordenaba por densidad pero dibujaba la longitud con la cantidad de títulos; en Looker el gráfico incluía una barra "Otros" y categorías que no eran géneros. Hipótesis (no verificada): "Otros" era la barra más larga y por eso Drama no aparecía dominando. Regla vigente: **una métrica, un criterio** (lo que ordena = lo que mide la barra = lo que dice el título), con **solo categorías reales** y **sin barra "Otros"**. En el dashboard, cada gráfico debe mostrar la misma métrica, orden y cifras que su equivalente en `data/processed/cifras_clave_eda.csv`.


**Checklist de diseño (IE5/IE6), en gráficos y dashboard:** Película = azul `#0072B2`, Serie = naranja `#E69F00` (Okabe-Ito, aptos para daltonismo; contraste de texto mínimo 4,5:1) en todo el proyecto; gris para lo no destacado; barras desde cero; escala log solo con aviso; sin 3D; título = mensaje; máximo 5–7 categorías mostradas como "Top N" rotulado en el título (sin barra "Otros" ni categorías que no sean del eje: excluir "No especificado" y declarar su cantidad en la página de limitaciones); rotular directo; filtros en el mismo lugar en todas las páginas; KPIs arriba y detalle abajo; página breve de "cómo leer / limitaciones".

**Contexto de las fuentes y de EP1:**
- Crudos: películas 16.000 × 18 columnas; series 16.000 × 16 (15.991 `show_id` únicos, sin `budget` ni `revenue`); exactamente 1.000 títulos por año en cada una. `duration` vacía (películas) o constante (series); `rating` idéntica a `vote_average`.
- Nulos originales (películas / series): director 0,8 % / 68,5 %; description 0,8 % / 20 %; country 2,9 % / 11,2 %; genres 0,7 % / 6,1 %.
- 514–528 títulos tienen el mismo nombre en ambos archivos (p. ej. una película y su serie): no son duplicados, se separan por `tipo_contenido`. Solo 8 géneros coinciden literalmente entre las dos taxonomías.
- Base EP1: solo películas (16.000, 2010–2025), `popularity` como proxy de engagement y `vote_average`/`vote_count` como calificación. Entregables EP1: `01_limpieza_y_eda_streamview.ipynb`, `informe_ejecutivo_streamview.docx`, dashboard en Looker. Sus 7 hallazgos: quiebre 2021, Drama vs Adventure/Animation, correlación 0,071, directores por ingresos, ROI mediano 1,70×, EE. UU./Japón, 78 % sin datos financieros.
- Artefacto de EP1 por los ceros: el "1,71 en 2025" de calificación era en gran parte ceros sin votos (73,5 % de las películas de 2025 tienen 0 votos). Sin ceros, 2025 queda en 6,45 (películas) y 7,31 (series). La correlación de EP1 (0,071 y 0,052) usaba esos ceros; la corregida es r = 0,246 y 0,133 (G7).

---

## 3. DATOS Y LIMPIEZA (Fases 0–1)

**Estado:** hecha y reproducible. `python src/limpieza.py` regenera los 6 CSV y falla si un control no se cumple (verificado 06/10).

**Archivos:** `src/limpieza.py`, `notebooks/01_integracion_y_limpieza.ipynb` (incluye diccionario de variables con columna "% sin dato" = nulos + "No especificado"), y en `data/processed/`: `contenido_unificado.csv` (31.991 filas), `contenido_por_genero.csv`, `contenido_por_pais.csv`, `contenido_por_director.csv`, `mapa_generos.csv`, `control_calidad.csv` (15 controles). Las tablas largas no incluyen `description` ni `cast`.

**Decisiones:**
1. `content_id` = `M-<show_id>` / `S-<show_id>`: 397 `show_id` se solapan entre archivos con títulos distintos.
2. 9 `show_id` duplicados en series (todos de 2025): se conserva la fila con más votos y, si empatan, la de mayor popularidad → 15.991 series; total 31.991.
3. `vote_average_clean` = NaN si `vote_count` = 0; `calificacion_confiable` = `vote_count` ≥ 10 (`UMBRAL_VOTOS`).
4. `indice_engagement` = percentil (0–100) de `popularity` dentro de cada tipo (escalas no comparables: mediana 10,9 vs 36,2).
5. `genero_unificado` en español vía `mapa_generos.csv` (Action+Adventure y Science Fiction+Fantasy se unen); sin repeticiones por título.
6. Finanzas solo en películas (0 = no informado). Se eliminan `duration`, `rating` y `type`.

**Cifras:**

| | Películas | Series |
|---|---|---|
| Títulos | 16.000 | 15.991 |
| Sin votos | 894 (5,6 %) | 3.666 (22,9 %) |
| Con ≥ 10 votos | 14.183 (88,6 %) | 6.148 (38,4 %) |
| Calificación prom. EP1 (con ceros) | 5,956 | 5,420 |
| Calificación prom. corregida | 6,309 | 7,024 |
| Popularidad mediana / promedio | 10,91 / 20,38 | 36,20 / 64,88 |
| Con datos financieros | 3.540 (22,1 %) | 0 |

ROI mediano 1,70×; promedio 781,5× (outliers). Géneros: 12 en ambos tipos (11 reales + "No especificado"); solo películas: Suspenso, Terror, Romance, Historia, Música, Película de TV; solo series: Reality, Telenovela, Talk show, Noticias. "No especificado": 107 películas, 1.071 series.

**Límites y riesgos:** ambos datasets tienen exactamente 1.000 títulos por año (la composición por año no es hallazgo); director nulo en 68,5

**Pendiente:** — (opcional: calcular "% sin dato" por tipo en el diccionario).

## 4. EDA (Fase 2)

**Estado:** hecha. `notebooks/02_eda_visualizaciones.ipynb` corre sin errores (verificado 06/10). Cada gráfico tiene ficha (problema, mensaje, métrica, por qué ese gráfico) y título dinámico calculado desde los datos. Termina con una síntesis que responde la pregunta de negocio.

**Archivos:** 8 PNG en `images/` (`g1_calidad_y_cobertura`, `g2_popularidad_boxplot_iqr`, `g3_engagement_calidad_genero`, `g4_volumen_por_genero`, `g5_evolucion_por_anio`, `g6_paises`, `g7_engagement_vs_calidad`, `g8_roi_por_director`), `data/processed/cifras_clave_eda.csv` (control cruzado notebook ↔ dashboard ↔ informe) y `control_g4_vs_looker.csv` (evidencia de la diferencia de género, sección 6).

**Decisiones:**
1. G3 es una dispersión (engagement mediano vs calificación promedio solo con ≥ 10 votos, tamaño = n); reemplazó a la densidad, que estaba dominada por el denominador (popularidad).
2. G4 y G6 usan base fija = total del tipo; un título puede tener varios géneros/países, por eso no suman 100 %.
3. G6 con países en español (`PAISES_ES`, definido en `src/kpis.py`).
4. G8: mínimo 5 películas con datos por director (con 3 lo dominaba la franquicia Terrifier, 46×); se rotula la mediana; "Anthony y Joe Russo" se unifican (codirigen las mismas películas).
5. G2: boxplot con regla IQR, atípicos fuera del dibujo y cuantificados.

**Cifras:**

| G | Hallazgo | Cifra clave |
|---|---|---|
| G1 | Series puntúan más alto, pero pocas tienen votos suficientes | 7,02 vs 6,31; 38 % vs 89 % |
| G2 | Popularidad sesgada: la mediana representa mejor | medianas 10,9 / 36,2; atípicos 1.705 / 1.609 |
| G3 | Engagement y calidad por género | mayor engagement: Acción y Aventura 66,4 (películas), Telenovela 84,6 (series); mejor nota: Documental 7,14 / 7,48 |
| G4 | Oferta por género (top 7 de los géneros comunes) | Drama 43,2 % películas / 49,1 % series |
| G5 | Los títulos de 2025 casi no tienen votos | series 49,7 % (2024) → 6,7 % (2025); películas 2025: 8,6 % |
| G6 | EE. UU. domina películas, no series | 48,5 % vs 20,0 % |
| G7 | Relación débil engagement–calificación | r = 0,246 películas, 0,133 series |
| G8 | ROI agregado por director (≥ 5 películas) | top1 Chris Renaud 8,8×; su película principal aporta 23,5 % de sus ingresos; mediana global 1,70×; cobertura 22,1 % |

**Límites y riesgos:**
- G5: la caída de 2025 es por títulos recientes sin votos, no una tendencia.
- G8: el ROI agregado pesa más a las películas de mucha taquilla; por eso se muestra también la mediana.
- G3 depende de D7 (mediana en Looker).

**Pendiente:** — (la síntesis del notebook se actualiza si cambia D4).

---

## 5. KPIs (Fase 3)

**Estado:** `python src/kpis.py` corre y termina en OK: 301/301 comparaciones entre la vía pandas y la vía estilo Looker (rtol 1e-9; total, por tipo, 30 selecciones aleatorias de año × género × país y verificación de la tabla de género) y 9/9 contrastes con las cifras de la sección 3 (verificado 06/10).

**Archivos:** `src/kpis.py` y, en `data/processed/`: `looker_contenido.csv` (una fila por título, con `genero_principal`, `pais_principal` y columnas aditivas), `looker_genero.csv` (una fila por título y género, sin "No especificado"), `kpis_resumen.csv`, `kpis_por_anio.csv`, `kpis_por_genero.csv`, `kpis_por_pais.csv`, `kpis_verificacion.csv`, y `reports/glosario.md`.

**Decisiones:**
1. Una sola tabla (`looker_contenido.csv`) alimenta todos los KPIs de la portada, para que cada filtro los afecte.
2. Porcentajes bajo filtro: Σ numerador ÷ registros de la selección (nunca promedio de porcentajes).
3. ROI agregado solo con filas con presupuesto e ingresos (`presupuesto_roi`, `ingresos_roi`).
4. "% alto engagement" = percentil ≥ 75 dentro del tipo; vale 25 % por construcción en cada tipo y solo informa bajo filtros.
5. "% estrella" = alto engagement (percentil ≥ 75) y ≥ 10 votos y nota ≥ 7 (`UMBRAL_ESTRELLA_NOTA`).
6. Mediana del ROI solo se calcula en `kpis_resumen.csv`; en Looker se usa el ROI agregado (salvo que exista función de mediana).

**Cifras:**

| KPI (columna) | Total | Película | Serie |
|---|---|---|---|
| Títulos (`titulos`) | 31.991 | 16.000 | 15.991 |
| Calificación promedio (`calificacion_prom`) | 6,630 | 6,309 | 7,024 |
| % con votos suficientes (`pct_confiable`) | 63,6 % | 88,6 % | 38,4 % |
| % alto engagement (`pct_alto_engagement`) | 25,0 % | 25,0 % | 25,0 % |
| % estrella (`pct_estrella`) | 8,5 % | 8,1 % | 8,9 % |
| ROI agregado (`roi_agregado`) | 2,917× | 2,917× | N/A |
| ROI mediano (`roi_mediano`) | 1,700× | 1,700× | N/A |
| Películas con datos financieros (`peliculas_con_datos_fin`) | 3.540 | 3.540 | 0 |
| Cobertura financiera (`pct_cobertura_fin`) | 22,1 % | 22,1 % | N/A |

Además `kpis_resumen.csv` trae `pct_del_catalogo` (base fija 31.991).

**Límites y riesgos:** "% alto engagement" y "% estrella" son casi constantes en el catálogo completo; solo son informativos al filtrar por género, país o año. Los KPIs de la portada y los gráficos de género usan criterios distintos de género (D4): declararlo siempre.

**Pendiente:**
1. Actualizar `reports/glosario.md`: quitar densidad y popularidad promedio; agregar la fila de "% estrella"; incluir la sección "Diferencia entre fuentes" (sección 6).
2. Agregar `looker_genero.csv` a la lista de salidas del docstring de `src/kpis.py`.

---

## 6. DASHBOARD LOOKER (Fase 4)

**Estado:** pendiente; aún no se ha tocado Looker.

**Archivos:** por crear en `dashboard/` (capturas, PDF exportado, especificación de páginas y filtros). Looker no se reproduce con archivos: entregar capturas, PDF exportado, enlace público probado en ventana de incógnito y la especificación en el README.

**Decisiones (ver D4, D5, D6, D7 en 1.2):**
1. Fuente A `looker_contenido.csv`: KPIs de la portada y gráficos por tipo, año, idioma y país. Fuente B `looker_genero.csv`: todos los gráficos de género (misma base que G3 y G4). No se mezclan por `content_id` (un cruce uno-a-muchos inflaría los SUM/COUNT de la fuente A).
2. Fórmulas de los KPIs en Looker: Títulos `COUNT_DISTINCT(content_id)`; % del catálogo `COUNT_DISTINCT(content_id)/31991`; Calificación promedio `AVG(vote_average_clean)`; % con votos suficientes `SUM(confiable_num)/COUNT(content_id)`; % alto engagement `SUM(alto_engagement)/COUNT(content_id)`; % estrella `SUM(es_estrella)/COUNT(content_id)`; ROI agregado `SUM(ingresos_roi)/SUM(presupuesto_roi)`; Cobertura financiera `SUM(financiero_num)/SUM(es_pelicula)`.
3. Páginas: (1) resumen con KPIs, (2) catálogo película vs serie, (3) engagement vs calidad, (4) financiero (solo películas), (5) cómo leer / limitaciones. Filtros globales en el mismo lugar en todas las páginas: tipo, rango de años, idioma, género, país.
4. Reglas para cada gráfico de categorías (D10): revisar en la interfaz que no agrupe el resto en "Otros" (en gráficos tipo torta/donut Looker puede mostrar un segmento "Otros"; verificar la opción en el editor y desactivarla), limitar el Top N, y filtrar "No especificado". La fuente A (`looker_contenido.csv`) incluye "No especificado" (1.087 títulos por género principal, 2.261 por país); la fuente B ya lo excluye.

**Cifras:** `control_g4_vs_looker.csv` — Drama 43,2 % películas / 49,1 % series (todos los géneros del título, G4 y fuente B) frente a 22,7 % / 31,5 % (solo `genero_principal`, fuente A); Acción y Aventura en películas 26,0 % vs 14,2 %.

**Límites y riesgos (declarar en la página 5):**
- Los KPIs de la portada cuentan solo el género principal (primero listado); los gráficos de género cuentan todos los géneros. 76 % de las películas y 57 % de las series tienen 2 o más géneros (promedio 2,28 y 1,82). Que el primero listado sea el género dominante no está verificado.
- Objeción registrada a D4 (a favor de la opción B): con la tabla larga los porcentajes por género no suman 100 % y, al filtrar por varios géneros, un título cuenta más de una vez; además hay dos criterios de género en la misma pantalla. Mitigación: rotular cada gráfico ("todos los géneros del título" vs "género principal") y declarar la diferencia.
- **Hueco abierto:** `looker_genero.csv` no tiene país, así que el filtro de país no afecta los gráficos de género; y el filtro de género solo afecta los gráficos de la fuente B y los KPIs de la fuente A que usen `genero_principal`. IE14 pide filtros que afecten todos los gráficos: decidir si agregar `pais_principal` a `looker_genero.csv` y cómo rotular los controles.
- Comprobar en Looker si existe `MEDIAN` (D7); si no, precalcular una tabla por género con la mediana del engagement.
- Verificar que cada gráfico de Looker muestre la misma métrica, orden y cifras que `cifras_clave_eda.csv` (el fallo de EP1).
- Verificación en vivo obligatoria (IE9): publicar el dashboard, abrir el enlace en ventana de incógnito, y comparar 3 cifras por gráfico con `cifras_clave_eda.csv` y con el informe. Que texto y gráfico coincidan es la causa de los comentarios IE6 e IE9 de EP1.
- Revisar si "Película de TV" (en realidad un formato y no un género) aparece en rankings de género: puede leerse como "categoría que no es género" (comentario IE5).

**Pendiente:** conectar las dos fuentes, construir las 5 páginas, comparar 3 cifras por gráfico contra el notebook, exportar capturas/PDF y probar el enlace público.

---

## 7. INFORME, RESUMEN E INFOGRAFÍA (Fase 5)

**Estado:** pendiente. `src/infografia.py` (genera `images/infografia_resumen.png`, una página, para IE17) y `parches_ep3.md` fueron mencionados en notas anteriores pero NO se revisaron en la consolidación del 06/10 (no se subieron): verificar antes de usar.

**Archivos:** por crear en `reports/`: `informe_ejecutivo.pdf` (10 secciones de la sección 2), `resumen_ejecutivo` (PDF o PPTX, D8).

**Decisiones:**
1. El informe incluye una subsección en "Justificación de las representaciones gráficas" sobre atributos visuales y carga cognitiva (apunta a IE5/IE6).
2. Cada gráfico del informe declara problema de negocio y audiencia, y por qué ese tipo de gráfico minimiza el esfuerzo de lectura.
3. El informe declara la corrección de los ceros de EP1 (D3) como punto de calidad de datos y evaluación crítica.

**Cifras:** las de las secciones 3 a 5; no recalcular a mano, usar `cifras_clave_eda.csv` y `kpis_resumen.csv`.

**Límites y riesgos:** una imagen completa de géneros (todos los géneros del título) puede ir en el informe como análisis aparte, con nombre distinto al del dashboard.

**Pendiente:** redactar informe y resumen; incluir la infografía; enlace/capturas del Looker (el pendiente de EP1 que la docente señaló).

---

## 8. ENTREGA Y CIERRE (Fase 6)

**Estado:** pendiente.

**Archivos:** `README.md` (pasos de ejecución, estructura, especificación del dashboard, enlace de Looker), `requirements.txt`.

**Decisiones:** `kpis.py` y `limpieza.py` usan `if __name__ == "__main__":`, así que el `from kpis import PAISES_ES` de NB02 es seguro.

**Cifras:** —

**Límites y riesgos:** el enlace de Looker no puede quedar como placeholder (feedback de EP1).

**Pendiente:** correr todo desde una carpeta limpia (`limpieza.py` → `kpis.py` → NB01 → NB02), revisar contra la rúbrica, cruzar cifras notebook ↔ dashboard ↔ informe, y ejecutar la LIMPIEZA FINAL de la sección 0.

---

## 9. RIESGOS Y LECCIONES

1. No atribuir a la docente reglas que no dio (una nota anterior lo hizo): ver sección 2.
2. El mismo número debe salir igual en notebook, dashboard e informe: es el fallo de EP1.
3. Los títulos de los gráficos afirman hallazgos: verificar siempre contra los datos (G5 y G7 tenían títulos falsos antes de corregirse).
4. Las métricas con valor fijo por construcción (engagement promedio, % alto engagement en el total) no informan: no usarlas como KPI global.
5. Cualquier gráfico de calidad debe mostrar la cobertura de votos; los títulos de 2025 aún no tienen nota fiable.
6. El gráfico de géneros de EP1 mezclaba una barra "Otros" y categorías que no eran géneros, y contradecía el texto (comentarios IE5, IE6 e IE9). En todo gráfico de categorías: solo categorías reales, Top N rotulado, sin "Otros".
---

## 10. REGISTRO DE CAMBIOS (más reciente arriba; una línea por cambio)

- 06/10/2026 — Se amplía la retroalimentación real de la docente (comentarios IE5, IE6 e IE9 sobre la barra "Otros" y las categorías que no son géneros); se agrega D10 y se corrige el checklist que sugería agrupar en "Otros" (secciones 1.2, 2, 6 y 9).
- 06/10/2026 — Consolidación completa de este archivo en la estructura 0–10; verificación con datos reales (`limpieza.py` 15/15 OK, `kpis.py` 301/301, NB01 y NB02 sin errores); se registra D4 y el hueco del filtro de país (secciones 1, 5, 6).
- 05/10/2026 — Se aplican los fixes de revisión: `looker_genero.csv` como fuente de gráficos de género; G3 se guarda como `g3_engagement_calidad_genero.png`; se eliminan densidad y popularidad promedio de los KPIs; se agrega `pct_estrella`.
- 05/10/2026 — G5, G6, G7 y G8 corregidos (títulos falsos, países en español, mínimo 5 películas por director); G3 rediseñado; síntesis agregada a NB02; diccionario de variables en NB01.
- 05/10/2026 — Decisiones D1 y D2 (Looker Studio; notebooks solo de análisis). Se corrige la retroalimentación de la docente (sección 2).
- 05/10/2026 — Fases 0–3 construidas por primera vez (estructura, integración y limpieza, EDA G1–G8, KPIs).