# Taller Práctico 01 — Del Dato Crudo a la Decisión
### Recolección, Estructura y Analítica Descriptiva Multi-Fuente
**Maestría en Ciencia de Datos y Analítica — Universidad EAFIT**
Curso: Fundamentos en Ciencia de Datos | Docente: Jorge Iván Padilla-Buriticá
Periodo académico 2026-2 | Bloques evaluados: 1 y 2 (Pacto Pedagógico)

**Trabajo en equipo | Fecha límite de entrega: domingo 26 de julio de 2026 (11:59 p.m.)**

---

## 1. Objetivo del taller

Migrar de la ejecución técnica al **criterio analítico**: usar `pandas` no como un fin, sino como
la herramienta que permite responder preguntas de negocio bajo datos imperfectos. Al finalizar,
cada equipo debe poder sostener, frente al docente, **por qué** tomó cada decisión de limpieza y
**qué impacto** tiene esa decisión sobre la conclusión final.

Este taller no busca que ustedes escriban mucho código. Busca que **argumenten** cada línea que
la IA generativa les ayude a escribir. La nota no premia la cantidad de celdas del notebook, premia
la calidad del razonamiento documentado en Markdown junto al código.

## 2. Competencias que se evalúan (ver Pacto Pedagógico)

- Adquisición de datos en al menos dos formatos (estructurado + semi-estructurado).
- Diagnóstico de calidad de datos bajo el principio **GIGO** (Completitud, Unicidad, Consistencia).
- Transformación y limpieza con `pandas`: fechas, categorías, geolocalización, valores atípicos.
- Analítica descriptiva cuantitativa, cualitativa y gráfica, aplicando el estadístico correcto
  según el tipo de dato (nominal, ordinal, discreto, continuo).
- Traducción de un hallazgo estadístico en una **decisión de negocio** explícita y defendible.

## 3. Formato de trabajo

- El taller se resuelve **en equipo**, hasta 3 integrantes (según Pacto Pedagógico). No se aceptan
  entregas individuales salvo autorización expresa del docente.
- **Fecha límite de entrega: domingo 26 de julio de 2026.** Después de esa fecha el repositorio
  se considera cerrado para efectos de calificación.
- Lenguaje: Python + `pandas` obligatorio.
- Entrega: repositorio de GitHub (ver sección 6).
- Uso de IA generativa: **permitido y esperado** para la escritura de código. Debe declararse
  explícitamente en el notebook (celda inicial) qué partes se apoyaron en IA y qué se validó
  manualmente. El criterio, la interpretación y la decisión final son responsabilidad del equipo.

## 4. Elija su conjunto de datos

Cada equipo elige **uno** de los tres conjuntos de datos. Todos comparten la misma estructura pedagógica:
un archivo tabular **limpio**, un archivo tabular **contaminado** (mismo esquema, con errores
deliberados) y una fuente **semi-estructurada en JSON** que debe integrarse al análisis.

| Dataset | Dominio | Pregunta de negocio ancla |
|---|---|---|
| **A — Retail** | Cadena de panaderías en Medellín (8 tiendas) | ¿Qué categoría y qué tienda requieren intervención de inventario/servicio? |
| **B — Salud pública** | Consultas por comuna de Medellín | ¿En qué comunas se debe reforzar la capacidad de atención de urgencias? |
| **C — Movilidad urbana** | Sensores IoT de tráfico y clima | ¿En qué corredores y horarios se debe pilotear semaforización inteligente? |

### Archivos por conjunto de datos

```
datasets/
├── datasetA_retail/
│   ├── retail_ventas_LIMPIO.csv
│   ├── retail_ventas_CONTAMINADO.csv
│   └── reseñas_clientes.json          (semi-estructurado)
├── datasetB_salud/
│   ├── salud_consultas_LIMPIO.csv
│   ├── salud_consultas_CONTAMINADO.csv
│   └── logs_app_sintomas.json         (semi-estructurado, anidado)
└── datasetC_movilidad/
    ├── movilidad_sensores_LIMPIO.csv
    ├── movilidad_sensores_CONTAMINADO.csv
    └── clima_api_log.json             (semi-estructurado, anidado)
```

> Los diccionarios de variables completos están en el Anexo A del **Taller Práctico 01**
> (`taller_practico/Taller_Practico_01.tex`), el documento de 3 partes que el equipo resuelve
> junto con el notebook y entrega en el mismo repositorio.

