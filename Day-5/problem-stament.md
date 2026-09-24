# Logística: probabilidad de entrega tardía por congestión vial

**Tipo:** Pregunta Matemática · **Nivel:** 1 · **Apoyo:** Guiado · **Tema:** Probabilidad · **Etapa:** modelado · **Tiempo objetivo:** 20-30 min

(leer la teoría y el paso a paso NO cuenta en Tiempo Real)

## 🎯 Enunciado

Trabajas como analista de datos en **RutaExpress**, una empresa de logística de última milla en zona urbana. El equipo de operaciones quiere decidir, cada mañana, si activa personal de refuerzo para el turno del día. La regla que están evaluando es: activar refuerzo si la probabilidad de que una entrega llegue tarde supera el 20%.

Con datos históricos del último trimestre, el equipo estimó lo siguiente para un día típico:

- El 30% de las rutas del día pasan por una zona con evento de tráfico reportado (congestión, cierre vial, accidente).
- El 70% restante no pasa por ninguna zona con evento de tráfico.
- Cuando una entrega pasa por una zona con evento de tráfico, el 45% llega tarde.
- Cuando una entrega no pasa por ninguna zona con evento de tráfico, el 8% llega tarde.

**Pregunta:** si se elige una entrega al azar del día, ¿cuál es la probabilidad de que llegue tarde? Con ese resultado, ¿se debería activar el personal de refuerzo según la regla del equipo?

## 📦 Datos

Sea $T$ el evento "la entrega pasa por una zona con evento de tráfico" y $L$ el evento "la entrega llega tarde".

| Cantidad | Valor |
| --- | --- |
| $P(T)$ | 0.30 |
| $P(\neg T)$ | 0.70 |
| $P(L \mid T)$ | 0.45 |
| $P(L \mid \neg T)$ | 0.08 |
| Umbral operativo para activar refuerzo | 20% |

## 📖 Teoría aplicada

1. **Probabilidad condicional.** $P(A \mid B)$ es la probabilidad de que ocurra $A$, sabiendo que ya ocurrió $B$. Se define como:

$$
P(A \mid B) = \frac{P(A \cap B)}{P(B)}
$$

En este reto ya te dan las condicionales ($P(L \mid T)$ y $P(L \mid \neg T)$) calculadas de antemano a partir del histórico; no necesitas derivarlas, solo usarlas correctamente.

1. **Partición del espacio muestral.** Un conjunto de eventos forma una partición cuando son mutuamente excluyentes (no pueden ocurrir a la vez) y exhaustivos (cubren todos los casos posibles). Aquí, $T$ y $\neg T$ particionan todas las entregas del día: cada entrega pasa por una zona con tráfico o no, nunca ambas cosas. Esto importa porque permite "descomponer" un evento complicado ($L$) en piezas más simples ponderadas por cada parte de la partición.
2. **Ley de probabilidad total.** Si $T$ y $\neg T$ particionan el espacio muestral, entonces la probabilidad total de cualquier evento $L$ se puede escribir como la suma de sus probabilidades condicionales, cada una ponderada por la probabilidad de la parte de la partición correspondiente:

$$
P(L) = P(L \mid T) \cdot P(T) + P(L \mid \neg T) \cdot P(\neg T)
$$

Este es el concepto central del reto: combina lo que sabes por separado sobre cada segmento (con tráfico / sin tráfico) en una sola probabilidad agregada para todo el día.

1. **Regla de decisión con umbral.** En un contexto de negocio, una probabilidad estimada rara vez se usa "en crudo": se compara contra un umbral operativo (aquí, 20%) para disparar una acción concreta (activar refuerzo o no). El resultado numérico solo tiene valor si se traduce en una decisión.

**Ejemplo resuelto** (dominio distinto, solo para ilustrar el método — no uses estos números en tu respuesta):

Un centro de soporte telefónico de telecomunicaciones recibe llamadas de dos tipos: sobre facturación ($F$) o sobre otros temas ($\neg F$).

| Cantidad | Valor |
| --- | --- |
| $P(F)$ | 0.40 |
| $P(\neg F)$ | 0.60 |
| $P(E \mid F)$ (escalada si es de facturación) | 0.25 |
| $P(E \mid \neg F)$ (escalada si no es de facturación) | 0.05 |

Aplicando la ley de probabilidad total:

$$
P(E) = P(E \mid F) \cdot P(F) + P(E \mid \neg F) \cdot P(\neg F) = (0.25)(0.40) + (0.05)(0.60)
$$

$$
P(E) = 0.10 + 0.03 = 0.13
$$

Es decir, el 13.0% de las llamadas del centro se escalan. Nota cómo cada condicional se pondera por la probabilidad de su segmento antes de sumar — no es un simple promedio de 0.25 y 0.05.

**Para profundizar (opcional):** ley de probabilidad total, teorema de la probabilidad total, partición del espacio muestral, probabilidad condicional / law of total probability, sample space partition, conditional probability, mutually exclusive and exhaustive events.

## 🪜 Paso a paso

