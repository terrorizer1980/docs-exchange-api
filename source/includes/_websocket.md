# Websocket API

The Websocket API is used to stream market data, user data and to interact with the trading system in real time.
The messages are serialized using JSON.

## Connection and authentication

```json
{
  "action": "auth",
  "token": "your-jwt-token"
}
```

The WS endpoint are, depending on the environment:

| Environment | URI                                           |
| ----------- | --------------------------------------------- |
| local       | `ws://localhost:8080/trading/v1`              |
| dev         | `wss://ws.dev.blockchain.info/trading/v1`     |
| staging     | `wss://ws.staging.blockchain.info/trading/v1` |
| prod        | `wss://ws.blockchain.info/trading/v1`         |

To authenticate with the websocket server, you must send the following message in the 5 seconds following your connection:

If the message is not sent after 5 sec, or is invalid, you’ll be disconnected automatically.

## Messages

```json
{
  "channel": "auth",
  "seqnum": 0,
  "connected": true
}
```

> Server response:

```json
{
  "channel": "error",
  "source": "ticker",
  "message": "Invalid instrument key",
  "seqnum": 1337
}
```

Messages have a `seqnum` attached to them, which starts at 0 and increases by 1 for each message sent by the server. If you receive an out-of-order message, the recommended action is to disconnect and reconnect.

All messages sent back by the server have a `channel` attribute, if the `channel` is `error`, another field named `message` describes the error. The `source` field describe which channel the error is from (if from a channel):

## Heartbeats

```json
{
  "channel": "heartbeat",
  "seqnum": 1
}
```

Once connected, you will receive regular heartbeats:

## Disconnection

```json
{
  "channel": "auth",
  "seqnum": 1337,
  "connected": false
}
```

If you get disconnected by the server (invalid auth, token timeout, etc..), you’ll receive this message:

## Actions

Messages you send to the websocket server have an `action` attribute, the most common actions are `subscribe` and `unsubscribe`.

### Subscribe

```json
{
  "action": "subscribe",
  "channel": "<channel_name>"
}
```

The `subscribe` action is used to subscribe to a channel:

Some channels necessitate extra arguments like instrument keys, etc..

### Unsubscribe

```json
{
  "action": "unsubscribe",
  "channel": "<channel_name>"
}
```

The `unsubscribe` action is used to unsubscribe from a channel:

## Data types

Dates and timestamps are returned as ISO 8601 strings in the UTC timezone (2018-03-02T04:56:59Z)

## Market Data

### Tickers

> Subscribe to Ticker:

```json
{
  "action": "subscribe",
  "channel": "ticker",
  "instrument": "XBLK-BTC-USD-C"
}
```

> Ticker Updates:

```json
{
  "seqnum": 1337,
  "channel": "ticker",
  "instrument": "XBLK-BTC-USD-C",
  "bid": 10000.01,
  "ask": 10000.02,
  "timestamp": "2018-03-02T11:49:05Z"
}
```

> Unsubscribe from Ticker:

```json
{
  "action": "unsubscribe",
  "channel": "ticker",
  "instrument": "XBLK-BTC-USD-C"
}
```

You can subscribe to tickers for instruments using the following message:

You will then get updates about ticker for this instrument:

To unsubscribe:

### Top of Book

> Subscribe to Top of Book

```json
{
  "action": "subscribe",
  "channel": "topofbook",
  "instrument": "XBLK-BTC-USD-C",
  "depth": "FUNDED"
}
```

> Top of Book Ticker Updates

```json
{
  "seqnum": 1337,
  "channel": "topofbook",
  "instrument": "XBLK-BTC-USD-C",
  "depth": "FUNDED",
  "bid": 10000.01,
  "bidSize": 0.53454,
  "ask": 10000.02,
  "askSize": 1.59306
}
```

> Unsubcribe from Top of Book

```json
{
  "action": "unsubscribe",
  "channel": "topofbook",
  "instrument": "XBLK-BTC-USD-C",
  "depth": "FUNDED"
}
```

You can subscribe to the top of book for instruments, specifying the depth type, using the following message:

You will then get updates about ticker for this instrument:

To unsubscribe:

### Market Trades

> Subscribe to Market Trades

```json
{
  "action": "subscribe",
  "channel": "markettrades",
  "instrument": "XGDX-BTC-USD-C"
}
```

> Market Trades Ticker updates

```json
{
  "seqnum": 1337,
  "channel": "markettrades",
  "instrument": "XGDX-BTC-USD-C",
  "timestamp": "2018-03-15T08:43:32.456Z",
  "price": 7854.34,
  "amount": 0.5396,
  "type": "MARKET",
  "side": "BUY"
}
```

