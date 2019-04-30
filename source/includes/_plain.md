# Simple plaintext query

Simple plain text API for querying blockchain data.

Please limit your queries to a maximum of 1 every 10 seconds. All bitcoin values are in Satoshi i.e. divide by 100000000 to get the amount in BTC

<aside class="notice">
Some API calls are available with <a href="https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS">CORS headers</a> if you add a <code>&cors=true</code> parameter to the GET request
</aside>

## Real-Time

Endpoint | Description
-------- | -----------
[getdifficulty](https://blockchain.info/q/getdifficulty) | Current difficulty target as a decimal number
[getblockcount](https://blockchain.info/q/getblockcount) | Current block height in the longest chain
[latesthash](https://blockchain.info/q/latesthash) | Hash of the latest block
[bcperblock](https://blockchain.info/q/bcperblock) | Current block reward in BTC
[totalbc](https://blockchain.info/q/totalbc) | Total Bitcoins in circulation (delayed by up to 1 hour])
[probability](https://blockchain.info/q/probability) | Probability of finding a valid block each hash attempt
[hashestowin](https://blockchain.info/q/hashestowin) | Average number of hash attempts needed to solve a block
[nextretarget](https://blockchain.info/q/nextretarget) | Block height of the next difficulty retarget
[avgtxsize](https://blockchain.info/q/avgtxsize) | Average transaction size for the past 1000 blocks. Change the number of blocks by passing an integer as the second argument e.g. avgtxsize/2000
[avgtxvalue](https://blockchain.info/q/avgtxvalue) | Average transaction value (1000 Default)
[interval](https://blockchain.info/q/interval) | Average time between blocks in seconds
[eta](https://blockchain.info/q/eta) | Estimated time until the next block (in seconds)
[avgtxnumber](https://blockchain.info/q/avgtxnumber) | Average number of transactions per block (100 Default)

## Address Lookups

To filter by x number of confirmations include the confirmations parameter e.g. `/q/addressbalance/1EzwoHtiXB4iFwedPr49iywjZn2nnekhoj?confirmations=6` to only include transactions with 6 or more confirmations.

<aside class="warning">
This is extremely important if you are processing valuable transactions.
</aside>

Endpoint | Path Parameter | Description
-------- | -------------- | -----------
[getreceivedbyaddress](https://blockchain.info/q/getreceivedbyaddress) | /address | Get the total number of bitcoins received by an address (in satoshi). Multiple addresses separated by &#124;
[getsentbyaddress](https://blockchain.info/q/getsentbyaddress) | /address | Get the total number of bitcoins send by an address (in satoshi). Multiple addresses separated by &#124;
[addressbalance](https://blockchain.info/q/addressbalance) | /address | Get the balance of an address (in satoshi). Multiple addresses separated by &#124;
[addressfirstseen](https://blockchain.info/q/addressfirstseen) | /address | Timestamp of the block an address was first confirmed in.

## Transaction Lookups

Endpoint | Path Parameter | Description
-------- | -------------- | -----------
[txtotalbtcoutput](https://blockchain.info/q/txtotalbtcoutput) | /TxHash | Get total output value of a transaction (in satoshi)
[txtotalbtcinput](https://blockchain.info/q/txtotalbtcinput) | /TxHash | Get total input value of a transaction (in satoshi)
[txfee](https://blockchain.info/q/txfee) | /TxHash | Get fee included in a transaction (in satoshi)
[txresult](https://blockchain.info/q/txresult) | /TxHash/Address | Calculate the result of a transaction sent or received to Address. Multiple addresses separated by &#124;

## Tools

Endpoint | Path Parameter | Description
-------- | -------------- | -----------
[addresstohash](https://blockchain.info/q/addresstohash) | Address | Converts a bitcoin address to a hash 160
[hashtoaddress](https://blockchain.info/q/hashtoaddress) | Hash | Converts a hash 160 to a bitcoin address
[hashpubkey](https://blockchain.info/q/hashpubkey) | Pubkey | Converts a public key to a hash 160
[addrpubkey](https://blockchain.info/q/addrpubkey) | Pubkey | Converts a public key to an Address
[pubkeyaddr](https://blockchain.info/q/pubkeyaddr) | Address | Converts an address to public key (if available)

## Misc

Endpoint | Description
-------- | -----------
[unconfirmedcount](https://blockchain.info/q/unconfirmedcount) | Number of pending unconfirmed transactions
[24hrprice](https://blockchain.info/q/24hrprice) | 24 hour weighted price from the largest exchanges
[marketcap](https://blockchain.info/q/marketcap) | USD market cap (based on 24 hour weighted price)
[24hrtransactioncount](https://blockchain.info/q/24hrtransactioncount) | Number of transactions in the past 24 hours
[24hrbtcsent](https://blockchain.info/q/24hrbtcsent) | Number of btc sent in the last 24 hours (in satoshi)
[hashrate](https://blockchain.info/q/hashrate) | Estimated network hash rate in gigahash
[rejected](https://blockchain.info/q/rejected) | Lookup the reason why the provided tx or block hash was rejected (if any)


