# Telecom y churn: precisión, recall y F1 de un clasificador de abandono

**Tipo:** Algoritmos Aplicados · **Nivel:** 1 · **Apoyo:** Guiado · **Tema:** ML - evaluación y validación · **Etapa:** evaluación · **Tiempo objetivo:** 20-30 min (leer la teoría y el paso a paso NO cuenta en Tiempo Real)

## 🎯 Enunciado

Trabajas como data scientist en el equipo de retención de clientes de una empresa de telecomunicaciones. El equipo de marketing ya entrenó un modelo de churn (abandono) y antes de lanzar la campaña de retención basada en sus alertas, te piden auditar qué tan confiables son esas predicciones sobre una muestra reciente de clientes.

Tu tarea: implementar desde cero (sin usar `sklearn.metrics`) una función que calcule precision, recall y F1 a partir de las predicciones del modelo, y usar esos números para decidir si el equipo puede confiar en el modelo para dimensionar la campaña de retención.

## 📦 Datos

```python
import numpy as np

# 12 clientes evaluados el mes pasado.
# y_true: 1 si el cliente abandonó realmente, 0 si se quedó.
# y_pred: 1 si el modelo predijo que abandonaría, 0 si no.
y_true = [1, 1, 1, 1, 1, 1, 1, 0, 0, 0, 0, 0]
y_pred = [1, 1, 1, 0, 0, 0, 0, 1, 0, 0, 0, 0]
```

## 📖 Teoría aplicada

**1. Matriz de confusión.** Cuando un modelo predice una clase binaria ("va a abandonar" o "no"), cada cliente cae en una de 4 categorías según si el modelo acertó y qué predijo: verdadero positivo (TP), falso positivo (FP), falso negativo (FN) y verdadero negativo (TN). Todas las métricas de este reto se calculan a partir de estos 4 conteos, no directamente de los datos crudos. Código mínimo para contar uno de ellos:

```python
tp = ((y_true == 1) & (y_pred == 1)).sum()
```

**2. Precision.** Mide, de todos los clientes que el modelo marcó como "va a abandonar", qué fracción realmente abandonó. Una precision baja significa que el equipo de retención gastará ofertas en clientes que nunca se iban a ir.

$$
P = \frac{TP}{TP + FP}
$$

**3. Recall.** Mide, de todos los clientes que realmente abandonaron, qué fracción detectó el modelo. Un recall bajo significa que muchos clientes en riesgo real de irse no reciben ninguna oferta de retención.

$$
R = \frac{TP}{TP + FN}
$$

**4. F1-score.** Combina precision y recall en un solo número usando la media armónica, que castiga fuerte cuando una de las dos es muy baja. Sirve para comparar modelos con un solo número cuando ni precision ni recall por sí solas cuentan toda la historia.

$$
F1 = 2 \cdot \frac{P \cdot R}{P + R}
$$

**5. El trade-off precision-recall en una decisión de negocio.** Subir el umbral de decisión del modelo típicamente sube precision pero baja recall, y viceversa; no existe un modelo con ambas en 100% salvo en datos triviales. En este reto, decidir si el modelo es "suficientemente bueno" depende de cuál error le cuesta más caro al negocio: contactar a quien no se iba a ir, o no contactar a quien sí se iba a ir.

$$
\hat y = \mathbb{1}[\hat p \geq t]
$$

**Ejemplo resuelto** (detección de fraude, datos distintos a los del reto):

```python
import numpy as np

y_true_fraude = np.array([1, 0, 1, 1, 0])   # 1 = transacción fraudulenta real
y_pred_fraude = np.array([1, 0, 0, 1, 1])   # 1 = el modelo la marcó como fraude

tp = int(((y_true_fraude == 1) & (y_pred_fraude == 1)).sum())   # tp = 2
fp = int(((y_true_fraude == 0) & (y_pred_fraude == 1)).sum())   # fp = 1
fn = int(((y_true_fraude == 1) & (y_pred_fraude == 0)).sum())   # fn = 1

precision = round(tp / (tp + fp), 2)                     # precision = 0.67
recall = round(tp / (tp + fn), 2)                        # recall = 0.67
f1 = round(2 * precision * recall / (precision + recall), 2)   # f1 = 0.67

print(precision, recall, f1)   # 0.67 0.67 0.67
```

