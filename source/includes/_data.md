# Blockchain data

Query JSON data on blocks and transactions. Almost all the data you see on this website is available in JSON format.

<aside class="notice">
Some API calls are available with <a href="https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS">CORS headers</a> if you add a <code>&cors=true</code> parameter to the GET request
</aside>

## Single Block

```shell
curl "https://blockchain.info/rawblock/0000000000000bae09a7a393a8acded75aa67e46cb81f7acaa5ad94f9eacd103"
```

> Response:

```json
{
    "hash":"0000000000000bae09a7a393a8acded75aa67e46cb81f7acaa5ad94f9eacd103",
    "ver":1,
    "prev_block":"00000000000007d0f98d9edca880a6c124e25095712df8952e0439ac7409738a",
    "mrkl_root":"935aa0ed2e29a4b81e0c995c39e06995ecce7ddbebb26ed32d550a72e8200bf5",
    "time":1322131230,
    "bits":437129626,
    "nonce":2964215930,
    "n_tx":22,
    "size":9195,
    "block_index":818044,
    "main_chain":true,
    "height":154595,
    "received_time":1322131301,
    "relayed_by":"108.60.208.156",
    "tx":[--Array of Transactions--]
}
```

`GET https://blockchain.info/rawblock/$block_hash`

### Path Parameters

Parameter | Description
--------- | -----------
block_hash | hash of the block

### Query Parameters

Parameter | Description
--------- | -----------
format | You can also request the block to return in binary form (Hex encoded) using ?format=hex

## Single Transaction

```shell
curl "https://blockchain.info/rawtx/b6f6991d03df0e2e04dafffcd6bc418aac66049e2cd74b80f14ac86db1e3f0da"
```

> Response:

```json
{
    "hash":"b6f6991d03df0e2e04dafffcd6bc418aac66049e2cd74b80f14ac86db1e3f0da",
    "ver":1,
    "vin_sz":1,
    "vout_sz":2,
    "lock_time":"Unavailable",
    "size":258,
    "relayed_by":"64.179.201.80",
    "block_height, 12200,
    "tx_index":"12563028",
    "inputs":[


            {
                "prev_out":{
                    "hash":"a3e2bcc9a5f776112497a32b05f4b9e5b2405ed9",
                    "value":"100000000",
                    "tx_index":"12554260",
                    "n":"2"
                },
                "script":"76a914641ad5051edd97029a003fe9efb29359fcee409d88ac"
            }

        ],
    "out":[

                {
                    "value":"98000000",
                    "hash":"29d6a3540acfa0a950bef2bfdc75cd51c24390fd",
                    "script":"76a914641ad5051edd97029a003fe9efb29359fcee409d88ac"
                },

                {
                    "value":"2000000",
                    "hash":"17b5038a413f5c5ee288caa64cfab35a0c01914e",
                    "script":"76a914641ad5051edd97029a003fe9efb29359fcee409d88ac"
                }

        ]
}
```

`GET https://blockchain.info/rawtx/$tx_hash`

### Path Parameters

Parameter | Description
--------- | -----------
tx_hash | hash of the transaction

### Query Parameters

Parameter | Description
--------- | -----------
format | You can also request the transaction to return in binary form (Hex encoded) using ?format=hex

## Block Height

```shell
curl "https://blockchain.info/block-height/1?format=json"
```

> Response:

```json
{ "blocks" : [
    {
        "hash":"00000000839a8e6886ab5951d76f411475428afc90947ee320161bbf18eb6048",
        "ver":1,
        "prev_block":"000000000019d6689c085ae165831e934ff763ae46a2a6c172b3f1b60a8ce26f",
        "mrkl_root":"0e3e2357e806b6cdb1f70b54c3a3a17b6714ee1f0e68bebb44a74b1efd512098",
        "time":1231469665,
        "bits":486604799,
        "fee":0,
        "nonce":2573394689,
        "n_tx":1,
        "size":215,
        "block_index":14850,
        "main_chain":true,
        "height":1,
        "tx":[
            {
               "lock_time":0,
               "ver":1,
               "size":134,
               "inputs":[
                  {
                     "sequence":4294967295,
                     "witness":"",
                     "script":"04ffff001d0104"
                  }
               ],
               "weight":536,
               "time":1231469665,
               "tx_index":14854,
               "vin_sz":1,
               "hash":"0e3e2357e806b6cdb1f70b54c3a3a17b6714ee1f0e68bebb44a74b1efd512098",
               "vout_sz":1,
               "relayed_by":"0.0.0.0",
               "out":[
                  {
                     "spent":false,
                     "tx_index":14854,
                     "type":0,
                     "addr":"12c6DSiU4Rq3P4ZxziKxzrL5LmMBrzjrJX",
                     "value":5000000000,
                     "n":0,
                     "script":"410496b538e853519c726a2c91e61ec11600ae1390813a627c66fb8be7947be63c52da7589379515d4e0a604f8141781e62294721166bf621e73a82cbf2342c858eeac"
                  }
               ]
            }
        ]
     }
    ]
}
```

`GET https://blockchain.info/block-height/$block_height?format=json`

### Path Parameters

Parameter | Description
--------- | -----------
block_height | height of block of interest

### Query Parameters

Parameter | Description
--------- | -----------
format | Use `json` to get json, otherwise web page will be shown

## Single Address

```shell
curl "https://blockchain.info/rawaddr/660d4ef3a743e3e696ad990364e555c271ad504b"
```

> Response:

```json
{
    "hash160":"660d4ef3a743e3e696ad990364e555c271ad504b",
    "address":"1AJbsFZ64EpEfS5UAjAfcUG8pH8Jn3rn1F",
    "n_tx":17,
    "n_unredeemed":2,
    "total_received":1031350000,
    "total_sent":931250000,
    "final_balance":100100000,
    "txs": [
        --Array of Transactions--
    ]
}
```

`GET https://blockchain.info/rawaddr/$bitcoin_address`

### Path Parameters

Parameter | Description
--------- | -----------
bitcoin_address | Address can be base58 or hash160

### Query Parameters

Parameter | Description
--------- | -----------
limit | Optional limit parameter to show n transactions e.g. &limit=50 (Default: 50, Max: 50)
offset | Optional offset parameter to skip the first n transactions e.g. &offset=100 (Page 2 for limit 50)

## Multi Address

```shell
curl "https://blockchain.info/multiaddr?active=1A8JiWcwvpY7tAopUkSnGuEYHmzGYfZPiq|1MDUoxL1bGvMxhuoDYx6i11ePytECAk9QK"
```

> Response:

```json
{
  "recommend_include_fee": true,
  "info": {
    "nconnected": 0,
    "conversion": 100000000,
    "symbol_local": {
      "code": "USD",
      "symbol": "$",
      "name": "U.S. dollar",
      "conversion": 13481.77668246,
      "symbolAppearsAfter": false,
      "local": true
    },
    "symbol_btc": {
      "code": "BTC",
      "symbol": "BTC",
      "name": "Bitcoin",
      "conversion": 100000000,
      "symbolAppearsAfter": true,
      "local": false
    },
    "latest_block": {
      "block_index": 1704542,
      "hash": "0000000000000000003e78dd3f30e9842cca9cc0b502c0f5451d68a3f167a15a",
      "height": 525463,
      "time": 1527866553
    }
  },
  "wallet": {
    "n_tx": 7878,
    "n_tx_filtered": 7878,
    "total_received": 276123491267,
    "total_sent": 276122668426,
    "final_balance": 822841
  },
  "addresses": [
    {
      "address": "1MDUoxL1bGvMxhuoDYx6i11ePytECAk9QK",
      "n_tx": 0,
      "total_received": 0,
      "total_sent": 0,
      "final_balance": 0,
      "change_index": 0,
      "account_index": 0
    },
    {
      "address": "1A8JiWcwvpY7tAopUkSnGuEYHmzGYfZPiq",
      "n_tx": 7878,
      "total_received": 276123491267,
      "total_sent": 276122668426,
      "final_balance": 822841,
      "change_index": 0,
      "account_index": 0
    }
  ],
  "txs": [
    --Array of Transactions--
  ]
}
```

`GET https://blockchain.info/multiaddr`


### Query Parameters

