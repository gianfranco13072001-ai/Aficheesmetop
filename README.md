# Trabajo final: agente de IA para analizar afiches políticos

Trabajo final de Metodología de Análisis de Opinión Pública (Met Op).

Un agente construido con Gemini describe afiches de campañas presidenciales argentinas (1983–2015), define sus propias variables y las codifica. Con esas variables, una regresión logística binaria mide qué rasgos distinguen a los afiches peronistas (1) de los no peronistas (0).

**Estado: en curso.** Están hechas la descarga, la descripción de los 141 afiches, la primera ronda de variables y su regresión. La segunda ronda de variables está propuesta por el agente y falta codificarla.

## Cómo funciona

1. `01_descarga.ipynb` baja los afiches del [Repositorio Electoral de la UBA](https://repositorioelectoral-uba.ar/afiches/) y arma `afiches.csv`, con una fila por imagen.
2. `afiches.ipynb`, celdas 0 a 4: conecta con Gemini y elige el modelo automáticamente (`gemini-3.1-flash-lite`).
3. Celdas 5 a 7: el agente mira cada afiche y guarda lo que se ve en `caracteristicas_afiches.json`.
4. Celdas 8 y 9: el agente lee las 141 descripciones, define las variables y codifica cada afiche. El resultado está en `variables_ronda1.json`.
5. Celda 10: regresión logística con los 141 afiches (`m2`).
6. Celdas 11 y 12: el agente recibe los resultados y propone una segunda ronda de variables.
7. Celda 13: prueba de la ronda 1 con afiches que el modelo no vio (`m3`).

## Archivos

| Archivo | Contenido |
| --- | --- |
| `01_descarga.ipynb` | Descarga de los afiches y armado de la tabla |
| `afiches.csv` | 141 filas: identificador, campaña, partido, fórmula, año, URL y ruta de la imagen |
| `afiches.ipynb` | El agente y la regresión |
| `caracteristicas_afiches.json` | Descripción de cada afiche hecha por el agente |
| `variables_ronda1.json` | Definiciones, fórmula y afiches codificados de la ronda 1 |

Las imágenes no están en el repositorio. Se generan en la carpeta `imagenes/` al ejecutar `01_descarga.ipynb`.

## Resultados de la ronda 1

Regresión con 141 afiches y las 5 variables que eligió el agente. Pseudo R² = 0,165; LLR p-value < 0,001.

| Variable | Coeficiente | p-valor | Odds ratio | IC 95 % |
| --- | --- | --- | --- | --- |
| Presencia de identidad nacional | 1,61 | < 0,001 | 5,0 | 2,3 a 10,8 |
| Referencia a la victoria | 1,61 | 0,012 | 5,0 | 1,4 a 17,5 |
| Tipografía manuscrita | 1,10 | 0,159 | 3,0 | 0,7 a 13,7 |
| Gesto de la V | 1,00 | 0,226 | 2,7 | 0,5 a 13,7 |
| Terminología obrera | 0,42 | 0,481 | 1,5 | 0,5 a 5,0 |

Prueba con afiches no vistos (25 % de las campañas, 38 afiches): AUC 0,78; accuracy 0,45; baseline 0,21. El modelo ordena bien los afiches, pero con el umbral de 0,5 reconoce solo 9 de los 30 peronistas.

## Limitaciones

- La muestra es chica: 141 imágenes de 38 campañas, y las de una misma campaña se parecen.
- La etiqueta peronista / no peronista la asignó el agente y no fue revisada afiche por afiche.
- Algunas variables están cerca de la etiqueta, como la palabra "victoria".
- La prueba usa una sola partición, con clases desparejas (30 y 8), así que sus números son inestables.
- El eje peronista / no peronista reemplaza al de izquierda / derecha de la consigna. Falta confirmarlo.

## Cómo ejecutarlo

1. Instalar las librerías: `google-genai`, `beautifulsoup4`, `pandas`, `pillow`, `statsmodels` y `scikit-learn`.
2. Ejecutar `01_descarga.ipynb` para bajar las imágenes.
3. Crear una API key gratuita en [Google AI Studio](https://aistudio.google.com).
4. Ejecutar `afiches.ipynb` en orden. La celda 1 pide la API key y no la guarda en el archivo.
