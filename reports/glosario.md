# Glosario de KPIs — StreamView Analytics (EP3)

Fuente única: `data/processed/looker_contenido.csv` (generada por `src/kpis.py`). Todos los KPIs responden a los filtros del dashboard (tipo, años, idioma, género, país).

| KPI | Definición | Cálculo | Base / límites |
|---|---|---|---|
| Títulos | Cantidad de títulos en la selección | COUNT_DISTINCT(content_id) | Una fila por título |
| % del catálogo | Peso de la selección en el catálogo | Títulos / 31.991 | Base fija: total unificado |
| Calificación promedio | Nota promedio de la audiencia (0–10) | AVG(vote_average_clean) | Solo títulos con ≥ 1 voto; los 0 sin votos no son nota |
| % con votos suficientes | Títulos cuya nota es confiable | SUM(confiable_num) / títulos | Confiable = ≥ 10 votos |
| % de alto engagement | Títulos entre el 25 % más popular de su tipo | SUM(alto_engagement) / títulos | Alto engagement = percentil ≥ 75 dentro del tipo. En el catálogo completo vale 25 % por construcción; informa al filtrar por género, país o año |
| % títulos estrella (`pct_estrella`) | Títulos de alto engagement con alta calidad validada | SUM(es_estrella) / títulos | Cumple 3 condiciones: engagement percentil ≥ 75 dentro del tipo, nota limpia ≥ 7,0 y ≥ 10 votos (confiable). Supera la limitación de "% alto engagement" al identificar el contenido de excelencia real: 8,5 % en el total (8,1 % películas, 8,9 % series) |
| Popularidad promedio | Nivel de popularidad TMDb promedio | AVG(popularity) | Popularidad cruda de TMDb. Escalas distintas entre películas (promedio 20,4) y series (promedio 64,9); solo comparable dentro del mismo tipo |
| Densidad | Calidad percibida por unidad de popularidad | AVG(calificación) / AVG(popularidad) | Solo comparable dentro de un mismo tipo |
| ROI agregado | Retorno financiero del conjunto | Σ ingresos / Σ presupuesto | Solo películas con presupuesto e ingresos > 0 |
| ROI mediano | Retorno de la película típica | Mediana de ingresos/presupuesto | Solo películas con datos; robusto a atípicos |
| Cobertura financiera | % de películas con datos financieros | Películas con datos / películas | Las series no tienen datos financieros |

Reglas: los porcentajes se calculan como suma de numerador dividida por cuenta de registros, nunca como promedio de porcentajes. Calificaciones de EP1 incluían ceros sin votos; aquí se corrigen.