> Unsubscribe from Market Trades

```json
{
  "action": "unsubscribe",
  "channel": "markettrades",
  "instrument": "XGDX-BTC-USD-C"
}
```

You can subscribe to market trades for instruments using the following message:

You will then get updates about market trades for this instrument:
To unsubscribe:

### Large Trades

> Subscribe to Larger Trades

```json
{
  "action": "subscribe",
  "channel": "whales",
  "selector": "BTC-USD",
  "baseQty": 1.0
}
```

> Larger Trades Ticker Updates

```json
{
  "seqnum": 1337,
  "channel": "whales",
  "instrument": "XGDX-BTC-USD-C",
  "timestamp": "2018-03-15T08:43:32.456Z",
  "price": 7854.34,
  "amount": 1.5396,
  "type": "MARKET",
  "side": "BUY"
}
```

> Unsubscribe from Larger Trades

```json
{
  "action": "unsubscribe",
  "channel": "whales",
  "selector": "BTC-USD",
  "baseQty": 1.0
}
```

You can filter the market trades feed for a given pair/instrument to only get information about trades larger than a given quantity:

You will then get updates about market trades that match the selector and are above the minQty:

To unsubscribe:

### Funded/Market Depth

> Subscribe to Funded Depth

```json
{
  "action": "subscribe",
  "channel": "depth",
  "depth": "FUNDED",
  "instrument": "XGDX-BTC-USD-C"
}
```

> Subscribe to Market Depth

```json
{
  "action": "subscribe",
  "channel": "depth",
  "depth": "MARKET",
  "instrument": "XGDX-BTC-USD-C"
}
```

> Funded/Market Depth Ticker Updates

```json
{
  "seqnum": 1337,
  "channel": "depth",
  "instrument": "XGDX-BTC-USD-C",
  "depth": "FUNDED",
  "bids": [
    { "price": 1.0, "quantity": 0.36937, "venue": "XGDX" },
    { "price": 0.99, "quantity": 23.689365, "venue": "XGDX" }
  ],
  "asks": [
    { "price": 1.01, "quantity": 68.4903, "venue": "XGDX" },
    { "price": 1.02, "quantity": 783.457395, "venue": "XGDX" }
  ]
}
```

```json
{
  "seqnum": 1337,
  "channel": "depth",
  "instrument": "AGGR-BTC-USD-C",
  "depth": "FUNDED",
  "bids": [
    { "price": 1.0, "quantity": 0.36937, "venue": "XBTS" },
    { "price": 1.0, "quantity": 14.5048, "venue": "XGDX" },
    { "price": 0.99, "quantity": 23.689365, "venue": "XBFX" }
  ],
  "asks": [
    { "price": 1.01, "quantity": 68.4903, "venue": "XGDX" },
    { "price": 1.02, "quantity": 783.457395, "venue": "XBTR" }
  ]
}
```

> Unsubscribe from Funded/Market Depth

```json
{
  "action": "unsubscribe",
  "channel": "depth",
  "depth": "FUNDED",
  "instrument": "XGDX-BTC-USD-C"
}
```

You can subscribe to funded/market depth for instruments using the following message:

You will then get updates about the depth for this instrument:

For an aggregated book:

To unsubscribe:

### Quote Depth

> Subscribe to Core Price

```json
{
  "action": "subscribe",
  "channel": "depth",
  "depth": "QUOTE",
  "instrument": "XBLK-BTC-USD-C"
}
```

> Core Price Ticker Updates

```json
{
  "seqnum": 1337,
  "channel": "quote",
  "instrument": "XBLK-BTC-USD-C",
  "depth": "QUOTE",
  "bids": [
    { "price": 1.0, "quantity": 1, "venues": ["XGDX", "XBFX"] },
    { "price": 0.99, "quantity": 2, "venues": ["XBTS", "XGDX"] }
  ],
  "asks": [
    { "price": 1.01, "quantity": 1, "venues": ["XBTS", "XGDX"] },
    { "price": 1.02, "quantity": 2, "venues": ["XGDX", "XBFX", "XBTS"] }
  ]
}
```

> Unsubscribe from Core Price

```json
{
  "action": "unsubscribe",
  "channel": "depth",
  "depth": "QUOTE",
  "instrument": "XBLK-BTC-USD-C"
}
```

You can subscribe to core price updates for instruments using the following message:
You will then get updates about our core price for this instrument:
To unsubscribe:

## Venue information

### Balances

> Subscribe to Balances:

```json
{
  "action": "subscribe",
  "channel": "balances"
}
```

> Balance Ticker Updates:

```json
{
  "seqnum": 1337,
  "channel": "balances",
  "venue": "XGDX",
  "currency": "BTC",
  "value": 134.694853
}
```

