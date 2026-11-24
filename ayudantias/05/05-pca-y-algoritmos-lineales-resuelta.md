# Ayudantía 05 — PCA, regresión lineal, regresión logística y Pipeline

Esta guía acompaña la ayudantía de preparación de Machine Learning de la UCN. Elige un ejercicio y avanza un inciso a la vez.

- Sigue los pasos numerados y abre las demostraciones cuando quieras ver de dónde sale un resultado.
- Guarda las fracciones o los decimales de la calculadora y redondea solo al final.
- Intenta la mini práctica de cada ejercicio antes de desplegar la respuesta.

## Índice

1. [PCA de dos características a una](#ejercicio-1-pca-de-dos-características-a-una)
2. [Regresión lineal para ajustar una recta](#ejercicio-2-regresión-lineal-para-ajustar-una-recta)
3. [Regresión logística con regularización L2](#ejercicio-3-regresión-logística-con-regularización-l2)
4. [Pipeline de clasificación con Iris](#ejercicio-4-pipeline-de-clasificación-con-iris)

## Ejercicio 1. PCA de dos características a una

Imagina cuatro puntos dibujados en un plano. Cada punto tiene dos coordenadas. Si elegimos una recta y proyectamos los puntos sobre ella, basta una coordenada para ubicar cada proyección. **PCA elige la dirección en la que esas proyecciones quedan más esparcidas.** La medida de esa dispersión se llama **varianza**.

```math
X=\begin{pmatrix}1&1\\2&0\\4&4\\5&3\end{pmatrix}.
```

Cada fila es un punto y cada columna es una característica medida en la misma unidad y escala. Vamos a centrar los datos, medir cómo varían y buscar una dirección que conserve al menos el 85 % de la varianza.

### a) Calcular las medias y centrar los datos

El subíndice $j$ identifica una columna. Su media $\overline{x_j}$ se calcula sumando sus cuatro valores y dividiendo por el número de observaciones $n=4$.

```math
\overline{x_1}=\frac{1+2+4+5}{4}=3,\qquad
\overline{x_2}=\frac{1+0+4+3}{4}=2.
```

Para centrar cada fila restamos $(3,2)$. El primer número se resta a la primera característica y el segundo a la segunda.

| Fila | Datos originales | Resta de las medias | Fila centrada |
| --- | --- | --- | --- |
| 1 | $(1,1)$ | $(1-3,1-2)$ | $(-2,-1)$ |
| 2 | $(2,0)$ | $(2-3,0-2)$ | $(-1,-2)$ |
| 3 | $(4,4)$ | $(4-3,4-2)$ | $(1,2)$ |
| 4 | $(5,3)$ | $(5-3,3-2)$ | $(2,1)$ |

Por tanto, la media de las filas es $\overline{\mathbf x}=(3,2)$ y la matriz centrada es

```math
X_c=\begin{pmatrix}-2&-1\\-1&-2\\1&2\\2&1\end{pmatrix}.
```

Las dos columnas ahora suman cero. Cada coordenada centrada indica cuánto se aleja esa medida de su media, en las unidades originales. Estandarizar añadiría otro paso, dividir por la desviación estándar de cada columna.

### b) Construir la matriz de covarianza

La entrada $s_{jk}$ de la matriz $S$ compara las columnas $j$ y $k$. Multiplicamos sus desviaciones **en la misma fila**, sumamos los productos y dividimos por $n-1$.

```math
s_{jk}=\frac{\sum_{i=1}^{n}(x_{ij}-\overline{x_j})(x_{ik}-\overline{x_k})}{n-1}.
```

- **En la diagonal** ($j=k$), multiplicamos una desviación por sí misma. El resultado mide la varianza de una columna.
- **Fuera de la diagonal** ($j\ne k$), multiplicamos desviaciones de dos columnas. El resultado mide su covarianza. En la primera fila, $(-2)(-1)=2$ aporta un producto positivo porque ambas medidas están por debajo de sus medias.

**¿Por qué dividimos por $n-1=3$?** Las cuatro desviaciones de una columna suman cero. Si conoces tres, puedes deducir la cuarta. Al estimar la media con los propios datos perdemos un grado de libertad.

<details>
<summary>Qué corrige el divisor n − 1 y qué pasa si usamos n</summary>

Las desviaciones respecto de la media de la muestra dan, en promedio, una dispersión menor que las desviaciones respecto de la media de la población. Dividir por $n-1$ corrige ese sesgo al estimar la varianza y la covarianza con observaciones independientes de la misma población.

Si dividimos por $n$ con estos datos, tanto la matriz como sus valores propios quedan multiplicados por $3/4$. Las direcciones y las proporciones de varianza se conservan.

</details>

La transpuesta $X_c^\top$ intercambia filas y columnas. En $X_c^\top X_c$, cada entrada es el producto punto de dos columnas originales de $X_c$. Las dimensiones son $(2\times 4)(4\times 2)=2\times 2$.

```math
\begin{aligned}
s_{11}&=\frac{(-2)^2+(-1)^2+1^2+2^2}{3}=\frac{10}{3},\\
s_{22}&=\frac{(-1)^2+(-2)^2+2^2+1^2}{3}=\frac{10}{3},\\
s_{12}=s_{21}&=\frac{(-2)(-1)+(-1)(-2)+(1)(2)+(2)(1)}{3}=\frac{8}{3}.
\end{aligned}
```

```math
S=\frac{X_c^\top X_c}{n-1}=\begin{pmatrix}10/3&8/3\\8/3&10/3\end{pmatrix}.
```

La simetría $s_{12}=s_{21}$ sirve para revisar la cuenta. La covarianza positiva muestra que, en estos datos, ambas medidas tienden a quedar por encima de sus medias juntas o por debajo juntas.

### c) Encontrar los valores propios y la dirección de PC1

**1. Entender qué estamos buscando.** Una dirección $v$ nos permite convertir la fila centrada $x_{c,i}$ en una coordenada $z_i=x_{c,i}v$. El producto punto multiplica las entradas de a pares y suma. Por ejemplo, sobre la dirección horizontal $(1,0)^\top$, la primera fila queda en $(-2)(1)+(-1)(0)=-2$.

Usamos direcciones de largo uno para comparar en la misma escala. Si duplicáramos el largo de $v$, se duplicarían las coordenadas y se cuadruplicaría la varianza, aun en la misma dirección.

Un **vector propio** $q$ es un vector distinto de cero que cumple $Sq=\lambda q$. Al multiplicar por $S$, el resultado queda sobre la misma recta que $q$. El factor $\lambda$ se llama **valor propio**.

Si $q$ tiene largo uno, su valor propio nos dice cuánta varianza obtenemos al proyectar en esa dirección. Por eso buscamos el mayor.

<details>
<summary>Demostración de por qué PC1 usa el mayor valor propio</summary>

Si juntamos las coordenadas en la columna $z=X_cv$, su media es cero, porque las columnas de $X_c$ suman cero. Su varianza muestral se puede escribir así.

$\mathrm{Var}(z)=\frac{\sum_i z_i^2}{n-1}=\frac{z^\top z}{n-1}$\
$=\frac{(X_cv)^\top(X_cv)}{n-1}$\
$=v^\top\left(\frac{X_c^\top X_c}{n-1}\right)v=v^\top Sv$.

Si tomamos $v=q$ con largo uno, la varianza proyectada es $q^\top Sq=\lambda q^\top q=\lambda$.

Una matriz real simétrica como $S$ tiene una base de vectores propios ortogonales, es decir, perpendiculares entre sí. Llamemos $q_1,q_2$ a esas direcciones de largo uno y ordenemos $\lambda_1\ge\lambda_2$. Cualquier dirección de largo uno se puede escribir $v=c_1q_1+c_2q_2$, con $c_1^2+c_2^2=1$.

Al calcular $v^\top Sv$, los términos cruzados desaparecen porque $q_1^\top q_2=0$.

$\mathrm{Var}(X_cv)=c_1^2\lambda_1+c_2^2\lambda_2$\
$\le c_1^2\lambda_1+c_2^2\lambda_1=\lambda_1$.

La cota se alcanza tomando $v=q_1$, con $c_1=1$ y $c_2=0$. Por eso la primera componente principal, PC1, usa la dirección del mayor valor propio.

</details>

**2. Calcular los valores propios.** Pasamos todo a un lado en $Sq=\lambda q$ y obtenemos $(S-\lambda I)q=0$. La matriz identidad $I$ tiene unos en la diagonal y ceros fuera de ella, de modo que $\lambda I$ resta $\lambda$ solo de la diagonal de $S$.

Buscamos un $q$ distinto de cero. Si $S-\lambda I$ fuera invertible, su inversa nos daría $q=0$. Por eso buscamos los valores de $\lambda$ que hacen que el determinante sea cero.

```math
\begin{aligned}
S-\lambda I
&=\begin{pmatrix}10/3-\lambda&8/3\\8/3&10/3-\lambda\end{pmatrix},\\
\det(S-\lambda I)
&=(10/3-\lambda)^2-(8/3)^2=0.
\end{aligned}
```

El determinante de una matriz $2\times2$ es el producto de la diagonal principal menos el de la otra diagonal. Aquí tenemos una diferencia de cuadrados, así que $10/3-\lambda=8/3$ o $10/3-\lambda=-8/3$. Los valores propios son $\lambda_2=10/3-8/3=2/3$ y $\lambda_1=10/3+8/3=6$.

**3. Encontrar la dirección de PC1.** Sustituimos el mayor valor, $\lambda_1=6$. Si escribimos el vector como $(u_1,u_2)^\top$, la primera fila de la matriz nos da $-\frac83u_1+\frac83u_2=0$. La segunda fila da la misma condición. Por tanto, $u_1=u_2$.

Elegimos el par $(1,1)^\top$ y dividimos por su largo $\sqrt{1^2+1^2}=\sqrt 2$ para obtener un vector unitario.

```math
q_1=\frac{1}{\sqrt2}\begin{pmatrix}1\\1\end{pmatrix}.
```

**Comprueba la cuenta.** Multiplica $S$ por $(1,1)^\top$. Obtienes $(18/3,18/3)^\top=6(1,1)^\top$. También puedes elegir $-q_1$. Eso invierte el signo de las proyecciones y conserva su varianza.

Para $\lambda_2=2/3$, el mismo cálculo da $\frac83u_1+\frac83u_2=0$. Elegimos $(1,-1)^\top$ y dividimos por su largo para obtener $q_2=(1,-1)^\top/\sqrt2$.

### d) Calcular la varianza explicada y decidir cuántas componentes usar

La varianza total es la suma de las varianzas de las columnas centradas, $10/3+10/3=20/3$. También se obtiene sumando los valores propios, porque PC1 y PC2 expresan esa misma variación en dos ejes ortogonales.

```math
\begin{aligned}
\text{Proporción explicada por PC1}
&=\frac{6}{6+2/3}=\frac{18}{20}=0{,}90,\\
\text{Proporción explicada por PC2}
&=\frac{2/3}{6+2/3}=\frac{2}{20}=0{,}10.
\end{aligned}
```

**Una componente basta.** PC1 conserva el 90 %, por encima del 85 % pedido. Al conservar solo PC1 queda fuera el 10 % de la varianza.

### e) Proyectar las cuatro observaciones e interpretar

Multiplicamos cada fila centrada por el vector columna $q_1$. Como ambas entradas de $q_1$ son $1/\sqrt2$, basta sumar las dos coordenadas centradas y dividir por $\sqrt2$.

| Fila | Producto punto | Proyección |
| --- | --- | --- |
| 1 | $\frac{-2-1}{\sqrt2}$ | $-\frac3{\sqrt2}\approx -2{,}1213$ |
| 2 | $\frac{-1-2}{\sqrt2}$ | $-\frac3{\sqrt2}\approx -2{,}1213$ |
| 3 | $\frac{1+2}{\sqrt2}$ | $\frac3{\sqrt2}\approx 2{,}1213$ |
| 4 | $\frac{2+1}{\sqrt2}$ | $\frac3{\sqrt2}\approx 2{,}1213$ |

Así, $Z=X_cq_1$ tiene las mismas cuatro filas y **una coordenada nueva** por fila que combina las dos columnas.

Fíjate en las filas 1 y 2. Tienen la misma suma y por eso coinciden en PC1. Al mirar la dirección perpendicular $q_2=(1,-1)^\top/\sqrt2$, sus coordenadas son $-1/\sqrt2$ y $1/\sqrt2$. Esa es la diferencia que perdemos al guardar solo PC1.

Como comprobación, la varianza muestral de las proyecciones es $[4(9/2)]/3=6$, que coincide con el valor propio de PC1.

**Para un ejercicio parecido**

1. Revisa si te piden centrar o estandarizar y qué divisor usar.
2. Construye $S$, ordena los valores propios y normaliza las direcciones que vas a usar.
3. Acumula la varianza explicada hasta llegar al umbral. Proyecta los datos preparados igual que al construir $S$.

Comprueba que las proporciones de todas las componentes sumen uno.

**Mini práctica.** Con los mismos datos, ¿cuántas componentes necesitas para conservar al menos el 95 %?

<details>
<summary>Ver respuesta</summary>

Necesitas dos. Con PC1 obtienes el 90 %. Al agregar PC2 acumulas 90 % + 10 % = 100 %.

</details>

## Ejercicio 2. Regresión lineal para ajustar una recta

Tenemos tres puntos y queremos dibujar una recta que se acerque a ellos. En (a) encontraremos la mejor recta, en (b) y (c) la usaremos, y en (d) veremos cómo dar un primer paso hacia ella partiendo de cero.

| Observación $i$ | Entrada $x_i$ | Respuesta real $y_i$ |
| --- | --- | --- |
| 1 | $0$ | $1$ |
| 2 | $1$ | $2$ |
| 3 | $2$ | $2$ |

- **Predicción** $\hat y_i=wx_i+b$. La pendiente $w$ indica cuánto cambia la predicción por unidad de $x$. El intercepto $b$ es la predicción en $x=0$.
- **Residuo** $e_i=\hat y_i-y_i$. Es la diferencia entre lo que predice la recta y lo que observamos.
- **Costo** $J(b,w)$. Junta los errores de la recta en un número que podemos usar para comparar distintos valores de $b$ y $w$.

```math
J(b,w)=\frac{1}{2n}\sum_{i=1}^{n}e_i^2
=\frac{1}{2n}\sum_{i=1}^{n}(wx_i+b-y_i)^2,\qquad n=3.
```

Elevar cada error al cuadrado evita que se cancelen errores de distinto signo y da más peso a errores grandes. Por ejemplo, en la primera observación el residuo es $w(0)+b-1=b-1$ y su cuadrado aporta $(b-1)^2$ a la suma.

### a) Deducir y calcular la recta de mínimos cuadrados

**1. Derivar el costo.** Una derivada parcial mide cómo cambia el costo al mover un parámetro y mantener el otro fijo. Los datos $x_i,y_i$ también quedan fijos.

Para derivar $e_i^2$ usamos la **regla de la cadena**. La función de afuera eleva al cuadrado y aporta el factor $2e_i$. La función de adentro es el residuo $e_i=wx_i+b-y_i$.

- Respecto de $b$, el término $wx_i-y_i$ es constante y $\partial e_i/\partial b=1$.
- Respecto de $w$, el término $b-y_i$ es constante y $\partial e_i/\partial w=x_i$.

La derivada de la función de afuera es $2e_i$. La multiplicamos por la derivada de adentro y sumamos los aportes de todas las observaciones.

```math
\begin{aligned}
\frac{\partial J}{\partial b}
&=\frac{1}{2n}\sum_i 2e_i\cdot 1
=\frac{1}{n}\sum_i (wx_i+b-y_i),\\
\frac{\partial J}{\partial w}
&=\frac{1}{2n}\sum_i 2e_i\cdot x_i
=\frac{1}{n}\sum_i (wx_i+b-y_i)x_i.
\end{aligned}
```

**¿Por qué $1/(2n)$?** El $1/n$ promedia los errores cuadrados y da el MSE. El $1/2$ extra hace que el $2$ de la derivada se cancele. Puedes verlo en la cuenta de arriba al pasar de $\frac{1}{2n}\sum_i 2e_i$ a $\frac{1}{n}\sum_i e_i$.

- **La mejor recta se conserva.** Usar MSE da el doble del costo para todas las rectas y las deja en el mismo orden.
- **El tamaño del paso cambia.** Con MSE las dos derivadas valen el doble. A la misma tasa de aprendizaje, el paso también sería el doble.

**2. Despejar $b$.** En un mínimo las dos derivadas son cero. Si alguna pendiente fuera distinta de cero, podríamos mover ese parámetro en la dirección que baja el costo.

Partimos con la derivada respecto de $b$. Al sumar, $\sum_i b=nb$, porque agregamos el mismo $b$ una vez por fila. Al dividir por $n$ aparecen las medias $\overline{x}=\sum_i x_i/n$ y $\overline{y}=\sum_i y_i/n$.

```math
\begin{aligned}
0 &=\sum_i(wx_i+b-y_i)\\
  &=w\sum_i x_i+nb-\sum_i y_i,\\
0 &= w\overline{x}+b-\overline{y},\\
b &= \overline{y}-w\overline{x}.
\end{aligned}
```

**3. Sustituir $b$ y despejar $w$.** Al poner ese intercepto, el residuo queda escrito en términos de las medias.

```math
wx_i+\overline{y}-w\overline{x}-y_i=w(x_i-\overline{x})-(y_i-\overline{y}).
```

La primera ecuación ya nos dice que $\sum_i e_i=0$. Por eso, en la segunda podemos cambiar $x_i$ por $x_i-\overline{x}$, ya que estaríamos restando $\overline{x}\sum_i e_i=0$. Así aparecen las desviaciones respecto de las medias en ambos factores.

```math
\begin{aligned}
0 &= \sum_i e_ix_i
   =\sum_i e_i(x_i-\overline{x}),\\
  &=\sum_i [w(x_i-\overline{x})-(y_i-\overline{y})](x_i-\overline{x}),\\
  &=w\sum_i(x_i-\overline{x})^2-\sum_i(x_i-\overline{x})(y_i-\overline{y}).
\end{aligned}
```

Si las entradas varían, $\sum_i(x_i-\overline{x})^2>0$ y se puede dividir.

```math
w=\frac{\sum_i(x_i-\overline{x})(y_i-\overline{y})}{\sum_i(x_i-\overline{x})^2}.
```

**4. Poner los números de la tabla.** Con $\overline{x}=(0+1+2)/3=1$ y $\overline{y}=(1+2+2)/3=5/3$, calculamos los términos que aparecen arriba y sumamos por columna.

| $i$ | $x_i-\overline{x}$ | $y_i-\overline{y}$ | Producto de desviaciones | $(x_i-\overline{x})^2$ |
| --- | --- | --- | --- | --- |
| 1 | $-1$ | $-2/3$ | $2/3$ | $1$ |
| 2 | $0$ | $1/3$ | $0$ | $0$ |
| 3 | $1$ | $1/3$ | $1/3$ | $1$ |
| **Suma** | $0$ | $0$ | $1$ | $2$ |

```math
w=\frac12,\qquad b=\frac53-\frac12(1)=\frac76,\qquad
\hat y(x)=\frac12x+\frac76.
```

<details>
<summary>Demostración de que esta recta da el costo mínimo</summary>

Si cambias los parámetros en $\Delta b,\Delta w$, el residuo pasa a ser $e_i+\Delta b+\Delta w x_i$. Al elevarlo al cuadrado, los términos cruzados suman $2\Delta b\sum_i e_i+2\Delta w\sum_i e_i x_i=0$ por las dos ecuaciones anteriores.

Lo único que se agrega al costo es $\frac1{2n}\sum_i(\Delta b+\Delta w x_i)^2\ge0$. Con al menos dos entradas distintas, esa suma es cero solo si ambos cambios son cero. Por eso la recta que encontramos es el mínimo único para estos datos.

</details>

### b) Calcular predicciones, residuos, MSE y costo

Sustituimos cada $x_i$ en la recta y restamos el valor real. Para $x_2=1$, por ejemplo, la predicción es $1/2+7/6=5/3$ y el residuo es $5/3-2=-1/3$.

| $x_i$ | $y_i$ | $\hat y_i$ | $e_i=\hat y_i-y_i$ | $e_i^2$ |
| --- | --- | --- | --- | --- |
| $0$ | $1$ | $7/6$ | $1/6$ | $1/36$ |
| $1$ | $2$ | $5/3$ | $-1/3$ | $1/9$ |
| $2$ | $2$ | $13/6$ | $1/6$ | $1/36$ |

La suma de los cuadrados de los residuos, o SSE, vale $1/36+4/36+1/36=6/36=1/6$.

```math
\mathrm{MSE}=\frac{1/6}{3}=\frac1{18}\approx0{,}0556,\qquad
J=\frac{1/6}{2\cdot3}=\frac1{36}\approx0{,}0278.
```

Puedes revisar las condiciones del óptimo con la tabla. Los residuos suman $1/6-1/3+1/6=0$, y al multiplicarlos por sus $x_i$ se obtiene $0-1/3+2/6=0$. Tanto el MSE como $J$ se calcularon sobre los datos usados para ajustar la recta.

### c) Predecir en $x=3$ e interpretar la recta

```math
\hat y(3)=\frac12(3)+\frac76=\frac96+\frac76=\frac83\approx2{,}6667.
```

Por cada unidad que aumenta $x$, la recta predice un aumento de $0{,}5$ unidades en la respuesta. En $x=0$ predice $7/6$, el intercepto. Como las entradas observadas están entre $0$ y $2$, la predicción en $3$ es una **extrapolación**. Para calcular su error haría falta observar el valor real de $y$ en ese punto.

### d) Dar un paso de descenso de gradiente y comparar

El **gradiente** junta las dos pendientes del costo. Para dar un paso que apunte hacia un costo menor, a cada parámetro le restamos su pendiente multiplicada por la tasa $\alpha=0{,}1$. Esa tasa determina el tamaño del paso.

**1. Calcular las dos pendientes en el mismo punto.** El superíndice $(0)$ indica el inicio. Con $b^{(0)}=w^{(0)}=0$, las predicciones son $(0,0,0)$ y los residuos son $(-1,-2,-2)$.

```math
g_b=\frac{-1-2-2}{3}=-\frac53,\qquad
g_w=\frac{(-1)(0)+(-2)(1)+(-2)(2)}{3}=-2.
```

**2. Actualizar los parámetros.** Usamos las dos pendientes que acabamos de calcular. El superíndice $(1)$ indica que ya dimos un paso.

```math
\begin{aligned}
b^{(1)} &= b^{(0)}-\alpha g_b
=0-\frac1{10}\left(-\frac53\right)=\frac16\approx0{,}1667,\\
w^{(1)} &= w^{(0)}-\alpha g_w
=0-\frac1{10}(-2)=\frac15=0{,}2.
\end{aligned}
```

Al restar pendientes negativas, ambos parámetros suben.

**3. Comprobar si bajó el costo.** La recta después de un paso es $\hat y(x)=x/5+1/6$. La evaluamos en los tres puntos y obtenemos los residuos $(-5/6,-49/30,-43/30)$.

```math
\begin{aligned}
J_{\text{inicial}}&=\frac{1+4+4}{6}=\frac32,\\
J_{\text{primer paso}}&=\frac{(5/6)^2+(49/30)^2+(43/30)^2}{6}=\frac{65}{72},\\
J_{\text{óptimo}}&=\frac1{36}.
\end{aligned}
```

El primer paso bajó el costo de $1{,}5$ a aproximadamente $0{,}9028$. La solución analítica, con $b=7/6$ y $w=1/2$, alcanza $0{,}0278$. Para dar otro paso habría que volver a calcular predicciones, residuos y derivadas usando la recta nueva.

**Para un ejercicio parecido**

1. Escribe el residuo y la definición exacta del costo. Deriva manteniendo fijos los datos.
2. Si te piden el mínimo, iguala las derivadas a cero y despeja los parámetros.
3. Si te piden una iteración, calcula todo el gradiente en el punto inicial y actualiza los parámetros juntos.

**Mini práctica.** Si el costo fuera el MSE y quisieras obtener exactamente el mismo primer paso desde $(b,w)=(0,0)$, ¿qué tasa usarías?

<details>
<summary>Ver respuesta</summary>

Usarías $\alpha=0{,}05$. La derivada del MSE es el doble de la de $J$, así que $(0{,}05)(2\nabla J)=(0{,}1)\nabla J$. El producto de la tasa por el gradiente queda igual.

</details>

## Ejercicio 3. Regresión logística con regularización L2

Queremos asignar cada observación a la clase 0 o 1. Primero calcularemos las probabilidades y usaremos esas probabilidades para medir el error. Luego veremos cuánto cambia el peso $w$ al aplicar una penalización llamada **L2**.

Tenemos dos observaciones, $(x_1,y_1)=(-1,0)$ y $(x_2,y_2)=(1,1)$. Partimos de $w^{(0)}=1$ y $b^{(0)}=0$. Usaremos una tasa de aprendizaje $\alpha=0{,}5$ y una intensidad de regularización $\lambda=0{,}2$.

### a) Calcular los puntajes, las probabilidades y las clases

1. **Puntaje.** Calculamos $a_i=wx_i+b$. Puede tomar cualquier valor real.
2. **Probabilidad.** Aplicamos la sigmoide $p_i=\sigma(a_i)=1/(1+\exp(-a_i))$. Aquí $\exp(t)$ significa $e^t$ y $p_i$ es la probabilidad que el modelo asigna a la clase 1. La sigmoide lleva un puntaje real a un valor entre 0 y 1. En $a_i=0$, da $p_i=1/2$.
3. **Clase predicha.** Aplicamos el umbral del enunciado. Si $p_i\ge0{,}5$, entonces $\hat y_i=1$. Si $p_i<0{,}5$, entonces $\hat y_i=0$.

| $x_i$ | Clase real $y_i$ | Puntaje $a_i$ | Probabilidad $p_i$ | Clase predicha $\hat y_i$ |
| --- | --- | --- | --- | --- |
| $-1$ | $0$ | $1(-1)+0=-1$ | $1/(1+\exp(1))\approx0{,}2689$ | $0$ |
| $1$ | $1$ | $1(1)+0=1$ | $1/(1+\exp(-1))\approx0{,}7311$ | $1$ |

Las dos predicciones coinciden con sus etiquetas. En la primera fila, $0{,}2689$ es la probabilidad de la clase 1. La probabilidad de la clase 0 es su complemento, aproximadamente $0{,}7311$.

### b) Calcular la pérdida, la penalización L2 y el costo inicial

**1. Mirar cuánta probabilidad recibió la clase real.** Acertar una etiqueta con probabilidad $0{,}51$ y acertarla con $0{,}99$ cuenta igual si solo miras aciertos. La pérdida logística sí distingue esos casos porque calcula $-\ln$ de la probabilidad asignada a la clase real.

```math
\ell_i(p_i)=-[y_i\ln p_i+(1-y_i)\ln(1-p_i)].
```

El logaritmo $\ln$ tiene base $e$. Para leer la fórmula, reemplaza $y_i$ por su etiqueta. Uno de los dos sumandos queda multiplicado por cero.

| Clase real | Término que queda | Probabilidad de la clase real |
| --- | --- | --- |
| $y_i=0$ | $-\ln(1-p_i)$ | $1-p_i$ |
| $y_i=1$ | $-\ln p_i$ | $p_i$ |

Cuanto más cerca de 1 quede la probabilidad de la clase real, más cerca de cero queda la pérdida. Si el modelo le da muy poca probabilidad, la pérdida crece.

En estos dos datos $1-p_1=p_2$. Por eso las pérdidas coinciden.

```math
\ell_1=-\ln(1-p_1),\qquad
\ell_2=-\ln p_2=\ln(1+\exp(-1))\approx0{,}3132617.
```

**2. Promediar las pérdidas y sumar L2.** Aquí tenemos un solo peso $w$. L2 agrega un término que crece con el cuadrado de ese peso.

```math
J(b,w)=L(b,w)+\frac{\lambda}{2}w^2,\qquad
L(b,w)=\frac{\ell_1+\ell_2}{2}.
```

En $(b,w)=(0,1)$, el promedio es $L\approx0{,}3132617$. El peso aporta $(0{,}2/2)(1)^2=0{,}1$ y el intercepto aporta cero. Sumamos la penalización **una sola vez** al promedio.

```math
J(0,1)\approx0{,}3132617+0{,}1\approx0{,}4133.
```

### c) Derivar el costo y actualizar $w$ y $b$

**1. Identificar la cadena.** Si cambiamos $w$ o $b$, cambia el puntaje $a_i$. Eso cambia la probabilidad $p_i$ y finalmente la pérdida $\ell_i$. La regla de la cadena multiplica las derivadas de esos pasos.

- Del puntaje a la probabilidad, $\frac{dp_i}{da_i}=p_i(1-p_i)$.
- De la probabilidad a la pérdida, $\frac{d\ell_i}{dp_i}=\frac{p_i-y_i}{p_i(1-p_i)}$.

<details>
<summary>Ver cómo se derivan la sigmoide y la pérdida, línea por línea</summary>

En estas dos cuentas omitimos el subíndice $i$ para poder leer mejor las expresiones.

**Sigmoide.** Escribimos $p=(1+\exp(-a))^{-1}$. Al derivar la potencia $-1$, aparece $-(1+\exp(-a))^{-2}$. Al derivar la función de adentro, aparece $-\exp(-a)$. Los dos signos negativos se cancelan.

$\frac{dp}{da}=-(1+\exp(-a))^{-2}[-\exp(-a)]$\
$=\frac{\exp(-a)}{(1+\exp(-a))^2}$\
$=\frac{1}{1+\exp(-a)}\cdot\frac{\exp(-a)}{1+\exp(-a)}=p(1-p)$.

El segundo factor es $1-p$ porque $1-1/(1+\exp(-a))=\exp(-a)/(1+\exp(-a))$.

**Pérdida.** La etiqueta $y$ queda fija. La derivada de $\ln p$ es $1/p$. En $\ln(1-p)$ aparece además un $-1$ al derivar $1-p$.

$\frac{d\ell}{dp}=-\left[y\frac{1}{p}+(1-y)\frac{-1}{1-p}\right]$\
$=-\frac{y}{p}+\frac{1-y}{1-p}$\
$=\frac{-y(1-p)+(1-y)p}{p(1-p)}=\frac{p-y}{p(1-p)}$.

En el numerador, $-y+yp+p-yp=p-y$.

</details>

**2. Multiplicar y simplificar.** En el producto, el factor $p_i(1-p_i)$ aparece arriba y abajo. Al cancelarlo queda una expresión corta.

```math
\frac{d\ell_i}{da_i}
=\frac{d\ell_i}{dp_i}\frac{dp_i}{da_i}
=\frac{p_i-y_i}{p_i(1-p_i)}p_i(1-p_i)
=p_i-y_i.
```

**3. Llegar a los parámetros y agregar L2.** En $a_i=wx_i+b$, un cambio en $w$ entra multiplicado por $x_i$ y un cambio en $b$ entra multiplicado por $1$. Por eso las derivadas usan $(p_i-y_i)x_i$ y $(p_i-y_i)$, respectivamente.

Promediamos los aportes de los $n=2$ datos. Al derivar L2, el término adicional es $\frac{\lambda}{2}(2w)=\lambda w$ para $w$ y cero para $b$. Fíjate en que usamos la probabilidad $p_i$ antes de aplicar el umbral.

```math
\frac{\partial J}{\partial w}=\frac1n\sum_i(p_i-y_i)x_i+\lambda w,\qquad
\frac{\partial J}{\partial b}=\frac1n\sum_i(p_i-y_i).
```

**4. Evaluar el gradiente y dar el paso.** Sea $q=1/(1+\exp(1))\approx0{,}2689414$. En el punto inicial, $p_1=q$ y $p_2=1-q$.

| Observación | $p_i-y_i$ | $(p_i-y_i)x_i$ |
| --- | --- | --- |
| 1 | $q-0=q$ | $q(-1)=-q$ |
| 2 | $(1-q)-1=-q$ | $(-q)(1)=-q$ |

Para $b$ sumamos $q$ y $-q$, que se cancelan por la simetría de estos datos y del punto inicial. Para $w$ promediamos $-q$ y $-q$ y agregamos $0{,}2(1)$.

```math
g_b=\frac{q-q}{2}=0,\qquad
g_w=\frac{-q-q}{2}+0{,}2=-q+0{,}2\approx-0{,}0689414.
```

```math
b^{(1)}=0-0{,}5(0)=0,\qquad
w^{(1)}=1-0{,}5(0{,}2-q)\approx1{,}0345.
```

**Comprueba el paso.** Con $b=0$, los puntajes son $-w$ y $w$. Ambas clases reales reciben probabilidad $\sigma(w)$, así que $L=-\ln(\sigma(w))=\ln(1+\exp(-w))$.

Con el peso actualizado sin redondear, $L\approx0{,}3041073$ y $(\lambda/2)w^2\approx0{,}1070130$. El costo bajó de $0{,}4133$ a $0{,}4111$.

### d) Repetir el paso con $\lambda=0$ y explicar el efecto

Volvemos a $(b^{(0)},w^{(0)})=(0,1)$. Como los puntajes iniciales son los mismos, la parte del gradiente que viene de los datos sigue siendo $-q$ para $w$ y $0$ para $b$. Con $\lambda=0$, el peso queda

```math
w^{(1)}_{\text{sin L2}}=1-0{,}5(-q)\approx1{,}1345,\qquad b^{(1)}=0.
```

En este paso actúan dos aportes sobre el peso.

- **Los datos** suman $0{,}5q\approx0{,}1345$.
- **L2** resta $\alpha\lambda w^{(0)}=(0{,}5)(0{,}2)(1)=0{,}1$ cuando usamos la penalización.

Así, el peso crece hasta $1{,}0345$ con L2 y hasta $1{,}1345$ sin L2. La penalización empuja el peso hacia cero y el cambio total depende también de los datos.

### e) Predecir la clase para $x=2$ con los parámetros actualizados

Usamos $w^{(1)}=1-0{,}5(0{,}2-q)$ y $b^{(1)}=0$. Sustituimos en el puntaje, aplicamos la sigmoide y finalmente el umbral.

```math
a=2w^{(1)}+0\approx2{,}0689414,\qquad
p=\frac{1}{1+\exp(-a)}\approx0{,}8878,\qquad
\hat y=1.
```

El valor $0{,}8878$ es la probabilidad asignada a la clase 1. Como supera $0{,}5$, la clase predicha es 1.

### f) Comparar el costo de los dos candidatos

El enunciado pide comparar $(b,w)=(0,1)$ y $(0,2)$ usando el costo con $\lambda=0{,}2$. Ambos pesos son positivos, así que las observaciones $x=-1$ y $x=1$ quedan a lados distintos del umbral. En ambos casos acertamos las dos etiquetas. Al cambiar de peso sí cambia la probabilidad asignada a cada clase real y el término cuadrático de L2.

| $(b,w)$ | Pérdida promedio $L=\ln(1+\exp(-w))$ | Penalización $0{,}1w^2$ | Costo $J$ |
| --- | --- | --- | --- |
| $(0,1)$ | $\ln(1+\exp(-1))\approx0{,}3133$ | $0{,}1$ | $0{,}4133$ |
| $(0,2)$ | $\ln(1+\exp(-2))\approx0{,}1269$ | $0{,}4$ | $0{,}5269$ |

Al pasar a $w=2$, la pérdida de datos baja unos $0{,}1863$ y la penalización sube $0{,}3$. El segundo cambio pesa más, por lo que $w=1$ da el menor costo **entre estos dos candidatos**. De hecho, en el inciso c encontramos un peso intermedio cuyo costo es todavía menor.

**Para un ejercicio parecido**

1. Arma una tabla con puntaje, probabilidad, clase predicha y pérdida por fila.
2. Usa las probabilidades para el gradiente y las clases para contar aciertos. Revisa qué parámetros se penalizan y con qué factor.
3. Si comparas actualizaciones, parte con los mismos parámetros y cambia solo lo que indique el enunciado.

**Mini práctica.** Repite el primer paso desde $(b,w)=(0,1)$ con $\lambda=0{,}4$ y tasa $0{,}5$. ¿Qué les pasa a $b$ y $w$?

<details>
<summary>Ver respuesta</summary>

Las probabilidades iniciales se mantienen. Así, $g_b=0$ y $g_w=-q+0{,}4\approx0{,}1310586$. El nuevo intercepto sigue en $b^{(1)}=0$ y el peso baja a $w^{(1)}=1-0{,}5(0{,}4-q)\approx0{,}9345$. Aquí la penalización supera en magnitud al empuje del gradiente de los datos.

</details>

## Ejercicio 4. Pipeline de clasificación con Iris

Tenemos 100 flores de dos especies, setosa (clase 0) y versicolor (clase 1). Cada flor tiene cuatro medidas en centímetros. Queremos usar esas medidas para predecir a qué especie pertenece una flor.

**Recorrido de una flor**

Cuatro medidas → cuatro medidas estandarizadas → dos componentes de PCA → probabilidades y clase predicha.

Verás qué hace el código siguiendo los incisos (a) a (e). Puedes abrir el programa completo para copiarlo y después volver a cada paso.

<details>
<summary>Ver y copiar el código completo</summary>

Copia el bloque en una celda de Colab o ejecútalo en Python con NumPy y scikit-learn. Iris viene incluido en scikit-learn.

```python
import numpy as np
from sklearn.datasets import load_iris
from sklearn.model_selection import train_test_split
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import StandardScaler
from sklearn.decomposition import PCA
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import accuracy_score, confusion_matrix

# a) Cargar, filtrar y separar las observaciones.
datos = load_iris()
mascara = datos.target < 2
X = datos.data[mascara]
y = datos.target[mascara]
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.20, random_state=42, stratify=y
)

# b) Construir y ajustar las tres etapas.
modelo = Pipeline([
    ("escalado", StandardScaler()),
    ("pca", PCA(n_components=2, svd_solver="full")),
    ("clasificador", LogisticRegression(C=1.0, solver="lbfgs", max_iter=1000)),
])
modelo.fit(X_train, y_train)

# c) Predecir y evaluar.
y_pred = modelo.predict(X_test)
print("Entrenamiento:", X_train.shape, "Clases:", np.bincount(y_train))
print("Prueba:", X_test.shape, "Clases:", np.bincount(y_test))
print("Exactitud:", accuracy_score(y_test, y_pred))
print("Matriz de confusión:")
print(confusion_matrix(y_test, y_pred, labels=[0, 1]))

# d) Inspeccionar las probabilidades y seguir el recorrido de la primera flor.
probabilidades = modelo.predict_proba(X_test)
print("Orden de clases:", modelo.classes_)
print("Real:", y_test[0], "Predicha:", y_pred[0])
print("Probabilidades:", np.round(probabilidades[0], 4))

escalador = modelo.named_steps["escalado"]
pca = modelo.named_steps["pca"]
clasificador = modelo.named_steps["clasificador"]
primera = X_test[:1]
primera_escalada = escalador.transform(primera)
primera_pca = pca.transform(primera_escalada)
print("Medidas:", primera[0])
print("Estandarización:", np.round(primera_escalada[0], 5))
print("Dirección de PC1:", np.round(pca.components_[0], 5))
print("Componentes:", np.round(primera_pca[0], 4))
print("Pesos:", np.round(clasificador.coef_[0], 4))
print("Intercepto:", np.round(clasificador.intercept_[0], 4))
print("Puntaje:", np.round(clasificador.decision_function(primera_pca)[0], 4))
print("Varianza explicada por componente:", np.round(pca.explained_variance_ratio_, 4))
```

Los resultados se reprodujeron con scikit-learn 1.6.1 y NumPy 2.1.3. Los últimos decimales pueden variar según el entorno.

</details>

### a) Importar, filtrar y separar el 20 % para prueba

**1. Cargar Iris con `load_iris()` y quedarnos con las clases 0 y 1.**

- `datos.data` guarda las medidas de cada flor. `datos.target` guarda su clase.
- `mascara = datos.target < 2` produce un `True` para cada setosa o versicolor. La máscara indica qué filas conservar.
- `datos.data[mascara]` toma esas filas para formar `X`. `datos.target[mascara]` toma las etiquetas de las mismas flores para formar `y`. Al final, `X` tiene forma `(100, 4)` e `y` tiene forma `(100,)`, con una etiqueta por flor.

<details>
<summary>Qué trae cada importación</summary>

| Importación | Qué usamos de ella |
| --- | --- |
| `numpy as np` | `np.bincount` cuenta cuántas etiquetas hay de cada clase. `np.round` redondea los números que mostramos. |
| `load_iris` | Carga las medidas y las etiquetas del conjunto Iris que viene con la biblioteca. |
| `train_test_split` | Parte las observaciones y sus etiquetas en entrenamiento y prueba, manteniéndolas alineadas. |
| `Pipeline` | Encadena los pasos en el orden en que se aplican. |
| `StandardScaler` | Resta la media de cada columna y divide por su desviación estándar, estimadas durante el ajuste. |
| `PCA` | Encuentra los ejes de mayor varianza y proyecta los datos sobre ellos. |
| `LogisticRegression` | Ajusta pesos e intercepto para clasificar. |
| `accuracy_score` y `confusion_matrix` | Comparan las etiquetas reales con las predichas. |

</details>

**2. Separar los conjuntos con `train_test_split`.** Usaremos 80 flores para entrenar y 20 para comprobar qué tan bien predice el modelo con flores que quedaron aparte.

| Argumento de `train_test_split` | Efecto |
| --- | --- |
| `test_size=0.20` | Reserva 20 flores para prueba y deja 80 para entrenamiento. |
| `random_state=42` | Fija la semilla para poder repetir la misma separación. |
| `stratify=y` | Hace la separación teniendo en cuenta las clases. Aquí hay 50 flores de cada especie, por lo que quedan 40 de cada clase en entrenamiento y 10 de cada clase en prueba. |

`X_train` y `X_test` contienen las medidas de sus respectivas flores. `y_train` y `y_test` contienen sus etiquetas en el mismo orden. Por ejemplo, `X_test[0]` y `y_test[0]` se refieren a la misma flor.

### b) Construir el Pipeline con escalado, PCA y regresión logística

Un **Pipeline** ejecuta las etapas en orden y le pasa a cada una el resultado de la anterior. En el código, cada etapa tiene un nombre y un objeto que hace el trabajo. El nombre permite encontrarla más tarde con `modelo.named_steps`. [Documentación de Pipeline](https://scikit-learn.org/1.8/modules/generated/sklearn.pipeline.Pipeline.html)

| Etapa | Qué aprende con entrenamiento | Qué entrega |
| --- | --- | --- |
| `StandardScaler()` | La media $\mu_j$ y la desviación estándar $s_j$ de cada una de las cuatro columnas. | Cuatro columnas con valores $(x_{ij}-\mu_j)/s_j$. |
| `PCA` | El centro de los datos que recibe y sus dos direcciones de mayor varianza. | Dos coordenadas por flor, PC1 y PC2. |
| `LogisticRegression` | Un peso por componente y un intercepto. Usa las componentes y etiquetas de entrenamiento. | Una clase por flor con `predict`, o una probabilidad por clase con `predict_proba`. |

<details>
<summary>Por qué escalamos y qué significan los argumentos de cada etapa</summary>

- **Escalado.** En el ejercicio 1 centramos las columnas y conservamos la escala original. Aquí `StandardScaler` también pone sus dispersiones en una escala común antes de PCA. Usa el divisor $n$ al calcular la varianza para escalar. El $n-1$ del ejercicio 1 correspondía a la covarianza muestral pedida ahí. [Documentación de StandardScaler](https://scikit-learn.org/1.8/modules/generated/sklearn.preprocessing.StandardScaler.html)
- **PCA.** `n_components=2` conserva dos direcciones. `svd_solver="full"` las calcula mediante la descomposición completa en valores singulares de la matriz centrada. Es otra manera de obtener los ejes que encontramos con la matriz de covarianza en el ejercicio 1. [Documentación de PCA](https://scikit-learn.org/1.8/modules/generated/sklearn.decomposition.PCA.html)
- **Regresión logística.** Esta configuración usa L2. `C=1.0` fija su intensidad inversa, `solver="lbfgs"` elige el algoritmo que busca los parámetros y `max_iter=1000` fija su máximo de iteraciones. La tasa $\alpha$ que usamos en el ejercicio 3 era para el paso manual. [Documentación de LogisticRegression](https://scikit-learn.org/1.8/modules/generated/sklearn.linear_model.LogisticRegression.html)

</details>

### c) Entrenar, predecir y evaluar

1. **Entrenar con `modelo.fit(X_train, y_train)`.** El escalador aprende con 80 flores. PCA recibe esas 80 flores ya escaladas. El clasificador usa sus dos componentes y sus etiquetas reales.
2. **Predecir con `modelo.predict(X_test)`.** El modelo usa el escalado y la proyección aprendidos para devolver 20 etiquetas, una por cada flor de prueba.
3. **Evaluar con las etiquetas reales.** `accuracy_score(y_test, y_pred)` divide los aciertos por el total de predicciones. La matriz de confusión cuenta cuántas flores hubo de cada combinación de clase real y clase predicha. `labels=[0, 1]` fija el orden de filas y columnas.

| Clase real ↓ / predicha → | 0 | 1 |
| --- | --- | --- |
| 0 | 10 | 0 |
| 1 | 0 | 10 |

Las filas son las clases reales y las columnas, las predichas. La diagonal contiene los aciertos, así que la exactitud es $(10+10)/20=1{,}0$. El 100 % describe el resultado en estas 20 flores de prueba de dos especies, con esta partición.

### d) Leer las probabilidades y la clase de la primera flor

`modelo.predict_proba(X_test)` devuelve una fila por flor y una columna por clase. En este caso su forma es `(20, 2)`. Para saber qué clase corresponde a cada columna, mira `modelo.classes_`. Aquí el orden es `[0, 1]`.

Para la primera flor, `probabilidades[0]` da $0{,}1502$ para setosa y $0{,}8498$ para versicolor. Las dos probabilidades suman 1. La clase predicha es 1 y coincide con su etiqueta real.

| Etapa | Resultado aproximado para la primera flor |
| --- | --- |
| Medidas originales, en orden largo y ancho de sépalo, largo y ancho de pétalo | $[4{,}9;\ 2{,}4;\ 3{,}3;\ 1{,}0]$ |
| Salida de `escalador.transform` | $[-0{,}9039;\ -1{,}4088;\ 0{,}3050;\ 0{,}3835]$ |
| Salida de `pca.transform` | $\mathrm{PC1}\approx0{,}4770,\ \mathrm{PC2}\approx-1{,}6274$ |
| Puntaje de `clasificador.decision_function` | $a\approx1{,}7332$ |
| Probabilidades en el orden `[0, 1]` | $[0{,}1502;\ 0{,}8498]$ |

<details>
<summary>Seguir las cuentas de la primera flor, desde las medidas hasta la probabilidad</summary>

`X_test[:1]` conserva la primera flor como una matriz de una fila y cuatro columnas. Ese es el formato que esperan las etapas. `X_test[0]` devuelve un vector de cuatro entradas.

**1. Escalar.** Para la primera medida, la media de entrenamiento es $5{,}4575$ y la desviación estándar es $0{,}6168012$. La cuenta $(4{,}9-5{,}4575)/0{,}6168012$ da aproximadamente $-0{,}9039$.

**2. Proyectar.** PCA resta su centro de entrenamiento. Es prácticamente cero porque el escalador ya centró las columnas. Luego hace un producto punto por dirección, tal como en el ejercicio 1.

Las cuatro entradas de `pca.components_[0]` son los coeficientes de la primera dirección. Para verificar su proyección usamos los números que imprime el código con cinco decimales.

$z_1\approx (-0{,}90386)(0{,}48539)+(-1{,}40881)(-0{,}37775)$\
$+(0{,}30505)(0{,}56138)+(0{,}38346)(0{,}55366)\approx 0{,}4770$.

La segunda coordenada se obtiene del mismo modo con `pca.components_[1]`. El signo de una dirección de PCA puede invertirse junto con el de su coordenada. Si el clasificador se ajusta sobre esa coordenada invertida, puede compensar el cambio con el signo del peso correspondiente.

**3. Calcular el puntaje y aplicar la sigmoide.** Usamos la misma idea del ejercicio 3 con dos pesos, uno por componente. `clasificador.coef_[0]` contiene los pesos y `clasificador.intercept_[0]` contiene el intercepto.

$a=w_1\mathrm{PC1}+w_2\mathrm{PC2}+b$\
$\approx (2{,}4449)(0{,}4770)+(-0{,}2330)(-1{,}6274)+0{,}1877$.

Con los valores completos se obtiene $a\approx1{,}7332$ y $\sigma(a)\approx0{,}8498$ para la clase 1. Su complemento, $1-\sigma(a)\approx0{,}1502$, corresponde a la clase 0. `np.round` redondea lo que se imprime. El cálculo usa todos los decimales disponibles.

</details>

**Dos porcentajes, dos preguntas.** `pca.explained_variance_ratio_` dice cuánto conservamos de las medidas estandarizadas de entrenamiento. Aquí las componentes explican $0{,}7740$ y $0{,}1889$, que juntas suman cerca de 96,29 %. La exactitud del 100 % responde cuántas etiquetas acertó el clasificador entre las 20 flores de prueba.

### e) Explicar qué aprende cada etapa, cómo evitar la fuga y qué cambia al reducir C

**1. Separar primero.** Las 20 flores de prueba representan datos nuevos. Si calculamos la media con las 100 flores, las de prueba ya influyeron en el escalado que se usa para entrenar. Lo mismo pasa si PCA aprende direcciones con las 100. Eso se llama **fuga de información** y puede ocurrir aunque solo usemos las medidas.

**2. Aprender con entrenamiento y usar lo aprendido en prueba.** `fit` aprende parámetros y `transform` los usa para convertir observaciones. Al predecir, el Pipeline aplica a prueba las transformaciones guardadas y consulta al clasificador ya ajustado.

| Datos | Medidas originales | Después del escalado | Después de PCA | Quién aporta información al ajuste |
| --- | --- | --- | --- | --- |
| Entrenamiento | `(80, 4)` | `(80, 4)` | `(80, 2)` | Las medidas ajustan el escalador y PCA. Los componentes y las etiquetas ajustan el clasificador. |
| Prueba | `(20, 4)` | `(20, 4)` | `(20, 2)` | Las tres etapas reutilizan lo aprendido en entrenamiento. Las etiquetas sirven para evaluar. |

**3. Entender qué pasa si bajamos `C`.** Un `C` menor aumenta la fuerza de L2. Eso favorece pesos más pequeños y puede cambiar las probabilidades, las clases predichas y la exactitud.

<details>
<summary>Cómo se relacionan C y λ y cómo elegir C</summary>

En el ejercicio 3 escribimos $\lambda w^2/2$ junto a la pérdida **promedio**. Scikit-learn, para el caso binario sin ponderaciones de este Pipeline, escribe $\lVert w\rVert_2^2/(2nC)$ junto a ese promedio. Aquí $\lVert w\rVert_2^2=w_1^2+w_2^2$ y $n$ es la cantidad de flores de entrenamiento.

Al comparar ambas expresiones, el factor equivalente a $\lambda$ es $1/(nC)$. Si el costo usa la **suma** de pérdidas, el factor junto al término cuadrático cambia. Para convertir entre $\lambda$ y `C`, primero revisa la escala del costo. [Costo de la regresión logística binaria en scikit-learn](https://scikit-learn.org/1.8/modules/linear_model.html#binary-case)

En este ejercicio ya están fijados `C` y el número de componentes. Si tuvieras que elegirlos, podrías usar validación cruzada. Eso consiste en turnar qué parte de las 80 flores se usa para ajustar el Pipeline completo y qué parte para comparar configuraciones. Las 20 flores de prueba quedan para evaluar la configuración final.

</details>

**Para un ejercicio parecido**

1. Identifica las formas de `X` e `y` y el orden de las clases.
2. Separa antes de ajustar. Encadena el preprocesamiento con el clasificador y anota las dimensiones a la salida de cada paso.
3. Evalúa con las etiquetas de prueba y lee las probabilidades usando `classes_`.

**Mini práctica.** Encuentra la fuga en este fragmento. ¿En qué orden harías los pasos?

```python
X_escalado = StandardScaler().fit_transform(X)
X_train, X_test, y_train, y_test = train_test_split(
    X_escalado, y, test_size=0.20, random_state=42, stratify=y
)
```

<details>
<summary>Ver respuesta</summary>

La primera línea calcula medias y desviaciones con todas las flores, incluidas las que después van a prueba. Primero separa `X` e `y` originales. Luego ajusta el Pipeline con `modelo.fit(X_train, y_train)`. Al llamar a `modelo.predict(X_test)`, el Pipeline transforma la prueba con las estadísticas y direcciones aprendidas en entrenamiento.

</details>

## Material de referencia

- Universidad Católica del Norte. *Ayudantía de Machine Learning. PCA, regresión lineal, regresión logística y Pipeline*. PDF de preparación distribuido en el curso.
