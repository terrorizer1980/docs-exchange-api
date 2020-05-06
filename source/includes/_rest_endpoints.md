# REST Endpoints

## Query single order status

> Example request:

```
GET https://api.blockchain.com/exchange/order/21745988181
```

> Response:

```json
{
  "orderId": 21745988181,
  "gwOrderId": 2789221636,
  "clOrdId": "d7402f75314a4cf1webd",
  "symbol": "ETH-BTC",
  "ordType": "limit",
  "timeInForce": "GTC",
  "side": "buy",
  "orderQty": 1.0,
  "minQty": 0.0,
  "cumQty": 0.0,
  "price": 0.015797,
  "stopPx": 0.0,
  "ordStatus": "cancelled",
  "expireDate": 20200414,
  "execID": "537374697",
  "avgPx": 0.0
}
```

In order to look up the status of a single order, users can utilize the following endpoint.

| Item             | Description                                         |
| ---------------- | --------------------------------------------------- |
| URL              | https://api.blockchain.com/exchange/order/{orderId} |
| HTTP Method      | GET                                                 |
| Required headers | Cookie: auth_token={apiKey}                         |

Users can authenticate using API keys, by providing a cookie named auth_token with the value set to their API key.
