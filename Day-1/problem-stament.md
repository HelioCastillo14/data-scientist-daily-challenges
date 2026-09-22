**Tipo:** Python · **Nivel:** 1 · **Tema:** Manipulación de datos · **Tiempo objetivo:** 20-30 min

## 🎯 Enunciado

Trabajas en el equipo de datos de una tienda pequeña. Tienes un registro de transacciones individuales, cada una con el producto vendido, su categoría, la cantidad vendida y el precio unitario en dólares. El equipo de finanzas te pide un resumen: el ingreso total generado por cada categoría de producto (ingreso de una transacción = cantidad × precio_unitario; el ingreso de una categoría es la suma de esos ingresos sobre todas sus transacciones), para ver de un vistazo qué categoría generó más dinero.

## 📦 Datos de entrada

```python
import pandas as pd

transacciones = [
    {"producto": "Laptop",     "categoria": "Electrónica", "cantidad": 2,  "precio_unitario": 750.00},
    {"producto": "Mouse",      "categoria": "Electrónica", "cantidad": 5,  "precio_unitario": 15.50},
    {"producto": "Silla",      "categoria": "Muebles",     "cantidad": 3,  "precio_unitario": 120.00},
    {"producto": "Escritorio", "categoria": "Muebles",     "cantidad": 1,  "precio_unitario": 300.00},
    {"producto": "Cuaderno",   "categoria": "Oficina",     "cantidad": 10, "precio_unitario": 2.50},
    {"producto": "Bolígrafo",  "categoria": "Oficina",     "cantidad": 20, "precio_unitario": 0.75},
    {"producto": "Monitor",    "categoria": "Electrónica", "cantidad": 2,  "precio_unitario": 180.00},
    {"producto": "Lámpara",    "categoria": "Muebles",     "cantidad": 4,  "precio_unitario": 25.00},
]

df = pd.DataFrame(transacciones)
```

## 📚 Conceptos para investigar

1. **Estructuras de datos tabulares en pandas** — necesitas saber cómo se referencian columnas y filas de un DataFrame antes de poder transformarlo. Búsqueda: "pandas DataFrame estructura básica" / "pandas DataFrame basics".
2. **Columnas derivadas mediante operaciones vectorizadas** — el ingreso de cada fila no viene dado, hay que calcularlo combinando dos columnas existentes sin usar un bucle. Búsqueda: "operaciones vectorizadas entre columnas pandas" / "vectorized column operations pandas".
3. **Agregación de datos por grupos** — el resultado final es un resumen por categoría, no por fila individual. Búsqueda: "agregación de datos por grupos pandas" / "pandas aggregation by group".
4. **Ordenamiento y control del índice de un DataFrame** — el entregable exige un orden específico y un índice limpio, distinto del que queda por defecto tras transformar los datos. Búsqueda: "ordenar DataFrame y resetear índice pandas" / "sort DataFrame and reset index pandas".
5. **Redondeo y precisión numérica en pandas** — el criterio de aceptación exige un formato numérico exacto; los floats pueden no coincidir si el redondeo se aplica en el momento equivocado. Búsqueda: "redondear columna numérica pandas" / "rounding pandas column float precision".

Si no puedes explicar cada concepto en una frase, investígalo antes de empezar. Ese tiempo NO cuenta en Tiempo Real.

## 📤 Entregable

- [ ]  Entregas una función `ingresos_por_categoria(df: pd.DataFrame) -> pd.DataFrame` que reciba el DataFrame de transacciones y devuelva un DataFrame nuevo con exactamente las columnas `categoria` (texto) e `ingreso_total` (número, redondeado a 2 decimales).
- [ ]  El DataFrame resultante tiene una fila por cada categoría presente en los datos, ordenado de mayor a menor `ingreso_total`, con el índice reiniciado (0, 1, 2, ...).
- [ ]  Tu función pasa, sin errores, los 5 asserts de abajo sobre los datos de ejemplo dados — incluyendo el orden exacto de filas y los valores redondeados.
- [ ]  Restricciones: solo puedes usar pandas (y opcionalmente numpy); no está permitido iterar fila por fila con un `for` explícito sobre el DataFrame ni usar librerías externas de análisis. Esto lo verificas tú mismo, no hay chequeo automático.
- [ ]  Terminado = el bloque "Mi respuesta" tiene tu código y los 5 asserts corren sin lanzar excepción, en 30 minutos o menos.
- [ ]  Marca Resultado: Acierto si cumples todos los criterios sin abrir la solución; Con ayuda si abriste la solución o pediste una pista antes de lograrlo; Fallo si al terminar el tiempo no cumples los criterios. Anota en Debilidad/notas los conceptos que tuviste que investigar.

```python
resultado = ingresos_por_categoria(df)

assert list(resultado.columns) == ["categoria", "ingreso_total"]
assert len(resultado) == 3
assert list(resultado["categoria"]) == ["Electrónica", "Muebles", "Oficina"]
assert list(resultado["ingreso_total"]) == [1937.5, 760.0, 40.0]
assert list(resultado.index) == [0, 1, 2]
print("OK: todos los asserts pasaron")
```

## ✍️ Mi respuesta

```python

```

- 🔒 Solución (no abrir hasta intentarlo)
    
    **Código**
    
    ```python
    def ingresos_por_categoria(df: pd.DataFrame) -> pd.DataFrame:
        d = df.copy()
        d["ingreso"] = d["cantidad"] * d["precio_unitario"]
        out = d.groupby("categoria", as_index=False)["ingreso"].sum()
        out = out.rename(columns={"ingreso": "ingreso_total"})
        out["ingreso_total"] = out["ingreso_total"].round(2)
        out = out.sort_values("ingreso_total", ascending=False).reset_index(drop=True)
        return out
    ```
    
    **Razonamiento paso a paso**
    
    1. Copiar el DataFrame para no modificar el original.
    2. Calcular una columna "ingreso" multiplicando cantidad por precio_unitario (operación vectorizada, sin bucles).
    3. Agrupar las filas por "categoria" y sumar la columna "ingreso" dentro de cada grupo.
    4. Renombrar la columna resultante a "ingreso_total" y redondear a 2 decimales.
    5. Ordenar de mayor a menor "ingreso_total" y reiniciar el índice con reset_index(drop=True).
    
    **Complejidad / alternativa**
    
    La agregación por grupos recorre las filas una vez, O(n) sobre el número de transacciones. Una alternativa equivalente es usar pivot_table con aggfunc="sum", que produce el mismo resultado con otra sintaxis.
    
    **Error común**
    
    Redondear antes de agrupar en vez de después (acumula pequeños errores), u olvidar reiniciar el índice después de ordenar, dejando índices desordenados como 0, 2, 1.
    
    **Habilidad que entrena**
    
    Transformar datos transaccionales de grano fino en un resumen agregado de grano grueso mediante columnas derivadas y agregación por grupos — la base de casi cualquier reporte analítico.