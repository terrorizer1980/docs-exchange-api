# Authenticated Channels

## Authenticated

To authenticate a web socket connection, the client must subscribe to the `auth` channel passing a token field. Alternatively a client can provide the header cookie `auth_token` on a new connection and then authentication will take place immediately without the need to subscribing to `auth` channel.

> If authentication is successful, the server will send an initial notification.

```json
{
  "seqnum": 0,
  "event": "subscribed",
  "channel": "auth"
}
```

> If authentication was unsuccessful, the server will send the following notification:

```json
{
  "seqnum": 0,
  "event": "rejected",
  "channel": "auth",
  "text": "Authentication Failed"
}
```

## Trading

The client can submit and cancel orders, as well as receive order executions, through the `trading` channel. The messages are in JSON format and the attribute names are using standardised FIX 4.2 Field names.

Below is a table showing the permitted FIX fields used as a JSON key for cancelling / creating an order:

| Tag | Field       | Type   | Mandatory                                                                | Description                                                                                                                                                            | Example   |
| --- | ----------- | ------ | ------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------- |
| 11  | clOrdID     | string | YES                                                                      | Reference field provided by client and cannot exceed 20 characters                                                                                                     | "ABC"     |
| 55  | symbol      | string | YES                                                                      | Blockchain symbol identifier                                                                                                                                           | "BTC-USD" |
| 40  | ordType     | string | YES                                                                      | "market" for market "limit" for limit, "stop" for stop, "stopLimit" for stopLimit                                                                                      | "limit"   |
| 59  | timeInForce | string | YES                                                                      | "GTC" for Good Till Cancel, "IOC" for Immediate or Cancel, "FOK" for Fill or Kill, “GTC” Good Till Date                                                                | "GTC"     |
| 54  | side        | string | YES                                                                      | "buy" for Buy, "sell" for Sell                                                                                                                                         | "buy"     |
| 38  | orderQty    | number | YES                                                                      | The order size in the terms of the base currency                                                                                                                       | 10.23     |
| 44  | price       | number | required for limit and stopLimit order types                             | The limit price for the order                                                                                                                                          | 0.12345   |
| 432 | expireDate  | number | required for GTD orders                                                  | expiry date in the format YYYYMMDD                                                                                                                                     | 20190318  |
| 99  | stopPx      | number | required for limit and stopLimit order types                             | Price to trigger the stop order                                                                                                                                        | 3500.12   |
| 110 | minQty      | number | Optional for all market orders and for limit orders with IOC timeInForce | The minimum quantity required for an IOC fill                                                                                                                          | 10.0      |
| 18  | execInst    | string | Optional for Limit Orders                                                | The order is placed with Add Liquidity Only (aka Post Only): it will not match liquidity immediately. It will be rejected instead of matching liquidity in the market. | "ALO"     |

Below is a table representing the different permutations permitted for the FIX fields, with X indicating what can be added and where O is not required:

|             | Market | Limit | Stop | Stop Limit |
| ----------- | ------ | ----- | ---- | ---------- |
| clOrdID     | X      | X     | X    | X          |
| symbol      | X      | X     | X    | X          |
| ordType     | X      | X     | X    | X          |
| timeInForce | O      | X     | X    | X          |
| side        | X      | X     | X    | X          |
| orderQty    | X      | X     | X    | X          |
| price       |        | X     |      | X          |
| expireDate  |        | O     |      |            |
| stopPx      |        |       | X    | X          |
| minQty      | O      | O     |      |            |

The server can include the following additional FIX fields when notifying of an order update:

