# Proyecto Stream de datos de Massive: base de datos agregaciones de precio para acciones

## Enlaces a la API y documentación del stream
El stream de datos seleccionado, proviene de la plataforma de datos Massive, la cual se encarga de recopilar datos financieros y proporciona un acceso al mercado en tiempo real a través de APIs y WebSockets actualizandose instantaneamente y en tiempo real, pues obtiene datos de las bolsas financieras más importantes del mundo.

El enlace a la API y a su documentación es el siguiente: 

### WebSocket (Streaming en tiempo real)
- Documentación general: <https://massive.com/docs>
- Dashboard: <https://massive.com/dashboard>
- WebSocket Options Overview: <https://massive.com/docs/websocket/options/overview>
- Aggregates Per Minute: <https://massive.com/docs/websocket/options/aggregates-per-minute>
- Aggregates Per Second: <https://massive.com/docs/websocket/options/aggregates-per-second>
- Trades: <https://massive.com/docs/websocket/options/trades>
- Quotes: <https://massive.com/docs/websocket/options/quotes>

### REST API (Datos históricos y de referencia)
- All Contracts: <https://massive.com/docs/rest/options/contracts/all-contracts>
- Option Chain Snapshot: <https://massive.com/docs/rest/options/snapshots/option-chain-snapshot>
- Custom Bars (OHLC): <https://massive.com/docs/rest/options/aggregate-bars/custom-bars>
- Technical Indicators (SMA, EMA, RSI, MACD): <https://massive.com/docs/rest/options/technical-indicators>
- Exchanges: <https://massive.com/docs/rest/options/market-operations/exchanges>
- Condition Codes: <https://massive.com/docs/rest/options/market-operations/condition-codes>

### SDK y recursos adicionales
- SDK Python oficial: <https://github.com/massive-com/client-python>

Esta API permite suscribirse a distintos tipos de eventos de mercado como trades, quotes, agregaciones de precio por minuto y por segundo, así como snapshots con Greeks e Implied Volatility para contratos de opciones.

## Resumen
El stream de datos contiene información financiera en tiempo real del mercado de valores. En particular contiene agregaciones de precio por minuto para acciones que cotizan en la bolsa estadounidense.
El proyecto utiliza dos tipos principales de eventos:

1. **Aggregates Per Minute (AM):** Barras OHLC (Open, High, Low, Close) construidas cada minuto a partir de trades elegibles. Incluyen volumen, VWAP y número de transacciones.
2. **Snapshots con Greeks e IV:** Datos obtenidos periódicamente vía REST API que enriquecen los eventos con delta, gamma, theta, vega, implied volatility y open interest.

Adicionalmente se utilizan aggregates diarios, indicadores técnicos (SMA, RSI) y datos de referencia (contratos, exchanges, condition codes) para complementar el análisis.
Cada evento del stream representa un resumen de la actividad de una acción durante un minuto específico incluyendo:

- Tipo de Evento
- Ticker de la acción
- Volúmen total negociado
- Precio apertura
- Precio de cierre
- Precio máximo
- Precio mínimo
- Precio promedio ponderado por volúmen
- Marca de tiempo de inicio del minuto
- Marca de tiempo final del minuto

## Origen y Autoría
Los datos del stream son recolectados y distribuidos por Massive, una empresa especializada en la recoleccion e infraestructura de datos financieros y APIs de mercado. Massive obtiene la información directamente de proveedores del mercado y posteriormente los procesa y distribuye (Massive, 2024). Las centrales de datos de Massive se encuentran en Nueva Jersey y cuentan con una conexión física directa a distintas bolsas como:

- Bolsa de Valores de Nueva York (NYSE, NYSE American, NYSE Arca, NYSE Chicago, NYSE National)
- Nasdaq (OMX, BX, PSX, Filadelfia)
- Mercados globales de CBOE (BZX, BYX, EDGX, EDGA)
- Grupo de Intercambio MIAX (Perla, Esmeralda, Acciones)
- Intercambio de miembros (MEMX)
- Bolsa de Inversores (IEX)
- Bolsa de valores a largo plazo (LTSE)

