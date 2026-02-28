# 📊 Informe de Análisis Exploratorio de Datos (EDA)
## Dataset: Dating App Reviews Dataset (Kaggle)

> **Contexto del proyecto:** Dataset extraído de Kaggle que recoge reseñas de usuarios de aplicaciones de citas (Tinder, Bumble y Hinge), junto con metadatos como la valoración numérica, el número de thumbs up recibidos, la fecha y la app asociada. Su finalidad principal en el proyecto es el análisis de sentimiento y la evaluación de la experiencia de usuario. A diferencia del dataset sintético, este dataset ha requerido un proceso de limpieza y preprocesado antes de su uso analítico.

---

## 1. Visión General

El dataset original está compuesto por **681.994 registros** y **6 variables**:

| Variable | Tipo | Descripción |
|---|---|---|
| `Name` | object | Nombre del usuario que escribe la reseña |
| `Review` | object | Texto de la reseña |
| `Rating` | int64 | Valoración numérica (0–5) |
| `#ThumbsUp` | int64 | Número de "me gusta" recibidos por la reseña |
| `Date&Time` | object | Fecha y hora de publicación |
| `App` | object | Aplicación a la que hace referencia la reseña |

No se detectaron filas duplicadas. La memoria en bruto asciende a **36,4 MB**.

---

## 2. Calidad de los Datos

### Valores nulos

Se identificaron nulos en dos variables categóricas:

| Variable | Nulos | % Nulos | Clasificación |
|---|---|---|---|
| `Name` | 7 | 0,00% | 🟢 Bajo |
| `Review` | 1.385 | 0,20% | 🟢 Bajo |

El porcentaje de nulos es residual en ambos casos. Aun así, dado que el análisis principal del dataset gira en torno al texto de las reseñas, los registros sin `Review` carecen de utilidad analítica y han sido eliminados en la fase de limpieza.

### Observaciones sobre la variable `Rating`

La variable `Rating` presenta **6 valores únicos** (0, 1, 2, 3, 4, 5), cuando cabría esperar únicamente 5 (1–5). La presencia de valoraciones con valor 0 puede corresponder a registros con rating no registrado o a un error en la fuente original. Este aspecto debe tenerse en cuenta en el análisis de sentimiento.

### Cardinalidad

- `Name` y `Review` presentan una cardinalidad muy alta (554.761 y 518.393 valores únicos respectivamente), lo cual es esperable dado su naturaleza textual libre.
- `App` tiene únicamente **3 categorías**: Tinder, Bumble y Hinge.
- Tinder concentra la gran mayoría de las reseñas (**526.616 de 681.994**, el 77,2%).

---

## 3. Estadísticas Descriptivas

### Variables numéricas

| Variable | Media | Mediana | Std | Mín | Máx |
|---|---|---|---|---|---|
| `Rating` | 3,00 | 3,0 | 1,75 | 0 | 5 |
| `#ThumbsUp` | 1,87 | 0,0 | 24,45 | 0 | 5.507 |

La distribución de `Rating` es aproximadamente simétrica en torno al valor 3, con una desviación típica de 1,75 que indica polarización entre valoraciones muy bajas y muy altas, patrón habitual en reseñas de apps.

La variable `#ThumbsUp` presenta una mediana de 0 y una media de 1,87, con un máximo de 5.507. Esto indica que la gran mayoría de reseñas no reciben ningún voto positivo, mientras que un reducido grupo de reseñas acumula una cantidad muy elevada, lo que genera una distribución fuertemente sesgada a la derecha.

### Variable temporal

`Date&Time` se almacena como texto en el dataset original con formato `DD-MM-YYYY HH:MM`, lo que impide su uso analítico directo sin conversión previa. Esta transformación se aborda en la fase de limpieza.

---

## 4. Outliers

El EDA no reportó flags de outliers mediante el método IQR para las variables numéricas del dataset. Esto es coherente con la naturaleza de las variables: `Rating` está acotada entre 0 y 5, y `#ThumbsUp`, aunque con valores extremos puntuales, no supera los umbrales IQR de forma que el método los clasifique como problemáticos a nivel estructural.

---

## 5. Conclusiones del EDA

- El dataset presenta una **calidad estructural aceptable**, con nulos marginales y sin duplicados.
- La variable `Review` es la columna central del análisis y requiere un **filtrado de calidad textual** previo, dado que existen reseñas muy cortas o sin contenido semántico relevante.
- El **desequilibrio entre apps** (Tinder representa el 77% de las reseñas) debe considerarse en los análisis comparativos.
- La variable temporal requiere **conversión a tipo datetime** para habilitar el análisis de tendencias y estacionalidad.
- El **alto volumen del dataset** (681.994 registros) aconseja aplicar técnicas de muestreo estratificado para optimizar el coste computacional del análisis de sentimiento sin comprometer la representatividad.
- El dataset ha requerido un **proceso de limpieza y preprocesado** completo antes de su uso analítico, documentado en el informe de limpieza correspondiente.

