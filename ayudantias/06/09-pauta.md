# Pauta de autocorrección

[Inicio](README.md) · [Ejercicios propuestos](07-ejercicios-propuestos.md) · [Ir a la pauta del simulacro](#simulacro)

Compara el procedimiento antes de mirar solo el número final. Si te equivocaste, identifica el primer paso que cambió tu respuesta y vuelve a intentar ese inciso. En el simulacro, los puntos se asignan por el trabajo indicado en cada inciso. Acepta resultados equivalentes que respeten la convención solicitada y pequeñas diferencias de redondeo.

## Preparación y PCA

[Volver a la guía](01-preparacion-y-pca.md) · [Volver a los propuestos](07-ejercicios-propuestos.md#preparación-y-pca)

### P01

**A.** Las magnitudes son $0{,}9$ y $0{,}4$. El signo negativo de la primera indica que, al subir una variable, la otra tiende a bajar. La magnitud mayor indica mayor asociación lineal. Si elegiste por el signo, repasa la [tabla de métodos](01-preparacion-y-pca.md#teoría).

### P02

**RFE → 3.** Ajusta, revisa importancia, elimina y vuelve a ajustar. **L1 → 1.** La penalización por valores absolutos puede dejar pesos en cero. **Isomap → 4.** Usa caminos sobre un grafo de vecinos. **LLE → 2.** Reconstruye puntos a partir de combinaciones locales de vecinos.

### P03

1. La tabla observada tiene filas $(12,4)$ y $(4,12)$. Cada fila y columna suma 16. En total hay 32 piezas.
2. Cada esperado vale $16(16)/32=8$, así que la tabla esperada tiene dos filas $(8,8)$.
3. Las diferencias observado menos esperado son $+4,-4,-4,+4$. Cada contribución es $4^2/8=2$. Hay cuatro celdas y $\chi^2=8$.

**Resultado.** $8>3{,}84$. Se rechaza la independencia al nivel dado. Si comparaste observados sin calcular los esperados, revisa el [ejercicio de chi-cuadrado](01-preparacion-y-pca.md#2-calcular-chi-cuadrado-con-una-tabla-de-conteos).

### P04

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
    ("escala", StandardScaler()),
    ("pca", PCA(n_components=2)),
    ("logistica", LogisticRegression(max_iter=1000)),
])
modelo.fit(X_tr, y_tr)
pred = modelo.predict(X_te)
print(pred.shape)
```

`StandardScaler` aprende con TRAIN y conserva las cuatro columnas originales en escala estandarizada. PCA las convierte en **dos** columnas para logística. El 25 % de 150 son $37{,}5$ y se reservan 38 filas, así que se imprime **`(38,)`**. Si aplicaste `fit` sobre TEST, revisa [la separación y el ajuste](05-ensambles-y-validacion.md#separar-buscar-reajustar-y-evaluar).

## Modelos lineales

[Volver a la guía](02-modelos-lineales.md) · [Volver a los propuestos](07-ejercicios-propuestos.md#modelos-lineales)

### P05

**A.** En ambos, los valores absolutos suman 6 y el MAE vale 2. En A, el RMSE es $\sqrt{36/3}=\sqrt{12}\approx3{,}4641$. En B, es $\sqrt{(4+4+4)/3}=2$. Elevar al cuadrado destaca el error grande de A.

### P06

1. **L2.** Suma los cuadrados de los pesos, por lo que los pesos grandes aportan más al costo.
2. **L1.** Suma valores absolutos y puede dejar coeficientes exactamente en cero.
3. **GaussianNB.** Modela la distribución de cada atributo por clase con una gaussiana y supone independencia condicional.
4. **BayesianRidge.** Puede entregar la desviación estándar predictiva además de la media. Si la desviación crece, aumenta el ancho del intervalo predictivo aproximado.

### P07

1. La recta inicial es $\hat y=x$. Predice $(0,2)$ y sus residuos son $(-1,-3)$.
2. Con MSE, las derivadas son $g_b=(2/2)(-1-3)=-4$ y $g_w=(2/2)((-1)(0)+(-3)(2))=-6$.
3. En el mismo paso, $b'=0-0{,}2(-4)=0{,}8$ y $w'=1-0{,}2(-6)=2{,}2$.
4. Ahora predices $(0{,}8,5{,}2)$. Los residuos son $(-0{,}2,0{,}2)$ y el MSE es $(0{,}04+0{,}04)/2=0{,}04$.

**Resultado.** $(b',w')=(0{,}8,2{,}2)$ y el MSE baja de 5 a $0{,}04$. Si tus cambios fueron la mitad, revisa [el factor del costo](02-modelos-lineales.md#encontrar-los-parámetros).

### P08

```python
import numpy as np

x = np.array([0, 1, 2], dtype=float)
y = np.array([1, 3, 5], dtype=float)

X = np.column_stack((np.ones_like(x), x))
b, w = np.linalg.lstsq(X, y, rcond=None)[0]
print(np.round([b, w, b + w * 3], 4))
```

Se imprime **`[1. 2. 7.]`**. Como la primera columna es constante, su coeficiente es el intercepto. La segunda multiplica $x$, por lo que su coeficiente es la pendiente. Con $x=3$, la recta predice $1+2(3)=7$.

## SVM

[Volver a la guía](03-svm.md) · [Volver a los propuestos](07-ejercicios-propuestos.md#svm)

### P09

**B.** RBF permite fronteras curvas. Cambiar el umbral de una recta mantiene su forma y cambiar `C` en una SVM lineal puede mover o girar la recta. Para interpretar probabilidades de SVM se necesita calibración.

### P10

**Mal clasificada**, **dentro de** y **cero**. Si $m<0$, los signos de etiqueta y puntaje son opuestos. Si $0<m<1$, coinciden, pero hinge todavía cobra $1-m$. En $m=1$ se llega al borde de la clase y $\max(0,1-1)=0$.

### P11

1. $f(x)=2x_2-2$, así que la frontera es $x_2=1$. Los bordes $f=-1$ y $f=+1$ están en $x_2=0{,}5$ y $x_2=1{,}5$. El ancho es $2/\lVert w\rVert=2/2=1$.
2. Los puntajes son $(-2,0{,}5,2)$. El segundo punto queda a distancia $0{,}5/2=0{,}25$ de la frontera.
3. Al multiplicar por las etiquetas, los márgenes son $(2,0{,}5,2)$ y las pérdidas $(0,0{,}5,0)$.

**Resultado.** Las tres predicciones aciertan y la hinge suma $0{,}5$, porque el segundo punto queda dentro de la franja. Si contaste un error al ver hinge positiva, revisa [margen suave](03-svm.md#qué-intenta-optimizar-svm).

### P12

```python
import numpy as np

x = np.array([1.0, 2.0])
z = np.array([4.0, 2.0])
gamma = 0.5

distancia_cuadrada = np.sum((x - z) ** 2)
kernel = np.exp(-gamma * distancia_cuadrada)
print(round(kernel, 4))
```

La resta da $(-3,0)$, los cuadrados suman 9 y $K=\exp(-4{,}5)\approx\mathbf{0{,}0111}$. Si solo sumaste diferencias, revisa cómo se mide la [similitud con RBF](03-svm.md#qué-aporta-un-kernel).

## Embeddings y métricas

[Volver a la guía](04-embeddings-y-metricas.md) · [Volver a los propuestos](07-ejercicios-propuestos.md#embeddings-y-métricas)

### P13

**PCA → 2.** Busca varianza. **LDA → 3.** Usa las clases para aprovechar su separación. **Fisherfaces → 1.** Ajusta PCA sobre las entradas y LDA sobre las coordenadas de PCA con las etiquetas de TRAIN.

### P14

**A.** A es mayoría en ambos. En k-NN hay 2 votos A entre 3 vecinos. En el árbol hay 3 muestras A entre 5 que llegan a la hoja. Una SVM calibrada usa otro mecanismo de estimación.

### P15

1. La diagonal suma $7+2=9$ y hay 12 filas en total. Accuracy $=9/12=0{,}75$.
2. Para clase 1, $TP=2$, $FP=1$ desde la columna 1 y $FN=2$ desde la fila 1. Precision $=2/3$, recall $=2/4=0{,}5$ y $F_{1,1}=4/(4+1+2)=4/7$.
3. Para clase 0, $TP=7$, $FP=2$ y $FN=1$. Así, $F_{1,0}=14/(14+2+1)=14/17$.

**Resultado.** Macro-F1 $=(4/7+14/17)/2\approx0{,}6975$. Si intercambiaste FP y FN, marca [fila real y columna predicha](04-embeddings-y-metricas.md#leer-una-matriz-de-confusión).

### P16

`X` tiene forma **`(3, 4)`**. Cada una de las tres fotos aporta cuatro píxeles en el mismo orden. Las dos primeras filas son $(0,1,2,3)$ y $(4,5,6,7)$. Al promediar por columna, la media queda $(2,3,4,5)$. A la tercera fila, $(8,9,10,11)$, le restas esa media y quedan cuatro seises. Cada uno aporta $6(0{,}5)=3$, así que el programa imprime **`(3, 4) 12.0`**. Si la foto de prueba participara en la media, influiría en la representación que debía aprenderse con TRAIN.

## Ensambles y validación

[Volver a la guía](05-ensambles-y-validacion.md) · [Volver a los propuestos](07-ejercicios-propuestos.md#ensambles-y-validación)

### P17

**Random Forest → 2.** Los árboles exploran muestras y atributos distintos. **AdaBoost → 1.** El estimador siguiente presta más atención a los errores que se reponderaron. **Gradient Boosting → 3.** Cada etapa suma una corrección guiada por la pérdida actual.

### P18

**Dos**, **uno**, **promedio** y **TEST**. Cada fold hace una vez de validación. La media resume el rendimiento de cada candidato en esos turnos. TEST queda para el modelo final reajustado.

### P19

1. Grid cruza $2(3)(3)=18$ candidatos. Random prueba 5.
2. Grid hace $18(4)+1=73$ ajustes. Random hace $5(4)+1=21$.
3. La diferencia es $73-21=52$ ajustes. Los reajustes aportan uno a cada método, por lo que la diferencia también se obtiene con $(18-5)(4)$.

**Resultado.** Random ahorra 52 ajustes bajo ese presupuesto. Para compararlo en tiempo real, considera también lo que tarda cada candidato y su calidad. Si sumaste o multiplicaste el reajuste por los folds, vuelve al [conteo del laboratorio](05-ensambles-y-validacion.md#2-contar-los-ajustes-y-elegir-por-validación).

### P20

La media macro-F1 mayor es $0{,}76$. Está en el índice 0, así que se imprime **`{'bosque__max_depth': 2} 0.76`**. Con accuracy gana la profundidad 5, porque $0{,}83>0{,}79$. En `cv_results_`, `mean_test_f1_macro` es la media de los puntajes de validación de los folds. TEST exterior sigue reservado.

## Simulacro

[Volver al simulacro](08-simulacro.md)

### Parte A. Teoría (50 puntos)

**Selección múltiple.** 4 puntos por acierto.

| Ítem | Respuesta y por qué |
| --- | --- |
| T01 | **B.** El filtro conserva columnas originales. PCA, t-SNE y Fisherfaces construyen coordenadas nuevas. |
| T02 | **C.** $0{,}56$ supera $0{,}50$ y queda bajo $0{,}65$. Cambia la clase de 1 a 0. La salida de la sigmoide sigue en $0{,}56$. |
| T03 | **D.** k-NN conserva los ejemplos. Con dimensión de entrada fija, los demás ejemplos tienen un número fijo de pesos en la representación dada. |
| T04 | **A.** La varianza describe dispersión. El rendimiento del clasificador se mide con sus predicciones. |
| T05 | **C.** El criterio usa el promedio de CV. Se reajusta A con todo TRAIN y se mide su rendimiento en TEST. |

**Completar oraciones.** 3 puntos por acierto.

| Ítem | Respuesta y por qué |
| --- | --- |
| T06 | **$n-1$.** El cálculo estima la media en la misma muestra, así que se pierde un grado de libertad. |
| T07 | **Intercepto**. También sirve **sesgo** o **término independiente**. Si $x=0$, la predicción es $b$. |
| T08 | **Hinge** o **bisagra**. Vale cero cuando $yf(x)\ge1$. |
| T09 | **$C-1$.** Las desviaciones de las medias de clase respecto a la global tienen una dependencia lineal al ponderarlas por tamaño de clase. |
| T10 | **Con reemplazo.** Una observación puede volver a salir. |

**Unir pares.** 3 puntos por cada relación correcta.

| Par | Por qué |
| --- | --- |
| **1–B** | La selección hacia adelante empieza sin variables y agrega una por vez según el rendimiento de validación. |
| **2–E** | La BCE aplica logaritmo negativo a la probabilidad de la etiqueta observada. |
| **3–D** | t-SNE intenta conservar vecindades locales en el mapa. |
| **4–A** | Macro-F1 promedia los F1 por clase con el mismo peso. |
| **5–C** | Gradient Boosting añade etapas que siguen una corrección guiada por la pérdida. |

Si fallaste más de un ítem de un tema, vuelve a la [guía correspondiente](README.md#guías) y practica el ejercicio resuelto antes de rehacer la pregunta.

### Parte B. Desarrollo (30 puntos)

#### D01. PCA desde cero

**1 · 3 puntos.** La primera columna suma $12$ y la segunda $16$. Dividir por cuatro da la media $(3,4)$ (1 punto). Al restarla, queda la siguiente matriz (2 puntos).

```math
X_c=\begin{pmatrix}-2&-2\\-1&1\\1&-1\\2&2\end{pmatrix}.
```

**2 · 4 puntos.** Cada suma de cuadrados da $10$ y la suma de productos es $4-1-1+4=6$. Con divisor 3, sale (2 puntos):

```math
S=\frac13\begin{pmatrix}10&6\\6&10\end{pmatrix}.
```

Para la matriz multiplicada por 3, el determinante de $3S-tI$ es $(10-t)^2-36$. Igualarlo a cero da $t=16$ y $t=4$. Al dividir por 3 quedan $\lambda_1=16/3$ y $\lambda_2=4/3$ (1 punto). La primera fila de $(3S-16I)q=0$ exige que las dos entradas sean iguales. Al normalizar con signos positivos queda $q_1=(1,1)/\sqrt2$ (1 punto).

**3 · 3 puntos.** $\lambda_1/(\lambda_1+\lambda_2)=16/20=0{,}8$. **Una componente** basta (1 punto). Suma las dos coordenadas centradas de cada fila y divide por $\sqrt2$. Así sale $(-4/\sqrt2,0,0,4/\sqrt2)$ (2 puntos).

**Si te equivocaste al proyectar**, vuelve a [hacer PCA de dos columnas a una](01-preparacion-y-pca.md#3-hacer-pca-de-dos-columnas-a-una).

#### D02. Recta, gradiente y decisión logística

**1 · 4 puntos.** La recta inicial predice $(0,0)$ y deja los residuos $(-1,-3)$. Como el costo usa $1/(2n)$:

```math
g_b=\frac{-1-3}{2}=-2,\qquad
g_w=\frac{(-1)(0)+(-3)(2)}{2}=-3.
```

Las dos derivadas valen 1 punto cada una. Resta $0{,}25$ veces cada derivada, usando los parámetros iniciales en ambas cuentas. Sale $b'=0{,}5$ y $w'=0{,}75$ (1 punto cada uno).

**2 · 3 puntos.** La nueva recta predice $0{,}5$ en $x=0$ y $2$ en $x=2$ (1 punto). El costo inicial es $(1+9)/4=2{,}5$ (1 punto). Los nuevos residuos son $(-0{,}5,-1)$, por lo que el costo baja a $(0{,}25+1)/4=0{,}3125$ (1 punto).

**3 · 3 puntos.** Para el modelo logístico, el puntaje es $0{,}5+0{,}75(2)=2$ (1 punto). La probabilidad es $\sigma(2)=1/(1+\exp(-2))\approx0{,}8808$ (1 punto). Como queda bajo $0{,}90$, la clase predicha es **0** (1 punto).

**Si el paso quedó duplicado**, revisa [las convenciones del costo](02-modelos-lineales.md#encontrar-los-parámetros). Si asignaste la clase 1, vuelve a [la regla del umbral](02-modelos-lineales.md#de-puntaje-a-clase).

#### D03. Márgenes y pérdidas en SVM

**1 · 3 puntos.** $f(x)=2x_1+x_2-2$ y la frontera es $2x_1+x_2=2$ (1 punto). La norma es $\sqrt{2^2+1^2}=\sqrt5$ (1 punto), así que el ancho es $2/\sqrt5\approx0{,}8944$ (1 punto).

**2 · 4 puntos.** Sustituye en el puntaje (1 punto), multiplica por la etiqueta (1 punto) y toma $\max(0,1-m)$ (1 punto).

| Punto | Puntaje $f(x)$ | Margen $m$ | Hinge |
| --- | --- | --- | --- |
| $(0,0)$ | $-2$ | $2$ | $0$ |
| $(1,0{,}5)$ | $0{,}5$ | $0{,}5$ | $0{,}5$ |
| $(0,2{,}5)$ | $0{,}5$ | $-0{,}5$ | $1{,}5$ |

El error es el tercer punto. Su puntaje positivo predice $+1$ y su clase real es $-1$ (1 punto).

**3 · 3 puntos.** La suma de pérdidas es $0+0{,}5+1{,}5=2$ (1 punto). El término de norma vale $5/2$ (1 punto). Con $C=0{,}5$ el costo es $5/2+0{,}5(2)=\mathbf{3{,}5}$ (1 punto).

**Si confundiste un acierto dentro de la franja con un error**, revisa [margen suave y hinge](03-svm.md#qué-intenta-optimizar-svm).

### Parte C. Código (20 puntos)

#### D04. Completar un pipeline de Fisherfaces

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
    ("pca", PCA(n_components=3)),
    ("lda", LinearDiscriminantAnalysis(n_components=2)),
    ("vecinos", KNeighborsClassifier(n_neighbors=3)),
])
modelo.fit(X_tr, y_tr)
pred = modelo.predict(X_te)
```

1. **2 puntos.** `PCA(n_components=3)` reduce de cuatro columnas a tres. Da 1 punto por la etapa y 1 por la dimensión.
2. **2 puntos.** `LinearDiscriminantAnalysis(n_components=2)` aprende dos direcciones. Da 1 punto por la etapa y 1 por la dimensión.
3. **2 puntos.** `fit(X_tr, y_tr)` ajusta usando únicamente TRAIN. Da 1 punto por cada argumento correcto.
4. **2 puntos.** `predict(X_te)` aplica las etapas aprendidas a los datos de prueba para devolver una etiqueta por fila.
5. **2 puntos.** k-NN recibe **dos columnas** (1 punto). **LDA** usa etiquetas de entrenamiento en la representación (1 punto). El clasificador también usa etiquetas para su propio ajuste.

Si invertiste las etapas o te faltaron etiquetas, revisa [PCA, LDA y Fisherfaces](04-embeddings-y-metricas.md#pca-lda-y-fisherfaces).

#### D05. Interpretar una búsqueda

**1 · 4 puntos.** `pca.fit_transform(X_tr)` aprende con **todo** TRAIN antes de CV. Eso incluye las filas que actuarán como validación en cada fold (2 puntos). Para corregirlo, pon PCA y el bosque en un `Pipeline` sin ajustar y entrega `X_tr, y_tr` a `GridSearchCV.fit` (2 puntos).

**2 · 4 puntos.** La primera cifra es la media de macro-F1 en los folds del candidato ganador. En el código original los folds ya recibieron los datos transformados con el PCA preajustado (2 puntos por identificar la media de CV). La segunda es el macro-F1 del ganador en la partición TEST y es la única que usa `y_te` (2 puntos).

**3 · 2 puntos.** Dos profundidades por tres folds dan seis ajustes de CV (1 punto). El reajuste final agrega uno, así que Grid hace **siete** ajustes del estimador que recibe (1 punto).

El código corregido vuelve a aprender PCA en cada fold y en el reajuste final:

```python
from sklearn.datasets import load_iris
from sklearn.decomposition import PCA
from sklearn.ensemble import RandomForestClassifier
from sklearn.metrics import f1_score
from sklearn.model_selection import (
    GridSearchCV, StratifiedKFold, train_test_split,
)
from sklearn.pipeline import Pipeline

X, y = load_iris(return_X_y=True)
X_tr, X_te, y_tr, y_te = train_test_split(
    X, y, test_size=0.25, stratify=y, random_state=42
)
modelo = Pipeline([
    ("pca", PCA(n_components=2)),
    ("bosque", RandomForestClassifier(n_estimators=10, random_state=42)),
])
cv = StratifiedKFold(n_splits=3, shuffle=True, random_state=42)
grid = GridSearchCV(
    modelo, {"bosque__max_depth": [2, 4]}, scoring="f1_macro", cv=cv
)
grid.fit(X_tr, y_tr)
print(grid.best_score_)
print(f1_score(y_te, grid.predict(X_te), average="macro"))
```

Si pensaste que separar TEST primero era suficiente, revisa [qué aprende el pipeline en cada fold](05-ensambles-y-validacion.md#separar-buscar-reajustar-y-evaluar).