1. Identifica los eventos relevantes del enunciado y define su notación (por ejemplo, $T$ = evento de tráfico, $L$ = entrega tardía).
2. Escribe las probabilidades marginales dadas, $P(T)$ y $P(\neg T)$, y confirma que suman 1.
3. Escribe las probabilidades condicionales dadas, $P(L \mid T)$ y $P(L \mid \neg T)$.
4. Identifica qué fórmula de la sección de teoría combina estas cuatro cantidades en una sola probabilidad.
5. Sustituye los valores en la fórmula y calcula el resultado numérico paso a paso (no saltes directo al resultado final).
6. Redondea el resultado a 1 decimal en formato porcentaje y compáralo contra el umbral operativo del enunciado.
7. Verifica: confirma que $P(T)+P(\neg T)=1$, que el resultado está entre 0% y 100%, y escribe la interpretación de negocio con la decisión que se deriva.

## 📤 Entregable

- [x]  Entregas el valor numérico de $P(L)$ en formato porcentaje con 1 decimal, junto con la fórmula explícita que usaste (con los cuatro valores sustituidos, no solo el resultado final).
- [ ]  Tu desarrollo muestra por separado el cálculo de cada término ponderado ($P(L \mid T) \cdot P(T)$ y $P(L \mid \neg T) \cdot P(\neg T)$) antes de sumarlos.
- [ ]  Tu desarrollo indica explícitamente que $T$ y $\neg T$ forman una partición del espacio muestral (por qué se pueden sumar los dos términos).
- [ ]  Interpretación de negocio en 1-3 líneas: compara tu resultado contra el umbral del 20% e indica si se activa el refuerzo, mencionando qué supuesto sobre los datos históricos sostiene tu conclusión.
- [ ]  Terminado cuando tengas el valor de $P(L)$, la fórmula con los cuatro valores sustituidos, y la interpretación de negocio escrita.

**Restricciones:** resuelve a mano o con calculadora (no uses simulación ni código); no busques directamente "ley de probabilidad total ejemplo entregas" — usa la teoría de arriba.

**Cómo marcar Resultado:** Acierto = cumples todos los criterios sin abrir el toggle de Solución (leer la teoría o el paso a paso NO cuenta como ayuda). Con ayuda = abriste la Solución antes de cumplirlos. Fallo = al terminar el tiempo no cumples los criterios. Anota en Debilidad/notas los conceptos que tuviste que investigar o lo que no entendiste de la teoría.

## ✍️ Mi respuesta

### Paso 1: Notación

(completa aquí)

### Paso 2: Probabilidades marginales

(completa aquí)

### Paso 3: Probabilidades condicionales

(completa aquí)

### Paso 4: Fórmula aplicada

(completa aquí)

### Paso 5: Cálculo paso a paso

(completa aquí)

### Paso 6: Resultado y comparación con el umbral

(completa aquí)

### Paso 7: Interpretación de negocio

(completa aquí)

- 🔒 Solución (no abrir hasta intentarlo)
    
    **Respuesta**
    
    $P(L) = 19.1\%$, por debajo del umbral del 20%, pero muy cerca. Con este resultado, estrictamente no se activaría el refuerzo hoy, aunque el margen es pequeño.
    
    **Razonamiento paso a paso**
    
    $T$ = evento de tráfico, $L$ = entrega tardía. $T$ y $\neg T$ particionan todas las entregas del día, así que aplica la ley de probabilidad total:
    
    $$
    P(L) = P(L \mid T) \cdot P(T) + P(L \mid \neg T) \cdot P(\neg T)
    $$
    
    Sustituyendo:
    
    $$
    P(L) = (0.45)(0.30) + (0.08)(0.70) = 0.135 + 0.056
    $$
    
    $$
    P(L) = 0.191 = 19.1\%
    $$
    
    **Cómo se conecta la teoría con la solución**
    
    La partición ($T$, $\neg T$) permite descomponer $P(L)$, un evento que depende de un factor externo (el tráfico), en dos piezas condicionales que sí conoces directamente del histórico. La ley de probabilidad total es exactamente el mecanismo para recombinarlas ponderando por qué tan frecuente es cada segmento.
    
    **Interpretación de negocio**
    
    19.1% está por debajo del umbral del 20%, así que según la regla estricta no se activa el refuerzo hoy. Sin embargo, el margen (0.9 puntos porcentuales) es pequeño frente a la incertidumbre normal de una estimación histórica, por lo que en la práctica el equipo podría optar por dejar el refuerzo en espera en vez de descartarlo del todo. El supuesto clave es que las probabilidades históricas ($P(T)$, $P(L \mid T)$, $P(L \mid \neg T)$) siguen siendo representativas del día actual (estacionariedad); si hoy hay un evento de tráfico inusualmente severo, esas tasas condicionales podrían subestimar el riesgo real.
    
    **Error común**
    
    Promediar directamente $P(L \mid T)$ y $P(L \mid \neg T)$ sin ponderar por $P(T)$ y $P(\neg T)$ (es decir, calcular $(0.45+0.08)/2 = 0.265$). Eso ignora que solo el 30% de las entregas están en el segmento de mayor riesgo, e infla artificialmente el resultado.
    
    **Conexión con el flujo de DS**
    
    Esta etapa corresponde a modelado: estimar una probabilidad agregada a partir de tasas condicionales segmentadas es un paso típico antes de fijar reglas de negocio, alertas automatizadas o umbrales de decisión operativa.
    
    **Habilidad que entrena**
    
    Aplicar la ley de probabilidad total sobre una partición del espacio muestral para combinar tasas condicionales segmentadas en una probabilidad agregada accionable.
    
    Verificación: ejecutada (cálculo verificado con Python: 0.45*0.30 + 0.08*0.70 = 0.191).