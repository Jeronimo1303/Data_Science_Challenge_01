# Respuestas Taller Práctico 1

**Dataset elegido:** C — Movilidad urbana
**Curso:** Fundamentos en Ciencia de Datos — Maestría en Ciencia de Datos y Analítica, EAFIT
**Integrantes:** Santiago Acevedo Urrego, Santiago Betancur, Jerónimo Acosta Acevedo

---

## 1. Parte 1 — Análisis estadístico general (30%)

### 1.1. Taxonomía

| Variable | Clasificación | Porque |
|---|---|---|
| sensor_id | Nominal | Consecutivo sin jerarquía |
| conteo_vehiculos | Discreta | Solo puede tomar valores positivos enteros |
| temperatura_c | Continua | Variable física que toma valores decimales |
| condicion_clima | Nominal | Es una descripción del clima en una palabra |
| ubicacion | Nominal | Nombre de la calle en palabras, sin jerarquía |

### 1.2. Medidas de tendencia central y dispersión

**Variable continua: temperatura_c**

a) Para la variable continua calcularía el promedio para tener claro la temperatura general que se manifiesta en la ciudad; dado que no hay valores muy extremos, esta medida me será más útil que la mediana.

b) La desviación estándar, para conocer qué tanto se alejan los datos reales de la media; dado que no hay valores muy extremos de temperatura (min 14, max 31), esta medida me dará la información que requiero.

c) En caso de que tengamos valores atípicos muy altos o muy bajos, ya que estos alteran la desviación estándar, a diferencia del rango intercuartílico, el cual únicamente refleja dónde se concentra el 50% central de los datos, excluyendo los extremos.

### 1.3. Cualitativo vs. cuantitativo

**Variable cualitativa: ubicación**

**Numérica:** Una tabla de frecuencias. Ayudará a conocer cuántas mediciones tenemos por cada ubicación y su proporción con respecto a las demás. Además, nos daría luces de si un sensor envía información más frecuentemente que otro o si se encuentra en falla.

**Gráfica:** Un gráfico de pastel. Apoyando el punto anterior, sería una forma visual de conocer la proporción de información (filas) obtenida de cada ubicación y ver qué tan proporcionales son la cantidad de medidas que tenemos de cada una, permitiendo conocer de manera inmediata cuál tiene mayor cantidad de información (mediciones).

**Decisión de negocio:** Estas herramientas estadísticas pueden utilizarse para evaluar la distribución de las mediciones y apoyar decisiones sobre la planificación de futuras campañas de monitoreo, dándonos información sobre en qué ubicaciones podríamos aumentar la cantidad de mediciones según la necesidad.

### 1.4. Forma de la distribución y atípicos

La variable escogida es temperatura (°C); esperaría que tuviera una distribución simétrica dado que se tomaron los datos en un periodo corto de tiempo y en un mismo espacio (la misma ciudad), por lo que la mayoría de valores debería encontrarse relativamente cerca del valor promedio.

Para diferenciar un valor atípico pero que este sea posible, tendría en cuenta el contexto de los datos: la ciudad donde se toma y más o menos la hora y fecha en la cual fue tomado. Es decir, un valor de 35° es atípico en una ciudad como Medellín, pero en una tarde muy calurosa esto es posible; sin embargo, ese mismo valor a las 11 de la noche es imposible, al igual que uno de más de 50° en cualquier momento del día.

### 1.5. Pregunta transversal

Dado que, a pesar de que la media sea exactamente del mismo valor, los datos pueden distribuirse de manera completamente impredecible: podría haber sesgos, valores extremos, etc.

Por ejemplo, en nuestro caso de negocio podemos tener dos ubicaciones (calles) con exactamente el mismo promedio, pero que en una el valor se mantenga cercano a la media durante todo el día (tráfico fluido, apto para ese flujo constante), mientras que en la otra haya picos de congestión y momentos donde pasan muy pocos vehículos. La primera podría requerir una gestión de tráfico constante bastante sencilla, mientras que la otra podría conllevar la expansión de la calle, la instalación de semáforos para apoyar la gestión del tráfico y hasta el envío de agentes de tránsito en momentos estratégicos del día.