Parameter | Description
--------- | -----------
active | Multiple addresses separated by `|`, Address can be base58 or xpub
limit | Optional limit parameter to show n transactions e.g. &n=50 (Default: 50, Max: 100)
offset | Optional offset parameter to skip the first n transactions e.g. &offset=100 (Page 2 for limit 50)

## Unspent outputs

```shell
curl "https://blockchain.info/unspent?active=12se8UhjbNZHRjF8RLtFHPs2grFRudoKBk"
```

> Response:

```json
{
    "unspent_outputs":[
        {
            "tx_age":"1322659106",
            "tx_hash":"e6452a2cb71aa864aaa959e647e7a4726a22e640560f199f79b56b5502114c37",
            "tx_index":"12790219",
            "tx_output_n":"0",
            "script":"76a914641ad5051edd97029a003fe9efb29359fcee409d88ac", (Hex encoded)
            "value":"5000661330"
        }
    ]
}
```

`GET https://blockchain.info/unspent`

The tx hash is in reverse byte order. What this means is that in order to get the html transaction hash from the JSON tx hash for the following transaction, you need to decode the hex (using [this site](https://paulschou.com/tools/xlate/) for example). This will produce a binary output, which you need to reverse (the last 8bits/1byte move to the front, second to last 8bits/1byte needs to be moved to second, etc.). Then once the reversed bytes are decoded, you will get the html transaction hash.

### Query Parameters

Parameter | Description
--------- | -----------
active | Multiple addresses separated by `|`, Address can be base58 or xpub
limit | Optional limit parameter to show n transactions e.g. &n=50 (Default: 50, Max: 100)
offset | Optional offset parameter to skip the first n transactions e.g. &offset=100 (Page 2 for limit 50)

## Balance

```shell
curl "https://blockchain.info/balance?active=1MDUoxL1bGvMxhuoDYx6i11ePytECAk9QK|15EW3AMRm2yP6LEF5YKKLYwvphy3DmMqN6"
```

> Response:

```json
{
    "1MDUoxL1bGvMxhuoDYx6i11ePytECAk9QK": {
        "final_balance": 0,
        "n_tx": 0,
        "total_received": 0
    },
    "15EW3AMRm2yP6LEF5YKKLYwvphy3DmMqN6": {
        "final_balance": 0,
        "n_tx": 2,
        "total_received": 310630609
    }
}
```

`GET https://blockchain.info/balance`

List the balance summary of each address listed.

### Query Parameters

Parameter | Description
--------- | -----------
active | Multiple addresses separated by `|`, Address can be base58 or xpub

## Latest Block

```shell
curl "https://blockchain.info/latestblock"
```

> Response:

```json
{
    "hash":"0000000000000000002828b3919c6e31fcd2934f0b6cd2713bd8885d5de016f7",
    "time":1527867163,
    "block_index":1703884,
    "height":525464,
    "txIndexes":[
        --Array of tx indexes--
    ]
}
```

`GET https://blockchain.info/latestblock`

## Unconfirmed Transactions

```shell
curl "https://blockchain.info/unconfirmed-transactions?format=json"
```

> Response:

```json
{
    "txs": [
        --Array of Transactions--
    ]
}
```

`GET https://blockchain.info/unconfirmed-transactions?format=json`

## Blocks

```shell
curl "https://blockchaininfo/blocks/1498830483000?format=json
```

> Response:

```json
{
    "blocks" : [
    {
        "height" : 473439,
        "hash" : "000000000000000000f6093b56455dd357c88533a69620096687bb242d362448",
        "time" : 1498781246,
        "main_chain" : true
    },

    {
        "height" : 473440,
        "hash" : "0000000000000000000d4878d826e7c4557809a5f8ecf0d0f6e58944a5ccca1d",
        "time" : 1498781513,
        "main_chain" : true
    },
    ... more blocks ...
    ]
}
```

`GET https://blockchain.info/blocks/$block`

### Path Parameters

Parameter | Description
--------- | -----------
block | time in milliseconds will return blocks for one day
block | pool name will return blocks for a specific pool

### Query Parameters

Paramater | Description
--------- | -----------
format | Use `json` to get json, otherwise web page will be shown

