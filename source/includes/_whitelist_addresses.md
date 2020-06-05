# HTTP Payments API

## View whitelisted addresses

> Example request:

```shell
curl --request GET 'https://api.blockchain.info/nabu-gateway/payments/whitelist' \
--header 'Authorization: Bearer $API_KEY_JWT_TOKEN'
```

> Example Response:

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

`GET /nabu-gateway/payments/whitelist`

## Initiate a withdrawal

> Example Request

```shell
curl --request POST 'https://api.blockchain.info/nabu-gateway/payments/withdrawals' \
--header 'blockchain-origin: PIT' \
--header 'Authorization: Bearer $API_KEY_JWT_TOKEN' \
--header 'Content-Type: application/json' \
--data-raw '{
    "beneficiary": "96b9b56a-832e-4632-9e1b-b93b525202c3",
    "currency": "ETH",
    "amount": "0.025"
}'
```

> Example Response

```json
{
  "id": "29a0f1bf-4926-4e1e-bb65-84ef7e755538",
  "user": "c494c4d7-72bd-4a67-8a54-cb7af88d4329",
  "product": "MERCURY",
  "amount": {
    "symbol": "ETH",
    "value": "0.025"
  },
  "state": "NONE"
}
```

POST `/nabu-gateway/payments/withdrawals` `beneficiary` in the request JSON body
must match the ID from `GET /nabu-gateway/payments/whitelist` service.
