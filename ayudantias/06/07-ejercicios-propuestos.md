# Ejercicios propuestos

[Inicio](README.md) · [Ayuda memoria](06-ayuda-memoria.md) · [Pauta completa](09-pauta.md)

Intenta cada ejercicio en papel antes de desplegar la pista. En los ejercicios de código, escribe o explica las líneas que faltan y predice qué se imprimiría. Conserva tus pasos para comparar con la pauta.

## Preparación y PCA

[Volver a la guía](01-preparacion-y-pca.md)

### P01

**Selección múltiple.** Para predecir una respuesta continua, dos columnas no constantes tienen Pearson $r=-0{,}9$ y $r=0{,}4$, respectivamente. Si conservas una por fuerza de asociación lineal, ¿qué eliges?

- A. La primera.
- B. La segunda.
- C. Las dos por igual.
- D. Ninguna, porque para Pearson importa que el signo sea positivo.

<details>
<summary>Pista</summary>

Piensa en la distancia de cada coeficiente al cero.

</details>

[Ver solución de P01](09-pauta.md#p01)

### P02

**Unir pares.** Relaciona **RFE**, **L1**, **Isomap** y **LLE** con estas descripciones.

1. Puede dejar pesos exactamente en cero.
2. Reconstruye cada punto a partir de vecinos locales.
3. Reajusta el modelo y elimina atributos de forma recursiva.
4. Usa caminos en un grafo de vecinos para aproximar distancias sobre una superficie.

<details>
<summary>Pista</summary>

Dos son formas de decidir sobre atributos. Los otros dos construyen representaciones a partir de vecinos.

</details>

[Ver solución de P02](09-pauta.md#p02)

### P03

**Desarrollo.** En una inspección, la máquina A registró 12 piezas correctas y 4 defectuosas. La B registró 4 correctas y 12 defectuosas. Construye la tabla de esperados bajo independencia, calcula $\chi^2$ y toma una decisión usando el valor crítico $3{,}84$ con un grado de libertad y nivel $0{,}05$.

<details>
<summary>Pista</summary>

Los totales de ambas filas y columnas son iguales. Calcula primero un esperado y revisa cuáles otros coinciden.

</details>

[Ver solución de P03](09-pauta.md#p03)

### P04

**Completar código.** Estandariza, conserva dos componentes de PCA y entrena logística. Completa las etapas y las llamadas para entrenar y predecir en la partición correcta.

```python
from sklearn.datasets import load_iris
from sklearn.decomposition import PCA
from sklearn.linear_model import LogisticRegression
from sklearn.model_selection import train_test_split
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import StandardScaler

X, y = load_iris(return_X_y=True)
X_tr, X_te, y_tr, y_te = train_test_split(
    X, y, test_size=0.25, stratify=y, random_state=42
)
modelo = Pipeline([
    ("escala", ...),
    ("pca", ...),
    ("logistica", LogisticRegression(max_iter=1000)),
])
modelo.fit(...)
pred = modelo.predict(...)
print(pred.shape)
```

¿Cuántas columnas entran en logística? ¿Qué forma tiene `pred`? Para el tamaño de TEST, scikit-learn redondea hacia arriba si $0{,}25n$ no es entero.

<details>
<summary>Pista</summary>

Iris tiene 150 filas. El número de componentes cambia las columnas. `predict` devuelve una etiqueta por fila de prueba.

</details>

[Ver solución de P04](09-pauta.md#p04)

## Modelos lineales

[Volver a la guía](02-modelos-lineales.md)

### P05

**Selección múltiple.** Dos modelos tienen residuos A: $(0,0,6)$ y B: $(2,2,2)$. ¿Cuál es correcta?

- A. Tienen el mismo MAE y el RMSE de A es mayor.
- B. A tiene MAE menor y ambos tienen el mismo RMSE.
- C. B tiene MAE mayor y RMSE mayor.
- D. Tienen los mismos MAE y RMSE.

<details>
<summary>Pista</summary>

Compara primero las sumas de magnitudes y luego las sumas de cuadrados.

</details>

[Ver solución de P05](09-pauta.md#p05)

### P06

**Completar oraciones.** Usa **L1**, **L2**, **GaussianNB** y **BayesianRidge** una vez cada uno.

1. La penalización ______ suma cuadrados de los pesos.
2. La penalización ______ puede usarse para seleccionar atributos si deja algunos pesos exactamente en cero.
3. ______ representa los atributos con gaussianas por clase y supone independencia condicional.
4. ______ puede calcular una media y una desviación estándar predictivas en regresión.

<details>
<summary>Pista</summary>

Primero distingue los dos modelos por su tipo de salida. Luego recuerda la forma de las penalizaciones.

</details>

[Ver solución de P06](09-pauta.md#p06)

### P07

**Desarrollo.** Con $(x,y)=(0,1),(2,5)$, inicio $(b,w)=(0,1)$ y tasa $\alpha=0{,}2$, da un paso de gradiente usando **$J=\mathrm{MSE}$**. Da las predicciones iniciales, ambas derivadas, los parámetros nuevos y el MSE nuevo.

<details>
<summary>Pista</summary>

Si el costo es MSE, la derivada de cada cuadrado aporta un 2 y no hay un $1/2$ que lo cancele.

</details>

[Ver solución de P07](09-pauta.md#p07)

### P08

**Completar código.** Crea una matriz de diseño con una columna de unos **primero** y la de `x` después. Usa mínimos cuadrados para obtener el intercepto y la pendiente. Predice para $x=3$ e indica qué imprime.

```python
import numpy as np

x = np.array([0, 1, 2], dtype=float)
y = np.array([1, 3, 5], dtype=float)

X = np.column_stack((..., ...))
b, w = np.linalg.lstsq(..., ..., rcond=None)[0]
print(np.round([b, w, ...], 4))
```

<details>
<summary>Pista</summary>

En una fila de la matriz quieres escribir $(1,x_i)$. El vector que devuelve el ajuste sigue el orden de esas dos columnas.

</details>

[Ver solución de P08](09-pauta.md#p08)

## SVM

[Volver a la guía](03-svm.md)

### P09

**Selección múltiple.** Un clasificador debe separar puntos que están en el centro de otros que forman un anillo. ¿Qué alternativa tiene sentido?

- A. Ajustar una sola recta con los atributos originales y cambiar su umbral hasta que sea circular.
- B. Probar una SVM con RBF y elegir `C` y `gamma` con validación.
- C. Interpretar cualquier puntaje positivo de SVM como probabilidad de al menos 50 %.
- D. Aumentar `C` hasta que la frontera de una SVM lineal sea curva.

<details>
<summary>Pista</summary>

Cambiar el umbral de una función lineal desplaza la frontera en paralelo.

</details>

[Ver solución de P09](09-pauta.md#p09)

### P10

**Completar oraciones.** Si el margen firmado $m=yf(x)$ es negativo, la fila está ______. Si $0<m<1$, la clase es correcta y la fila queda ______ la franja. Si $m=1$, la pérdida hinge es ______.

<details>
<summary>Pista</summary>

Los bordes canónicos están donde el puntaje vale $+1$ o $-1$.

</details>

[Ver solución de P10](09-pauta.md#p10)

### P11

**Desarrollo.** Una SVM tiene $w=(0,2)$ y $b=-2$. Los puntos etiquetados son $(1,0)\to-1$, $(0,1{,}25)\to+1$ y $(1,2)\to+1$. Escribe la frontera y los bordes. Calcula el ancho total, la distancia del segundo punto a la frontera y la suma de las pérdidas hinge. Indica qué puntos acierta.

<details>
<summary>Pista</summary>

El puntaje depende de $x_2$. La hinge usa $y_i f(x_i)$ y la distancia usa la magnitud del puntaje.

</details>

[Ver solución de P11](09-pauta.md#p11)

### P12

**Completar código.** Completa el cálculo del kernel RBF entre las dos filas con `gamma` dado. Indica el resultado a cuatro decimales.

```python
import numpy as np

x = np.array([1.0, 2.0])
z = np.array([4.0, 2.0])
gamma = 0.5

distancia_cuadrada = ...
kernel = ...
print(round(kernel, 4))
```

<details>
<summary>Pista</summary>

Resta las coordenadas, eleva cada diferencia al cuadrado y suma. La exponencial se aplica una vez, al final.

</details>

[Ver solución de P12](09-pauta.md#p12)

## Embeddings y métricas

[Volver a la guía](04-embeddings-y-metricas.md)

### P13

**Unir pares.** Relaciona **PCA**, **LDA** y **Fisherfaces** con estas descripciones.

1. Aplica una proyección sin etiquetas y luego otra que usa las etiquetas.
2. Ordena direcciones por la varianza de los datos proyectados.
3. Usa etiquetas para buscar direcciones que separen las clases respecto de su dispersión interna.

<details>
<summary>Pista</summary>

Un método encadena a los otros dos. En ese encadenamiento, identifica cuál necesita `y`.

</details>

[Ver solución de P13](09-pauta.md#p13)

### P14

**Selección múltiple.** En un ejemplo, k-NN con pesos uniformes tiene 3 vecinos con etiquetas A, A y B. Un árbol llega a una hoja con 3 ejemplos A y 2 B. ¿Qué se puede afirmar?

- A. Ambos predicen A. La estimación de A es $2/3$ en k-NN y $3/5$ en el árbol.
- B. k-NN predice B porque aparece una sola vez.
- C. Ambos predicen A y ambos asignan $2/3$.
- D. El árbol siempre estima la misma probabilidad que una SVM calibrada.

<details>
<summary>Pista</summary>

Cada método cuenta en un conjunto distinto. Uno consulta vecinos y el otro una hoja.

</details>

[Ver solución de P14](09-pauta.md#p14)

### P15

**Desarrollo.** Fila real y columna predicha. Calcula accuracy, precision y recall de la clase 1 y macro-F1.

| Real / predicha | 0 | 1 |
| --- | --- | --- |
| 0 | 7 | 1 |
| 1 | 2 | 2 |

<details>
<summary>Pista</summary>

Para el F1 de la clase 0, trátala como la clase de interés. Para macro-F1, promedia los dos F1 resultantes.

</details>

[Ver solución de P15](09-pauta.md#p15)

### P16

**Entender código.** Predice la forma de `X` y el valor de `z`. Explica con qué fotos se calcula `media` y qué pasaría si incluyeras la foto de prueba en ese cálculo.

```python
import numpy as np

fotos = np.arange(12).reshape(3, 2, 2)
X = fotos.reshape(len(fotos), -1)
media = X[:2].mean(axis=0)
patron = np.array([0.5, 0.5, 0.5, 0.5])
z = (X[2] - media) @ patron
print(X.shape, z)
```

<details>
<summary>Pista</summary>

El `-1` pide a NumPy deducir la segunda dimensión. `@` multiplica las coordenadas y suma.

</details>

[Ver solución de P16](09-pauta.md#p16)

## Ensambles y validación

[Volver a la guía](05-ensambles-y-validacion.md)

### P17

**Unir pares.** Relaciona **Random Forest**, **AdaBoost** y **Gradient Boosting** con la descripción más específica.

1. Repondera los ejemplos mal clasificados para el siguiente estimador.
2. Ajusta árboles por separado con muestras bootstrap en la configuración del laboratorio.
3. Aproxima una corrección a partir del gradiente negativo de la pérdida.

<details>
<summary>Pista</summary>

Identifica si cada modelo ajusta árboles por separado, modifica pesos de las filas o corrige un puntaje.

</details>

[Ver solución de P17](09-pauta.md#p17)

### P18

**Completar oraciones.** Con CV de tres folds, para cada candidato cada turno ajusta con ______ folds y valida con ______. Si el criterio es macro-F1, gana la configuración con mayor ______ de macro-F1 de validación. Para medir el rendimiento final se usa ______.

<details>
<summary>Pista</summary>

Distingue los datos que rotan dentro de TRAIN de la partición reservada al comienzo.

</details>

[Ver solución de P18](09-pauta.md#p18)

### P19

**Desarrollo.** Una cuadrícula tiene 2 profundidades, 3 valores de `n_estimators` y 3 valores de `min_samples_leaf`. Random toma 5 combinaciones. Ambas búsquedas usan 4 folds y reajustan una vez cada una. Calcula candidatos, ajustes totales por búsqueda y cuántos ajustes ahorra Random.

<details>
<summary>Pista</summary>

Una combinación fija un valor para cada hiperparámetro. El reajuste se cuenta después de comparar candidatos.

</details>

[Ver solución de P19](09-pauta.md#p19)

### P20

**Entender código.** La siguiente tabla representa un extracto de `cv_results_` de una búsqueda con dos candidatos. ¿Qué configuración gana si se selecciona por macro-F1? ¿Qué imprimiría el código? ¿Qué configuración ganaría por accuracy?

```python
resultados = {
    "params": [{"bosque__max_depth": 2}, {"bosque__max_depth": 5}],
    "mean_test_f1_macro": [0.76, 0.74],
    "mean_test_accuracy": [0.79, 0.83],
}
puntajes = resultados["mean_test_f1_macro"]
i = puntajes.index(max(puntajes))
print(resultados["params"][i], puntajes[i])
```

Explica si `mean_test_f1_macro` usa la partición TEST exterior.

<details>
<summary>Pista</summary>

Cada posición corresponde al mismo candidato en las tres listas. En `cv_results_`, `test` se refiere al lado de validación de cada fold.

</details>

[Ver solución de P20](09-pauta.md#p20)
