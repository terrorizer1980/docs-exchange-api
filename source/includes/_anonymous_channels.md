# Anonymous Channels

## Heartbeat

> Subscribe to channel:

```json
{
  "action": "subscribe",
  "channel": "heartbeat"
}
```

A heartbeat can be sent by the server by subscribing to the `heartbeat` channel.

> Server response:

```json
{
  "seqnum": 0,
  "event": "subscribed",
  "channel": "heartbeat"
}
```

The server will send confirmation of the subscription.

> Snapshot Example:

```json
{
  "seqnum": 1,
  "event": "updated",
  "channel": "heartbeat",
  "timestamp": "2019-05-31T08:36:45.666753Z"
}
```

Snapshot messages are sent every 5 seconds and have the following format:

## L2 Order Book

> Subscribe to channel:

```json
{
  "action": "subscribe",
  "channel": "l2",
  "symbol": "BTC-USD"
}
```

Level 2 Order Book data is available through the `l2` channel. This channel returns the volume available at each price. All the price levels are retrieved with this channel. Subscribing is done per symbol. Each entry in bids and asks arrays is a price level, along with its price (`px`), quantity (`qty`) and number of orders (`num`) attributes.

> Server response:

```json
{
  "seqnum": 1,
  "event": "subscribed",
  "channel": "l2",
  "symbol": "BTC-USD"
}
```

> Snapshots:

```json
{
  "seqnum": 2,
  "event": "snapshot",
  "channel": "l2",
  "symbol": "BTC-USD",
  "bids": [
    {
      "px": 8723.45,
      "qty": 1.45,
      "num": 2
    },
    {
      "px": 8124.45,
      "qty": 123.45,
      "num": 1
    }
  ],
  "asks": [
    {
      "px": 8730.0,
      "qty": 1.55,
      "num": 2
    },
    {
      "px": 8904.45,
      "qty": 13.66,
      "num": 2
    }
  ]
}
```

> There is no ordering guarantee in bids and asks entries.

```json
{
  "seqnum": 3,
  "event": "updated",
  "channel": "l2",
  "symbol": "BTC-USD",
  "bids": [
    {
      "px": 8723.45,
      "qty": 1.1,
      "num": 1
    }
  ],
  "asks": []
}
```

> An update with qty equal to 0, means the price should be removed.

```json
{
  "seqnum": 4,
  "event": "updated",
  "channel": "l2",
  "symbol": "BTC-USD",
  "bids": [
    {
      "px": 8723.45,
      "qty": 0.0,
      "num": 0
    }
  ],
  "asks": []
}
```

## L3 Order Book

> Subscribe to channel:

```json
{
  "action": "subscribe",
  "channel": "l3",
  "symbol": "BTC-USD"
}
```

Level 3 Order Book data is available through the `l3` channel. This channel returns all the order updates reaching the exchange; by applying the updates to the snapshot you can recreate the full state of the orderbook. Subscribing is done per symbol. Each entry in bids and asks arrays is an order, along with its id (`id`), price (`px`) and quantity (`qty`) attributes.

> Server response:

```json
{
  "seqnum": 1,
  "event": "subscribed",
  "channel": "l3",
  "symbol": "BTC-USD"
}
```

> Example snapshot:

```json
{
  "seqnum": 2,
  "event": "snapshot",
  "channel": "l3",
  "symbol": "BTC-USD",
  "bids": [
    {
      "id": "1234",
      "px": 8723.45,
      "qty": 1.1
    },
    {
      "id": "1235",
      "px": 8723.45,
      "qty": 0.35
    },
    {
      "id": "234",
      "px": 8124.45,
      "qty": 123.45
    }
  ],
  "asks": [
    {
      "id": "2222",
      "px": 8730.0,
      "qty": 0.65
    },
    {
      "id": "2225",
      "px": 8730.0,
      "qty": 0.9
    },
    {
      "id": "2343",
      "px": 8904.45,
      "qty": 8.66
    },
    {
      "id": "2353",
      "px": 8904.45,
      "qty": 5.0
    }
  ]
}
```

> Updates will follow. An update with qty equal to 0, means the order should be removed.

```json
{
  "seqnum": 3,
  "event": "updated",
  "channel": "l3",
  "symbol": "BTC-USD",
  "bids": [
    {
      "id": "1234",
      "px": 8723.45,
      "qty": 0
    }
  ],
  "asks": []
}
```