> Unsubscribe to Balances:

```json
{
  "action": "unsubscribe",
  "channel": "balances"
}
```

You can subscribe to balances updates using the following message:

You will then receive balance updates when the balance at a venue changes. For example, if the BTC balance of GDAX, you would receive:

To unsubscribe:

### Total Balances

> Subscribe to Total Balances

```json
{
  "action": "subscribe",
  "channel": "totalBalances"
}
```

> Total Balance Ticker Updates

```json
{
  "seqnum": 1337,
  "channel": "totalBalances",
  "venue": "XGDX",
  "fiatTotal": 200000.0,
  "fiatCryptoTotal": 100000.0,
  "totalByCurrencies": {
    "BTC": 50000.0,
    "ETH": 50000.0,
    "USD": 100000.0
  }
}
```

You can subscribe to total balances updates using the following message:

You will then receive balance updates each 10 seconds of the total balance in USD. For example, for GDAX, you would receive:

> Unsubscribe from Total Balances

```json
{
  "action": "unsubscribe",
  "channel": "totalBalances"
}
```

To unsubscribe:

## Orders

### Order Updates

> Subscribe to Orders

```json
{
  "action": "subscribe",
  "channel": "orders"
}
```

> Order has been accepted by system

```json
{
  "seqnum": 1337,
  "channel": "orders",
  "created": "2018-01-01T00:00:00.000Z",
  "id": "123e4567-e89b-12d3-a456-426655440000",
  "ref": "6994",
  "instrument": "XGDX-BTC-USD-C",
  "route": "DIRECT",
  "parent": null,
  "side": "BUY",
  "type": "LIMIT",
  "quantity": 0.1,
  "executedQuantity": 0,
  "price": 5000.01,
  "executedPrice": null,
  "portfolio": "BTC_BC",
  "state": {
    "coreState": "INITIAL",
    "pendingState": "NONE",
    "fillState": "NONE"
  },
  "attributes": {}
}
```

> Order has been locked by a venue

```json
{
  "seqnum": 1337,
  "channel": "orders",
  "created": "2018-01-01T00:00:00.000Z",
  "id": "123e4567-e89b-12d3-a456-426655440000",
  "ref": "6994",
  "instrument": "XGDX-BTC-USD-C",
  "route": "DIRECT",
  "parent": null,
  "side": "BUY",
  "type": "LIMIT",
  "quantity": 0.1,
  "executedQuantity": 0,
  "price": 5000.01,
  "executedPrice": null,
  "portfolio": "BTC_BC",
  "state": {
    "coreState": "LOCKED",
    "pendingState": "NONE",
    "fillState": "NONE"
  },
  "attributes": {}
}
```

> Order has been sitting on the book

```json
{
  "seqnum": 1337,
  "channel": "orders",
  "created": "2018-01-01T00:00:00.000Z",
  "id": "123e4567-e89b-12d3-a456-426655440000",
  "ref": "6994",
  "instrument": "XGDX-BTC-USD-C",
  "route": "DIRECT",
  "parent": null,
  "side": "BUY",
  "type": "LIMIT",
  "quantity": 0.1,
  "executedQuantity": 0,
  "price": 5000.01,
  "executedPrice": null,
  "portfolio": "BTC_BC",
  "state": {
    "coreState": "WORKING",
    "pendingState": "NONE",
    "fillState": "NONE"
  },
  "attributes": {}
}
```

> Order has been partially filled

```json
{
    "seqnum": 1337,
    "channel": "orders",
    "created": "2018-01-01T00:00:00.000Z",
    "id": "123e4567-e89b-12d3-a456-426655440000",
    "ref": "6994",
    "instrument" : "XGDX-BTC-USD-C",
    "route": "DIRECT",
    "parent": null,
    "side": "BUY",
    "type": "LIMIT",
    "quantity": 0.1,
    "executedQuantity": 0.05,
    "price": 5000.01,
    "executedPrice": 5000.01,
    "portfolio" : "BTC_BC",
    "state": {
        "coreState": "WORKING",
        "pendingState": "NONE",
        "fillState": "PARTIALLY_FILLED"
    }
    "attributes": {}
}
```

> Order has been completed, no more updates will be sent

```json
{
  "seqnum": 1337,
  "channel": "orders",
  "created": "2018-01-01T00:00:00.000Z",
  "id": "123e4567-e89b-12d3-a456-426655440000",
  "ref": "6994",
  "instrument": "XGDX-BTC-USD-C",
  "route": "DIRECT",
  "parent": null,
  "side": "BUY",
  "type": "LIMIT",
  "quantity": 0.1,
  "executedQuantity": 0.1,
  "price": 5000.01,
  "executedPrice": 5000.01,
  "portfolio": "BTC_BC",
  "state": {
    "coreState": "FILLED",
    "pendingState": "NONE",
    "fillState": "FILLED"
  },
  "attributes": {}
}
```

