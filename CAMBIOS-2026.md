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
