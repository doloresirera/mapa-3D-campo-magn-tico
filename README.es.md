[English](README.md) | [Español](README.es.md)

# Mapa 3D de intensidad de campo magnético

Visualización tridimensional de la intensidad de campo magnético medida
en distintos puntos de una habitación. A partir de mediciones puntuales
de la magnitud del campo, el código genera:

1. Un **scatter 3D** de las mediciones reales (sin interpolar).
2. Un **volumen 3D interpolado** de la intensidad.

Ambos se exportan como archivos HTML interactivos que se abren en el
navegador y permiten rotar, hacer zoom y consultar valores.

## Contexto

Caracterización del campo magnético ambiente en un laboratorio de
paleomagnetismo, para identificar zonas de mayor o menor intensidad de
cara al manejo de muestras geológicas sensibles.

## Datos de ejemplo

El repositorio incluye `datos_ejemplo.csv`, un conjunto **ficticio** de
441 mediciones generadas sobre una grilla regular (paso 0.25 m) con una
fuente magnética simulada. Sirve para probar el código sin datos reales.
Los HTML de demostración se generaron con este archivo.

## Formato de los datos

El script espera un CSV con una fila por medición y estas columnas:

| Columna | Significado                   | Unidad |
|---------|-------------------------------|--------|
| `px`    | Posición en el eje x          | metros |
| `py`    | Posición en el eje y          | metros |
| `pz`    | Posición en el eje z (altura) | metros |
| `B`     | Magnitud del campo magnético  | µT     |

Las posiciones se miden desde un origen fijo (una esquina de la
habitación); todos los puntos deben referirse al mismo origen y ejes.
Como solo se usa la magnitud del campo, la orientación del sensor en
cada medición no afecta el resultado.

## Uso

1. Instalar dependencias:

   pip install -r requirements.txt

2. Colocar el CSV en la misma carpeta que el notebook y ajustar el
   nombre en la línea de `pd.read_csv(...)`.
3. Ejecutar las celdas en orden.

Se generan dos HTML: uno con las mediciones reales y otro con el volumen
interpolado.

## Resolución de la grilla

El número de nodos de la grilla de salida (líneas de `np.linspace`) no es
arbitrario: como referencia, se calcula por eje como
**largo del eje ÷ paso de medición**.

**Advertencia sobre sobreinterpolación:** aumentar el número de nodos hace
que el volumen se vea más continuo y suave, pero por encima de la densidad
real de muestreo esa suavidad es interpolación, no dato medido. Los HTML de
demostración de este repositorio usan una grilla más densa de lo estrictamente
justificado, solo para que la visualización se vea limpia. Para análisis
reales, mantener el número de nodos adecuado.

Nota: el parámetro `surface_count` (capas internas del volumen) es distinto:
solo cambia cuántas isosuperficies se dibujan sobre un volumen ya calculado,
no agrega datos interpolados. Subirlo no compromete la validez, solo el aspecto.

## Método de interpolación

Interpolación **lineal por triangulación de Delaunay**
(`scipy.interpolate.griddata`, `method="linear"`) en el interior del casco
convexo de los puntos. Opcionalmente, las zonas de borde sin datos se
rellenan con el punto vecino más cercano (`method="nearest"`).

**Limitación:** el relleno de bordes es extrapolación, no interpolación de
datos medidos, y debe interpretarse con cautela. Para dejar esas zonas
vacías comentar las líneas del relleno `nearest`.

## Dependencias

- numpy
- pandas
- scipy
- plotly

## Licencia

MIT