**Para profundizar (opcional):** matriz de confusión / confusion matrix · precisión y exhaustividad / precision and recall · puntaje F1 / F1 score · compensación precisión-exhaustividad / precision-recall tradeoff · verdaderos y falsos positivos / true and false positives.

## 🪜 Paso a paso

1. Convierte `y_true` e `y_pred` a arrays de NumPy con `np.array()` para poder usar comparaciones vectorizadas.
2. Usa una comparación booleana combinada con `&` y el método `.sum()` sobre los arrays para contar cuántos casos cumplen a la vez `true == 1` y `pred == 1` (verdaderos positivos).
3. Repite la misma lógica de comparación para contar los falsos positivos (`true == 0`, `pred == 1`) y los falsos negativos (`true == 1`, `pred == 0`).
4. Calcula precision dividiendo TP entre la suma de TP y FP; usa `round(..., 2)` para dejarlo en 2 decimales.
5. Calcula recall dividiendo TP entre la suma de TP y FN; redondea de la misma forma.
6. Calcula F1 con la fórmula de la media armónica entre precision y recall (mini-lección 4); redondea a 2 decimales.
7. Verifica tu resultado ejecutando los 3 asserts del Entregable antes de dar por terminado el reto.

## 📤 Entregable

- [ ]  Entregas una función `metricas_churn(y_true, y_pred)` en Python que reciba dos listas o arrays de 0/1 y devuelva una tupla `(precision, recall, f1)`, cada valor redondeado a 2 decimales con `round()`.
- [ ]  Deben cumplirse los 3 asserts que aparecen en el bloque de verificación de abajo, ejecutados sobre los datos de "📦 Datos" de este reto.
- [ ]  No uses `sklearn.metrics` ni ninguna función que ya calcule precision/recall/F1 por ti; sí puedes usar NumPy para las comparaciones y conteos.
- [ ]  Interpretación de negocio (1-3 líneas): con los valores de precision y recall que obtuviste, dile al equipo de retención si el modelo es confiable para dimensionar la campaña, y qué limitación tiene esa conclusión.
- [ ]  Terminado cuando: tu función pasa los 3 asserts y escribiste la interpretación de negocio.

```python
import numpy as np

y_true = np.array([1, 1, 1, 1, 1, 1, 1, 0, 0, 0, 0, 0])
y_pred = np.array([1, 1, 1, 0, 0, 0, 0, 1, 0, 0, 0, 0])

resultado = metricas_churn(y_true, y_pred)
assert isinstance(resultado, tuple) and len(resultado) == 3
assert resultado[0] == 0.75, f"precision esperada 0.75, obtuvo {resultado[0]}"
assert resultado[1] == 0.43, f"recall esperado 0.43, obtuvo {resultado[1]}"
assert resultado[2] == 0.55, f"f1 esperado 0.55, obtuvo {resultado[2]}"
```

**Cómo marcar Resultado:** Acierto = cumples todos los criterios sin abrir el toggle de Solución (leer la teoría o el paso a paso NO cuenta como ayuda). Con ayuda = abriste la Solución antes de cumplirlos. Fallo = al terminar el tiempo no cumples los criterios. Anota en Debilidad/notas los conceptos que tuviste que investigar o lo que no entendiste de la teoría.

## ✍️ Mi respuesta

```python
import numpy as np

def metricas_churn(y_true, y_pred):
    # Paso 1: convierte y_true e y_pred a arrays de NumPy
    # Paso 2: cuenta TP (true==1 y pred==1)
    # Paso 3: cuenta FP (true==0 y pred==1)
    # Paso 4: cuenta FN (true==1 y pred==0)
    # Paso 5: calcula precision = TP / (TP + FP), redondea a 2 decimales
    # Paso 6: calcula recall = TP / (TP + FN), redondea a 2 decimales
    # Paso 7: calcula F1 = 2 * precision * recall / (precision + recall), redondea a 2 decimales
    return ...
```