| Tag  | Field        | Type   | Description                                                                                                                                                                                                                                                                           | Example                          |
| ---- | ------------ | ------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------- |
| 35   | msgType      | string | "8" for ExecutionReport, "9" for OrderCancelRejected                                                                                                                                                                                                                                  | "8"                              |
| 150  | execType     | string | "0" for New, "4" for Canceled, "C" for Expired, "8" for Rejected, "F" for partial fill, "A" for Pending, "H" for Trade Break, "I" Order Status                                                                                                                                        | "O"                              |
| 39   | ordStatus    | string | ‘cancelled’, ‘expired’, ...                                                                                                                                                                                                                                                           |                                  |
| 37   | orderID      | string | The unique order id assigned by the exchange                                                                                                                                                                                                                                          | "11111111"                       |
| 37   | execID       | string | Unique identifier for the execution                                                                                                                                                                                                                                                   | "123456"                         |
| 32   | lastShares   | number | The executed quantity for the order's last fill                                                                                                                                                                                                                                       | 0.5678                           |
| 31   | lastPx       | number | The executed price for the last fill                                                                                                                                                                                                                                                  | 3500.12                          |
| 151  | leavesQty    | string | For Open and Partially Filled orders this is the remaining quantity open for execution. For Cancelled and Expired orders this is the quantity than was still open before cancellation/expiration. For Rejected order this is equal to orderQty. For other states this is always zero. | 10.0                             |
| 14   | cumQty       | number | The quantity of the order which has been filled                                                                                                                                                                                                                                       | 0.123345                         |
| 60   | transactTime | string | The time the transaction occurred                                                                                                                                                                                                                                                     | "2019-08-13T13:15:35.000955868Z" |
| 6    | avgPx        | number | Calculated the Volume Weighted Average Price of all fills for this order                                                                                                                                                                                                              | 345.33                           |
| 1004 | tradeId      | string | The unique ID assigned to the order fill, also known as trade                                                                                                                                                                                                                         | "77309432596"                    |

### state

Enumerated fields are defined as follows:

| Name      | Description                                                                | Example                                                                                                                                                                                                                                                                 |
| --------- | -------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| pending   | Order is pending acceptance. Only applicable to stop and stop-limit orders |                                                                                                                                                                                                                                                                         |
| open      | Order has been accepted                                                    |                                                                                                                                                                                                                                                                         |
| rejected  | Order has been rejected                                                    | Limit and market orders can get rejected if you have no balance to fill the order even partially.                                                                                                                                                                       |
| cancelled | Order has been cancelled                                                   | A market order might get in state cancelled if you don’t have enough balance to fill it at market price. Both market orders and limit orders with IOC can have ordStatus ‘cancelled’ if there is no market for them, even without the user requesting the cancellation. |
| filled    | Order has been filled                                                      | A limit order get in state cancelled after the user requested a cancellation.                                                                                                                                                                                           |
| partial   | Order has been partially filled                                            |                                                                                                                                                                                                                                                                         |
| expired   | Order has been expired                                                     |                                                                                                                                                                                                                                                                         |

### type

| Name      | Description                                                                                                                       |
| --------- | --------------------------------------------------------------------------------------------------------------------------------- |
| limit     | order which has a price limit                                                                                                     |
| market    | order that will match at any price available in the market, starting from the best prices and filling up to the available balance |
| stop      | order which has a stop/trigger price, and when that price is reached, it triggers a market order                                  |
| stopLimit | order which has a stop price and limit price, and when the stop price is reached, it triggers a limit order at the limit price    |

### timeInForce

> To subscribe:

```json
{
  "action": "subscribe",
  "channel": "trading"
}
```

To submit & cancel orders as well as receive live order updates, subscribe to the authenticated `trading` channel.

> Server response:

```json
{
  "seqnum": 1,
  "event": "subscribed",
  "channel": "trading"
}
```

> The next message will be a snapshot of live orders for the logged on user"