**Importante:** el archivo `_CONTAMINADO.csv` no es un dataset distinto — es el mismo universo
de datos capturado con errores reales de un proceso operativo (digitación, integración de
sistemas, fallas de sensor, formularios libres). Su tarea es reconstruir la versión confiable
**sin haber visto el archivo limpio primero como referencia de solución**: úsenlo únicamente al
final para verificar su criterio, no para copiar el resultado.

## 5. Tareas del taller

### Tarea 1 — Recolección e inventario de activos (Individual/Diagnóstico)
1. Cargue los tres archivos de su conjunto de datos (`LIMPIO`, `CONTAMINADO`, `.json`) con `pandas`.
2. Construya un inventario: para cada variable, indique tipo de dato (nominal / ordinal /
   discreto / continuo / fecha / geoespacial), formato de origen (CSV, JSON anidado) y si
   proviene de una fuente estructurada o semi-estructurada.
3. Para el JSON: identifique campos anidados o listas (ej. lista de síntomas, diccionarios de
   ubicación) y decida cómo aplanarlos (`pd.json_normalize` o equivalente) para poder cruzarlos
   con el archivo tabular.

**Pregunta de reflexión (obligatoria en Markdown):** ¿qué información se pierde o se distorsiona
al forzar una fuente no estructurada/semi-estructurada dentro de una tabla rectangular?

### Tarea 2 — Diagnóstico GIGO sobre el archivo contaminado
Sin corregir todavía, documenten en una tabla Markdown dentro del notebook:

| Problema detectado | Columna(s) afectada(s) | Método de detección en pandas | ¿Por qué es un riesgo para la decisión de negocio? |
|---|---|---|---|

Deben identificar **al menos 6 problemas distintos**, cubriendo como mínimo:
- Completitud (valores faltantes).
- Unicidad (duplicados exactos y duplicados de "evento de negocio").
- Consistencia de categorías (mayúsculas, sinónimos, espacios).
- Formatos de fecha/hora mixtos.
- Valores imposibles o fuera de rango (edades, conteos negativos, ratings fuera de escala).
- Errores de georreferenciación (coordenadas invertidas, en cero, o fuera del área de Medellín).

### Tarea 3 — Transformación y limpieza con pandas
Corrijan cada problema documentado en la Tarea 2. Por cada transformación, el notebook debe
tener: (a) el código, (b) una celda Markdown justificando **la decisión** (no solo la sintaxis).
Ejemplos de decisiones que deben justificar explícitamente:
- ¿Imputar con media, mediana, moda, un valor centinela, o eliminar la fila? ¿Por qué esa y no otra?
- ¿Cómo definieron el rango geográfico válido para Medellín y qué hicieron con las coordenadas
  fuera de rango: corregir automáticamente o descartar?
- ¿Qué llave de negocio usaron para detectar duplicados reales (no solo `.duplicated()` ingenuo)?

### Tarea 4 — Analítica descriptiva cuantitativa, cualitativa y gráfica
Sobre los datos ya limpios (los suyos, no el archivo `_LIMPIO.csv` de referencia):
1. **Cuantitativa:** medidas de tendencia central y dispersión para al menos 2 variables continuas
   y 1 discreta, con la medida correcta según su distribución (¿usarían media o mediana? ¿por qué?).
2. **Cualitativa:** tablas de frecuencia/proporción para al menos 2 variables categóricas,
   incluyendo el cruce (tabla de contingencia) entre una variable categórica y la variable
   ordinal o de resultado de su conjunto de datos.
3. **Gráfica:** mínimo 3 visualizaciones distintas (ej. barras, histograma/densidad, dispersión o
   mapa de puntos con `lat`/`lon`), cada una con una conclusión de una frase — no basta con
   mostrar el gráfico.

### Tarea 5 — De la estadística a la decisión
Cierre el notebook con una sección `## Decisión Recomendada` que responda:
1. ¿Cuál es la pregunta de negocio que este análisis realmente responde? (recuerden el ejercicio
   de la Sesión 1: "no es *cuánto*, sino *cuál es la probabilidad de que*...").
2. ¿Cuál es la recomendación concreta y accionable?
3. ¿Qué le costaría a la organización un Falso Positivo y un Falso Negativo en esta decisión?
4. ¿Qué limitación de los datos (algo que ni siquiera la limpieza pudo resolver) debe quedar
   explícita para quien tome la decisión?

## 6. Entregable y estructura del repositorio de GitHub

Cada equipo entrega **un repositorio de GitHub** (enlace + colaboradores agregados). No se
aceptan archivos sueltos por correo o Teams. Estructura obligatoria:

