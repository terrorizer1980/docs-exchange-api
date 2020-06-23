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

## View whitelisted addresses

> Example request:

```
GET https://api.blockchain.info/exchange/beneficiaries/whitelist
```

> Response:

```json
{
  "capabilities": [
    {
      "currency": "BTC",
      "address": true,
      "xpub": false,
      "existingBeneficiaryOnly": false,
      "fiat": false
    },
    {
      "currency": "ETH",
      "address": true,
      "xpub": false,
      "existingBeneficiaryOnly": false,
      "fiat": false
    }
  ],
  "enabled": true,
  "enabledAt": "2020-05-08T11:41:32.664Z",
  "whitelisted": [
    {
      "id": "298907ac-07df-47ab-93d5-5594e019813b",
      "address": "******************************2v7b",
      "agent": {
        "account": "",
        "address": null,
        "code": null,
        "country": null,
        "name": null,
        "recipient": null,
        "routingNumber": null
      },
      "currency": "BTC",
      "state": "ACTIVE",
      "name": "My Ledger Device",
      "whitelisted": true,
      "fiat": false
    }
  ]
}
```

In order to see the list of whitelisted beneficiaries, users can utilize the following endpoint.

| Item             | Description                                                 |
| ---------------- | ----------------------------------------------------------- |
| URL              | https://api.blockchain.com/exchange/beneficiaries |
| HTTP Method      | GET                                                         |
| Required headers | Cookie: auth_token={apiKey}                                 |

Users can authenticate using API keys, by providing a cookie named auth_token with the value set to their API key.

## Create A Withdrawal

> Example request:

```
POST https://api.blockchain.com/exchange/withdrawals
```
```json
{
	"currency": "BTC",
	"amount": "0.0001",
	"beneficiary": "3ff5bc82-b118-45b3-b468-9697be208bdf"
}
```

> Response:

```json
{
    "id": "1941fcdd-d16a-4b5a-998e-f58f5862af88",
    "user": "e9307d88-bd92-4fdf-840c-7e30fbb0bbd7",
    "product": "MERCURY",
    "amount": {
        "symbol": "BTC",
        "value": "0.0001"
    },
    "fee": {
        "symbol": "BTC",
        "value": "0.0005"
    },
    "state": "NONE"
}
```

In order to create a withdrawal via api, users can utilize the following endpoint.

| Item             | Description                                                 |
| ---------------- | ----------------------------------------------------------- |
| URL              | https://api.blockchain.com/exchange/withdrawals |
| HTTP Method      | POST                                                         |
| Required headers | Cookie: auth_token={apiKey}                                 |

Users can authenticate using API keys, by providing a cookie named auth_token with the value set to their API key.

**Request Parameters**

| Parameter        | Description                                                 |
| ---------------- | ----------------------------------------------------------- |
| currency         | The ticker symbol for the withdrawal currency e.g. "BTC |
| amount           | The amount of the withdrawal as a decimal e.g. 1.231  |
| beneficiary | The id of your beneficiary for this currency, which can be retrieved by using the /beneficiaries endpoint|