```json
{
  "seqnum": 3,
  "event": "snapshot",
  "channel": "trading",
  "orders": [
    {
      "orderID": "12891851020",
      "clOrdID": "78502a08-c8f1-4eff-b",
      "symbol": "BTC-USD",
      "side": "sell",
      "ordType": "limit",
      "orderQty": 5.0e-4,
      "leavesQty": 5.0e-4,
      "cumQty": 0.0,
      "avgPx": 0.0,
      "ordStatus": "open",
      "timeInForce": "GTC",
      "text": "New order",
      "execType": "0",
      "execID": "11321871",
      "transactTime": "2019-08-13T11:30:03.000593290Z",
      "msgType": 8,
      "lastPx": 0.0,
      "lastShares": 0.0,
      "tradeId": "0",
      "price": 15000.0
    }
  ]
}
```

| Name | Description                                                                                                                                          |
| ---- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| GTC  | Good Till Cancel. The order will rest on the order book until it is cancelled or filled                                                              |
| GTD  | Good Till Date. The order will reset on the order book until it is cancelled, filled, or expired                                                     |
| FOK  | Fill or Kill. The order is either completely filled or cancelled. No Partial Fills are permitted.                                                    |
| IOC  | Immediate or Cancel. The order is either a) completely filled, b) partially filled and the remaining quantity canceled, or c) the order is canceled. |

Which TIF are supported. X for supported

|     | Market | Limit | Stop | Stop Limit |
| --- | ------ | ----- | ---- | ---------- |
| GTC |        | X     | X    | X          |
| GTD |        | X     | X    | X          |
| IOC |        | X     |      |            |
| FOK |        | X     |      |            |

The `trading` channel supports the following additional actions:

| Action             | Description      |
| ------------------ | ---------------- |
| NewOrderSingle     | Creates an order |
| CancelOrderRequest | Cancels an order |

## Create a new order (NewOrderSingle)

> Example creating a GTC limit order:

```json
{
  "action": "NewOrderSingle",
  "channel": "trading",
  "clOrdID": "Client ID 3",
  "symbol": "BTC-USD",
  "ordType": "limit",
  "timeInForce": "GTC",
  "side": "sell",
  "orderQty": 10.0,
  "price": 3400.0,
  "execInst": "ALO"
}
```

This action creates an order using the provided fields as described above.

> Each time an order changes state or has a match, an event will be sent from the server:

```json
{
  "seqnum": 3,
  "event": "updated",
  "channel": "trading",
  "msgType": "8",
  "clOrdID": "Client ID 3",
  "orderID": "999999878",
  "ordStatus": "open",
  "execType": "0",
  "symbol": "BTC-USD",
  "side": "sell",
  "orderQty": 10.0,
  "ordType": "limit",
  "price": 3400.0,
  "transactTime": "2019-08-13T13:09:34.000659345Z",
  "leavesQty": 10.0,
  "cumQty": 0.0,
  "avgPx": 0.0
}
```

> The next message will be a snapshot of live orders for the logged on user:

```json
{
  "seqnum": 3,
  "event": "snapshot",
  "channel": "trading",
  "orders": [
    {
      "orderID": "12891851020",
      "clOrdID": "78502a08-c8f1-4eff-b",
      "symbol": "BTC-USD",
      "side": "sell",
      "ordType": "limit",
      "orderQty": 5.0e-4,
      "leavesQty": 5.0e-4,
      "cumQty": 0.0,
      "avgPx": 0.0,
      "ordStatus": "open",
      "timeInForce": "GTC",
      "text": "New order",
      "execType": "0",
      "execID": "11321871",
      "transactTime": "2019-08-13T11:30:03.000593290Z",
      "msgType": 8,
      "lastPx": 0.0,
      "lastShares": 0.0,
      "tradeId": "0",
      "price": 15000.0
    }
  ]
}
```

### Examples

> Example of a reply if your market order get filled:

```json
{
  "seqnum": 5,
  "event": "updated",
  "channel": "trading",
  "orderID": "12891915594",
  "clOrdID": "b50112a2-9851-43ce-a",
  "symbol": "BTC-USD",
  "side": "sell",
  "ordType": "market",
  "orderQty": 0.001,
  "leavesQty": 0.0,
  "cumQty": 0.001,
  "avgPx": 11142.7,
  "ordStatus": "filled",
  "timeInForce": "GTC",
  "text": "Fill",
  "execType": "F",
  "execID": "11451022",
  "transactTime": "2019-08-13T13:50:02.000027480Z",
  "msgType": 8,
  "lastPx": 11142.7,
  "lastShares": 0.001,
  "tradeId": "12884910084"
}
```

