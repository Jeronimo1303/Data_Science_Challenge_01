# Taller Práctico 01 — Movilidad
 
**Curso:** Fundamentos en Ciencia de Datos — Maestría en Ciencia de Datos y Analítica, EAFIT
**Conjunto de datos elegido:** C - Movilidad
**Fecha límite de entrega:** domingo 26 de julio de 2026
 
**Integrantes del equipo:**
 
| Nombre completo | Cédula |
| --- | --- |
| Santiago Acevedo Urrego | 1000191527 |
| Santiago Betancur | 1014657607 |
| Jeronimo Acosta Acevedo | 1000410386 |
 
---
 
## Descripción
 
Este proyecto realiza un análisis exploratorio y de calidad de datos sobre un conjunto de
movilidad urbana compuesto por tres fuentes: un archivo CSV de referencia (`LIMPIO`), un archivo
CSV con problemas de calidad (`CONTAMINADO`) y un archivo `JSON` semi-estructurado con datos
climáticos por sensor. El trabajo cubre todo el flujo de un proyecto de datos: inventario de
activos, diagnóstico de errores (GIGO), limpieza y transformación, analítica descriptiva
(cuantitativa, cualitativa y gráfica), y una recomendación de negocio fundamentada en la
evidencia encontrada.
 
## Estructura del repositorio
 
```
.
├── data/
│   ├── raw/            # Archivos originales (LIMPIO, CONTAMINADO, JSON de clima)
│   └── processed/       # Dataset final limpio y tablas de resumen exportadas
├── docs/                # Documentación adicional del taller
├── notebooks/           # Notebook principal de análisis (.ipynb)
├── results/
│   └── Figuras/          # Gráficas exportadas en alta resolución (.png)
├── taller_practico/     # Sección practica del taller
├── .gitignore
└── README.md
```
 
## Contenido del notebook
 
El notebook `taller_practico_01_analisis.ipynb` está organizado en las siguientes tareas:
 
1. **Recolección e inventario de activos** — carga de los tres archivos con `pandas`,
   inventario de variables por tipo de dato y fuente, y aplanado del JSON con `pd.json_normalize`.
2. **Diagnóstico GIGO** — identificación de valores imposibles, categorías inconsistentes,
   errores de georreferenciación, valores faltantes, formatos de fecha mixtos y duplicados en
   el archivo contaminado.
3. **Transformación y limpieza con pandas** — corrección de cada problema detectado, con
   justificación explícita de cada decisión (imputación, descarte, corrección de coordenadas).
4. **Analítica descriptiva** — medidas de tendencia central y dispersión, tablas de
   frecuencia/proporción, tabla de contingencia y tres visualizaciones (barras, histograma y
   dispersión geoespacial).
5. **Decisión recomendada** — traducción de los hallazgos estadísticos en una recomendación de
   negocio, con análisis del costo de falsos positivos/negativos y limitaciones de los datos.
## Datos de entrada esperados
 
Colocar en `data/raw/`:
 
- `movilidad_sensores_LIMPIO.csv`
- `movilidad_sensores_CONTAMINADO.csv`
- `clima_api_log.json`
## Cómo ejecutar
 
1. Instalar dependencias:
```bash
   pip install pandas numpy matplotlib tabulate
```
2. Abrir `notebooks/taller_practico_01_analisis.ipynb` y ejecutar las celdas en orden.
3. Al final del notebook, la sección **"Exportación de resultados"** genera automáticamente:
   - `data/processed/movilidad_procesado.csv` — dataset final limpio (`df_trabajo`).
   - `results/diagnostico_gigo.md` — tabla de diagnóstico GIGO en formato Markdown.
   - `results/Figuras/` — panel combinado y versiones individuales de las tres gráficas.

## Uso de herramientas de IA

La declaración de uso de IA puede ser encontrada en:
`docs/declaracion_uso_IA.md`
