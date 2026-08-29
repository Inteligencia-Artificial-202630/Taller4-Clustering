# Taller 04 — Clustering con K-Means sobre datos sintéticos 2D

Implementación de un pipeline completo de **K-Means** sobre un dataset sintético en dos dimensiones, incluyendo generación de datos, remoción de outliers y selección del número óptimo de clusters mediante tres criterios distintos: **método del codo**, **coeficiente de Silhouette** y **comparación contra ground truth (ARI / NMI)**.

## ¿Qué es esto y cómo funciona?

**K-Means** es un algoritmo de *clustering* (agrupamiento no supervisado): dado un conjunto de puntos sin etiquetas, busca dividirlos en `K` grupos ("clusters") de forma que los puntos dentro de un mismo grupo sean lo más parecidos posible entre sí, y lo más distintos posible de los puntos de otros grupos.

Funciona de manera iterativa:

1. Se eligen `K` centroides iniciales (al azar o con una heurística como `k-means++`).
2. **Asignación**: cada punto se asigna al centroide más cercano.
3. **Actualización**: cada centroide se recalcula como el promedio de los puntos que le fueron asignados.
4. Se repiten los pasos 2 y 3 hasta que los centroides dejan de moverse (convergencia).

El problema es que K-Means necesita que le digamos `K` de antemano, y en la vida real casi nunca sabemos cuántos grupos "reales" hay en los datos. Por eso este taller no se queda solo en aplicar el algoritmo, sino que explora **cómo elegir el mejor valor de K** usando tres estrategias distintas:

- **Método del codo**: mide qué tan compactos quedan los clusters (inercia) para cada K. A medida que K crece, la inercia siempre baja, pero llega un punto donde bajar más ya no aporta mucho — ese punto de quiebre ("codo") es un buen candidato a K óptimo.
- **Coeficiente de Silhouette**: para cada punto compara qué tan cerca está de su propio cluster frente a qué tan lejos está del cluster más cercano. Se promedia sobre todos los puntos y se busca el K que maximiza este valor.
- **Comparación contra ground truth (ARI / NMI)**: como en este taller los datos son sintéticos, sí conocemos la etiqueta real de cada punto. Este método compara directamente el clustering obtenido contra esa verdad conocida, algo que solo es posible cuando se generan datos artificialmente (en un problema real no existiría este "as bajo la manga").

Además, antes de aplicar K-Means, el notebook remueve los **outliers** (puntos que no pertenecen a ningún grupo real) usando `LocalOutlierFactor`, un método no supervisado que detecta puntos en zonas de baja densidad respecto a sus vecinos — igual que tendría que hacerse en un escenario real donde no se conocen las etiquetas de antemano.

Lo interesante del resultado es que el codo y Silhouette (que solo miran la geometría de los puntos) tienden a "fusionar" los dos grupos que se superponen parcialmente, mientras que el método de ground truth sí logra distinguirlos — mostrando una limitación real de los métodos de validación interna frente a datos con clusters solapados.

## Descripción del dataset

Se genera un conjunto sintético con las siguientes características, tal como exige el enunciado:

- **6 grupos** generados con distribuciones **gaussianas isotrópicas** (misma varianza en ambos ejes).
- **Mismo número de puntos** por grupo, pero **diferentes desviaciones estándar** (densidades distintas).
- Todos los valores contenidos en el rango **[-10, 10]** en ambos ejes.
- **Dos grupos parcialmente superpuestos**, ubicados deliberadamente cerca entre sí.
- **Outliers** adicionales generados con distribución **uniforme** en [-10, 10].

<img width="688" height="690" alt="image" src="https://github.com/user-attachments/assets/aa9cbc74-eefc-425c-87fc-f369300264dd" />


## Pipeline implementado

1. **Generación de datos** sintéticos (6 grupos + outliers) con verificación de rango.
2. **Remoción de outliers** de forma no supervisada usando `LocalOutlierFactor`.
3. **Exploración de K** entre 2 y 12 clusters, entrenando un modelo K-Means por cada valor.
4. **Selección del K óptimo** mediante tres métodos independientes:
   - Método del codo (heurística de distancia máxima a la recta, tipo *kneedle*).
   - Coeficiente de Silhouette (maximización del promedio).
   - Comparación contra las etiquetas verdaderas usando **Adjusted Rand Index (ARI)** y **Normalized Mutual Information (NMI)**.
5. **Visualización comparativa** de los resultados de los tres criterios.

## Resultado principal

El codo y Silhouette tienden a converger en **K = 5**, mientras que el criterio basado en ground truth identifica correctamente los **6 grupos reales**. Esto no es un error: ilustra una limitación conocida de los métodos internos de validación cuando existen clusters parcialmente superpuestos, que tienden a "fusionarse" en la geometría observada aunque provengan de distribuciones distintas.

| Método del codo | Silhouette |
|---|---|
| <img width="635" height="470" alt="Método del codo" src="https://github.com/user-attachments/assets/92a8907f-22fa-4c41-87e7-8dbc6c763c54" /> | <img width="622" height="470" alt="Silhouette" src="https://github.com/user-attachments/assets/dc843033-a05f-44c8-9981-81416dfd8a40" /> |

<img width="1790" height="590" alt="Comparación final de los tres criterios" src="https://github.com/user-attachments/assets/62f05c2c-84b6-485e-834c-a6090f85a558" />

## Requisitos

```
numpy
pandas
matplotlib
scikit-learn
```

Instalación rápida:

```bash
pip install numpy pandas matplotlib scikit-learn
```

## Uso

Abrir y ejecutar `main.ipynb` con Jupyter Notebook / JupyterLab:

```bash
jupyter notebook main.ipynb
```

## Autoras

Natalia Carpintero, Paula Núñez e Isabella Arrieta.
