# Market Exchange Rates

Market Prices and exchanges rates api

<aside class="notice">
Some API calls are available with <a href="https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS">CORS headers</a> if you add a <code>&cors=true</code> parameter to the GET request
</aside>

## Ticker

```json
{
  "USD" : {"15m" : 7441.97, "last" : 7441.97, "buy" : 7441.97, "sell" : 7441.97, "symbol" : "$"},
  "AUD" : {"15m" : 9830.85, "last" : 9830.85, "buy" : 9830.85, "sell" : 9830.85, "symbol" : "$"},
  "BRL" : {"15m" : 27958.85, "last" : 27958.85, "buy" : 27958.85, "sell" : 27958.85, "symbol" : "R$"},
  "CAD" : {"15m" : 9643.93, "last" : 9643.93, "buy" : 9643.93, "sell" : 9643.93, "symbol" : "$"},
  "CHF" : {"15m" : 7353.9, "last" : 7353.9, "buy" : 7353.9, "sell" : 7353.9, "symbol" : "CHF"},
  "CLP" : {"15m" : 4699606.01, "last" : 4699606.01, "buy" : 4699606.01, "sell" : 4699606.01, "symbol" : "$"},
  "CNY" : {"15m" : 47787.05, "last" : 47787.05, "buy" : 47787.05, "sell" : 47787.05, "symbol" : "¥"},
  "DKK" : {"15m" : 47491.03, "last" : 47491.03, "buy" : 47491.03, "sell" : 47491.03, "symbol" : "kr"},
  "EUR" : {"15m" : 6385.83, "last" : 6385.83, "buy" : 6385.83, "sell" : 6385.83, "symbol" : "€"},
  "GBP" : {"15m" : 5576.29, "last" : 5576.29, "buy" : 5576.29, "sell" : 5576.29, "symbol" : "£"},
  "HKD" : {"15m" : 58390.84, "last" : 58390.84, "buy" : 58390.84, "sell" : 58390.84, "symbol" : "$"},
  "INR" : {"15m" : 498327.17, "last" : 498327.17, "buy" : 498327.17, "sell" : 498327.17, "symbol" : "₹"},
  "ISK" : {"15m" : 778943.75, "last" : 778943.75, "buy" : 778943.75, "sell" : 778943.75, "symbol" : "kr"},
  "JPY" : {"15m" : 817891.27, "last" : 817891.27, "buy" : 817891.27, "sell" : 817891.27, "symbol" : "¥"},
  "KRW" : {"15m" : 7981664.99, "last" : 7981664.99, "buy" : 7981664.99, "sell" : 7981664.99, "symbol" : "₩"},
  "NZD" : {"15m" : 10650.61, "last" : 10650.61, "buy" : 10650.61, "sell" : 10650.61, "symbol" : "$"},
  "PLN" : {"15m" : 27442.69, "last" : 27442.69, "buy" : 27442.69, "sell" : 27442.69, "symbol" : "zł"},
  "RUB" : {"15m" : 462471.0, "last" : 462471.0, "buy" : 462471.0, "sell" : 462471.0, "symbol" : "RUB"},
  "SEK" : {"15m" : 65689.28, "last" : 65689.28, "buy" : 65689.28, "sell" : 65689.28, "symbol" : "kr"},
  "SGD" : {"15m" : 9959.57, "last" : 9959.57, "buy" : 9959.57, "sell" : 9959.57, "symbol" : "$"},
  "THB" : {"15m" : 238366.4, "last" : 238366.4, "buy" : 238366.4, "sell" : 238366.4, "symbol" : "฿"},
  "TWD" : {"15m" : 222046.15, "last" : 222046.15, "buy" : 222046.15, "sell" : 222046.15, "symbol" : "NT$"}
}
```

`GET https://blockchain.info/ticker`

Returns a JSON object with the currency codes as keys. "15m" is the 15 minutes delayed market price, "last" is the most recent market price, "symbol" is the currency symbol.

## Conversion

```shell
curl "https://blockchain.info/tobtc?currency=USD&value=10000"
> 1.34394332
```

`GET https://blockchain.info/tobtc`

Convert x value in the provided currency to btc

### Query Parameters

Parameter | Description
--------- | -----------
currency | A currency code. See list above.
value | Value in currency to convert