[Ver más acerca de Massive](https://massive.com/docs/rest/stocks/overview)


## Diccionario de datos 

### Evento de Aggregate Per Minute (`ev: AM`)

| Atributo | Tipo de dato | Descripción |
|----------|--------------|-------------|
| `ev` | string | Tipo de evento. Siempre `"AM"` para aggregates por minuto. |
| `sym` | string | Ticker del contrato de opción (ej. `O:SPY260327C00500000`). |
| `v` | integer | Volumen de contratos negociados en esta ventana de 1 minuto. |
| `av` | integer | Volumen acumulado del día para este contrato. |
| `op` | number | Precio oficial de apertura del día para este contrato. |
| `o` | number | Precio de apertura de esta ventana de agregación. |
| `c` | number | Precio de cierre de esta ventana de agregación. |
| `h` | number | Precio más alto de esta ventana de agregación. |
| `l` | number | Precio más bajo de esta ventana de agregación. |
| `vw` | number | Precio promedio ponderado por volumen (VWAP) de esta ventana. |
| `a` | number | VWAP del día completo. |
| `z` | integer | Tamaño promedio de los trades en esta ventana. |
| `s` | integer | Timestamp de inicio de la ventana en Unix Milliseconds (UTC). |
| `e` | integer | Timestamp de fin de la ventana en Unix Milliseconds (UTC). |

### Evento de Trade (`ev: T`) — disponible vía WebSocket en vivo

| Atributo | Tipo de dato | Descripción |
|----------|--------------|-------------|
| `ev` | string | Tipo de evento. Siempre `"T"` para trades. |
| `sym` | string | Ticker del contrato de opción. |
| `x` | integer | ID de la bolsa donde se ejecutó el trade. |
| `p` | number | Precio de ejecución del trade (USD por acción). |
| `s` | integer | Número de contratos operados (size). |
| `c` | array[integer] | Códigos de condición del trade. |
| `t` | integer | Timestamp en Unix Milliseconds (UTC). |
| `q` | integer | Número de secuencia (creciente, único por ticker). |

### Snapshot con Greeks e IV (REST API)

| Atributo | Tipo de dato | Descripción |
|----------|--------------|-------------|
| `details.contract_type` | string | Tipo de contrato: `"call"` o `"put"`. |
| `details.exercise_style` | string | Estilo de ejercicio: `"american"`, `"european"`. |
| `details.expiration_date` | string | Fecha de expiración (YYYY-MM-DD). |
| `details.strike_price` | number | Precio de ejercicio (strike). |
| `details.shares_per_contract` | number | Acciones por contrato (generalmente 100). |
| `greeks.delta` | number | Sensibilidad del precio al movimiento del subyacente. |
| `greeks.gamma` | number | Tasa de cambio del delta. |
| `greeks.theta` | number | Decaimiento temporal (valor perdido por día). |
| `greeks.vega` | number | Sensibilidad a cambios en volatilidad implícita. |
| `implied_volatility` | number | Volatilidad implícita del mercado para este contrato. |
| `open_interest` | number | Contratos abiertos al cierre del último día de operación. |
| `break_even_price` | number | Precio del subyacente para punto de equilibrio. |
| `day` | object | Barra diaria más reciente (open, high, low, close, volume). |
| `underlying_asset` | object | Información del activo subyacente (precio, ticker). |

### Decodificación del Ticker de Opciones

El formato del ticker de opciones sigue la estructura OSI (Options Symbology Initiative):

```
O:SPY260327C00500000
│ │   │      │ │
│ │   │      │ └── Strike price × 1000 (500.000 → $500.00)
│ │   │      └──── Tipo: C = Call, P = Put
│ │   └─────────── Fecha de expiración: YYMMDD (2026-03-27)
│ └──────────────── Subyacente: SPY
└────────────────── Prefijo: O: indica contrato de opción
```

---

## Variables Cuantitativas

Las variables cuantitativas del stream son:

- `v`: Volumen de contratos negociados
- `o`, `h`, `l`, `c`: Precios OHLC (Open, High, Low, Close)
- `vw`, `a`: Precio promedio ponderado por volumen (VWAP)
- `av`: Volumen acumulado del día
- `op`: Precio oficial de apertura del día
- `z`: Tamaño promedio de trades
- `p` (en trades): Precio de ejecución
- `s` (en trades): Tamaño del trade
- `greeks.delta`, `greeks.gamma`, `greeks.theta`, `greeks.vega`: Griegas del contrato
- `implied_volatility`: Volatilidad implícita
- `open_interest`: Interés abierto

## Variables Cualitativas

Las variables cualitativas son:

- `ev`: Tipo de evento (`"AM"`, `"T"`, `"A"`, `"Q"`)
- `sym`: Ticker del contrato de opción
- `x` (en trades): ID de la bolsa de ejecución
- `c` (en trades): Códigos de condición del trade
- `details.contract_type`: Tipo de contrato (`"call"`, `"put"`)
- `details.exercise_style`: Estilo de ejercicio (`"american"`, `"european"`)

Del ticker `sym` se pueden extraer sub-variables cualitativas: subyacente (SPY, AAPL, etc.), tipo (Call/Put), fecha de expiración y strike price.

## Texto No Estructurado

El stream de datos **no cuenta con variables de texto no estructuradas**. Todos los atributos son numéricos, categóricos o timestamps. El campo `sym` es un identificador codificado con estructura fija, no texto libre.

## Series Temporales

Las variables con timestamps o marcas de tiempo son:

- `s`: Timestamp de inicio de la ventana de agregación (Unix Milliseconds, UTC)
- `e`: Timestamp de fin de la ventana de agregación (Unix Milliseconds, UTC)
- `t` (en trades): Timestamp del trade (Unix Milliseconds, UTC)

Para alinear con el horario de mercado (9:30 AM – 4:00 PM ET), es necesario convertir explícitamente de UTC a Eastern Time.

---

## Consideraciones éticas 
El procesamiento del stream de datos de mercado proporcionado por Massive no implica el manejo de datos personales, ya que la información corresponde únicamente a actividad pública del mercado financiero (precios, volumen y tiempos de transacción). Sin embargo, existen consideraciones éticas relacionadas con el posible sesgo inherente al mercado, el uso desigual del acceso a datos en tiempo real y la necesidad de respetar los términos de uso del proveedor y las regulaciones financieras. Por ello, es importante utilizar estos datos de manera responsable y transparente en aplicaciones o análisis.

## Ejemplos Reales de Datos

Datos obtenidos vía REST API del último día de mercado (2026-03-06).

### Aggregate Per Minute (formato WebSocket canal `AM`)

```json
{
  "ev": "AM",
  "sym": "O:SPY260327C00500000",
  "o": 189.01,
  "h": 189.01,
  "l": 189.01,
  "c": 189.01,
  "v": 3,
  "vw": 189.01,
  "n": 1,
  "s": 1772480940000,
  "e": 1772481000000
}
```

### Aggregate Diario (REST OHLC)

```json
{
  "ev": "A_daily",
  "sym": "O:SPY260327C00500000",
  "o": 188.13,
  "h": 188.13,
  "l": 188.13,
  "c": 188.13,
  "v": 2,
  "vw": 188.13,
  "n": 1,
  "t": 1770958800000
}
```

### Snapshot con Greeks e IV (REST API)

```json
{
  "details": {
    "contract_type": "call",
    "exercise_style": "american",
    "expiration_date": "2026-03-16",
    "shares_per_contract": 100,
    "strike_price": 500,
    "ticker": "O:SPY260316C00500000"
  },
  "greeks": {
    "delta": 0.9929619720642283,
    "gamma": 0.0002243054072911806,
    "theta": -0.12551220342863942,
    "vega": 0.01887361705993933
  },
  "implied_volatility": 0.8298913384881482,
  "open_interest": 5,
  "break_even_price": null,
  "day": {
    "change": 0,
    "change_percent": 0,
    "close": 178.98,
    "high": 178.98,
    "last_updated": 1772571600000000000,
    "low": 178.98,
    "open": 178.98,
    "previous_close": 178.98,
    "volume": 5,
    "vwap": 178.98
  },
  "underlying_asset": {
    "ticker": "SPY"
  }
}
```

### Technical Indicator (SMA)

```json
{
  "ticker": "O:SPY260327C00500000",
  "indicator": "SMA_10",
  "values": [
    {
      "timestamp": 1772773200000,
      "value": 185.344
    },
    {
      "timestamp": 1772686800000,
      "value": 187.024
    },
    {
      "timestamp": 1772600400000,
      "value": 187.78199999999998
    }
  ]
}
```

### Estadísticas del Sample Data (Conseguidas un fin de semana)

| Métrica | Valor |
|---------|-------|
| Contratos seleccionados | 36 |
| Barras 1 minuto | 125 |
| Barras diarias | 67 |
| Trades | 0 (disponibles vía WebSocket en vivo)/ (entre semana a horas de mercado se recibiran datos de aqui)|
| Snapshots con Greeks | 281 |
| Indicadores técnicos | 8 |
| Subyacentes | SPY, AAPL, TSLA, QQQ, NVDA, AMZN |
| Fecha de mercado | 2026-03-06 |

---


## Bibiografía
Massive. (2024). Massive API documentation. <https://massive.com/docs>

Massive. (2024). WebSocket streaming documentation. <https://massive.com/docs/websocket>

Massive. (2024). Stocks WebSocket streaming API. <https://massive.com/stocks/websocket>


