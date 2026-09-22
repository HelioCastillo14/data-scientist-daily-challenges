**Tipo:** Limpieza y Visualizacion · **Nivel:** 1 · **Apoyo:** Guiado · **Tema:** Visualización · **Etapa:** comunicación · **Tiempo objetivo:** 20-30 min (leer la teoría y el paso a paso NO cuenta en Tiempo Real)

## 🎯 Enunciado

Trabajas como analista junior en una agencia de marketing digital. Mañana el equipo de cuentas presenta al cliente los resultados de la última campaña y necesita decidir en qué canal reforzar el presupuesto del próximo mes. Alguien ya calculó el CTR (tasa de clics) de cada canal, y tu tarea es **un solo gráfico de barras** claro y ordenado, listo para pegar en el reporte.

El CTR ya viene calculado en la tabla como el porcentaje de clics sobre impresiones:

$$
\text{CTR} = \frac{\text{clics}}{\text{impresiones}} \times 100
$$

## 📦 Datos

```python
import matplotlib
matplotlib.use("Agg")  # solo si ejecutas en un script sin pantalla
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt

df = pd.DataFrame({
    "canal": ["Google Ads", "Meta Ads", "Email", "TikTok Ads", "Influencers"],
    "ctr":   [2.40, 3.45, 5.00, 2.00, 3.17],   # CTR en porcentaje
})
```

## 📖 Teoría aplicada

**1. Un gráfico es un objeto que se puede inspeccionar.** En matplotlib, `fig, ax = plt.subplots()` crea una figura (`fig`) y unos ejes (`ax`), que es donde se dibuja. seaborn dibuja *sobre* esos ejes si le pasas `ax=ax`. Lo importante para ti: `ax` guarda el título, las etiquetas y las barras, y por eso podemos comprobar el gráfico con código en lugar de mirarlo.

**2. Un gráfico de barras compara categorías.** En `sns.barplot(data=df, x="canal", y="ctr")`, `x` es la categoría (el canal) e `y` es el valor que mide la altura de cada barra (el CTR). Aquí cada fila del DataFrame es una barra.

**3. El orden de las barras se decide con `order`.** Un ranking se lee mejor de mayor a menor. Para lograrlo, ordenas el DataFrame con `sort_values("ctr", ascending=False)`, tomas la columna `canal` de ese resultado y se la pasas a seaborn con `order=`. Así el gráfico queda ordenado sin modificar tu DataFrame original.

**4. Un gráfico sin rótulos no comunica.** `ax.set_title(...)`, `ax.set_xlabel(...)` y `ax.set_ylabel(...)` ponen el título y los nombres de los ejes. Un lector que no conoce los datos debe entender qué mide cada eje sin que se lo expliques.

**Ejemplo resuelto** (otro dominio: educación)

```python
cursos = pd.DataFrame({
    "curso": ["Álgebra", "Historia", "Química", "Inglés"],
    "aprobados_pct": [72.5, 88.0, 65.0, 91.5],
})
orden = cursos.sort_values("aprobados_pct", ascending=False)["curso"]
# orden: Inglés, Historia, Álgebra, Química

fig, ax = plt.subplots(figsize=(6, 4))
sns.barplot(data=cursos, x="curso", y="aprobados_pct", order=orden, ax=ax)
ax.set_title("Aprobación por curso")
ax.set_xlabel("Curso")
ax.set_ylabel("Aprobados (%)")

# Cómo comprobarlo sin mirar el gráfico:
# ax.get_title()                              -> "Aprobación por curso"
# len(ax.patches)                             -> 4 (una por barra)
# [t.get_text() for t in ax.get_xticklabels()] -> ["Inglés", "Historia", "Álgebra", "Química"]
# [p.get_height() for p in ax.patches]         -> [91.5, 88.0, 72.5, 65.0]
```

Para profundizar (opcional): `figura y ejes matplotlib` / `matplotlib figure and axes`; `gráfico de barras seaborn` / `seaborn barplot`; `ordenar un DataFrame pandas` / `pandas sort_values`.

## 🪜 Paso a paso

1. Ordena `df` de mayor a menor `ctr` y quédate con la columna `canal` de ese resultado: será el orden de las barras.
2. Crea la figura y los ejes con `plt.subplots(figsize=(6, 4))`.
3. Dibuja el gráfico con `sns.barplot`, indicando el DataFrame, `x`, `y`, el `order` del paso 1 y `ax=ax`.
4. Ponle el título "CTR por canal" y los nombres de ejes "Canal" y "CTR (%)".
5. Devuelve `ax` desde tu función.
6. Ejecuta los asserts de la sección Entregable y comprueba que pasan.