```
nombre-equipo-taller-practico-01/
├── README.md
├── data/
│   ├── raw/                  # archivos originales sin modificar (LIMPIO, CONTAMINADO, .json)
│   └── processed/            # su dataset ya limpio, exportado (ej. dataset_procesado.csv)
├── notebooks/
│   └── taller_practico_01_analisis.ipynb
├── src/                      # (opcional) funciones auxiliares reutilizables (.py)
├── results/
│   ├── figuras/               # imágenes exportadas de las gráficas clave
│   └── tabla_diagnostico_gigo.csv/.md
├── taller_practico/
│   └── Taller_Practico_01_respuestas.pdf   # respuestas a las 3 partes (ver sección 6.1)
└── docs/
    └── declaracion_uso_IA.md  # qué partes se apoyaron en IA generativa y cómo se validaron
```

- **`data/raw/`**: nunca se edita manualmente; es la evidencia de dónde partieron.
- **`data/processed/`**: el resultado de la Tarea 3, reproducible desde el notebook.
- **`notebooks/`**: un único notebook narrativo (código + Markdown intercalado); no se aceptan
  notebooks solo con código sin explicación.
- **`results/`**: lo que iría en una presentación ejecutiva — tablas y figuras finales.
- **`taller_practico/`**: las respuestas al documento de 3 partes (ver 6.1).
- **`docs/declaracion_uso_IA.md`**: obligatorio según el Pacto Pedagógico (uso ético y transparente
  de IA generativa).

Use la plantilla de `README.md` incluida en este material (`repo_template/README.md`) como punto
de partida; complétela, no la deje con los placeholders.

### 6.1 Dónde y cómo responder el Taller Práctico 01 (documento de 3 partes)

El docente entrega, junto con los datos, el documento `Taller_Practico_01.tex` / `.pdf`
(Parte 1, 2 y 3). El equipo debe responderlo y subirlo al repositorio de una de estas dos formas
(elijan una, no ambas):

1. **Editando el `.tex`** directamente en Overleaf o en su editor, agregando las respuestas debajo
   de cada pregunta, y subiendo el PDF compilado final a `taller_practico/Taller_Practico_01_respuestas.pdf`
   (además del `.tex` editado, para trazabilidad).
2. **Respondiendo en Markdown** dentro de una sección final del mismo notebook
   (`## Taller Práctico 01 — Respuestas`), replicando la numeración de las preguntas (1.1, 1.2, 2.1...
   3.A/B/C). En este caso no es obligatorio compilar el `.tex`, pero sí conservarlo en el repositorio
   como referencia del enunciado recibido.

En ambos casos, la Parte 2 puede incluir fragmentos cortos de código (1 a 5 líneas) siempre que
vayan acompañados de la justificación escrita solicitada.

## 7. Rúbrica de evaluación

| Criterio | Peso | Qué se evalúa |
|---|---|---|
| Diagnóstico GIGO | 20% | Cobertura y precisión de los problemas identificados |
| Transformación en pandas | 25% | Corrección técnica **y** justificación de cada decisión |
| Analítica descriptiva (cuanti/cuali/gráfica) | 25% | Estadístico correcto según tipo de dato; claridad de las visualizaciones |
| Decisión de negocio final | 20% | Argumentación, no solo el número; manejo de costo de error |
| Organización del repositorio y trazabilidad | 10% | Estructura, README, reproducibilidad, declaración de uso de IA |

> Esta rúbrica evalúa el **notebook** (Tareas 1 a 5). El documento **Taller Práctico 01**
> (`taller_practico/Taller_Practico_01.tex`) se califica de forma independiente con su propia
> ponderación interna (Parte 1: 30%, Parte 2: 40%, Parte 3: 30%, ver Anexo B de ese documento),
> y su nota se promedia con la de esta rúbrica según lo indique el docente en el cronograma.

## 8. Errores frecuentes que bajan la nota (aunque el código "funcione")

- Imputar con la media sin verificar si la variable es ordinal o tiene outliers extremos.
- Eliminar todas las filas con nulos sin evaluar cuánta información se pierde.
- "Corregir" coordenadas sin declarar la regla de validación usada.
- Presentar una gráfica sin una conclusión escrita al lado.
- Confundir "el modelo/código corrió sin error" con "la limpieza fue correcta".
- Copiar la recomendación de negocio del enunciado del conjunto de datos en vez de derivarla de sus propios
  resultados.
