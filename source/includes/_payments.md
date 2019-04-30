# Payment Processing

## Introduction

The Blockchain Receive Payments API V2 is the quickest and easiest way to begin accepting automated bitcoin payments. Consisting of just a simple HTTP GET request, you can be up and running in minutes.

One of the difficulties involved with receiving bitcoin payments is the need to generate a unique address for each new user or invoice. These addresses need to monitored and stored securely. The blockchain receive payments API takes care of the generation and monitoring of addresses. We will notify your server using a simple callback whenever a payment is received.

## Requesting an API key

In order to use the Receive Payments API V2, please apply for an API key [here](https://api.blockchain.info/v2/apikey/request/). This API key is only for our Receive Payments API. You cannot use the standard blockchain wallet API key for Receive Payments V2, and vice versa.

## Obtaining an Extended Public Key (xPub)

This API requires you to have a BIP 32 account xPub in order to receive payments. The easiest way to start receiving payments is to open a [Blockchain Wallet](https://blockchain.info/wallet/#/signup). You should create a new account inside your wallet exclusively for transactions facilitated by this API. When making API calls, use the xPub for this account (located in **Settings -> Addresses -> Manage -> More Options -> Show xPub**).

## Generating a Receiving Address

This method creates a unique address which should be presented to the customer. For any payments sent to this address, you will be sent an HTTP notification. Please note that every call to the server will increment the `index` parameter. This is done so you do not show the same address to two different customers. However, all funds will still show within the same account.

> Derive an unused address using your xPub:

```shell
curl "https://api.blockchain.info/v2/receive?xpub=xpub6CWiJoiwxPQni3DFbrQNHWq8kwrL2J1HuBN7zm4xKPCZRmEshc7Dojz4zMah7E4o2GEEbD6HgfG7sQid186Fw9x9akMNKw2mu1PjqacTJB2&callback=https%3A%2F%2Fmystore.com%3Finvoice_id%3D058921123&key=[yourkeyhere]"
```

> Have your customer send bitcoin to the address contained in the response:

```json
{
    "address" : "19jJyiC6DnKyKvPg38eBE8R6yCSXLLEjqw",
    "index" : 23,
    "callback" : "https://mystore.com?invoice_id=058921123"
}
```

> PHP Example

```php
<?
$secret = 'ZzsMLGKe162CfA5EcG6j';

$my_xpub = '{YOUR XPUB ADDRESS}';
$my_api_key = '{YOUR API KEY}';

$my_callback_url = 'https://mystore.com?invoice_id=058921123&secret='.$secret;

$root_url = 'https://api.blockchain.info/v2/receive';

$parameters = 'xpub=' .$my_xpub. '&callback=' .urlencode($my_callback_url). '&key=' .$my_api_key;

$response = file_get_contents($root_url . '?' . $parameters);

$object = json_decode($response);

echo 'Send Payment To : ' . $object->address;
```

`GET https://api.blockchain.info/v2/receive?xpub=$xpub&callback=$callback_url&key=$key`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
xpub | required | Your xPub (where you would like the payment to be sent)
callback_url | required | The callback URL to be notified when a payment is received. Remember to URL Encode the callback url when calling the create method.
key | required | Your blockchain.info receive payments v2 api key. [Request an API key](https://api.blockchain.info/v2/apikey/request/).
gap_limit | optional | How many unused addresses are allowed before erroring out.


As defined in BIP 44, wallet software will not scan past 20 unused addresses. Given enough requests from this API that don't have a matching payment, you could generate addresses past this horizon, which would make spending funds paid to those addresses quite difficult. For this reason, this API will return an error and refuse to generate new addresses if it detects it would create a gap of over 20 unused addresses. If you encounter this error, you will either need to switch to a new xPub (within the same wallet is fine), or receive a payment to one of the previous 20 created addresses

You can control this behavior by optionally passing `gap_limit` as an extra URL parameter. Please note, this will not increase the number of addresses that will be monitored by our servers. Passing the `gap_limit` parameter changes the maximum allowed gap before the API will stop generating new addresses. Using this feature will require you understand the gap limitation and how to handle it (for advanced users only):

## Balance Updates

> Monitor an address for every received payment with 5 confirmations:

```shell
curl -H "Content-Type: text/plain" --data '{"key":"[your-key-here]","addr":"183qrMGHzMstARRh2rVoRepAd919sGgMHb","callback":"https://mystore.com?invoice_id=123","onNotification":"KEEP", "op":"RECEIVE", "confs": 5}' https://api.blockchain.info/v2/receive/balance_update
```

> Response:

```json
{
  "id" : 70,
  "addr" : "183qrMGHzMstARRh2rVoRepAd919sGgMHb",
  "op" : "RECEIVE",
  "confs" : 5,
  "callback" : "https://mystore.com?invoice_id=123",
  "onNotification" : "KEEP"
}
```

> The id in the response can be used to delete the request:

```shell
curl -X DELETE "https://api.blockchain.info/v2/receive/balance_update/70?key=[your-key-here]"
```

> Response:
```json
{ "deleted": true }
```

This method monitors an address of your choice for received and / or spent payments. You will be sent an HTTP notification immediately when a transaction is made, and subsequently when it reaches the number of confirmations specified in the request.

You are required to specify the request's notification behaviour. Setting the behaviour to `DELETE` will delete the request after the first relelvant notification is sent to your callback address. Setting the behaviour to `KEEP` will send additional notifications every time a transaction with the specified confirmations and operation type is sent to or from the address in the request.

Operation type is an optional parameter indicating whether the address will be monitored for received or spent transactions, or both. By default both operation types are monitored.

You may also optionally specify the number of confirmations a transaction reaches before being sent a notification. Note that you will receive a notification at 0 confirmations (i.e. immediately when the transaction is made), and again when it reaches the number of confirmations specified in the request (3 confirmations by default).

`POST https://api.blockchain.info/v2/receive/balance_update`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
address | required | The address you would like to monitor
callback | required | The callback URL to be notified when a payment is received.
key | required | Your blockchain.info receive payments v2 api key. [Request an API key](https://api.blockchain.info/v2/apikey/request/).
onNotification | required | The request notification behaviour ('KEEP' &#124; 'DELETE')
confs | 3 | The number of confirmations the transaction needs to have before a notification is sent.
op | ALL | The operation type you would like to receive notifications for ('SPEND' &#124; 'RECEIVE' &#124; 'ALL').


## Block Notification

> Request a single notification when the Bitcoin Blockchain reaches 500,000 blocks:

```shell
curl -H "Content-Type: text/plain" --data '{"key":"[your-key-here]","height":500000,"callback":"https://mysite.com/block?request_id=1234","onNotification":"DELETE"}' https://api.blockchain.info/v2/receive/block_notification
```

> Response:

```json
{
  "id" : 64,
  "height" : 500000,
  "callback" : "https://mysite.com/block?request_id=1234",
  "confs" : 1,
  "onNotification" : "DELETE"
}
```

> The id in the response can be used to delete the request:

```shell
curl -X DELETE "https://api.blockchain.info/v2/receive/block_notifcation/64?key=[your-key-here]"
```

> Response:

```json
{ "deleted": true }
```

This method allows you to request callbacks when a new block of a specified height and confirmation number is added to the blockchain.

As with balance update requests, you are required to specify the request's notification behaviour to either 'KEEP' or 'DELETE'.

Height is an optional parameter indicating at which height you would like to receive a block notification - if unspecified, this will be the height of the next block to arrive.

Confs is another optional parameter indicating how many confirmations a block should have when a notification is sent.

`POST https://api.blockchain.info/v2/receive/block_notification`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
callback | required | The callback URL to be notified when a block that matches your query is added.
key | required | Your blockchain.info receive payments v2 api key. [Request an API key](https://api.blockchain.info/v2/apikey/request/).
onNotification | required | The request notification behaviour ('KEEP' &#124; 'DELETE')
confs | 1 | The number of confirmations the block should have before a notification is sent.
height | current height + 1 | The height at which a notification should be sent.


## Implementing the Callback

> An example callback as a result of the PHP Example [Full source code (PHP, Python, Ruby)](https://github.com/blockchain/receive-payments-demos).

```php
<?
$real_secret = 'ZzsMLGKe162CfA5EcG6j';
$invoice_id = $_GET['invoice_id']; //invoice_id is passed back to the callback URL
$transaction_hash = $_GET['transaction_hash'];
$value_in_satoshi = $_GET['value'];
$value_in_btc = $value_in_satoshi / 100000000;

//Commented out to test, uncomment when live
if ($_GET['test'] == true) {
    return;
}

try {
  //create or open the database
  $database = new SQLiteDatabase('db.sqlite', 0666, $error);
} catch(Exception $e) {
  die($error);
}

//Add the invoice to the database
$stmt = $db->prepare("replace INTO invoice_payments (invoice_id, transaction_hash, value) values(?, ?, ?)");
$stmt->bind_param("isd", $invoice_id, $transaction_hash, $value_in_btc);

if($stmt->execute()) {
   echo "*ok*";
}
```

### Receive and Balance Update callbacks

Please note, the callback url is limited to 255 characters in length.

When a payment is received by a generated address, or by an address monitored by a balance update request, blockchain.info will notify the callback URL you specify. For balance update callbacks and additional notification will be sent once the transaction reaches the specified number of confirmations.

Parameter | Description
--------- | -----------
transaction_hash | The payment transaction hash.
address | The destination bitcoin address (part of your xPub account).
confirmations | The number of confirmations of this transaction.
value | The value of the payment received (in satoshi, so divide by 100,000,000 to get the value in BTC).
{custom} | Any parameters included in the callback URL will be passed back to the callback URL in the notification. You can use this functionality to include parameters in your callback URL like invoice_id or customer_id to track which payments are associated with which of your customers.

### Block Notification callbacks

A block notification is sent every time a new block is added to the blockchain, and matches the height and number of confirmations set in the notification request.

Parameter | Description
--------- | -----------
hash | The block hash.
confirmations | The number of confirmations of this block.
height | The block height.
timestamp | The unix timestamp indicating when the block was added.
size | The block size in bytes.
{custom} | Any parameters included in the callback URL will be passed back to the callback URL in the notification.

### Expected Callback Response

In order to acknowledge successful processing of the callback, your server should respond with the text "*ok*" (no quotes), in plain-text, no HTML. If the server responds with anything else, or nothing, the callback will be resent again for every new block (approximately every 10 minutes) up to 1000 times (1 week). Callback domains which appear dead or never return the "*ok*" response may be blocked from the service.


## Check xPub address gap

> Use the xPub you want to check and your API Key like so:

```shell
curl "https://api.blockchain.info/v2/receive/checkgap?xpub=[yourxpubhere]]&key=[yourkeyhere]"
```

> Response:

```json
{"gap":2}
```

Check the index gap between last address paid to and the last address generated using the using the checkgap endpoint.


## Callback Logs

> Use the exact callback in question and your API key like so:

```shell
curl "https://api.blockchain.info/v2/receive/callback_log?callback=https%3A%2F%2Fmystore.com%3Finvoice_id%3D05892112%26secret%3DZzsMLGKe162CfA5EcG6j&key=[yourkeyhere]"
```

> Response:

```json
 [
   {
       "callback": "https://mystore.com?invoice_id=058921123&secret=ZzsMLGKe162CfA5EcG6j&key=[yourkeyhere]",
       "called_at": "2015-10-21T22:43:47Z",
       "raw_response": "*bad*",
       "response_code": 200
   },
   {
       "callback": "http://mystore.com?invoice_id=058921123&secret=ZzsMLGKe162CfA5EcG6j&key=[yourkeyhere]",
       "called_at": "2015-10-21T22:43:55Z",
       "raw_response": "*bad*",
       "response_code": 200
   }
  ]
```

See logs related to callback attempts using the callback_logs endpoints.

## Security

A custom secret parameter should be included in the callback URL. The secret will be passed back to the callback script when the callback is fired, and should be checked by your code for validity. This prevents someone from trying to call your servers and falsely mark an invoice as 'paid'.


## Currency Conversion

Use the [Exchange Rates API](https://blockchain.info/api/exchange_rates_api) to convert values in local currencies to BTC.
If you would like convert payments received in Bitcoin to fiat currency quickly use a bitcoin address from an exchange wallet.

## Double Spends & Chargebacks

```php
<?
if ($_GET['confirmations'] >= 6) {
    //Insert into confirmed payments
    echo '*ok*';
} else {
    //Insert into pending payments
    //Don't print *ok* so the notification resent again on next confirmation
}
```

A double spend occurs when a malicious user spends the same BTC twice. A payment that initial appears successful could be reversed at a later date. This is counteracted by waiting for the transaction to be included in the blockchain and reaching a number of confirmations. 6 confirmations is generally considered safe for high value transactions.

Validate the transaction confirmations in the callback script by checking `confirmations` parameter. It is recommended you acknowledge the transaction at zero confirmations but only trust the transaction after one confirmation. For example, if purchasing a product, we would show the order as successful at zero confirmations (the first callback, but do not echo "*ok*" yet), but only ship the product when 4 or more confirmations are reached. See the PHP demo for an example.


## Address Expiration

Receive addresses never expire and will continue to be monitored until an "*ok*" is received in the callback response or blockchain.info has notified the callback 1000 times.


## Fair Usage

There is no limit to the number of receiving address which can be generated (as long as the 20 address gap limitation is met), the service is designed to monitor millions of addresses.

Callback domains which appear dead or never return the "*ok*" response may be blocked from the service.