## 📤 Entregable

- [ ]  Una función `graficar_ctr(df: pd.DataFrame)` que devuelve el objeto `Axes` del gráfico de barras.
- [ ]  Las barras van de mayor a menor CTR, con título "CTR por canal", eje X "Canal" y eje Y "CTR (%)".
- [ ]  Los 6 asserts de abajo pasan sin errores.
- [ ]  Restricciones: solo pandas, seaborn y matplotlib; no escribas el orden de los canales a mano, debe salir de los datos.
- [ ]  Interpretación de negocio en 1-3 líneas, como comentario al final de tu código: qué canal recomendarías reforzar y una limitación de comparar canales solo por CTR.
- [ ]  Terminado cuando los asserts pasan y tu interpretación está escrita.

```python
ax = graficar_ctr(df)
etiquetas = [t.get_text() for t in ax.get_xticklabels()]
alturas = [round(p.get_height(), 2) for p in ax.patches]

assert ax.get_title() == "CTR por canal"
assert ax.get_xlabel() == "Canal"
assert ax.get_ylabel() == "CTR (%)"
assert len(ax.patches) == 5
assert etiquetas == ["Email", "Meta Ads", "Influencers", "Google Ads", "TikTok Ads"]
assert alturas == [5.0, 3.45, 3.17, 2.4, 2.0]
print("OK: los 6 asserts pasaron")
```

**Cómo marcar Resultado:** Acierto = cumples todos los criterios sin abrir el toggle de Solución (leer la teoría y el paso a paso NO cuenta como ayuda). Con ayuda = abriste la Solución antes de cumplirlos. Fallo = al terminar el tiempo no cumples los criterios. Anota en Debilidad/notas lo que no entendiste de la teoría.

## ✍️ Mi respuesta

```python
def graficar_ctr(df):
    # Paso 1: orden de los canales, de mayor a menor ctr

    # Paso 2: crear figura y ejes

    # Paso 3: dibujar las barras con sns.barplot (usa order= y ax=ax)

    # Paso 4: título y nombres de ejes

    # Paso 5: devolver el objeto Axes
    return ...

# Interpretación de negocio (1-3 líneas):
```

- 🔒 Solución (no abrir hasta intentarlo)
    
    **Código**
    
    ```python
    def graficar_ctr(df):
        orden = df.sort_values("ctr", ascending=False)["canal"]
        fig, ax = plt.subplots(figsize=(6, 4))
        sns.barplot(data=df, x="canal", y="ctr", order=orden, ax=ax)
        ax.set_title("CTR por canal")
        ax.set_xlabel("Canal")
        ax.set_ylabel("CTR (%)")
        return ax
    ```
    
    **Resultado esperado**
    
    Cinco barras en este orden: Email (5.0), Meta Ads (3.45), Influencers (3.17), Google Ads (2.4) y TikTok Ads (2.0).
    
    **Razonamiento paso a paso**
    
    1. `sort_values` ordena de mayor a menor y `["canal"]` extrae solo los nombres en ese orden.
    2. `plt.subplots` crea la figura y los ejes donde se dibuja.
    3. `sns.barplot` dibuja una barra por fila y `order=` fuerza el orden del paso 1, sin tocar el DataFrame original.
    4. Los `set_*` rotulan el gráfico.
    5. Se devuelve `ax` para poder comprobar título, etiquetas y barras con asserts.
    
    **Cómo se conecta la teoría con la solución**
    
    La lección 1 explica por qué se devuelve `ax`, la 2 por qué `x` es el canal e `y` el CTR, la 3 el uso de `order` y la 4 los rótulos.
    
    **Interpretación de negocio**
    
    Email lidera con un CTR de 5.0 %, seguido de Meta Ads con 3.45 %, por lo que sería el candidato a reforzar. Limitación: el CTR no considera el costo por clic ni si los clics terminaron en una venta; un canal con CTR alto puede ser poco rentable si sus clics son caros.
    
    **Error común**
    
    Olvidar `order=`: las barras salen en el orden en que aparecen las filas del DataFrame (Google Ads primero) y el gráfico deja de leerse como un ranking.
    
    **Conexión con el flujo de DS**
    
    Es la etapa de comunicación: ya tienes el dato calculado y el trabajo es presentarlo para que alguien no técnico decida con él.
    
    **Habilidad que entrena**
    
    Construir un gráfico de barras ordenado y rotulado con seaborn, y verificarlo con código en lugar de a ojo.
    
    **Verificación:** ejecutada (el ejemplo resuelto, la solución y los 6 asserts corrieron sin errores con pandas 3.0.2 y seaborn 0.13.2).