---

## 2. Parte 2 — Análisis y transformación de datos con problemas (40%)

### 2.1. Diagnóstico de calidad

1. **Valores imposibles (fuera de rango):** usamos `df.describe()`, el cual genera estadísticas resumidas. Al observar las columnas numéricas como `conteo_vehiculos`, observamos un valor mínimo imposible de -5 y un máximo ilógico de 99999.
2. **Consistencia de categorías:** afecta a la columna `condicion_clima`. Se detecta con la función `df["columna"].unique()`, la cual crea una lista con los valores únicos de la columna, mostrando que hay palabras repetidas con diferentes combinaciones de mayúsculas y minúsculas.
3. **Errores de georreferenciación:** afectan a las columnas `lat` y `lon`. Se evidencian usando el método `df.describe()`, donde aparece una latitud con valor mínimo de -75.565970; al verificar en el DataFrame, resulta que las variables latitud y longitud están invertidas.
4. **Valores faltantes:** afectan a las columnas `conteo_vehiculos`, `temperatura_c` y `condicion_clima`. Usando el método `df.describe()` se evidencia que hay valores faltantes, porque el conteo de estas variables es inferior a la cantidad total de 1455 registros.
5. **Formatos de fecha/hora mixtos:** afectan a la columna `timestamp`. Se detectan usando el método `pd.to_datetime` para transformar la columna en fechas, indicando que en caso de errores los marque como vacíos; al imprimir esos errores se demuestra que hay valores tanto imposibles como formatos mixtos.

### 2.2. Fechas

Para convertir la columna a formato fecha y forzar los errores a valores nulos sin detener el proceso, la línea exacta utilizada en el notebook es:

```python
trabajo["timestamp"] = pd.to_datetime(trabajo["timestamp"], errors="coerce")
```

El timestamp (fecha y hora) actúa como un identificador o clave lógica. La justificación para eliminarlos y no imputarlos es que inventar fechas o coordenadas sin evidencia suficiente daña el dataset y no aporta al análisis.

Si nos basamos en la pregunta de negocio "¿En qué corredores y horarios se debe pilotear semaforización inteligente?", imputar una hora (por ejemplo, rellenando con la moda o el promedio) desplazaría los eventos de tráfico a horarios equivocados. Esto impediría ordenar correctamente los eventos en el tiempo y dificultaría el análisis de las franjas de mayor tráfico. A nivel de negocio, este error estadístico podría generar un falso positivo, haciendo que la organización invierta presupuesto y operación para pilotear semáforos inteligentes en un horario que realmente no genera mejoras suficientes.

### 2.3. Variable lógica / categórica

La variable que presenta inconsistencias de texto es `condicion_clima`. Al explorar previamente con `unique()`, se observó que contenía palabras repetidas pero con diferentes combinaciones (ej. "Soleado", "soleado", "SOLEADO") y sinónimos para el mismo clima.

Para solucionar esto, en el notebook se aplicó una transformación en dos fases. La primera es la estandarización de formato:

```python
.astype("string").str.strip().str.replace(r"\s+", " ", regex=True).str.title()
```

Esto elimina espacios iniciales/finales, normaliza espacios intermedios y capitaliza la primera letra. La segunda es el mapeo con diccionario: una vez estandarizado el texto, se utilizó el método `.replace()` con un diccionario para corregir los sinónimos:

```python
{"Lluvioso": "Lluvia", "Soleada": "Soleado", "Sol": "Soleado", "Nubes": "Nublado"}
```

### 2.4. Georreferenciación

