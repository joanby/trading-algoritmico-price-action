# Cambios de la rama `update-2026`

> Esta rama es el mismo curso —Trading Algorítmico utilizando estrategias de acción del precio—, con el código adaptado a las librerías de hoy
> (matplotlib 3.11.2, pandas 3.0.6, yfinance 1.7.0; octubre de 2026). La rama principal sigue exactamente como en el vídeo.

> **Qué está comprobado y qué no.** Se ha ejecutado cada notebook entero con las versiones de
> `requirements.txt`: **7 correctos · 0 con fallo · 0 con timeout · 5 omitidos**. Comprobado: que cada celda se ejecuta sin error y que los datos
> tienen la forma del vídeo (histórico completo, mismas columnas, mismo orden). **No comprobado:** la
> descarga real desde Yahoo Finance, que no era accesible desde el entorno de prueba; se usó una réplica
> de su respuesta con precios inventados. **Tus números saldrán distintos** a los del vídeo, porque los
> precios reales han seguido moviéndose.
> Sin ejecutar: `Chapter_09_MT5`, `ES_PA_Capítulo_07_MT5_Trading_en_Vivo_Engulfing_Figure`, `ES_PA_Capítulo_07_MT5_Trading_en_Vivo_Random`, `ES_PA_Capítulo_07_MT5_Trading_en_Vivo_SMA`, `ES_PA_Capítulo_07_MT5_Trading_en_Vivo_Soporte_Resistencia_SMA_RSI` (MetaTrader 5 solo funciona en Windows, con el terminal y una cuenta).
> En noviembre de 2026 se corrigieron además errores del propio código del curso, que dan resultados
> distintos a los del vídeo: ver [Correcciones de errores del curso](#correcciones-de-errores-del-curso-noviembre-de-2026).

## Cómo usarla

- **En Google Colab (como en el vídeo):** abre el notebook de esta rama y, cuando el código lea un CSV,
  súbelo al panel de archivos igual que en el vídeo.
- **En tu ordenador:** `git clone -b update-2026 https://github.com/joanby/trading-algoritmico-price-action`, instala `pip install -r requirements.txt`
  (versiones **fijadas**: las mismas con las que se ha comprobado, para que un cambio futuro de las
  librerías no lo vuelva a romper) y deja junto al notebook los CSV que use.

## Qué ha cambiado y por qué

### 1. Descargar precios con yfinance

Notebooks: `ES_PA_Capítulo_04_Detección_de_Figuras`, `ES_PA_Capítulo_03_Pre_Procesado_de_Datos`, `ES_PA_Capítulo_04_Detección_de_Figuras`, `ES_PA_Capítulo_05_Backtesting_Vectorizado`, `ES_PA_Capítulo_06_Scalping_usando_la_estrategia_de_price_action`.

yfinance cambió tres comportamientos por defecto de `yf.download` desde que se grabó el curso
(comprobado leyendo yfinance 0.1.70, la versión de entonces, y la 1.7.0 de hoy):

| En el vídeo | Hoy, si no dices nada | Qué pasa con el código del curso |
|---|---|---|
| Sin fechas, descarga **todo el histórico** | Descarga **solo el último mes** | Medias largas vacías, `.loc["2020"]` da `KeyError`, backtests de un mes |
| Con solo `end="2021-01-01"`, desde el principio hasta esa fecha | **Solo el mes anterior** a esa fecha | El mismo problema, sin ningún error |
| Columnas `Open, High, Low, Close, Adj Close, Volume` | Sin `Adj Close` (`auto_adjust=True`) | `KeyError: 'Adj Close'` y *Length mismatch* al renombrar |
| Columnas simples, en ese orden | Dos niveles (precio, ticker) y en **orden alfabético** | Aunque arregles lo anterior, al renombrar por posición `open` acabaría siendo `Adj Close` |

Cada `yf.download(...)` lleva ahora los argumentos que devuelven el comportamiento del vídeo:

```python
yf.download("EURUSD=X", period="max", auto_adjust=False, multi_level_index=False)
```

`period="max"` se añade siempre que la llamada no tenga fecha de inicio (`start`). Donde el código renombra las columnas por
posición (`df.columns = ["open", "high", ...]`), antes se reordenan como estaban:
`[["Open", "High", "Low", "Close", "Adj Close", "Volume"]]`.

**Si escribes el código siguiendo el vídeo**, añade esos argumentos en tu `yf.download`.

`yf.Ticker(...).history()` no ha cambiado (ya ajustaba precios y bajaba un mes por defecto). Y los dobles
corchetes de `df[["Close"]].rolling(15).mean()` **no son un fallo**: funcionan igual en pandas 3.

### 2. pandas 3: `fillna(method="ffill")` ya no existe

Notebooks: `ES_PA_Capítulo_05_Backtesting_Vectorizado`, `ES_PA_Capítulo_06_Scalping_usando_la_estrategia_de_price_action`, `ES_PA_Capítulo_07_MT5_Trading_en_Vivo_Soporte_Resistencia_SMA_RSI`.

Se escribe `.ffill()` (y `.bfill()` para `method="bfill"`). Hace exactamente lo mismo.

### 3. Rutas de Colab

Notebooks: `ES_PA_Capítulo_02_Python_para_Data_Science`, `ES_PA_Capítulo_03_Pre_Procesado_de_Datos`.

`pd.read_csv("/content/fichero.csv")` → `pd.read_csv("fichero.csv")`. En Colab es lo mismo (la carpeta de trabajo es `/content`) y además funciona en tu ordenador.

### 4. Errores que el vídeo provoca a propósito

Notebooks: `ES_PA_Capítulo_01_Los_fundamentos_de_Python`, `ES_PA_Capítulo_07_MT5_Trading_en_Vivo_Engulfing_Figure`, `ES_PA_Capítulo_07_MT5_Trading_en_Vivo_Random`, `ES_PA_Capítulo_07_MT5_Trading_en_Vivo_SMA`, `ES_PA_Capítulo_07_MT5_Trading_en_Vivo_Soporte_Resistencia_SMA_RSI`.

Algunas celdas dan un error a propósito para explicar algo (por ejemplo, qué es una variable local). Siguen dándolo; solo se han marcado (`raises-exception`) para que *Ejecutar todo* no se pare ahí.

## Correcciones de errores del curso (noviembre de 2026)

Aquí no ha cambiado ninguna librería: son fallos del código del curso que enseñaban algo mal. **Lo que
verás en pantalla será distinto del vídeo**, y está explicado en cada punto. Comprobado: los capítulos 4, 5 y 6 se han
vuelto a ejecutar enteros con `requirements.txt` y la réplica de Yahoo de arriba (todos sin error). Las
cifras de antes y después salen de Yahoo Finance real, descargado el 19 de noviembre de 2026, y de los CSV del repositorio. Los notebooks del capítulo 7 (MetaTrader 5) **no se han ejecutado** (solo Windows).
Los notebooks conservan la salida guardada del vídeo: en las celdas de estos puntos, lo guardado es el
resultado antiguo (por ejemplo, `Alpha: 0.9 %`); al ejecutarlas, sale el nuevo.

### 1. `requirements.txt`: falta `six` (capítulos 4 y 6)

Desde el 1 de noviembre de 2026, una instalación limpia con `pip install -r requirements.txt` hacía
fallar los capítulos 4 y 6 con `ModuleNotFoundError: No module named 'six'`. `mpl_finance` usa `six` pero
no la declara, y hasta entonces llegaba de rebote con `python-dateutil`, que en su versión 4.0 ha dejado de
necesitarla. Ahora `six` está fijada en `requirements.txt`.

### 2. Alfa (capítulo 5)

**En el vídeo:** alfa se calcula como `252 * mean * (1-beta)` (celda 15) y, dentro de `BackTest`, como
`serie.mean() - beta*serie.mean()`.

**Qué estaba mal:** las dos expresiones son el retorno medio de la cartera multiplicado por (1 − beta).
Ahí no aparece el mercado, así que no dicen si la cartera lo ha hecho mejor que él, y el signo puede salir
al revés que el real. La fórmula escrita en la celda 14 tenía el mismo fallo.

**Ahora:** es el alfa de Jensen, con tipo libre de riesgo cero: el retorno medio de la cartera menos beta
por el retorno medio del SP500, anualizado igual que antes.

```python
alpha = (252 * (mean - beta*val["SP500"].mean()))*100                        # celda 15
alpha = annualiazed_scalar * (serie.mean() - beta*val["SP500"].mean())       # BackTest
```

La fórmula de la celda 14 pasa a ser $r_{PF} - r_f - \beta (r_M - r_f)$. `BackTest` se llama igual y
recibe lo mismo.

**Qué cambia en los números** (alfa anual):

| | Antes | Ahora |
|---|---|---|
| RSI sobre Google 2004–2020 (beta −0,15), celda 15 | 7,9 % | 8,3 % |
| `BackTest(return_serie)`, celda 23 | 7,907 % | 8,276 % |
| `BackTest(engulfing("GOOG"))`, celda 26 | 2,376 % | 2,51 % |

Alfa no mide seguridad: es la rentabilidad que se obtiene por encima de la que tocaría por el riesgo de
mercado asumido (beta). Con beta cercana a 0 apenas cambia; con beta cercana a 1, el cálculo antiguo daba
casi 0 fuera cual fuera el alfa real.

### 3. `support_resistance` no restaba el spread (capítulo 6)

**En el notebook:** `support_resistance` recibe el parámetro `spread` para restar ese coste en cada
operación, y se aplica a seis pares de divisas con `spread=0.0001` (celda 24).

**Qué estaba mal:** se restaba solo en las filas en las que `df["return"]` valía exactamente 1 o −1. Es
un rendimiento, del orden de 0,0001: no vale ±1 nunca, así que el coste **no se restaba nunca**. Daba lo
mismo pasar `spread=0` que `spread=0.0001`.

**Ahora:** se resta en cada operación. Es decir, en las filas en las que la posición,
`df["signal"].shift(duration)`, vale 1 o −1.

```python
df.loc[df["signal"].shift(duration)==-1, "return"] = df["return"]-spread
df.loc[df["signal"].shift(duration)==1, "return"] = df["return"]-spread
```

**Qué cambia en los números:** mucho. Con estos datos, la estrategia hace **entre 12.700 y 14.400
operaciones por par**. La tabla final (`RETURN`, `DRAWDOWN`, `RETURN DRAWDOWN RATIO`, celda 28) queda así:

| | RETURN antes | RETURN ahora | RATIO antes | RATIO ahora |
|---|---|---|---|---|
| GBPUSD | 0,149 | −0,701 | 4,34 | −0,97 |
| cartera | 0,075 | −0,943 | 4,24 | −1,00 |
| AUDCAD | 0,189 | −0,690 | 3,62 | −1,00 |
| EURUSD | 0,103 | −1,316 | 1,45 | −1,00 |
| USDCHF | 0,040 | −1,234 | 0,77 | −1,00 |
| USDJPY | 0,009 | −0,873 | 0,14 | −1,00 |
| USDCAD | −0,040 | −0,845 | −0,46 | −1,00 |

El ratio de más de 4 sale de no contar el coste. Con el coste, todos los pares pierden. Esa es la lección:
en *scalping*, con miles de operaciones, el spread decide el resultado.

### 4. Capítulo 7 (MetaTrader 5): instalación y avisos (sin ejecutar)

- **Celda 0 de Random y SMA:** se han comentado `numpy==1.19.2` y `scipy==1.6.2`, que no se
  instalan en un Python actual: numpy 1.19.2 solo trae instaladores hasta Python 3.8, y scipy 1.6.2 no
  admite Python 3.10 o posterior (en 3.12, pip intenta compilar numpy y falla). También se ha comentado
  `pip install datetime`: `datetime` viene con Python, y en PyPI ese nombre es otro paquete. `numpy` se
  sigue instalando, porque la piden `ta` y `MetaTrader5`. Si instalas en un entorno virtual, quita también
  `--user`, que ahí da error.
- **Notebook SMA:** la orden se llamaba `"Bitcoin"`, pero opera `EURUSD` con 1 lote. Ahora se llama
  `"Euro vs US dollar"`, como en el notebook Random. El símbolo y el volumen no cambian.
- **Avisos, sin cambiar el código:** hay un comentario en `filling_mode - 1` (en `Chapter_09_MT5.py`) y otro en
  los `while True` de los cuatro notebooks. `filling_mode` es una máscara de bits del broker: restarle 1 solo acierta si el
  símbolo admite un único modo de ejecución, y si no, la orden puede rechazarse. El bucle no tiene ninguna
  pausa: repite la ronda durante todo el segundo de arranque y luego da vueltas sin parar. Opera
  siempre en una **cuenta demo**, y empieza con `live = False`.
- **El robot de figuras envolventes no replica el backtest** (aviso en el código, sin cambiarlo). En los
  capítulos 4 y 5, cada figura abre una posición que se mantiene 5 días (`duration=5`). El robot mira las
  velas una vez al día y `MT5.run` cierra la posición al día siguiente si ese día no hay una figura nueva
  en el mismo sentido; además, cada orden lleva un stop loss y un take profit del 1 %. La figura se busca
  en la última vela que da MetaTrader 5, que puede ser la del día en curso, todavía sin cerrar. Y opera
  cinco criptomonedas, Ethereum incluida, cuando el capítulo 4 concluye que con Ethereum la estrategia no
  funciona. Lo que ganes o pierdas en vivo no es lo que mide el backtest.

## Erratas del vídeo (el código no cambia)

- **`spread` en `engulfing` (capítulos 4 y 5).** No es un coste: es la tolerancia entre el cierre de ayer
  y la apertura de hoy (±0,5 %). Tampoco es un «intervalo de confianza», como se dice al presentar la
  figura: es una banda fija elegida a mano. En el capítulo 5, `engulfing(symbol, spread=0.00, duration=5)`
  recibe el parámetro y no lo usa: la tolerancia va escrita fija a 0,5. Por eso da lo mismo que la versión
  del capítulo 4 con su valor por defecto (`spread=0.5`), y pasarle otro `spread` no cambia nada.
- **Backtesting (capítulo 5).** En el vídeo, Sortino, beta, alfa y drawdown se calculan sobre las
  acciones de Google. En el notebook se calculan sobre una estrategia RSI aplicada a Google
  (`return_serie = RSI(f, 5, 14)`, celda 9), así que las cifras no son las del vídeo. Esta diferencia ya
  estaba en la rama principal.
- **«Estrategia segura» (capítulo 5).** El drawdown es pequeño porque la estrategia casi siempre está
  fuera del mercado, no porque sea inmune a las caídas. Y la duración de 5 días se eligió mirando el
  resultado sobre los mismos datos con los que se juzga, sin costes de operación.
