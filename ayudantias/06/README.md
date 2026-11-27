# Ayudantía 06 · Preparación para la Prueba 2

[← Volver al repositorio](../../README.md) · [Empezar por 1. Preparación de datos y PCA →](01-preparacion-y-pca.md)

La prueba combina teoría (50 %) con desarrollo y código (50 %). En la parte teórica practicarás selección múltiple, completar oraciones y unir conceptos con sus definiciones. En la parte aplicada practicarás cuentas a mano, completar fragmentos de Python y explicar qué hace un programa.

## Cómo estudiar

**Si un tema todavía te cuesta**, parte por su guía. Lee la teoría, intenta el primer inciso de cada ejercicio y luego compara tu cuenta con la solución. Pasa a los propuestos cuando puedas explicar qué hiciste y por qué. Puedes volver a la guía al corregirlos.

**Si ya estudiaste**, revisa la [ayuda memoria](06-ayuda-memoria.md), resuelve los [ejercicios propuestos](07-ejercicios-propuestos.md) de los temas que te cuestan y haz el [simulacro](08-simulacro.md) antes de abrir la [pauta](09-pauta.md).

## Guías

| Tema | Qué podrás hacer |
| --- | --- |
| [1. Preparación de datos y PCA](01-preparacion-y-pca.md) | Elegir un método de selección, calcular una proyección e interpretar la varianza retenida. |
| [2. Regresión y clasificación lineal](02-modelos-lineales.md) | Ajustar una recta, dar un paso de gradiente y pasar de un puntaje logístico a una clase. |
| [3. Clasificadores fundamentales y SVM](03-svm.md) | Leer una frontera, medir el margen y usar un kernel para predecir. |
| [4. Embeddings y evaluación](04-embeddings-y-metricas.md) | Seguir el recorrido de una imagen y calcular métricas por clase. |
| [5. Ensambles y búsqueda](05-ensambles-y-validacion.md) | Explicar cómo decide cada ensamble y evaluar una búsqueda sin fuga. |

- [Ayuda memoria](06-ayuda-memoria.md)
- [Banco de 20 ejercicios con pistas](07-ejercicios-propuestos.md)
- [Simulacro de 100 puntos](08-simulacro.md)
- [Pauta de autocorrección](09-pauta.md)

## Antes de empezar

Cada **fila** es una observación. Cada **columna** de `X` es una característica o atributo. `y` contiene el valor real que queremos predecir, uno por fila. En regresión ese valor es un número, como una temperatura. En clasificación es una categoría, como una especie.

Puedes seguir las cuentas con papel y calculadora. Los ejemplos de Python usan NumPy y scikit-learn con datos pequeños o incluidos en la biblioteca. Si haces una cuenta a mano, conserva las fracciones o los decimales de la calculadora y redondea al final.

Si necesitas otra explicación de PCA, mínimos cuadrados, logística o `Pipeline`, vuelve a la [Ayudantía 05 resuelta](../05/05-pca-y-algoritmos-lineales-resuelta.md).

## Material del curso

Las guías indican los títulos y páginas de los PDFs distribuidos en el curso. Si quieres repasar un concepto en las diapositivas, busca la referencia al final de la guía. Los ejercicios, el simulacro y su puntaje son material de práctica de esta ayudantía.