- 🔒 Solución (no abrir hasta intentarlo)
    
    **Fundamento matemático**
    
    $$
    P = \frac{TP}{TP+FP}, \quad R = \frac{TP}{TP+FN}, \quad F1 = 2 \cdot \frac{P \cdot R}{P+R}
    $$
    
    **Implementación**
    
    ```python
    import numpy as np
    
    def metricas_churn(y_true, y_pred):
        y_true = np.array(y_true)
        y_pred = np.array(y_pred)
        tp = int(((y_true == 1) & (y_pred == 1)).sum())
        fp = int(((y_true == 0) & (y_pred == 1)).sum())
        fn = int(((y_true == 1) & (y_pred == 0)).sum())
        precision = round(tp / (tp + fp), 2) if (tp + fp) > 0 else 0.0
        recall = round(tp / (tp + fn), 2) if (tp + fn) > 0 else 0.0
        f1 = round(2 * precision * recall / (precision + recall), 2) if (precision + recall) > 0 else 0.0
        return (precision, recall, f1)
    
    y_true = [1, 1, 1, 1, 1, 1, 1, 0, 0, 0, 0, 0]
    y_pred = [1, 1, 1, 0, 0, 0, 0, 1, 0, 0, 0, 0]
    print(metricas_churn(y_true, y_pred))   # (0.75, 0.43, 0.55)
    ```
    
    **Razonamiento paso a paso**
    
    De los 7 clientes que realmente abandonaron (`y_true == 1`), el modelo solo marcó 3 (los primeros tres), así que TP = 3 y FN = 4. De los 5 clientes que no abandonaron, el modelo marcó 1 de más (posición 8), así que FP = 1.
    
    $$
    TP = 3, \quad FP = 1, \quad FN = 4
    $$
    
    $$
    P = \frac{3}{3+1} = 0.75, \quad R = \frac{3}{3+4} \approx 0.43, \quad F1 = 2 \cdot \frac{0.75 \cdot 0.43}{0.75+0.43} \approx 0.55
    $$
    
    **Cómo se conectan los conceptos con la solución**
    
    La mini-lección 1 (matriz de confusión) da los 3 conteos (TP, FP, FN) que alimentan directamente las fórmulas de las mini-lecciones 2, 3 y 4 (precision, recall, F1). La mini-lección 5 (trade-off) es la que permite interpretar por qué precision (0.75) y recall (0.43) cuentan historias distintas del mismo modelo.
    
    **Interpretación de negocio**
    
    El modelo es bastante confiable cuando marca a alguien como riesgo de churn (75% de esas alertas son correctas), pero se le escapa más de la mitad de los clientes que sí van a abandonar (recall de solo 43%). Si el equipo de retención solo contacta a quienes el modelo marca, dejará ir a la mayoría de los clientes en riesgo real sin ninguna oferta. Limitación: esta muestra es de solo 12 clientes, así que estos porcentajes pueden cambiar bastante con más datos.
    
    **Error común**
    
    Calcular precision y recall usando los datos crudos en vez de los conteos de la matriz de confusión (por ejemplo, dividir aciertos totales entre el total de filas), lo que en realidad da *accuracy* y no dice nada sobre los falsos negativos que le importan al negocio.
    
    **Conexión con el flujo de DS**
    
    Esto ocurre en la etapa de evaluación, justo antes de decidir si un modelo pasa de un notebook a producción: sin este paso, un equipo podría lanzar una campaña confiando en un modelo que en realidad deja fuera a la mayoría de los clientes en riesgo.
    
    **Habilidad que entrena**
    
    Calcular métricas de clasificación desde cero a partir de una matriz de confusión, para poder auditar los números que da cualquier librería en vez de confiar en ellos a ciegas.
    
    **Verificación: ejecutada** (los 3 asserts y el ejemplo de fraude se corrieron en Python antes de publicar este reto).