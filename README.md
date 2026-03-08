# Proyecto Stream de datos de Massive: base de datos agregaciones de precio para acciones

## Enlaces a la API y documentación del stream
El stream de datos seleccionado, proviene de la plataforma de datos Massive, la cual se encarga de recopilar datos financieros y proporciona un acceso al mercado en tiempo real a través de APIs y WebSockets actualizandose instantaneamente y en tiempo real, pues obtiene datos de las bolsas financieras más importantes del mundo.

El enlace a la API y a su documentación es el siguiente: 

<https://massive.com/dashboard>
<https://massive.com/docs/websocket>
<https://massive.com/docs>
<https://massive.com/stocks/websocket>

Ésta API permite suscribirse a distintos tipos de eventos de mercado como trades, quotes y agregaciones de precio por minuto.

## Resumen
El stream de datos contiene información financiera en tiempo real del mercado de valores. En particular contiene agregaciones de precio por minuto para acciones que cotizan en la bolsa estadounidense.
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

| Atributo | Tipo de dato | Descripción |
|----------|--------------|-------------|
| ev | String | Tipo de evento |
| sym | String | Ticker de la acción |
| v | integer | Volúmen de acciones negociadas |
| o | float | Precio apertura |
| c | float | Precio de cierre |
| h | float | Precio máximo |
| l | float | Precio mínimo |
| a | float | Precio promedio ponderado por volúmen |
| s | integer | Marca de tiempo de inicio de minuto |
| e | integer | Marca de tiempo de fin de minuto |

### Tipo de evento 
El tipo de evento puede depender del tipo de transacción que se realice pero las más comunes son: 

- T:	Trade individual
- Q:	Quote (bid / ask)
- AM:	Aggregate por minuto
- A:	Aggregate por segundo
- status:	Mensajes de estado

### Ticker de la acción
Identificador único de acciones de cada empresa. 

Ejemplo de evento en el stream de datos: 

```
{
  "ev": "AM",
  "sym": "AAPL",
  "v": 12345,
  "o": 150.85,
  "c": 152.90,
  "h": 153.17,
  "l": 150.50,
  "a": 151.87,
  "s": 1611082800000,
  "e": 1611082860000
}
```

## Variables Cuantitativas 
Las variables cuantitativas son:
- v: Volúmen de acciones
- o: Precio apertura
- c: Precio de cierre
- h: Precio máximo
- l: Precio mínimo
- a: Promedio precio ponderado con volúmen de acciones
- s: Timestamp de inicio de segundo
- e: Timestamp de fin de segundo


## Variables Cualitativas
Las variables cualitativas son:
- ev: Tipo de evento
- sym: Ticker de acción

## Texto No Estructurado
El stream de datos no cuenta con variables de texto no estructuradas. 

## Series temporales
Las variables con timestamps o similares son:
- s: Timestamp de inicio de segundo
- e: Timestamp de fin de segundo

## Consideraciones éticas 
El procesamiento del stream de datos de mercado proporcionado por Massive no implica el manejo de datos personales, ya que la información corresponde únicamente a actividad pública del mercado financiero (precios, volumen y tiempos de transacción). Sin embargo, existen consideraciones éticas relacionadas con el posible sesgo inherente al mercado, el uso desigual del acceso a datos en tiempo real y la necesidad de respetar los términos de uso del proveedor y las regulaciones financieras. Por ello, es importante utilizar estos datos de manera responsable y transparente en aplicaciones o análisis.

## Bibiografía
Massive. (2024). Massive API documentation. <https://massive.com/docs>

Massive. (2024). WebSocket streaming documentation. <https://massive.com/docs/websocket>

Massive. (2024). Stocks WebSocket streaming API. <https://massive.com/stocks/websocket>


