# 📊 Informe de Análisis Exploratorio de Datos (EDA)
## Dataset: Comportamiento de Usuarios en Apps de Citas (España)

> **Contexto del proyecto:** Dataset sintético autogenerado para un proyecto de visualización sobre el uso de aplicaciones de citas en España. El dataset ha sido construido con criterios de calidad desde su origen, lo que explica la ausencia de problemas estructurales en el EDA.

---

## 1. Visión General

El dataset está compuesto por **50.000 registros** y **35 variables** que recogen información demográfica, comportamental y de rendimiento de usuarios en aplicaciones de citas. No se detectaron filas duplicadas ni valores nulos en ninguna de las columnas, lo que garantiza una base sólida para el análisis.

Las variables se distribuyen en tres grandes bloques:

- **Variables demográficas y de contexto** (10 variables categóricas): género, edad, comunidad autónoma, app principal, nivel de estudios, nivel de ingresos, orientación sexual, tipo de perfil de usuario, y variables de comportamiento binario (envío de mensaje, ghosting, cita realizada).
- **Variables de actividad y uso** (12 variables enteras): minutos y días activos al mes, sesiones por día, swipes, likes enviados y recibidos, matches, conversaciones y respuestas.
- **Variables de rendimiento y conversión** (13 variables continuas): tasas de éxito, calidad del match, índice de reciprocidad, eficiencia del funnel, score de atractivo, entre otras.

---

## 2. Calidad de los Datos

### ✅ Aspectos positivos

- **0 valores nulos** en las 35 columnas.
- **0 filas duplicadas**.
- Cardinalidad coherente en todas las variables categóricas (entre 2 y 17 valores únicos).
- Variables numéricas continuas con rangos plausibles para el dominio de estudio.

### ⚠️ Observación: valores negativos en `score_atractivo`

La variable `score_atractivo` presenta un mínimo de **-4,23** y una media cercana a 0 (-0,006). Esto es **esperado y correcto**, ya que la variable está construida como una puntuación estandarizada (tipo z-score o escala normalizada centrada en 0), donde valores negativos indican puntuaciones por debajo de la media y valores positivos por encima. No supone un problema de calidad.

---

## 3. Estadísticas Descriptivas Destacadas

### Variables demográficas

- **Género:** Predominancia masculina (73,3% hombres frente a 26,7% mujeres), lo que puede reflejar la distribución real de usuarios activos en apps de citas.
- **Edad:** Media de 29,3 años (rango 18–64), con el grueso de la población entre 24 y 34 años. Distribución joven, coherente con el perfil típico de usuario de estas plataformas.
- **Comunidad autónoma:** Andalucía es la CCAA con mayor representación (9.026 usuarios), seguida de otras comunidades con alta densidad de población.
- **App principal:** Tinder lidera con 15.336 usuarios, seguida de otras plataformas como Badoo, Hinge, Bumble y Grindr.
- **Orientación sexual:** El 71,6% se identifica como heterosexual, seguido de Gay, Bi y LGBTQ+.

### Variables de actividad

- Los usuarios pasan de media **218,9 minutos al mes** en la app (≈ 3,6 horas), con alta variabilidad (desviación típica de 219,5 min), lo que anticipa la presencia de perfiles muy dispares en intensidad de uso.
- La **mediana de matches al mes es 0**, con una media de 1,76, lo que indica que la mayoría de usuarios obtiene pocos o ningún match: el éxito está muy concentrado en un subconjunto de usuarios.
- De forma similar, **conversaciones enviadas** y **respuestas recibidas** tienen mediana 0, lo que confirma que una parte importante de la base de usuarios tiene una participación pasiva o baja.

### Variables de conversión

- La **tasa de conversión a cita** (`tasa_conversion_cita`) tiene una media de apenas el 1,7%, reflejando el estrecho embudo que existe desde el match hasta el encuentro real.
- El **score de atractivo** sigue una distribución aproximadamente normal (media ≈ 0, desv. típica ≈ 1), lo cual es consistente con su naturaleza de puntuación normalizada.

---

## 4. Análisis de Outliers

Se han detectado outliers mediante el método IQR (1.5x) en la mayoría de variables numéricas de actividad y comportamiento. Las tasas de outliers más elevadas se observan en:

| Variable | % Outliers |
|---|---|
| `indice_reciprocidad` | 18,30% |
| `respuestas_recibidas_mes` | 14,01% |
| `conversaciones_enviadas_mes` | 14,41% |
| `matches_por_hora` | 14,03% |
| `calidad_match` / `tasa_exito_match` | ~13,8–13,9% |
| `minutos_mes` / `horas_mes` | 12,53% |

### Decisión de tratamiento: **no se tratan los outliers**

Estos valores extremos son **plausibles y esperables** dado el dominio de estudio. El comportamiento de los usuarios en apps de citas es inherentemente heterogéneo: existe un segmento de usuarios altamente activos que generan una actividad muy superior a la media (muchos swipes, muchas conversaciones, alta reciprocidad), frente a una mayoría de usuarios con actividad baja o nula. Eliminar o transformar estos valores distorsionaría precisamente los patrones más interesantes del análisis, como la identificación de perfiles de usuario intensivos. Por ello, se opta por conservarlos y trabajar con la distribución real.

---

## 5. Perfiles de Usuario

El dataset incluye una variable categórica `perfil_usuario` con cuatro tipologías que permiten segmentar el comportamiento:

| Perfil | N | Descripción |
|---|---|---|
| **Selectivo** | 24.472 (48,9%) | Usuarios con baja actividad general pero criterios de selección elevados. Pocos swipes, pocos likes enviados. |
| **Pasivo** | — | Usuarios que apenas interactúan, con mínimas conversaciones y cero citas. |
| **Conversador** | — | Usuarios que priorizan el intercambio de mensajes sobre la conversión a cita. Alta tasa de respuesta. |
| **Eficiente** | — | Perfiles con alta tasa de conversión relativa: pocos matches pero alta conversión a cita. |

Los perfiles **Conversador** y **Eficiente** representan los usuarios más intensivos en su actividad dentro de la app, aunque con estrategias diferenciadas: el primero maximiza la interacción en plataforma, mientras que el segundo optimiza la conversión fuera de ella.

---

## 6. Conclusiones del EDA

- El dataset presenta una **calidad estructural óptima**, sin necesidad de imputación ni limpieza de nulos o duplicados.
- La distribución de variables refleja fielmente la **heterogeneidad real** del comportamiento de los usuarios en apps de citas: la mayoría son poco activos, mientras que una minoría concentra la mayor parte de la actividad y los resultados.
- Los outliers identificados son **consistentes con el dominio** y se conservan para no sesgar el análisis ni las visualizaciones posteriores.
- Las variables de conversión y rendimiento revelan un **funnel muy estrecho**: de los swipes a los matches, de los matches a las conversaciones, y de las conversaciones a la cita real.
- El dataset está listo para avanzar hacia la fase de **visualización y análisis segmentado** por perfil, género, orientación sexual, app y comunidad autónoma.

---

*EDA realizado como parte del proyecto de visualización de datos sobre comportamiento en apps de citas en España.*