## Prices

> Subscribe to channel:

```json
{
  "action": "subscribe",
  "channel": "prices",
  "symbol": "BTC-USD",
  "granularity": 60
}
```

To receive candlestick market data you can subscribe to the prices channel.
Subscriptions are per `symbol` and `granularity` (in seconds) has to be specified.
Supported granularity values are: `60, 300, 900, 3600, 21600, 86400`

You can subscribe for multiple symbols but not for multiple granularities per symbol.

> Server response:

```json
{
  "seqnum": 0,
  "event": "subscribed",
  "channel": "prices",
  "symbol": "BTC-USD"
}
```

The server will send confirmation of the subscription.

> Updated event on the prices channel:

```json
{
  "seqnum": 2,
  "event": "updated",
  "channel": "prices",
  "symbol": "BTC-USD",
  "price": [1559039640, 8697.24, 8700.98, 8697.27, 8700.98, 0.431]
}
```

The price data is an array consisting of [`timestamp`, `open`, `high`, `low`, `close`, `volume`]

## Symbols

To receive symbol updates, subscribe to the `symbols` channel. The server will send confirmation of the subscription. The next message on this channel will be a snapshot of the current `symbol` status.

When the `symbol` is not halted the auction data in the message may be blank.

When a `symbol` is in a halt state the auction data will populate as the book builds. When an opening time has been chosen, the `auction-time` field will show the opening time. Subsequent updates will be sent only if the `symbol` status changes in any way.

### Field Description

| Field                     | Description                                                                                                                                                   |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| auction-price             | If the symbol is halted and will open on an auction, this will be the opening price.                                                                          |
| auction-size              | Opening size                                                                                                                                                  |
| auction-time              | Opening time in HHMM format.                                                                                                                                  |
| imbalance                 | Auction imbalance. If > 0 then there will be buy orders left over at the auction price. If < 0 then there will be sell orders left over at the auction price. |
| status                    | Symbol status; open, close, suspend, halt, halt-freeze.                                                                                                       |
| base_currency             | The currency quantities are expressed in                                                                                                                      |
| base_currency_scale       | The number of decimals the currency can be split in                                                                                                           |
| counter_currency          | The currency prices are expressed in                                                                                                                          |
| counter_currency_scale    | The number of decimals the currency can be split in                                                                                                           |
| min_price_increment       | The price of the instrument must be a multiple of min_price_increment \* (10^-min_price_increment_scale)                                                      |
| min_price_increment_scale | The price of the instrument must be a multiple of min_price_increment \* (10^-min_price_increment_scale)                                                      |
| min_order_size            | The minimum quantity for an order for this instrument must be min_order_size\*(10^-min_order_size_scale)                                                      |
| min_order_size_scale      | The minimum quantity for an order for this instrument must be min_order_size\*(10^-min_order_size_scale)                                                      |
| max_order_size            | The maximum quantity for an order for this instrument is max_order_size\*(10^-max_order_size_scale). If this equal to zero, there is no limit                 |
| max_order_size_scale      | The maximum quantity for an order for this instrument is max_order_size\*(10^-max_order_size_scale). If this equal to zero, there is no limit                 |

> Subscribe to channel:

```json
{
  "action": "subscribe",
  "channel": "symbols"
}
```

> Server response:

```json
{
  "seqnum": 0,
  "event": "subscribed",
  "channel": "symbols"
}
```

> Example symbol status:

