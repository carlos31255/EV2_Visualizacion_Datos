# NOTAS DEL PROYECTO — EP3 ADY1104 Visualización de Datos (StreamView Analytics)

Última actualización: 07/10/2026.

---

## 0. REGLAS PARA AGENTES

1. No hacer commits sin permiso explícito del usuario.
2. Revisar = solo leer y reportar. No editar sin confirmación.
3. Las cifras llevan su valor exacto y su fuente. Si cambia una cifra, corrígela donde aparece; nunca dejar versiones viejas.
4. Antes de dar algo por hecho, verificar con los datos reales (`src/` y notebooks).
5. Todo debe re-ejecutarse sin modificaciones: rutas relativas a la raíz, sin rutas absolutas.
6. Si tu trabajo contradice una decisión registrada, anótalo y pide confirmación al usuario.

**Estructura de carpetas:**
```
data/raw/           CSV originales (no se modifican)
data/processed/     datasets limpios y KPIs (los genera src/)
notebooks/          01_integracion_y_limpieza, 02_eda_visualizaciones
src/                limpieza.py, kpis.py
dashboard/          capturas, PDF exportado
images/             gráficos PNG (usados en informe)
reports/            informe_ejecutivo.md
glosario.md
README.md
requirements.txt
```

**LIMPIEZA FINAL antes de entregar:** eliminar NOTAS.md y las pautas en PDF. La entrega contiene solo los entregables oficiales.

---

## 1. ESTADO ACTUAL

### 1.1 Fases

| Fase | Estado |
|------|--------|
| 0 Estructura | ✅ Hecha |
| 1 Integración y limpieza (`limpieza.py`, NB01) | ✅ Hecha — 17/17 controles OK (verificado 06/10) |
| 2 EDA y gráficos G1–G8 (NB02) | ✅ Hecha — 8 PNG, sin errores (verificado 06/10) |
| 3 KPIs (`kpis.py`, `glosario.md`) | ✅ Hecha — 300/300 comparaciones, glosario finalizado |
| 4 Dashboard en Looker Studio | ⏳ PENDIENTE |
| 5 Informe PDF + resumen ejecutivo | ⏳ PENDIENTE — borrador en `reports/informe_ejecutivo.md` |
| 6 README, requirements, QA final, limpieza | ⏳ PENDIENTE |

### 1.2 Decisiones

| ID | Decisión | Estado |
|----|----------|--------|
| D1 | Dashboard en Looker Studio | ✅ Cerrada |
| D2 | Notebooks solo para gráficos de análisis; dashboard no se construye en código | ✅ Cerrada |
| D3 | Los ceros de calificación de EP1 no son notas: se corrigen y declara en el informe | ✅ Cerrada |
| D4 | Dashboard usa exclusivamente `looker_contenido.csv` (una fila por título) con `genero_principal`, `pais_principal` y `director_principal`. Asegura integridad de filtros (IE14) y KPIs (IE18). La pérdida de géneros secundarios se declara en la página 5 del dashboard. | ✅ Cerrada |
| D5 | KPIs bajo filtro = Σ numerador ÷ COUNT registros. `% del catálogo` usa denominador fijo 31.991. G4 y G6 usan base fija = total del tipo | ✅ Cerrada |
| D6 | Se elimina "engagement promedio" (vale 50 por construcción). KPIs usados: `% alto engagement` y `% estrella` | ✅ Cerrada |
| D7 | G3 usa mediana del índice de engagement. Si Looker no permite MEDIAN, precalcular tabla auxiliar | ⏳ Abierta — verificar en Fase 4 |
| D8 | Formato del resumen ejecutivo: PDF o PPTX | ⏳ Abierta |
| D9 | Integrantes: los mismos de EP1 (Carlos Hidalgo, Gabriel Caroca, Bruno Miranda, Keiny Navarro) | ⏳ Confirmar |
| D10 | Gráficos de categorías en Looker: solo categorías reales, Top N en el título, sin barra "Otros", sin "No especificado" | ✅ Cerrada |
| D11 | "TV Movie" es formato, no género: se marca con `es_telefilm` (628 títulos) y se excluye de la dimensión género | ✅ Cerrada |

### 1.3 Pendientes (en orden)
1. **Fase 4:** construir el dashboard en Looker Studio (ver especificación en sección 6).
2. **Fase 5:** convertir `reports/informe_ejecutivo.md` a PDF con portada y formato final.
3. **Fase 6:** README, `requirements.txt`, correr todo desde cero, limpieza final.

---

