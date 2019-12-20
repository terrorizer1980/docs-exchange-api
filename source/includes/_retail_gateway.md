# Introduction

Welcome to Blockchain.com's Exchange API and developer documentation. These documents detail and give examples of various functionality offered by the API such as receiving real time market data, requesting balance information and performing trades. Interaction with the API will require an API key, request one by logging into the Exchange and going to Settings > API Settings.

# Retail Gateway - WS API

The Websocket API can be used to receive market data and to interact with the trading system in real time. Every message is in a JSON format and trading messages use the FIX standard for naming of fields, and message types.

Each type of data is provided over a dedicated channel. Clients need to subscribe to all relevant channels they wish to receive real time updates from.

## Connection and authentication

The WS endpoint are, depending on the environment:

| Environment | URI                                                      |
| ----------- | -------------------------------------------------------- |
| staging     | `wss://ws.staging.blockchain.info/mercury-gateway/v1/ws` |
| prod        | `wss://ws.prod.blockchain.info/mercury-gateway/v1/ws`    |

In order to connect you have to add the following headers to the connection request

| Environment | Headers                                   |
| ----------- | ----------------------------------------- |
| staging     | `Origin: https://exchange.blockchain.com` |
| prod        | `Origin: https://exchange.blockchain.com` |

## Requests

Each Message sent to the server will have at least 2 fields: action and channel.

### Channel

A channel provides context about the type of data being communicated between the client and server. There are multiple channels available:

| Channel   | Visibility    | Description                                                 |
| --------- | ------------- | ----------------------------------------------------------- |
| trading   | authenticated | Submit & cancel orders, receive order snapshots and updates |
| heartbeat | anonymous     | Receive heartbeat messages                                  |
| prices    | anonymous     | Receive candlestick market data                             |
| ticker    | anonymous     | Receive ticker messages                                     |
| symbols   | anonymous     | Receive symbol messages                                     |
| l2        | anonymous     | Receive level 2 order book data (aggregated)                |
| l3        | anonymous     | Receive level 3 order book data (aggregated)                |
| auth      | authenticated | To authenticate a web socket connection                     |
| balances  | authenticated | To receive balance updates                                  |

### Action

`action` describes what action to take for the provided channel. The follow standard action's are supported by all channels (with the exception of the auth channel):

| Action      | Description                                          |
| ----------- | ---------------------------------------------------- |
| subscribe   | Subscribe to the provided channel and attributes     |
| unsubscribe | Unsubscribe from the provided channel and attributes |

A channel may expose other bespoke actions.

## Response

### Sequence Numbers

Each message sent from the server will contain a sequence number `seqnum` which will be incremented by 1 with each message. If the client receives a `seqnum` which has skipped one or more sequences, it indicates that a message was missed and the client is recommended to restart the websocket connection.

### Event

In addition, each response field will contain an event field with the corresponding channel to indicate the purpose of the message. The following events are supported:

| Event        | Type  | Description                                                                                                 |
| ------------ | ----- | ----------------------------------------------------------------------------------------------------------- |
| subscribed   | admin | The channel was successfully subscribed to                                                                  |
| unsubscribed | admin | The channel was successfully unsubscribed to                                                                |
| rejected     | admin | the last action for the channel was rejected. A text field will be provided about the reason for the reject |
| snapshot     | app   | A channel snapshot has been provided                                                                        |
| updated      | app   | An update corresponding to the channel has occurred                                                         |

Each time an `action` is applied to a channel, an administrative event is sent from the server to notify whether the `action` was applied successfully.

## Data Types

All Messages use the standard JSON format. The following data types are supported:

| Type      | Description                                                                                                                   | Example                 |
| --------- | ----------------------------------------------------------------------------------------------------------------------------- | ----------------------- |
| number    | Signed decimal number                                                                                                         | 1234.45                 |
| string    | UTF-8 encoded unicode string                                                                                                  | "Authentication Failed" |
| timestamp | UTC Timestamps following the convention YYYY-MM-DDTHH:MM:SS.ssssssZ or YYYY-MM-DDTHH:MM:SS.000ssssssZ or YYYY-MM-DDT00:00:00Z |                         |

## API fair usage and rate limit

```json
{
  "event": "rejected",
  "text": "Connection throttling enabled, your messages will be ignored."
}
```

Currently there is a limit of 1200 messages per minute. If the limit is exceeded, you will receive a `rejected` event. After waiting a minute, normal functionality will resume and you will be able to send messages again.

## Response data throttling and delays

The response and update messages are never delayed, clients will get every update as soon as it happens.
