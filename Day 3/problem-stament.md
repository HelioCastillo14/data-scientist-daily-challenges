**Tipo:** SQL · **Nivel:** 1 · **Apoyo:** Guiado · **Tema:** SQL - window functions · **Etapa:** exploración · **Tiempo objetivo:** 20-30 min (leer la teoría y el paso a paso NO cuenta en Tiempo Real)

## 🎯 Enunciado

Trabajas como analista de datos en el equipo de prevención de fraude de un banco digital. El área de riesgo sospecha que los fraudes suelen ir precedidos de un salto brusco en el monto de una transacción respecto a la anterior del mismo cliente, y quiere una primera señal simple para su proceso de revisión manual.

Tu tarea: para cada transacción, calcula el monto de la transacción inmediatamente anterior del mismo cliente (ordenando por fecha) y la diferencia entre ambos montos. Esta señal alimentará un filtro que marcará para revisión manual las transacciones con un salto inusual.

## 📦 Datos

```sql
CREATE TABLE transacciones (
    id INTEGER PRIMARY KEY,
    cliente_id VARCHAR(10) NOT NULL,
    fecha DATE NOT NULL,
    monto NUMERIC(10,2) NOT NULL
);

INSERT INTO transacciones (id, cliente_id, fecha, monto) VALUES
(1, 'C001', '2026-08-01', 150.00),
(2, 'C001', '2026-08-05', 200.00),
(3, 'C001', '2026-08-12', 5000.00),
(4, 'C002', '2026-08-02', 80.00),
(5, 'C002', '2026-08-09', 95.00),
(6, 'C002', '2026-08-20', 120.00),
(7, 'C003', '2026-08-03', 300.00),
(8, 'C003', '2026-08-04', 310.00),
(9, 'C003', '2026-08-06', 295.00),
(10, 'C004', '2026-08-01', 60.00),
(11, 'C004', '2026-08-15', 4000.00),
(12, 'C004', '2026-08-16', 70.00);
```

## 📖 Teoría aplicada

1. **Funciones de ventana (window functions).** Son cálculos que se aplican sobre un conjunto de filas relacionadas ("ventana") sin colapsar el resultado en una sola fila por grupo, a diferencia de `GROUP BY`. Importan aquí porque necesitas ver cada transacción individual Y, al mismo tiempo, un valor de otra fila relacionada con ella (la transacción anterior del mismo cliente). Sintaxis general: `funcion(columna) OVER (PARTITION BY ... ORDER BY ...)`.
2. **PARTITION BY.** Divide las filas en grupos independientes para el cálculo de la ventana, sin fusionarlas en el resultado (cada fila se conserva). Aquí importa porque la comparación "transacción anterior" debe hacerse solo dentro de las transacciones del mismo cliente, nunca mezclando clientes distintos. `PARTITION BY cliente_id`.
3. **ORDER BY dentro de OVER().** Define la secuencia en la que la función de ventana "recorre" las filas de cada partición. Es indispensable para funciones que dependen de una posición relativa, como la transacción "anterior". `ORDER BY fecha`.
4. **LAG().** Devuelve el valor de una columna tomado de una fila anterior dentro de la partición y el orden definidos, sin necesidad de un self-join. Es la función que resuelve directamente "tráeme el monto de la transacción anterior". Su forma general es `LAG(columna, n) OVER (PARTITION BY ... ORDER BY ...)`, donde `n` indica cuántas filas atrás mirar (por defecto 1 si se omite).
5. **NULLs en la primera fila de cada partición.** La primera transacción de cada cliente no tiene una transacción anterior dentro de los datos, así que `LAG()` devuelve `NULL` para esa fila. No es un error: representa la ausencia de un valor anterior, y hay que decidir cómo interpretarlo en el resultado (aquí, simplemente se deja como `NULL`).

**Ejemplo resuelto** (dominio de logística, datos distintos a los del reto):