## 2. CONTEXTO Y REQUISITOS

**Encargo:** grupal, caso StreamView Analytics (películas + series). Ponderación 9 %, semana 11.

**Retroalimentación de EP1 (comentarios que generaron decisiones):**
- IE5/IE6: gráfico de géneros mezclaba barra "Otros" y categorías que no eran géneros → D10, D11.
- IE9: cifras del informe no coincidían con el dashboard → regla: el mismo número debe salir igual en notebook, dashboard e informe.
- Enlace de Looker quedó como placeholder → en EP3 el enlace debe funcionar y verificarse en incógnito.

---

## 3. DATOS Y FUENTES

**Archivos de entrada:** `data/raw/movies.csv` (16.000 películas) y `data/raw/series.csv` (16.007 series).
**397 show_id solapados** → se deduplicó conservando el registro con más votos.
**Resultado limpio:** `data/processed/contenido_unificado.csv` — 31.991 títulos (16.000 películas, 15.991 series).

**Exclusiones declaradas:**
- 628 telefilms ("TV Movie") excluidos de la dimensión género (formato, no género — D11).
- 11.092 títulos sin director (68,5 % de las series).
- 1.088 sin género principal, 2.261 sin país principal.

**Calificaciones:** `vote_average_clean` = nulo si `vote_count = 0`. Umbral de confiabilidad: ≥ 10 votos (`calificacion_confiable`).

---

## 4. LIMPIEZA (`src/limpieza.py`)

**Estado:** 17/17 controles OK (verificado 06/10/2026).
**Controles clave:** sin duplicados, sin nulos en campos clave, `vote_average_clean` nulo solo si `vote_count = 0`, `es_telefilm` marcado correctamente.
**Salidas:** `contenido_unificado.csv`, `contenido_por_genero.csv`, `contenido_por_pais.csv`.

---

## 5. EDA Y GRÁFICOS (`notebooks/02_eda_visualizaciones.ipynb`)

**Estado:** 8 gráficos PNG generados, sin errores (verificado 06/10/2026). NB01 tiene todas las decisiones de integración justificadas.

**Cifras clave verificadas contra `cifras_clave_eda.csv`:**

| Gráfico | Cifra | Valor |
|---------|-------|-------|
| G1 | Calificación películas / series | 6,31 / 7,02 |
| G1 | % con ≥ 10 votos películas / series | 88,6 % / 38,4 % |
| G2 | Mediana popularidad películas / series | 10,9 / 36,2 |
| G3 | Correlación engagement–calidad (r) | 0,25 películas / 0,13 series |
| G4 | Drama % (todos los géneros) | 43,2 % películas / 49,1 % series |
| G4 | Drama % (solo género principal) | 22,9 % películas / 31,5 % series |
| G6 | EE. UU. % catálogo películas / series | 48,5 % / 20,0 % |
| G8 | ROI agregado top director | 8,80× |
| G8 | Cobertura financiera películas | 22,1 % (3.540 títulos) |
| G8 | ROI mediano global | 1,70× |

**Notas de gráficos:**
- G3 usa mediana del índice de engagement (no promedio) para evitar el sesgo de popularidad.
- G8 usa **todos los directores listados** (Renaud: 6 películas, ROI 8,8×). El dashboard usará `director_principal` (primer director, Renaud: 5 películas, ROI 9,0×). Diferencia declarada en el informe.
- Engagement por género es significativo solo dentro de cada tipo: en películas lideran Acción y Aventura (60,6) y Ciencia ficción y Fantasía (60,4); en series lideran Telenovela (70,9) y Talk show (60,4).

---

## 6. KPIs Y DASHBOARD (`src/kpis.py`, Looker Studio)

**Estado kpis.py:** 300/300 comparaciones OK (verificado 06/10/2026).

**Cifras globales del catálogo (sin filtros):**

| KPI | Valor |
|-----|-------|
| Títulos totales | 31.991 (16.000 películas / 15.991 series) |
| Calificación promedio | 6,63 |
| % con votos suficientes | 63,6 % |
| % alto engagement | 25,0 % |
| % estrella | 8,47 % |
| Cobertura financiera | 22,1 % (solo películas) |
| ROI agregado | 2,92× |
| ROI mediano | 1,70× |

**Fórmulas para Looker Studio:**
- Títulos: `COUNT_DISTINCT(content_id)`
- % del catálogo: `COUNT_DISTINCT(content_id) / 31991` (denominador fijo)
- % alto engagement: `SUM(alto_engagement) / COUNT_DISTINCT(content_id)`
- % estrella: `SUM(es_estrella) / COUNT_DISTINCT(content_id)`
- ROI agregado: `SUM(ingresos_roi) / SUM(presupuesto_roi)`

**Columnas de `looker_contenido.csv` (20 columnas, 31.991 filas):**
`content_id`, `title`, `tipo_contenido`, `es_pelicula`, `release_year`, `idioma`, `genero_principal`, `pais_principal`, `director_principal`, `popularity`, `indice_engagement`, `alto_engagement`, `es_estrella`, `vote_count`, `vote_average_clean`, `confiable_num`, `financiero_num`, `presupuesto_roi`, `ingresos_roi`, `roi`.

**Top 7 países por volumen (sin "No especificado"):**
EE. UU. (8.180), Japón (2.759), China (2.283), Corea del Sur (2.070), Reino Unido (1.712), Canadá (1.509), Francia (1.353).

**Estructura del dashboard (5 páginas):**
1. **Resumen y Tendencias:** dispersión Calidad vs Engagement por género + líneas de tiempo desglosadas por tipo.
2. **Película vs. Serie:** barras comparativas con cobertura de votos.
3. **Exploración de Categorías:** Top 7 géneros y países por volumen, sin "No especificado", sin "Otros".
4. **Desempeño Financiero:** tabla de directores (mínimo 5 películas), tarjetas ROI y cobertura.
5. **Metodología y Limitaciones:** decisiones D4, D10, D11 y ausencias declaradas.

**Limitaciones a declarar en página 5:**
- `genero_principal` subestima géneros transversales (Drama: 43,2 % real vs 22,9 % en dashboard).
- 75 % de películas y 57 % de series tienen 2 o más géneros.
- 1.088 sin género, 2.261 sin país, 11.092 sin director.
- 628 telefilms excluidos (formato, no género).
- Dataset muestreado artificialmente: 1.000 títulos por año y tipo (991 en series de 2025).
- Solo el 22,1 % de las películas tiene datos financieros; las series: 0 %.

---

## 7. INFORME (`reports/informe_ejecutivo.md`)

**Estado:** borrador completo del Capítulo 2 (Diseño de Dashboards Interactivo), verificado contra notebooks y CSV el 07/10/2026.

**Cifras del informe alineadas con EDA:** calificaciones, coberturas, correlaciones, porcentajes de género, ROI — todas verificadas el 07/10/2026.

**Pendiente:** convertir a PDF con portada y formato final de entrega.

---

## 8. ENTREGA Y CIERRE (Fase 6)

**Pendiente:**
1. Construir dashboard en Looker Studio y verificar en incógnito (3 cifras por gráfico contra `cifras_clave_eda.csv`).
2. Convertir informe a PDF con portada.
3. Completar `README.md` con pasos de ejecución, estructura y enlace de Looker.
4. Verificar `requirements.txt`.
5. Correr todo desde cero (`limpieza.py` → `kpis.py` → NB01 → NB02) desde carpeta limpia.
6. **Limpieza final:** eliminar `NOTAS.md` y pautas PDF antes de la entrega.

---

## 9. RIESGOS Y LECCIONES

1. El mismo número debe salir igual en notebook, dashboard e informe (fallo de EP1).
2. Los títulos de gráficos afirman hallazgos: verificar siempre contra los datos.
3. Métricas con valor fijo por construcción (engagement promedio global) no informan: no usarlas como KPI.
4. Todo gráfico de calidad debe mostrar la cobertura de votos.
5. Gráficos de categorías: solo categorías reales, Top N rotulado, sin "Otros" (IE5, IE6, IE9 de EP1).
6. El enlace de Looker debe funcionar y verificarse en incógnito antes de entregar.

---

## 10. REGISTRO DE CAMBIOS

- 07/10/2026 — Verificación EDA vs informe: 18/18 cifras clave coinciden entre notebooks y `reports/informe_ejecutivo.md`. NOTAS.md consolidado y resumido.
- 07/10/2026 — `kpis.py`: agrega `director_principal` (primer director, Russo unificado, limpieza de espacios y capitalización), 22 traducciones de países adicionales. CSV regenerado (20 columnas). Informe corregido: 9 imprecisiones técnicas y estadísticas.
- 06/10/2026 — D4 cerrada: dashboard usa `looker_contenido.csv` (Opción A). Verificación 17/17 y 300/300 OK. G3 labels corregidas. glosario.md finalizado.
- 05/10/2026 — Fases 0–3 construidas: estructura, limpieza, EDA G1–G8, KPIs.