```json
{
  "seqnum": 1,
  "event": "snapshot",
  "channel": "symbols",
  "symbols": {
    "BTC-USD": {
      "base_currency": "BTC",
      "base_currency_scale": 8,
      "counter_currency": "USD",
      "counter_currency_scale": 2,
      "min_price_increment": 10,
      "min_price_increment_scale": 0,
      "min_order_size": 50,
      "min_order_size_scale": 2,
      "max_order_size": 0,
      "max_order_size_scale": 8,
      "lot_size": 5,
      "lot_size_scale": 2,
      "status": "halt",
      "id": 1,
      "auction_price": 0.0,
      "auction_size": 0.0,
      "auction_time": "",
      "imbalance": 0.0
    },
    "ETH-BTC": {
      "base_currency": "ETH",
      "base_currency_scale": 8,
      "counter_currency": "BTC",
      "counter_currency_scale": 8,
      "min_price_increment": 100,
      "min_price_increment_scale": 8,
      "min_order_size": 220001,
      "min_order_size_scale": 8,
      "max_order_size": 0,
      "max_order_size_scale": 8,
      "lot_size": 0,
      "lot_size_scale": 0,
      "status": "open",
      "id": 3,
      "auction_price": 0.0,
      "auction_size": 0.0,
      "auction_time": "",
      "imbalance": 0.0
    },
    "BTC-EUR": {
      "base_currency": "BTC",
      "base_currency_scale": 8,
      "counter_currency": "EUR",
      "counter_currency_scale": 2,
      "min_price_increment": 10,
      "min_price_increment_scale": 0,
      "min_order_size": 50,
      "min_order_size_scale": 2,
      "max_order_size": 0,
      "max_order_size_scale": 0,
      "lot_size": 0,
      "lot_size_scale": 0,
      "status": "closed",
      "id": 4,
      "auction_price": 0.0,
      "auction_size": 0.0,
      "auction_time": "",
      "imbalance": 0.0
    },
    "ETH-EUR": {
      "base_currency": "ETH",
      "base_currency_scale": 8,
      "counter_currency": "EUR",
      "counter_currency_scale": 2,
      "min_price_increment": 10,
      "min_price_increment_scale": 0,
      "min_order_size": 50,
      "min_order_size_scale": 2,
      "max_order_size": 500,
      "max_order_size_scale": 2,
      "lot_size": 0,
      "lot_size_scale": 0,
      "status": "open",
      "id": 5,
      "auction_price": 0.0,
      "auction_size": 0.0,
      "auction_time": "",
      "imbalance": 0.0
    },
    "ETH-USD": {
      "base_currency": "ETH",
      "base_currency_scale": 8,
      "counter_currency": "USD",
      "counter_currency_scale": 2,
      "min_price_increment": 5,
      "min_price_increment_scale": 0,
      "min_order_size": 50,
      "min_order_size_scale": 2,
      "max_order_size": 0,
      "max_order_size_scale": 0,
      "lot_size": 0,
      "lot_size_scale": 0,
      "status": "open",
      "id": 2,
      "auction_price": 0.0,
      "auction_size": 0.0,
      "auction_time": "",
      "imbalance": 0.0
    }
  }
}
```

> Example not halted symbol:

```json
{
  "seqnum": 1,
  "event": "updated",
  "channel": "symbols",
  "symbol": "BTC-USD",
  "auction-price": 0,
  "auction-size": 0,
  "auction-time": "",
  "imbalance": 0,
  "status": "open"
}
```

> Example halted symbol:

```json
{
  "seqnum": 1,
  "event": "updated",
  "channel": "symbols",
  "symbol": "BTC-USD",
  "auction-price": 4500.5,
  "auction-size": 220.125,
  "auction-time": "2230",
  "imbalance": -0.5,
  "status": "halt"
}
```

## Ticker

> Subscribe to channel:

```json
{
  "action": "subscribe",
  "channel": "ticker",
  "symbol": "BTC-USD"
}
```

In order to receive ticker updates, `ticker` channel is available. Subscriptions are again per symbol. The server will send confirmation of the subscription.

> Server response:

```json
{
  "seqnum": 0,
  "event": "subscribed",
  "channel": "ticker"
}
```

> Example ticker snapshot:

```json
{
  "seqnum": 8,
  "event": "snapshot",
  "channel": "ticker",
  "symbol": "BTC-USD",
  "price_24h": 4988.0,
  "volume_24h": 0.3015,
  "last_trade_price": 5000.0
}
```

## Trades

In order to receive trade updates on executions within each market across the Exchange, you can subscribe to the `trades` channel.

> Subscriptions are again per symbol.

```json
{
  "action": "subscribe",
  "channel": "trades",
  "symbol": "ETH-USD"
}
```

> The server will send confirmation of the subscription.

```json
{
  "seqnum": 0,
  "event": "subscribed",
  "channel": "trades",
  "symbol": "ETH-USD"
}
```

> Example of a trade update:

```json
{
  "seqnum": 21,
  "event": "updated",
  "channel": "trades",
  "symbol": "BTC-USD",
  "timestamp": "2019-08-13T11:30:06.100140Z",
  "side": "sell",
  "qty": 8.5e-5,
  "price": 11252.4,
  "trade_id": "12884909920"
}
```