```sql
CREATE TABLE entregas (
    id INTEGER PRIMARY KEY,
    repartidor_id VARCHAR(10) NOT NULL,
    fecha DATE NOT NULL,
    minutos_entrega INTEGER NOT NULL
);

INSERT INTO entregas (id, repartidor_id, fecha, minutos_entrega) VALUES
(1, 'R1', '2026-07-01', 30),
(2, 'R1', '2026-07-03', 45),
(3, 'R2', '2026-07-02', 20),
(4, 'R2', '2026-07-05', 50),
(5, 'R2', '2026-07-06', 15);

SELECT
    repartidor_id,
    fecha,
    minutos_entrega,
    LAG(minutos_entrega, 1) OVER (PARTITION BY repartidor_id ORDER BY fecha) AS minutos_entrega_anterior
FROM entregas
ORDER BY repartidor_id, fecha;

-- Resultado (verificado):
-- ('R1', '2026-07-01', 30, NULL)   <- primera entrega de R1, no hay anterior
-- ('R1', '2026-07-03', 45, 30)
-- ('R2', '2026-07-02', 20, NULL)   <- primera entrega de R2, no hay anterior
-- ('R2', '2026-07-05', 50, 20)
-- ('R2', '2026-07-06', 15, 50)
```

**Para profundizar (opcional):** `funciones de ventana` / `window functions`, `LAG LEAD SQL`, `self join vs window function`, `PARTITION BY ORDER BY SQL`.

## 🪜 Paso a paso

1. Identifica sobre qué columna necesitas "mirar hacia atrás" (el monto) y sobre qué columna necesitas agrupar las comparaciones (el cliente).
2. Escribe un `SELECT` que traiga `cliente_id`, `fecha` y `monto` de la tabla `transacciones`.
3. Agrega una columna calculada con `LAG()` sobre `monto`, usando `PARTITION BY` para separar por `cliente_id`.
4. Dentro del `OVER()`, ordena por `fecha` para que "anterior" tenga sentido cronológico.
5. Agrega una columna adicional que reste el monto anterior al monto actual (redondeada a 2 decimales) para obtener la diferencia.
6. Ordena el resultado final por `cliente_id` y `fecha` para poder revisarlo visualmente.
7. Verifica tu resultado comparándolo fila por fila con la tabla de salida esperada del Entregable.

## 📤 Entregable

- [ ]  Una única consulta SQL (PostgreSQL) sobre la tabla `transacciones` que devuelva las columnas `cliente_id`, `fecha`, `monto`, `monto_anterior` y `diferencia` (esta última redondeada a 2 decimales), ordenada por `cliente_id` y `fecha`.
- [ ]  El resultado debe coincidir exactamente con esta tabla esperada (12 filas):

| cliente_id | fecha | monto | monto_anterior | diferencia |
| --- | --- | --- | --- | --- |
| C001 | 2026-08-01 | 150.00 | NULL | NULL |
| C001 | 2026-08-05 | 200.00 | 150.00 | 50.00 |
| C001 | 2026-08-12 | 5000.00 | 200.00 | 4800.00 |
| C002 | 2026-08-02 | 80.00 | NULL | NULL |
| C002 | 2026-08-09 | 95.00 | 80.00 | 15.00 |
| C002 | 2026-08-20 | 120.00 | 95.00 | 25.00 |
| C003 | 2026-08-03 | 300.00 | NULL | NULL |
| C003 | 2026-08-04 | 310.00 | 300.00 | 10.00 |
| C003 | 2026-08-06 | 295.00 | 310.00 | -15.00 |
| C004 | 2026-08-01 | 60.00 | NULL | NULL |
| C004 | 2026-08-15 | 4000.00 | 60.00 | 3940.00 |
| C004 | 2026-08-16 | 70.00 | 4000.00 | -3930.00 |
- [ ]  Restricciones: usa solo `LAG()` como función de ventana (nada de self-joins ni subconsultas correlacionadas); el nombre de los alias puede variar siempre que el significado sea el mismo.
- [ ]  Interpretación de negocio (1-3 líneas): ¿qué cliente(s) muestran el salto más grande y por qué eso sería una señal útil (aunque no definitiva) para el equipo de fraude? Menciona una limitación.
- [ ]  Terminado cuando: tu consulta corre sin error sobre los datos de arriba y las 12 filas del resultado coinciden exactamente con la tabla esperada.

