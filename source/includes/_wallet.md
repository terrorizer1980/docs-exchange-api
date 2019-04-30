# Wallet API

Simple API for Blockchain Wallet users to send and receive bitcoin payments.

The Blockchain Wallet API provides a simple interface Merchants can use to programmatically interact with their wallet.

## Installation

> Python - Install via pip: [Github](https://github.com/blockchain/api-v1-client-python)

```shell
$ pip install blockchain
```

> Java - Add the following repository to your pom.xml: [Github](https://github.com/blockchain/api-v1-client-java)

```xml
    <repository>
        <id>api-v1-client-java-mvn-repo</id>
        <url>https://raw.githubusercontent.com/blockchain/api-v1-client-java/mvn-repo/</url>
        <snapshots>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
        </snapshots>
    </repository>
```

> Add the following dependency to your pom.xml:

```xml
  <dependency>
    <groupId>info.blockchain</groupId>
    <artifactId>api</artifactId>
    <version>LATEST</version>
  </dependency>
```

> .NET (C#) - Installation via NuGet: [Github](https://github.com/blockchain/api-v1-client-csharp)

```shell
PM> Install-Package BlockchainAPI
```

> Ruby - Installation via RubyGems: [Github](https://github.com/blockchain/api-v1-client-ruby)

```shell
$ gem install blockchain
```

> PHP - Composer install support is available. Or, download/clone the repository. Copy the src/ folder into your project and add: [Github](https://github.com/blockchain/api-v1-client-php)

```text
require_once('src/Blockchain.php');
```

> Node - Installation via `npm`: [Github](https://github.com/blockchain/api-v1-client-node)

```shell
$ npm install blockchain.info
```

To use this API, you will need to run a small local service which will be responsible for managing your Blockchain Wallet. Your application interacts with this service locally via HTTP API calls. [Click here for complete setup instructions on GitHub](https://github.com/blockchain/service-my-wallet-v3).

## Create Wallet API

```shell
curl "http://localhost:3000/api/v2/create?password=xxx&api_code=[api
key]&email=user@email.com"

```

> Response:

```json
{
    "guid": "4b8cd8e9-9480-44cc-b7f2-527e98ee3287",
    "address": "12AaMuRnzw6vW6s2KPRAGeX53meTf8JbZS",
    "label": "Main address"
}
```

`POST or GET http://localhost:3000/api/v2/create`

The create_wallet method can be used to create a new blockchain.info bitcoin wallet.

### Query Parameters

Parameter | Description
--------- | -----------
password | The password for the new wallet. Must be at least 10 characters in length.
api_code | An API code with create wallets permission.
priv | A private key to add to the wallet (Wallet import format preferred). (Optional)
label | A label to set for the first address in the wallet. Alphanumeric only. (Optional)
email | An email to associate with the new wallet i.e. the email address of the user you are creating this wallet on behalf of. (Optional)

## Making Outgoing Payments

```shell
curl "http://localhost:3000/merchant/$guid/payment?password=$main_password&second_password=$second_password&to=$address&amount=$amount&from=$from&fee=$fee"
```

> Response:

```json
{
    "message" : "Sent 0.1 BTC to 1A8JiWcwvpY7tAopUkSnGuEYHmzGYfZPiq",
    "tx_hash" : "f322d01ad784e5deeb25464a5781c3b20971c1863679ca506e702e3e33c18e9c",
    "notice" : "Some funds are pending confirmation and cannot be spent yet (Value 0.001 BTC)"
}
```

Send bitcoin from your wallet to another bitcoin address. All transactions include a 0.0001 BTC miners fee.

All bitcoin values are in Satoshi i.e. divide by 100000000 to get the amount in BTC. $guid should be replaced with your Blockchain Wallet identifier (found on the login page).

`GET http://localhost:3000/merchant/$guid/payment`

### Query Parameters

Parameter | Description
--------- | -----------
main_password | Your Main Blockchain Wallet password
second_password | Your second Blockchain Wallet password if double encryption is enabled.
to | Recipient Bitcoin Address.
amount | Amount to send in satoshi.
from | Send from a specific Bitcoin Address (Optional)
fee | Transaction fee value in satoshi (Must be greater than default fee) (Optional)

### Response

Parameter | Description
--------- | -----------
message | Response message
tx_hash | Transaction Hash
notice | Additional message

## Send Many Transactions

> Sample `recipients` object:

```json
{
    "1JzSZFs2DQke2B3S4pBxaNaMzzVZaG4Cqh": 100000000,
    "12Cf6nCcRtKERh9cQm3Z29c9MWvQuFSxvT": 1500000000,
    "1dice6YgEVBf88erBFra9BHf6ZMoyvG88": 200000000
}
```

```shell
curl "http://localhost:3000/merchant/$guid/sendmany?password=$main_password&second_password=$second_password&recipients=$recipients&fee=$fee"
```
> Response:

```json
{
    "message" : "Sent To Multiple Recipients",
    "tx_hash" : "f322d01ad784e5deeb25464a5781c3b20971c1863679ca506e702e3e33c18e9c"
}
```

> PHP example:

```php
<?

$guid="GUID_HERE";
$firstpassword="PASSWORD_HERE";
$secondpassword="PASSWORD_HERE";
$amounta = "10000000";
$amountb = "400000";
$addressa = "1A8JiWcwvpY7tAopUkSnGuEYHmzGYfZPiq";
$addressb = "1ExD2je6UNxL5oSu6iPUhn9Ta7UrN8bjBy";
$recipients = urlencode('{
                  "'.$addressa.'": '.$amounta.',
                  "'.$addressb.'": '.$amountb.'
               }');

$json_url = "http://localhost:3000/merchant/$guid/sendmany?password=$firstpassword&second_password=$secondpassword&recipients=$recipients";

$json_data = file_get_contents($json_url);

$json_feed = json_decode($json_data);

$message = $json_feed->message;
$txid = $json_feed->tx_hash;

?>
```

Send a transaction to multiple recipients in the same transaction.

`GET http://localhost:3000/merchant/$guid/sendmany`

### Query Parameters

Parameter | Description
--------- | -----------
password | Your Main Blockchain wallet password
second_password | Your second Blockchain Wallet password if double encryption is enabled.
recipients | Is a JSON Object using Bitcoin Addresses as keys and the amounts to send as values (See below).
from | Send from a specific Bitcoin Address (Optional)
fee | Transaction fee value in satoshi (Must be greater than default fee) (Optional)

### Response

Parameter | Description
--------- | -----------
message | Response message
tx_hash | Transaction Hash

## Fetching the wallet balance

```shell
curl "http://localhost:3000/merchant/$guid/balance?password=$main_password"
```

> Response:

```json
{ "balance": 1000 }
```

Fetch the balance of a wallet. This should be used as an estimate only and will include unconfirmed transactions and possibly double spends.

`GET http://localhost:3000/merchant/$guid/balance`

### Query Parameters

Parameter | Description
--------- | -----------
password | Your Main Blockchain wallet password

## Listing Addresses

```shell
curl "http://localhost:3000/merchant/$guid/list?password=$main_password"
```

> Response:

```json
{
    "addresses": [
        {
            "balance": 1400938800,
            "address": "1Q1AtvCyKhtveGm3187mgNRh5YcukUWjQC",
            "label": "SMS Deposits",
            "total_received": 5954572400
        },
        {
            "balance": 79434360,
            "address": "1A8JiWcwvpY7tAopUkSnGuEYHmzGYfZPiq",
            "label": "My Wallet",
            "total_received": 453300048335
        },
        {
            "balance": 0,
            "address": "17p49XUC2fw4Fn53WjZqYAm4APKqhNPEkY",
            "total_received": 0
        }
    ]
}
```

List all active addresses in a wallet. Also includes a 0 confirmation balance which should be used as an estimate only and will include unconfirmed transactions and possibly double spends.

`GET http://localhost:3000/merchant/$guid/list`

### Query Parameters

Parameter | Description
--------- | -----------
password | Your Main Blockchain wallet password

## Getting the balance of an address

```shell
curl "http://localhost:3000/merchant/$guid/address_balance?password=$main_password&address=$address"
```

> Response:

```json
{
    "balance" : 50000000,
    "address" : "19r7jAbPDtfTKQ9VJpvDzFFxCjUJFKesVZ",
    "total_received" : 100000000
}
```

Retrieve the balance of a bitcoin address. Querying the balance of an address by label is depreciated.

`GET http://localhost:3000/merchant/$guid/address_balance`

### Query Parameters

Parameter | Description
--------- | -----------
password | Your Main Blockchain wallet password
address | The bitcoin address to lookup

### Response

Parameter | Description
--------- | -----------
balance | Balance in Satoshi
address | Bitcoin Address
total_received | Total Satoshi Received

## Generating a new address

```shell
curl "http://localhost:3000/merchant/$guid/new_address?password=$main_password&second_password=$second_password&label=$label"
```

> Response:

```json
{
    "address" : "18fyqiZzndTxdVo7g9ouRogB4uFj86JJiy",
    "label" : "Order No : 1234"
}
```

Generate a new address

`GET http://localhost:3000/merchant/$guid/new_address`

### Query Parameters

Parameter | Description
--------- | -----------
password | Your Main Blockchain wallet password
second_password | Your second Blockchain Wallet password if double encryption is enabled.
label | An optional label to attach to this address. It is recommended this is a human readable string e.g. "Order No : 1234".

### Response

Parameter | Description
--------- | -----------
address | The Bitcoin Address Generated
label | The Address Label

## Address Management

## Archiving an address

```shell
curl "http://localhost:3000/merchant/$guid/archive_address?password=$main_password&second_password=$second_password&address=$address"
```

> Response:

```json
{
    "archived" : "18fyqiZzndTxdVo7g9ouRogB4uFj86JJiy"
}
```

To improve wallet performance addresses which have not been used recently should be moved to an archived state. They will still be held in the wallet but will no longer be included in the "list" or "list-transactions" calls.

For example if an invoice is generated for a user once that invoice is paid the address should be archived.

Or if a unique bitcoin address is generated for each user, users who have not logged in recently (~30 days) their addresses should be archived.

`GET http://localhost:3000/merchant/$guid/archive_address`

### Query Parameters

Parameter | Description
--------- | -----------
password | Your Main Blockchain wallet password
second_password | Your second Blockchain Wallet password if double encryption is enabled.
address | The bitcoin address to archive

### Response

Parameter | Description
--------- | -----------
archived | The bitcoin address archived

## Unarchiving an address

```shell
curl "http://localhost:3000/merchant/$guid/unarchive_address?password=$main_password&second_password=$second_password&address=$address"
```

> Response:

```json
{
    "active" : "18fyqiZzndTxdVo7g9ouRogB4uFj86JJiy"
}
```

Unarchive an address. Will also restore consolidated addresses.

`GET http://localhost:3000/merchant/$guid/unarchive_address`

### Query Parameters

Parameter | Description
--------- | -----------
password | Your Main Blockchain wallet password
second_password | Your second Blockchain Wallet password if double encryption is enabled.
address | The bitcoin address to unarchive

### Response

Parameter | Description
--------- | -----------
active | The bitcoin address unarchived

