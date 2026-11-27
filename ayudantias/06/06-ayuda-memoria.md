# Ayuda memoria para la Prueba 2

[← Anterior](05-ensambles-y-validacion.md) · [Inicio](README.md) · [Simulacro](08-simulacro.md) · [Siguiente: Ejercicios propuestos →](07-ejercicios-propuestos.md)

Antes de calcular, anota qué piden, qué datos te dan y qué convención usa el enunciado. Redondea al final y escribe una frase sobre lo que significa el resultado.

## Preparación y PCA

- **Seleccionar** conserva columnas. **Extraer** construye coordenadas. Umbral de varianza revisa dispersión, Pearson asociación lineal, chi-cuadrado asociación con conteos, RFE o selección secuencial comparan subconjuntos y L1 puede dejar pesos en cero.
- Para comparar fuerza con Pearson, usa la magnitud de $r$. Para chi-cuadrado, calcula el esperado $E$ multiplicando los totales de fila y columna y dividiendo por el total. Suma $(O-E)^2/E$ por celda. En una tabla de $r$ filas y $c$ columnas hay $(r-1)(c-1)$ grados de libertad.
- **PCA.** Centra, calcula $S=X_c^\top X_c/(n-1)$ si piden covarianza muestral, ordena autovalores y normaliza autovectores. La varianza retenida es la suma de autovalores conservados dividida por la suma total. Proyecta con $Z=X_cQ$. Para reconstruir, suma la media a la combinación de patrones.
- **Mapas 2D.** t-SNE mira vecindades, UMAP usa un grafo de vecinos, Isomap usa caminos en el grafo y LLE preserva combinaciones locales. Usa métricas en datos reservados para evaluar predicciones.

[Volver a Preparación de datos y PCA](01-preparacion-y-pca.md)

## Modelos lineales

- **Recta.** $\hat y=wx+b$, residuo $e=\hat y-y$. Con una columna de unos y $\theta=(b,w)^\top$, resuelve $X^\top X\theta=X^\top y$ para mínimos cuadrados.
- **Un paso.** Con $J=\mathrm{MSE}/2$, $g_b=\sum_i e_i/n$ y $g_w=\sum_i e_ix_i/n$. Con $J=\mathrm{MSE}$, duplica ambos. Calcula los dos antes de aplicar $\theta_{\mathrm{nueva}}=\theta_{\mathrm{vieja}}-\alpha\nabla J$.
- **Regularización.** L1 usa valores absolutos. L2 usa cuadrados. Si la penalización es $\lambda w^2/2$, agrega $\lambda w$ al gradiente de $w$. Fíjate si el intercepto se penaliza.
- **Logística.** Puntaje $a=wx+b$, probabilidad $p=\sigma(a)=1/(1+\exp(-a))$, clase con el umbral pedido. Para BCE, si $y=1$ usa $-\ln p$ y si $y=0$ usa $-\ln(1-p)$. Promedia. Para el gradiente de BCE sustituye $e_i$ por $p_i-y_i$ en las fórmulas de MSE/2.
- **Métricas de regresión.** MAE promedia magnitudes. RMSE es la raíz del MSE y da más importancia a errores grandes. $R^2=1-\mathrm{SSE}/\mathrm{SST}$ compara con la media.

[Volver a Regresión y clasificación lineal](02-modelos-lineales.md)

## SVM

- **Puntaje:** $f(x)=w^\top x+b$ y predicción según su signo. Frontera $f=0$, bordes $f=\pm1$, ancho $2/\lVert w\rVert$, distancia de un punto $\lvert f(x)\rvert/\lVert w\rVert$.
- **Hinge:** con $y\in\{-1,+1\}$, calcula $m=yf(x)$ y después $\max(0,1-m)$. Acierto dentro de la franja si $0<m<1$. Error si $m<0$.
- **Margen suave:** objetivo $\lVert w\rVert^2/2+C\sum_i\ell_i$. `C` controla el peso de las violaciones. En RBF, $\gamma$ controla cuánto cae la similitud con la distancia.
- **Kernels:** lineal $x^\top z$, polinómico homogéneo $(x^\top z)^d$, RBF $\exp(-\gamma\lVert x-z\rVert^2)$. La decisión con kernel suma aportes de los soportes, $\alpha_i y_i K(x_i,x)$, y el sesgo.

[Volver a Clasificadores fundamentales y SVM](03-svm.md)

## Embeddings y métricas

- Aplana respetando el orden de los píxeles. PCA usa `X`, LDA usa `X` e `y`, Fisherfaces primero PCA y luego LDA. Con $C$ clases y $d_{\mathrm{PCA}}$ entradas a LDA, la cota de Fisherfaces es $\min(C-1,d_{\mathrm{PCA}})$.
- **Confusión:** fila real y columna predicha. Accuracy es diagonal sobre total. Para una clase, precision busca qué proporción de las predicciones de la clase acertó. Recall busca qué proporción de los casos reales de la clase se recuperó.
- **Calcular:** precision $=TP/(TP+FP)$, recall $=TP/(TP+FN)$ y $F_1=2TP/(2TP+FP+FN)$. Macro-F1 promedia los F1 por clase. Es útil cuando importa que cada clase cuente igual.

[Volver a Embeddings y evaluación](04-embeddings-y-metricas.md)

## Ensambles y validación

- **Random Forest:** bootstrap más atributos aleatorios, probabilidades promediadas. **AdaBoost:** cambia pesos de ejemplos y usa votos ponderados. **Gradient Boosting:** suma correcciones al puntaje por etapas.
- **Orden:** separa TRAIN y TEST. Busca con CV en TRAIN y un pipeline sin ajustar. Reajusta el ganador con todo TRAIN. Haz `predict` sobre TEST y calcula sus métricas.
- **Verbos de código:** `fit` aprende, `transform` utiliza lo aprendido para cambiar la representación, `fit_transform` hace ambas cosas en los datos de ajuste y `predict` obtiene la salida del modelo.
- **Grid:** producto de las cantidades de valores de la cuadrícula. **Random:** hasta `n_iter` candidatos del espacio. Ajustes de cada búsqueda $=$ candidatos $\times$ folds $+1$ si reajusta. `best_score_` viene de CV y `best_params_` indica qué configuración ganó.

[Volver a Ensambles y búsqueda](05-ensambles-y-validacion.md)

---

[← Anterior: 5. Ensambles y búsqueda](05-ensambles-y-validacion.md) · [Inicio](README.md) · [Siguiente: Banco de 20 ejercicios →](07-ejercicios-propuestos.md) · [Simulacro](08-simulacro.md)