**Cómo marcar Resultado:** Acierto = cumples todos los criterios sin abrir el toggle de Solución (leer la teoría o el paso a paso NO cuenta como ayuda). Con ayuda = abriste la Solución antes de cumplirlos. Fallo = al terminar el tiempo no cumples los criterios. Anota en Debilidad/notas los conceptos que tuviste que investigar o lo que no entendiste de la teoría.

## ✍️ Mi respuesta

```sql
SELECT
    cliente_id,              -- columna para identificar al cliente
    fecha,                   -- fecha de la transacción
    monto                    -- monto de la transacción actual
    -- TODO: agrega aquí la columna con LAG() para el monto anterior
    -- TODO: agrega aquí la columna con la diferencia respecto al monto anterior
FROM transacciones
-- TODO: ordena el resultado final por cliente_id y fecha
;
```

- 🔒 Solución (no abrir hasta intentarlo)
    
    **Consulta**
    
    ```sql
    SELECT
        cliente_id,
        fecha,
        monto,
        LAG(monto, 1) OVER (PARTITION BY cliente_id ORDER BY fecha) AS monto_anterior,
        ROUND(monto - LAG(monto, 1) OVER (PARTITION BY cliente_id ORDER BY fecha), 2) AS diferencia
    FROM transacciones
    ORDER BY cliente_id, fecha;
    ```
    
    **Resultado esperado**
    
    Ver la tabla de 12 filas en la sección Entregable; coincide exactamente con la salida de esta consulta.
    
    **Razonamiento paso a paso**
    
    `LAG(monto, 1) OVER (PARTITION BY cliente_id ORDER BY fecha)` trae el monto de la fila cronológicamente anterior dentro del mismo cliente. Se resta ese valor al monto actual y se redondea a 2 decimales para obtener `diferencia`. Finalmente se ordena por `cliente_id` y `fecha` para facilitar la revisión visual.
    
    **Cómo se conectan los conceptos con la solución**
    
    `PARTITION BY cliente_id` asegura que la comparación nunca cruce clientes distintos; `ORDER BY fecha` dentro del `OVER()` define qué transacción es "la anterior" en el tiempo; `LAG()` es la función que trae ese valor sin necesidad de un self-join, que sería más costoso de escribir y de mantener.
    
    **Interpretación de negocio**
    
    El cliente C004 muestra el salto más grande (de $60 a $4000, una diferencia de $3940), seguido de C001 (de $200 a $5000). Estas transacciones serían las primeras candidatas para revisión manual del equipo de fraude. Limitación: un salto grande no es prueba de fraude por sí solo (podría ser una compra legítima grande), y la primera transacción de cada cliente queda sin comparación porque no existe una anterior en los datos disponibles.
    
    **Error común**
    
    Confundir `GROUP BY` con `PARTITION BY`: `GROUP BY` colapsa las filas en una sola por grupo, mientras que `PARTITION BY` conserva todas las filas y solo agrupa el cálculo de la función de ventana.
    
    **Conexión con el flujo de DS**
    
    Esta transformación aparece en la etapa de exploración e ingeniería de variables, típicamente antes de construir un modelo de detección de anomalías o un sistema de reglas, donde variables como "diferencia respecto a la transacción anterior" son features de entrada.
    
    **Habilidad que entrena**
    
    Uso de funciones de ventana (`LAG`) para comparaciones fila a fila dentro de un grupo, sin necesidad de self-joins.
    
    Verificación: ejecutada (la consulta de la solución y el ejemplo resuelto se corrieron contra los datos de ejemplo con SQLite y los resultados coinciden con las tablas mostradas).