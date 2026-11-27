# Simulacro de la Prueba 2

[Inicio](README.md) · [Pauta del simulacro](09-pauta.md#simulacro)

Resuelve primero la prueba completa y luego corrige con la pauta. Usa papel y calculadora. Puedes leer y responder los ejercicios de Python sin ejecutar el código. Deja las cuentas y expresa los decimales a cuatro cifras cuando haga falta.

| Parte | Puntaje |
| --- | --- |
| Selección múltiple, completar oraciones y unir pares | 50 |
| Desarrollo | 30 |
| Completar e interpretar código | 20 |
| **Total** | **100** |

## Parte A. Teoría (50 puntos)

### Selección múltiple (20 puntos)

Marca una opción por pregunta. Cada respuesta vale 4 puntos.

1. **T01.** Quieres conservar dos columnas originales entre diez. ¿Cuál método cumple ese objetivo?
   - A. Proyectar toda la tabla en dos componentes de PCA.
   - B. Aplicar un filtro que ordene las columnas y conserve las dos mejores.
   - C. Usar t-SNE para dibujar los datos en dos dimensiones.
   - D. Calcular dos patrones de Fisherfaces y descartar las columnas originales.
2. **T02.** Un modelo logístico asigna $p=0{,}56$ a la clase 1. Se predice 1 cuando $p$ alcanza o supera el umbral. ¿Qué ocurre al subir el umbral de $0{,}50$ a $0{,}65$ sin reajustar el modelo?
   - A. La probabilidad baja a $0{,}50$ y la clase queda en 1.
   - B. La probabilidad sigue igual y la clase se mantiene en 1.
   - C. La probabilidad sigue igual y la clase pasa de 1 a 0.
   - D. El modelo tendrá que recalcular sus pesos para decidir.
3. **T03.** Con un número fijo de atributos, ¿cuál es un ejemplo de modelo cuya representación puede crecer al agregar datos?
   - A. Una recta de regresión con intercepto.
   - B. Regresión logística binaria sin expansión de atributos.
   - C. Una SVM expresada directamente con un vector lineal y un sesgo.
   - D. k-NN que conserva los ejemplos de entrenamiento.
4. **T04.** El embedding A conserva más varianza de entrenamiento que el B. ¿Qué puedes concluir con ese dato?
   - A. A retiene mayor dispersión según esa medida. Para comparar las predicciones todavía necesitas métricas.
   - B. A tiene mayor macro-F1 en prueba.
   - C. A usa las etiquetas de entrenamiento para elegir sus direcciones.
   - D. El clasificador de A siempre acierta más identidades.
5. **T05.** Una búsqueda obtiene macro-F1 promedio de CV $0{,}83$ para A y $0{,}79$ para B. Si macro-F1 es el criterio, ¿qué haces?
   - A. Pruebas ambos en TEST y eliges el mayor de esos dos.
   - B. Eliges B porque tiene un puntaje más moderado.
   - C. Eliges A, reajustas con todo TRAIN y evalúas el modelo final en TEST.
   - D. Reportas $0{,}83$ como macro-F1 de TEST.

### Completar oraciones (15 puntos)

Cada espacio vale 3 puntos.

- **T06.** Al calcular la covarianza muestral de $n$ filas centradas, dividimos por ______.
- **T07.** En $\hat y=wx+b$, el parámetro $b$ se llama ______ y da la predicción para $x=0$.
- **T08.** En SVM, la pérdida ______ se calcula como $\max(0,1-yf(x))$.
- **T09.** Con $C$ clases y suficientes atributos de entrada, la cota de dimensión de LDA que viene de la cantidad de clases es ______.
- **T10.** En bootstrap se muestrea ______, por lo que una fila puede aparecer varias veces en la muestra.

### Unir pares (15 puntos)

Escribe una letra para cada número. Usa cada letra una vez. Cada par correcto vale 3 puntos.

| Número | Concepto |
| --- | --- |
| 1 | Selección secuencial hacia adelante |
| 2 | Entropía cruzada binaria (BCE) |
| 3 | t-SNE |
| 4 | Macro-F1 |
| 5 | Gradient Boosting |

| Letra | Descripción |
| --- | --- |
| A | Da el mismo peso al F1 de cada clase. |
| B | Agrega una variable por paso según el rendimiento de validación. |
| C | Agrega etapas que aproximan una corrección basada en la pérdida. |
| D | Busca representar vecindades cercanas en un mapa de baja dimensión. |
| E | Penaliza con el logaritmo negativo de la probabilidad de la clase real. |

## Parte B. Desarrollo (30 puntos)

### D01. PCA desde cero (10 puntos)

Las cuatro filas son $(1,2)$, $(2,5)$, $(4,3)$ y $(5,6)$. Están en la misma escala. Usa covarianza muestral. Toma como PC1 la dirección con las dos entradas positivas.

1. **(3 puntos)** Calcula la media de cada columna y la matriz centrada.
2. **(4 puntos)** Construye la covarianza, calcula ambos autovalores y da un autovector unitario para PC1.
3. **(3 puntos)** Decide cuántas componentes conservan al menos el 80 % de la varianza y proyecta las cuatro filas en PC1.

### D02. Recta, gradiente y decisión logística (10 puntos)

Para regresión tienes $(x,y)=(0,1),(2,3)$, parámetros iniciales $(b,w)=(0,0)$, tasa $\alpha=0{,}25$ y costo $J=(1/2n)\sum_i(\hat y_i-y_i)^2$.

1. **(4 puntos)** Calcula los dos gradientes en el punto inicial y actualiza pendiente e intercepto.
2. **(3 puntos)** Calcula las predicciones de la recta nueva y compara su costo con el inicial.
3. **(3 puntos)** En otro problema, un modelo logístico ya ajustado usa como parámetros el $b$ y el $w$ del inciso 1. Para $x=2$, calcula el puntaje, la probabilidad positiva y la clase si la regla es $p\ge0{,}90$. Puedes usar $\exp(-2)\approx0{,}1353$.

### D03. Márgenes y pérdidas en SVM (10 puntos)

Usa $w=(2,1)$, $b=-2$ y $C=0{,}5$. Las filas etiquetadas son $(0,0)\to -1$, $(1,0{,}5)\to+1$ y $(0,2{,}5)\to-1$.

1. **(3 puntos)** Escribe la frontera y calcula el ancho total entre $f=-1$ y $f=+1$.
2. **(4 puntos)** Calcula $f(x)$, el margen firmado y la hinge para cada fila. Identifica el error.
3. **(3 puntos)** Calcula el objetivo de margen suave $\lVert w\rVert^2/2+C\sum_i \ell_i$.

## Parte C. Código (20 puntos)

### D04. Completar un pipeline de Fisherfaces (10 puntos)

El ejemplo usa las tres especies de Iris para poder practicar con un conjunto pequeño. Sigue el mismo orden del Laboratorio 02. PCA conserva tres coordenadas. LDA usa dos. k-NN consulta tres vecinos.

```python
from sklearn.datasets import load_iris
from sklearn.decomposition import PCA
from sklearn.discriminant_analysis import LinearDiscriminantAnalysis
from sklearn.model_selection import train_test_split
from sklearn.neighbors import KNeighborsClassifier
from sklearn.pipeline import Pipeline

X, y = load_iris(return_X_y=True)
X_tr, X_te, y_tr, y_te = train_test_split(
    X, y, test_size=0.25, stratify=y, random_state=42
)
modelo = Pipeline([
    ("pca", ...),   # 1
    ("lda", ...),   # 2
    ("vecinos", KNeighborsClassifier(n_neighbors=3)),
])
modelo.fit(...)       # 3
pred = modelo.predict(...)  # 4
```

1. **(2 puntos)** Completa la etapa 1.
2. **(2 puntos)** Completa la etapa 2.
3. **(2 puntos)** Completa los argumentos de `fit`.
4. **(2 puntos)** Completa los argumentos de `predict`.
5. **(2 puntos)** ¿Cuántas columnas recibe k-NN? ¿Qué etapa de representación usa las etiquetas?

### D05. Interpretar una búsqueda (10 puntos)

Lee el siguiente código. Se intenta evaluar un bosque con PCA usando los mismos principios del Laboratorio 03.

```python
from sklearn.datasets import load_iris
from sklearn.decomposition import PCA
from sklearn.ensemble import RandomForestClassifier
from sklearn.metrics import f1_score
from sklearn.model_selection import (
    GridSearchCV, StratifiedKFold, train_test_split,
)

X, y = load_iris(return_X_y=True)
X_tr, X_te, y_tr, y_te = train_test_split(
    X, y, test_size=0.25, stratify=y, random_state=42
)
pca = PCA(n_components=2)
z_tr = pca.fit_transform(X_tr)
z_te = pca.transform(X_te)

bosque = RandomForestClassifier(n_estimators=10, random_state=42)
cv = StratifiedKFold(n_splits=3, shuffle=True, random_state=42)
grid = GridSearchCV(
    bosque, {"max_depth": [2, 4]}, scoring="f1_macro", cv=cv
)
grid.fit(z_tr, y_tr)
print(grid.best_score_)
print(f1_score(y_te, grid.predict(z_te), average="macro"))
```

1. **(4 puntos)** ¿Dónde está la fuga dentro de CV? Explica cómo corregirla e indica qué variables deben llegar al `fit` de la búsqueda corregida.
2. **(4 puntos)** ¿Qué mide cada una de las dos cifras impresas? ¿Cuál usa etiquetas de TEST?
3. **(2 puntos)** ¿Cuántos ajustes del estimador que recibe Grid hay, incluyendo el reajuste por defecto?

[Corregir el simulacro con la pauta](09-pauta.md#simulacro)