> Unsubscribe from orders

```json
{
  "action": "unsubscribe",
  "channel": "orders"
}
```

To subscribe to order updates, send the following message:

When an order changes state or receives a fill, an update is sent, execution reports are sent separately.

Here’s the example of the lifecycle of an order:

The order has been accepted by the system.

The order has been locked by a venue.

The order is now sitting on the book.

The order got partially filled.

The order has been completed, no more updates will be sent.

To unsubscribe, send:

### Execution reports

> Subscribe to Execution Reports

```json
{
  "action": "subscribe",
  "channel": "reports"
}
```

> Execution Report Ticker Update

```json
{
  "seqnum": 1337,
  "channel": "reports",
  "id": "123e4567-e89b-12d3-a456-426655440000",
  "ref": "6994",
  "timestamp": "2018-04-01T06:46:34",
  "tradeType": "DMA",
  "quantity": "0.05",
  "price": "5000.01"
}
```

> Unsubscribe from Execution Reports

```json
{
  "action": "unsubscribe",
  "channel": "reports"
}
```

To subscribe to execution reports, send the following message:

Each time an order gets a fill, you’ll receive a notification as follows:

To unsubscribe to execution reports, send the following message:

## Portfolio

### Portfolio Updates

> Subscribe to Portfolio Updates

```json
{
  "action": "subscribe",
  "channel": "portfolio"
}
```

> Portfolio Update Ticker Update

```json
{
  "seqnum": 1337,
  "channel": "portfolio",
  "book": "BC_MM",
  "portfolio": "BTC_BC",
  "openingQuantity": 1000.0,
  "executedQuantity": 999.9,
  "transactions": [
    {
      "instrument": "XGDX-BTC-USD-C",
      "timestamp": "2018-03-16T13:35:45.045Z",
      "side": "SELL",
      "quantity": 0.1,
      "price": 10000.0
    }
  ]
}
```

> Unsubscribe from Portfolio Updates

```json
{
  "action": "unsubscribe",
  "channel": "portfolio"
}
```

You can subscribe to portfolio updates using the following message:
You will then receive the full portfolio when it updates:

To unsubscribe:

### Portfolio Analytics

> Subscribe to Portfolio Analytics

```json
{
  "action": "subscribe",
  "channel": "portfolioanalytics"
}
```

> Portfolio Analytic Ticker Update

```json
{
  "seqnum": 1337,
  "channel": "portfolioanalytics",
  "book": "BC_MM",
  "portfolio": "BTC_BC",
  "referencePrice": 9000.0,
  "spot": 9100.0,
  "openingQuantity": 2.0,
  "quantity": 10.0,
  "delta": 91000.0,
  "pnl": 1100.0
}
```

> Unsubscribe from Portfolio Analytics

```json
{
  "action": "unsubscribe",
  "channel": "portfolioanalytics"
}
```

You can subscribe to portfolio analytics using the following message:

You will then receive the last analytics updates about a portfolio:

To unsubscribe:

### Portfolio Risk

> Subscribe to Portfolio Risk

```json
{
  "action": "subscribe",
  "channel": "portfoliorisk"
}
```

> Portfolio Risk Ticker Update

```json
{
  "seqnum": 1337,
  "channel": "portfolioanalytics",
  "portfolio": "BTC_BC",
  "riskPosition": 10.0,
  "riskLongPosition": 7000.0,
  "riskLongPositionVwap": 6500.0,
  "riskShortPosition": 6500.0,
  "riskShortPositionVwap": 6300.0
}
```

> Unsubscribe from Portfolio Risk

```json
{
  "action": "unsubscribe",
  "channel": "portfoliorisk"
}
```

You can subscribe to portfolio risk using the following message:

You will then receive the last risk updates about a portfolio:

To unsubscribe:

## Miscellaneous

### Configuration

> Subscribe to Configuration Updates

```json
{
  "action": "subscribe",
  "channel": "config"
}
```

> Configuration Update Ticker Update

```json
{
  "seqnum": 1337,
  "channel": "config",
  "configuration": {
    "venue.aggregate.venues": ["XGDX", "XBTS"],
    "venue.quote.tiers": [1.0, 10.0, 20.0, 50.0]
  }
}
```

> Unsubscribe from Configuration Updates

```json
{
  "action": "unsubscribe",
  "channel": "config"
}
```

You can subscribe to configuration updates using the following message:

You will then receive the full public config when it updates:

To unsubscribe:
