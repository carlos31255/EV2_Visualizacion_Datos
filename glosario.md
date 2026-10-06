# Glosario de KPIs — StreamView Analytics (EP3)

Fuentes (generadas por `src/kpis.py`):
- `data/processed/looker_contenido.csv`: una fila por título. Alimenta los KPIs de la portada.
- `data/processed/looker_genero.csv`: una fila por título y género. Alimenta todos los gráficos de género.

Todos los KPIs responden a los filtros del dashboard. Los porcentajes se calculan como suma del numerador dividida por la cuenta de registros, nunca como promedio de porcentajes.

| KPI | Definición | Cálculo en Looker | Base / límites |
|---|---|---|---|
| Títulos | Cantidad de títulos en la selección | COUNT_DISTINCT(content_id) | Una fila por título |
| % del catálogo | Peso de la selección en el catálogo | Títulos / 31.991 | Total unificado |
| Calificación promedio | Nota promedio de la audiencia (0–10) | AVG(vote_average_clean) | Solo títulos con ≥ 1 voto; los 0 sin votos no son nota |
| % con votos suficientes | Títulos cuya nota es confiable | SUM(confiable_num) / COUNT(content_id) | Confiable = ≥ 10 votos |
| % de alto engagement | Títulos entre el 25 % más popular de su tipo | SUM(alto_engagement) / COUNT(content_id) | Alto = percentil ≥ 75 dentro del tipo; vale 25 % por construcción en el catálogo completo, informa al filtrar |
| % de títulos estrella | Títulos estrella (engagement y calificación) | SUM(es_estrella) / COUNT(content_id) | Estrella = percentil ≥ 75, nota ≥ 7, ≥ 10 votos |
| ROI agregado | Retorno financiero del conjunto | SUM(ingresos_roi) / SUM(presupuesto_roi) | Solo películas con presupuesto e ingresos > 0 |
| ROI mediano | Retorno de la película típica | Mediana de ingresos/presupuesto | Solo películas con datos; robusto a atípicos; precalculado en `kpis_resumen.csv` |
| Cobertura financiera | % de películas con datos financieros | SUM(financiero_num) / SUM(es_pelicula) | Las series no tienen datos financieros |

## Diferencia entre fuentes (limitación declarada)
Los KPIs de la portada usan `genero_principal` (el primer género de cada título), así que un título cuenta solo en un género. Los gráficos de género usan la tabla larga y cuentan cada género del título (Drama: 43,2 % de las películas con la tabla larga y 22,9 % con `genero_principal`). Por eso las cifras por género de los gráficos coinciden con el notebook y el informe, y las de la portada pueden diferir si se filtra por género.

## Cambios respecto a EP1
Las calificaciones de EP1 incluían ceros que en realidad eran títulos sin votos; aquí se excluyen.