Para garantizar que los sensores se encuentren dentro del área metropolitana de Medellín, se definió un rango donde la latitud debe estar entre 6.15 y 6.35, y la longitud entre -75.70 y -75.50. En el notebook usamos "corrección automática, pero condicionada a un patrón inequívoco". Antes de descartar los datos como fuera del rango, se verifica si las coordenadas están invertidas (es decir, si el valor absoluto de la latitud es mayor a 20 y el de la longitud es menor a 20).

```python
medellin_valida = trabajo["lat"].between(6.15, 6.35) & trabajo["lon"].between(-75.70, -75.50)
trabajo.loc[~medellin_valida, ["lat", "lon"]] = np.nan
```

Si se hace un swap de latitud y longitud sin una regla estricta, se podrían generar "falsos positivos" geográficos. Por eso, el código exige que los valores absolutos superen el umbral de 20 grados para confirmar que es un error humano de digitación, y luego pasa el filtro.

### 2.5. Imputación y valores imposibles

**Variable elegida:** `conteo_vehiculos` y `temperatura_c`.

**(a) Criterio para definir el rango válido:** se establece que los valores válidos para los vehículos deben estar en el rango de 0 a 5.000. La justificación de negocio es que este límite corresponde a un conteo por un intervalo de tiempo específico, no a un acumulado de todo el día (para la temperatura, el rango físico/lógico se definió entre -10 y 45 grados Celsius).

**(b) Estrategia de imputación o eliminación:** la estrategia consta de dos pasos. Primero, los valores imposibles que escapan del rango se convierten en datos nulos (`NaN`), es decir, se elimina la anomalía. Segundo, se aplica una imputación utilizando la mediana para rellenar esos vacíos.

**(c) Sesgo introducido si la estrategia es incorrecta:** como documentamos en el análisis, existían valores mínimos de -5 y máximos de 99.999 que son imposibles. Si la estrategia fuera a ignorarlos o a imputar con el promedio (la media), estos valores extremos introducirían un sesgo enorme, alterando las medidas para la toma de decisiones. Específicamente, jalarían la media a un valor distorsionado de 328.03, alejándose de la realidad del tráfico. Por el contrario, la decisión de usar la mediana es correcta porque esta métrica es más fuerte ante valores atípicos y sitúa el centro real de los datos.

### 2.6. Duplicados y llave de negocio

Para detectar los duplicados reales de nuestro problema, se utilizan las columnas `sensor_id` y `timestamp`. Se elige esta combinación porque define de forma única un evento en el mundo físico: es imposible que un mismo sensor registre dos momentos de tráfico distintos en el mismo segundo exacto.

**Diferencia entre duplicado exacto y duplicado de evento de negocio:**

- **Duplicado exacto:** se da cuando absolutamente todas las celdas de una fila son idénticas a las de otra. Suele ser un problema de bases de datos o errores al exportar/cargar la información.
- **Duplicado de evento de negocio:** ocurre cuando el evento base se registró varias veces (la llave `sensor_id` + `timestamp` se repite), pero los datos de las demás columnas varían ligeramente por ruido en la transmisión o errores de captura. Un `.drop_duplicates()` básico no detectaría este error porque las filas enteras no son 100% iguales.

Si no se filtran utilizando la llave de negocio, un mismo registro puede pesar varias veces en los análisis. Esto afecta los promedios de tráfico en ciertos horarios, lo que derivaría en una decisión errónea a la hora de priorizar la semaforización en la ciudad.

---

## 3. Parte 3 — Interpretación de resultados y decisión (30%)


**Evidencia — Dataset C: Movilidad urbana**

| Tipo de vía | N lecturas | Conteo prom. | Conteo máx. | Temp. prom. (°C) |
|---|---|---|---|---|
| Arteria | 480 | 22.5 | 54 | 23.3 |
| Local | 480 | 23.0 | 53 | 23.1 |
| Troncal | 480 | 23.2 | 53 | 22.9 |

