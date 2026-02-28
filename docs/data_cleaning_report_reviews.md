# 🧹 Informe de Limpieza y Preprocesado
## Dataset: Dating App Reviews Dataset (Kaggle)

> **Contexto:** Este informe documenta las decisiones de transformación aplicadas sobre el dataset original tras el EDA, justificando cada paso y su impacto en el dataset resultante. El dataset limpio se exporta a `data/processed/dating_app_reviews_clean.csv` y es el punto de entrada para las fases de análisis de sentimiento y visualización.

---

## Resumen Ejecutivo

| Métrica | Valor |
|---|---|
| Filas originales | 681.994 |
| Filas tras eliminación de nulos | 680.602 |
| Filas tras filtrado textual | 401.630 |
| Filas en muestra final (estratificada) | **50.000** |
| Columnas originales | 6 |
| Columnas finales | 13 |
| Nulos en dataset final | 0 |

---

## 1. Homogeneización de Nombres de Columnas

**Problema detectado:** Los nombres de columnas originales eran inconsistentes en formato: mezcla de CamelCase (`Name`, `Review`, `Rating`, `App`), uso de caracteres especiales (`#ThumbsUp`) y separadores no estándar (`Date&Time`).

**Transformación aplicada:** Normalización completa a `snake_case` mediante una función reutilizable que aplica las siguientes reglas:
- Eliminación de espacios extremos
- Conversión de CamelCase/PascalCase a snake_case
- Sustitución de caracteres no alfanuméricos por guión bajo
- Conversión a minúsculas
- Eliminación de guiones bajos duplicados y extremos

**Resultado:**

| Nombre original | Nombre normalizado |
|---|---|
| `Name` | `name` |
| `Review` | `review` |
| `Rating` | `rating` |
| `#ThumbsUp` | `thumbs_up` |
| `Date&Time` | `date_time` |
| `App` | `app` |

**Justificación:** La estandarización de nombres es una práctica fundamental para garantizar la consistencia en el acceso programático a las columnas, evitar errores por caracteres especiales y facilitar la integración con otras herramientas del pipeline de análisis.

---

## 2. Limpieza de Variables Categóricas

**Problema detectado:** Las variables categóricas de tipo texto podían presentar inconsistencias de formato: espacios al inicio o final de los valores, variaciones en capitalización que generarían categorías duplicadas equivalentes.

**Transformación aplicada:** Aplicación de `.str.strip().str.title()` sobre todas las variables de tipo `object`, eliminando espacios extremos y unificando la capitalización al formato Title Case.

**Justificación:** Esta normalización garantiza la consistencia de las categorías y evita la aparición de valores equivalentes con distinta representación (por ejemplo, `"tinder"` vs `"Tinder"`), lo que podría generar agrupaciones erróneas en el análisis posterior.

---

## 3. Tratamiento de Valores Nulos

**Problema detectado:** El EDA identificó nulos en dos variables categóricas:

| Variable | Nulos detectados |
|---|---|
| `name` | 7 |
| `review` | 1.385 |

**Transformación aplicada:** Eliminación de filas con valores nulos en columnas categóricas. No se detectaron nulos en variables numéricas.

**Impacto:**

| Métrica | Valor |
|---|---|
| Filas antes del tratamiento | 681.994 |
| Filas eliminadas (categóricas) | 1.392 |
| Filas eliminadas (numéricas) | 0 |
| Filas resultantes | 680.602 |
| Nulos restantes | 0 |

**Justificación:** Dado el elevado volumen del dataset (681.994 registros), la eliminación de 1.392 filas (0,20% del total) no compromete la representatividad del conjunto. Los registros sin `review` carecen de utilidad para el análisis de sentimiento, que es el objetivo principal del dataset, por lo que la imputación no sería viable ni apropiada en este contexto.

---

## 4. Filtrado de Calidad Textual en Reviews

**Problema detectado:** Existencia de reseñas con contenido textual insuficiente para el análisis de sentimiento: textos de una sola palabra, respuestas triviales o entradas incompletas que no aportan información semántica relevante.

**Transformación aplicada:** Filtrado de reviews que no superan umbrales mínimos de calidad textual:
- Mínimo de **30 caracteres**
- Mínimo de **5 palabras**

**Impacto:**

| Métrica | Valor |
|---|---|
| Filas antes del filtrado | 680.602 |
| Filas retenidas | 401.630 |
| Filas eliminadas | 278.972 (41,0%) |
| Nulos restantes en `review` | 0 |

**Justificación:** Este es el paso con mayor impacto en el volumen del dataset. La eliminación del 41% de las reseñas responde a que una proporción muy significativa del dataset original contenía entradas de escaso valor semántico (textos como "Good", "Nice", "Great" o similares de una o dos palabras), que introducirían ruido en el análisis de sentimiento y reducirían la fiabilidad de los modelos NLP. Los umbrales de 30 caracteres y 5 palabras se establecen como mínimos razonables para garantizar contenido informativo sin ser excesivamente restrictivos.

---

## 5. Procesamiento de la Variable Temporal

**Problema detectado:** La variable `date_time` se almacenaba como texto en formato `DD-MM-YYYY HH:MM`, impidiendo su uso directo en análisis temporales.

**Transformación aplicada:** Conversión a tipo `datetime` y extracción de variables derivadas:

| Variable creada | Descripción |
|---|---|
| `year` | Año |
| `month` | Mes (numérico) |
| `month_name` | Nombre del mes |
| `day` | Día del mes |
| `weekday` | Nombre del día de la semana |
| `hour` | Hora del día |
| `date` | Fecha sin hora |

**Impacto:** 0 nulos generados tras la conversión. El dataset pasa de 6 a 13 columnas.

**Justificación:** La descomposición de la variable temporal en componentes individuales facilita el análisis de patrones de uso y tendencias temporales (estacionalidad mensual, actividad por día de la semana, franjas horarias), sin necesidad de realizar extracciones en tiempo de análisis. La columna `date_time` original se conserva para mantener la trazabilidad.

---

## 6. Muestreo Estratificado

**Problema detectado:** El dataset, tras la limpieza textual, contiene 401.630 registros, un volumen que genera un coste computacional elevado para las fases de análisis de sentimiento (NLP) planificadas en el proyecto.

**Transformación aplicada:** Muestreo estratificado proporcional con `n = 50.000` filas, preservando la distribución conjunta de las variables `rating` y `app`, consideradas las dimensiones más relevantes para el análisis.

**Impacto:**

| Métrica | Valor |
|---|---|
| Filas antes del muestreo | 401.630 |
| Filas en la muestra final | 50.000 |
| Reducción | 87,5% |
| Semilla de reproducibilidad | 42 |

**Justificación:** El muestreo aleatorio simple podría infra o sobre-representar combinaciones concretas de app y rating (por ejemplo, reseñas de 1 estrella en Hinge, que son minoría en el dataset). El muestreo estratificado garantiza que la muestra final refleje la misma estructura proporcional que el dataset completo, manteniendo la validez analítica y la capacidad de generalización de los resultados. La reducción a 50.000 registros se alinea además con el tamaño del dataset sintético, facilitando comparaciones equilibradas entre ambas fuentes.

---

## 7. Exportación

El dataset limpio se exporta a:

```
data/processed/dating_app_reviews_clean.csv
```

**Estado final del dataset:**

| Característica | Valor |
|---|---|
| Filas | 50.000 |
| Columnas | 13 |
| Nulos | 0 |
| Duplicados | 0 |
| Formato | CSV (sin índice) |