> Example of a response where your limit order is accepted and resting in the orderbook without fills:

```json
{
  "seqnum": 3,
  "event": "updated",
  "channel": "trading",
  "orderID": "12895385711",
  "clOrdID": "ac3f50b0-ec1c-456f-9",
  "symbol": "BTC-USD",
  "side": "buy",
  "ordType": "limit",
  "orderQty": 0.001,
  "leavesQty": 0.001,
  "cumQty": 0.0,
  "avgPx": 0.0,
  "ordStatus": "open",
  "timeInForce": "GTC",
  "text": "New order",
  "execType": "0",
  "execID": "18389438",
  "transactTime": "2019-08-16T11:07:55.000648119Z",
  "msgType": 8,
  "lastPx": 0.0,
  "lastShares": 0.0,
  "tradeId": "0",
  "price": 10065.0
}
```

> If your limit order get rejected you will get:

```json
{
  "seqnum": 5,
  "event": "rejected",
  "channel": "trading",
  "text": "Invalid price",
  "clOrdID": "Client ID 3",
  "ordStatus": "rejected",
  "action": "NewOrderSingle"
}
```

> or:

```json
{
  "seqnum": 5,
  "event": "updated",
  "channel": "trading",
  "orderID": "-1",
  "clOrdID": "71bf645a-6619-499f-9",
  "symbol": "BTC-USD",
  "side": "sell",
  "ordType": "limit",
  "orderQty": 100.0,
  "leavesQty": 0.0,
  "cumQty": 100.0,
  "avgPx": 0.0,
  "ordStatus": "rejected",
  "timeInForce": "GTC",
  "text": "Insufficient Balance",
  "execType": "8",
  "execID": "0",
  "transactTime": "1970-01-01T00:00:00Z",
  "msgType": 8,
  "lastPx": 0.0,
  "lastShares": 0.0,
  "tradeId": "0",
  "price": 16000.3
}
```

> or:

```json
{
  "seqnum": 5,
  "event": "updated",
  "channel": "trading",
  "orderID": "0",
  "clOrdID": "12345678901234567890",
  "symbol": "BTC-USD",
  "side": "sell",
  "ordType": "limit",
  "orderQty": 0.001,
  "leavesQty": 0.001,
  "cumQty": 0.0,
  "avgPx": 0.0,
  "ordStatus": "rejected",
  "timeInForce": "GTC",
  "text": "Marketable AOL order",
  "execType": "8",
  "execID": "18331458",
  "transactTime": "2019-08-16T10:39:19.000525614Z",
  "msgType": 8,
  "lastPx": 0.0,
  "lastShares": 0.0,
  "tradeId": "0",
  "price": 100.3
}
```

> If your market order get rejected you will get:

```json
{
  "seqnum": 3,
  "event": "updated",
  "channel": "trading",
  "orderID": "-1",
  "clOrdID": "Client ID 3",
  "symbol": "BTC-USD",
  "side": "sell",
  "ordType": "market",
  "orderQty": 100.0,
  "leavesQty": 0.0,
  "cumQty": 100.0,
  "avgPx": 0.0,
  "ordStatus": "rejected",
  "timeInForce": "IOC",
  "text": "Insufficient Balance",
  "execType": "8",
  "execID": "0",
  "transactTime": "1970-01-01T00:00:00Z",
  "msgType": 8,
  "lastPx": 0.0,
  "lastShares": 0.0,
  "tradeId": "0",
  "minQty": 0.0
}
```

> or:

```json
{
  "seqnum": 1,
  "event": "updated",
  "channel": "trading",
  "orderID": "4561237891",
  "clOrdID": "Client ID 3",
  "symbol": "BTC-USD",
  "side": "buy",
  "ordType": "market",
  "orderQty": 3.0,
  "leavesQty": 3.0,
  "cumQty": 0.0,
  "avgPx": 0.0,
  "ordStatus": "cancelled",
  "timeInForce": "GTC",
  "text": "Met cash limit",
  "execType": "4",
  "execID": "1111111111",
  "transactTime": "2019-01-01T08:08:08.000888888Z",
  "msgType": 8,
  "lastPx": 0.0,
  "lastShares": 0.0,
  "tradeId": "0",
  "fee": 0.0
}
```

## Cancel an order (CancelOrderRequest)

> To cancel order:

```json
{
  "action": "CancelOrderRequest",
  "channel": "trading",
  "orderID": "999999878"
}
```

> Server response:

```json
{
  "seqnum": 18,
  "event": "updated",
  "channel": "trading",
  "orderID": "999999878",
  "clOrdID": "Client ID 3",
  "symbol": "BTC-USD",
  "side": "sell",
  "ordType": "limit",
  "orderQty": 10.0,
  "leavesQty": 10.0,
  "cumQty": 0.0,
  "avgPx": 0.0,
  "ordStatus": "cancelled",
  "timeInForce": "GTC",
  "text": "Canceled by User",
  "execType": "4",
  "execID": "11397697",
  "transactTime": "2019-08-13T13:15:35.000955868Z",
  "msgType": 8,
  "lastPx": 0.0,
  "lastShares": 0.0,
  "tradeId": "0",
  "price": 3400.0
}
```

> If the client gives an invalid orderId it will return a rejected cancellation:

```json
{
  "seqnum": 18,
  "event": "rejected",
  "channel": "trading",
  "text": "Internal server error"
}
```

## Balances

> To receive balances for a user, subscribe to the authenticated balances channel:

```json
{
  "action": "subscribe",
  "channel": "balances"
}
```

> Server response:

```json
{
  "seqnum": 1,
  "event": "subscribed",
  "channel": "balances"
}
```

> Snapshot of user balances (Zero balances are not sent in the initial snapshot):

```json
{
  "seqnum": 2,
  "event": "snapshot",
  "channel": "balances",
  "balances": [
    {
      "currency": "BTC",
      "balance": 0.00366963,
      "available": 0.00266963,
      "balance_local": 38.746779155,
      "available_local": 28.188009155,
      "rate": 10558.77
    },
    {
      "currency": "USD",
      "balance": 11.66,
      "available": 0.0,
      "balance_local": 11.66,
      "available_local": 0.0,
      "rate": 1.0
    },
    {
      "currency": "ETH",
      "balance": 0.18115942,
      "available": 0.18115942,
      "balance_local": 37.289855013,
      "available_local": 37.289855013,
      "rate": 205.84
    }
  ],
  "total_available_local": 65.477864168,
  "total_balance_local": 87.696634168
}
```

> Each time the balance changes, a new snapshot event will be sent from the server; this channel has only ‘snapshot’ event, not ‘updates’

```json
{
  "seqnum": 19,
  "event": "snapshot",
  "channel": "balances",
  "balances": [
    {
      "currency": "BTC",
      "balance": 0.00266963,
      "available": 0.00166963,
      "balance_local": 28.188009155,
      "available_local": 17.629239155,
      "rate": 10558.77
    },
    {
      "currency": "USD",
      "balance": 22.18,
      "available": 10.52,
      "balance_local": 22.18,
      "available_local": 10.52,
      "rate": 1.0
    },
    {
      "currency": "ETH",
      "balance": 0.18115942,
      "available": 0.18115942,
      "balance_local": 37.289855013,
      "available_local": 37.289855013,
      "rate": 205.84
    }
  ],
  "total_available_local": 65.439094168,
  "total_balance_local": 87.657864168
}
```