Figura 3 — Izquierda: conteo promedio de vehículos por hora del día (dos picos claros, ~06:00–08:00 y ~16:00–18:00, con un valle profundo entre 10:00 y 14:00 y valores mínimos nocturnos). Derecha: conteo promedio por sensor, de mayor a menor: SEN04 (Troncal), SEN06 (Local), SEN01 (Troncal), SEN03 (Local), SEN02 (Arteria), SEN05 (Arteria).

### 3.C — Decisión de gestión de tráfico

**a) Picos de tráfico e hipótesis.** La gráfica por hora muestra un patrón bimodal: un pico matutino entre las 06:00 y las 08:00 y otro pico entre las 16:00 y las 18:00, ambos cercanos a 40 vehículos en promedio, separados por un valle de mitad de día (~15 vehículos entre las 10:00 y las 14:00) y un mínimo nocturno (~14 vehículos entre las 22:00 y las 04:00). La hipótesis más razonable es el fenómeno clásico de "hora pico" de una ciudad con actividad laboral y educativa concentrada en horario de oficina: el pico de la mañana corresponde al desplazamiento hacia el trabajo/estudio y el de la tarde al regreso a casa, mientras que el valle de medio día refleja la ausencia de desplazamientos masivos durante la jornada laboral o educativa.

**b) ¿El tipo de vía no es relevante?** No necesariamente. La tabla agregada (promedio de 24 horas) diluye el fenómeno: mezcla las horas pico (~40 vehículos) con las horas valle (~14 vehículos) y, como los tres tipos de vía comparten el mismo patrón horario bimodal, el promedio diario global termina siendo parecido entre ellos (diferencia menor a 1 vehículo). Sin embargo, la gráfica por sensor sí matiza esta conclusión, aunque no en el sentido de "Troncal > Local > Arteria": el sensor con mayor conteo promedio es SEN04 (Autopista Norte, Troncal, 23.27), seguido muy de cerca por SEN06 (Circular 4ta, Local, 23.25) y luego SEN01 (Av. Regional, Troncal, 23.12); los dos sensores con menor conteo son, sin ambigüedad, los dos de tipo Arteria: SEN02 (Av. 33, 22.69) y SEN05 (Av. Oriental, 22.34). Es decir, el patrón real es que las vías tipo Arteria consistentemente rezagan al resto, mientras que Troncal y Local están entremezcladas en la parte alta — la tabla por tipo de vía esconde esto porque no aísla las horas pico, que es justo donde se concentraría la intervención.

**c) Recomendación final.** Cruzando esta evidencia oficial con el análisis propio del equipo sobre nuestro archivo contaminado ya limpio (Tarea 5 del notebook, restringida a las horas pico 06:00, 16:00 y 18:00), iniciaríamos el piloto en **Calle 10** (Local) y **Autopista Norte** (Troncal): son los dos corredores con mayor conteo promedio y mayor probabilidad de tráfico "Alto" en nuestros propios datos durante esas horas (38.57 y 38.48 vehículos respectivamente, prácticamente empatados entre sí y ~0.55 vehículos por encima del tercer lugar). Autopista Norte tiene además el respaldo directo de esta evidencia oficial, al ser el sensor (SEN04) de mayor conteo del Cuadro 3/Figura 3; Calle 10, en cambio, no aparece en el top de esa evidencia oficial (ocupa el puesto 4 de 6, mientras que Circular 4ta es el #2 oficial pero cae al puesto 5 de 6 en nuestros datos propios). Dejamos esta divergencia como limitación explícita: es esperable que la reconstrucción del archivo contaminado (imputación, duplicados descartados) introduzca ruido frente al archivo limpio de referencia, así que recomendamos validar la elección de corredor con más semanas de datos antes de comprometer presupuesto. Como variable adicional monitorearíamos la condición climática (clima), ya que la lluvia puede reducir la velocidad de flujo vehicular y distorsionar la medición del impacto real de la semaforización si no se controla por ese factor.
