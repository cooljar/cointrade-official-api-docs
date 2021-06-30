**Table of Contents**
- [General API Information](#general-api-information)
- [Error Codes](#error-codes)
- [General Information on Endpoints](#general-information-on-endpoints)
- [Endpoint security type](#endpoint-security-type)
- [SIGNED (TRADE and USER_DATA) Endpoint security](#signed-trade-and-user_data-endpoint-security)
    - [Timing Security](#timing-security)
    - [SIGNED Endpoint](#signed-endpoint-examples-for-post-getinfo)
- [Private API Endpoint](#private-api-endpoint)
    - [ENUM definitions](#enum-definitions)
    - [Private API endpoints](#private-api-endpoints)

## General API Information
* The base endpoint is: **https://papi.cointrade.id/main**
* All endpoints return either a JSON object or array.
* Data is returned in **descending** order. newest first, oldest last.
* All time and timestamp related fields are in **milliseconds**.

## Error Codes
* Any endpoint can return an ERROR

Sample Payload below:
```
{
    "success": 0,
    "error": "Invalid credentials. API not found or session has expired.",
    "error_code": "invalid_credentials"
}
```

## General Information on Endpoints
* Parameters may be sent in any order.
* All requests must be sent with POST.
* For each request you need to include these variable to make the call valid: method and nonce or timestamp.

  | Parameter | Description | Optional | Example |
  |-|-|-| - |
  |`method`| Specify the method you want to call | no | getInfo
  |`timestamp`| This parameter should be the millisecond timestamp of when the request was created and sent | no | 1578303960000
  |`recv_window`| The value should specify the number of millisecond after timestamp where your request is valid. That mean your request still valid if it sent and processed within timestamp and timestamp + recv_window. Default value is 5000 (milliseconds) | no | 1578303937000

## Endpoint Security Type
* API-keys are passed into the Rest API using Bearer authentication.
* API-keys and secret-keys **are case sensitive**.
* There are 3 different permissions that can be applied to API Key: view, trade and withdraw

  |Permission | Allowed Methods|
  |-|-|
  |view | getInfo, transHistory, tradeHistory, openOrders, orderHistory, getOrder |
  |trade | trade, cancelOrder |
  |withdraw | withdrawFeee, withdrawCoin |

## Signed (TRADE and USER_DATA) Endpoint Security
* `SIGNED` endpoints require an additional parameter, `Signature`, to be sent in the  `header`.
* Endpoints use `HMAC SHA512` signatures. Use your `secretKey` as the key and `totalParams` as the value for the HMAC operation.
* The `signature` is **not case sensitive**.
* `totalParams` is defined as the `query string` concatenated with the `request body`. example `*(?param=val&param1=val1) encrypted with method HMAC-SHA512 using secret key*`

### Timing Security
* A `SIGNED` endpoint also requires a parameter, `timestamp`, to be sent which should be the millisecond timestamp of when the request was created and sent.
* An additional parameter, `recv_window`, may be sent to specify the number of milliseconds after `timestamp` the request is valid for. If `recv_window` is not sent, **it defaults to 5000**.
* The logic is as follows:
  ```php
  if ($timestamp >= ($serverTime + 1000) || ($serverTime - $timestamp) > $recv_window) {
      // reject request
  } else {
      // process request
  }
  ```
**Serious trading is about timing.** Networks can be unstable and unreliable, which can lead to requests taking varying amounts of time to reach the servers. With `recv_window`, you can specify that the request must be processed within a certain number of milliseconds or be rejected by the server.

### SIGNED Endpoint Examples for POST getInfo
Here is a step-by-step example of how to send a vaild signed payload from the Linux command line using. `curl`

| Key | Value
|-|-
| `apiKey` | TZDJPNGD-VMSGVS7Q-5QAMYTSC-VAGOTEVQ-YIOMFQV
| `secretKey` | rkbvewdrbeitfxeliqwru0rzpfq5dz24favre45v4ev7y0jma3ykjqds3lwy018h8bray9uw3ebq0ma

| Parameter | Value |
|-|-|
|`method`| getInfo |
|`timestamp`| 1624962347062 |
|`recv_window`| 50000 |

#### Example : send parameter using request body
* **requestBody:** method=getInfo&timestamp=1624962347062&recv_window=50000
* **HMAC SHA512 signature:**
    ```
    [linux]$ echo -n "method=getInfo&timestamp=1624962347062&recv_window=50000" | openssl dgst -sha512 -hmac "rkbvewdrbeitfxeliqwru0rzpfq5dz24favre45v4ev7y0jma3ykjqds3lwy018h8bray9uw3ebq0ma"
    (stdin)= c73ab6fcc1eb89cb0a1341dfe123498ce303d9a780e9982d77a00fc92d4094ef0f80982ff571fe442c2474a618e4bb79c0b222d2f7374b4231411716280ba45f
    ```
* **curl command:**
    ```
    (HMAC SHA512)
    [linux]$ curl -H "Authorization: Bearer TZDJPNGD-VMSGVS7Q-5QAMYTSC-VAGOTEVQ-YIOMFQV" -H "Signature: c73ab6fcc1eb89cb0a1341dfe123498ce303d9a780e9982d77a00fc92d4094ef0f80982ff571fe442c2474a618e4bb79c0b222d2f7374b4231411716280ba45f" -X POST 'https://papi.cointrade.id/main' -d 'method=getInfo&timestamp=1624962347062&recv_window=50000'
    ```

## Private API Endpoint
### ENUM Definitions
**Parameter method (Method) :**
* getInfo
* transactionHistory
* trade
* tradeHistory
* openOrders
* orderHistory
* getOrder
* cancelOrder
* withdrawFee
* withdrawCoin

**Transaction type (type):**
* buy
* sell

**Sort by (order):**
* asc
* desc

**Pair to get the information from (pair):**
* btc_ctr
* btc_ctr
* doge_ctr

**Currency to withdraw (currency):**
* alg
* btc
* eth

### Private API Endpoints
All request sent to this endpoint

    POST https://papi.cointrade.id/main

All request sent with Request header

| Name | Type | Mandatory | Description |
|-|-|-|-|
|`Key`| string | yes | API Key|
|`Signature`| string | yes | Encrypted with method HMAC-SHA512 using secret key. (Request body (?param=val&param1=val1))|

All request sent with Request body

| Name | Type | Mandatory | Description |
|-|-|-|-|
|`method`| string |yes|Specify the method you want to call |
|`timestamp`| timestamp in milisecond | optional when sending request using `nonce` | The millisecond timestamp of when the request was created and sent. Default value is 5000 (milliseconds).|
|`recv_window`| timestamp in milisecond | no | This parameter is optional when you sending request using timestamp. The value should specify the number of millisecond after timestamp where your request is valid. That mean your request still valid if it sent and processed within timestamp and timestamp + recv_window. Default value is 5000 (milliseconds).|

#### Get Info Endpoint
This method gives user balances and server's timestamp.

Request Body

| Name | Type | Mandatory | Description | Value | default |
|-|-|-|-|-|-|
|`method`| string |yes|Specify the method you want to call |getInfo| |

Response
```json
{
  "server_time": 1625026603,
  "balance": {
    "btc": "0.0000000000000000000000000000",
    "doge": "3984143.496000000000",
    "eth": "0.00800000000000000000",
    "ctr": "95296550.000000000000",
    "alg": "48890.000000000000"
  },
  "balance_hold": {
    "ctr": 4700273,
    "alg": 100,
    "btc": 20,
    "doge": 1420.979
  },
  "address": {
    "alg": "BRJAvzLHnRM3Yaya8RX5EegaeqxFadNity",
    "btc": "UYiy1XqxmgIBu9vvOpEdUzVvwzOEYjgf",
    "dash": "4JYiGuMe6FD-hv-nUKIqp35spr9k45uL",
    "doge": "y9ObXJUgRheeiWC2VDVqeEwwNBZ5yOH9",
    "eth": "83282044d6aaca966f1bbce66108b7ebbfe5881f"
  },
  "user_id": 2,
  "name": "John Doe",
  "email": "johndoe@gmail.com",
  "profile_picture": "https://cointrade.id/image/avatar?id=8JsbLStMSuK9YIQptkHZzUuwVeC-e-NP",
  "verification_status": true,
  "gauth_enable": false
}
```

#### Transaction History Endpoints
This method gives list of deposits and withdrawals of all currencies.

Request Body

| Name | Type | Mandatory | Description | Value | default |
|-|-|-|-|-|-|
|`method`| string |yes|Specify the method you want to call |transactionHistory||

Response
```json
{
  "withdraw": {
    "ctr": [
      {
        "status": "Success",
        "type": "Voucher",
        "ctr": 100000,
        "fee": 0,
        "amount": 100000,
        "submit_time": 1625033544,
        "success_time": null,
        "withdraw_id": 20,
        "tx": "CXA1J44S-71099BE8-454ED791-65D10450-31AEDFA0-1710FD0B"
      }
    ],
    "alg": [
      {
        "status": "On Review",
        "type": "Blockchain Cryptocurrency",
        "alg": 90,
        "fee": 5,
        "amount": 85,
        "submit_time": 1624945548,
        "success_time": null,
        "withdraw_id": 18,
        "tx": "-"
      },
      {
        "status": "Frozen",
        "type": "Blockchain Cryptocurrency",
        "alg": 820,
        "fee": 5,
        "amount": 815,
        "submit_time": 1624942883,
        "success_time": null,
        "withdraw_id": 16,
        "tx": "-"
      }
    ]
  },
  "deposit": {
    "btc": [
      {
        "type": "Manual Admin",
        "btc": 20,
        "fee": 0,
        "amount": 20,
        "time": 1624759264,
        "deposit_id": 21,
        "tx": "manual oleh admin"
      },
      {
        "type": "Manual Admin",
        "btc": 1000,
        "fee": 0,
        "amount": 1000,
        "time": 1618194472,
        "deposit_id": 18,
        "tx": "manual oleh admin"
      }
    ],
    "eth": [
      {
        "type": "Manual Admin",
        "eth": 0.01,
        "fee": 0,
        "amount": 0.01,
        "time": 1618864963,
        "deposit_id": 20,
        "tx": "manual oleh admin"
      }
    ],
    "ctr": [
      {
        "type": "Manual Admin",
        "ctr": 10000000,
        "fee": 0,
        "amount": 10000000,
        "time": 1618189325,
        "deposit_id": 17,
        "tx": "manual by admin"
      },
      {
        "type": "Batal Voucher",
        "ctr": 50000,
        "fee": 0,
        "amount": 50000,
        "time": 1617178216,
        "deposit_id": 5,
        "tx": "QEZCZ0PW-744627CB-9352472D-E475DD3B-1C1AB270-9038BBE4"
      }
    ]
  }
}
```

#### Trade Endpoints
This method is for opening a new order

Request Body

| Name | Type | Mandatory | Description | Value | default |
|-|-|-|-|-|-|
|`method`| string |yes|Specify the method you want to call |trade| |
|`pair`|string|yes|Pair to get the information from| alg_ctr, btc_ctr, etc| |
|`type`|string|yes|transaction type (buy or sell)|||
|`price`|numeric|yes|order price|buy/sell||
|`ctr`|numeric|required on buying coin|amount of rupiah to buy coin|||
|`alg`|numeric|required on selling coin|amount of coin to sell|||

Response
```json
{
  "receive_alg": 20,
  "spend_ctr": 0,
  "fee": 0
}
```

#### Trade History Endpoints
This method gives information about transaction in buying and selling history.

Request Body

| Name | Type | Mandatory | Description | Value | default |
|-|-|-|-|-|-|
|`method`| string |yes|Specify the method you want to call |tradeHistory||
|`count`|numeric|no|number of transaction which will be displayed||1000|
|`from_id`|numeric|no|first ID||0|
|`end_id`|numeric|no|end ID||0|
|`order`|string|no|sort by|asc / desc|desc|
|`since`|timestamp|no|start time||unix time|
|`end`|timestamp|no|end time||unix time|
|`pair`|string|yes|Pair to get the information from|alg_ctr, btc_ctr, etc|alg_ctr|

Response
```json
[
  {
    "trade_id": 1,
    "order_id": 1,
    "type": "sell",
    "alg": "1000.0000000000000000",
    "price": 3000,
    "fee": "0",
    "trade_time": 1618325214
  },
  {
    "trade_id": 1,
    "order_id": 2,
    "type": "buy",
    "alg": "1000.0000000000000000",
    "price": 3000,
    "fee": "3.0000000000000000",
    "trade_time": 1618325214
  }
]
```

#### Open Orders Endpoints
This method gives the list of current open orders (buy and sell).

Request Body

| Name | Type | Mandatory | Description | Value | default |
|-|-|-|-|-|-|
|`method`| string |yes|Specify the method you want to call |openOrders||
|`pair`|string|no|Pair to get the information from|alg_ctr, btc_ctr, etc||

Response
```json
[
  {
    "order_id": 88,
    "submit_time": 1625035516,
    "pair": "alg_ctr",
    "side": "Sell",
    "price": 4400,
    "amount": "20.0000000000000000",
    "remain": "20.0000000000000000"
  },
  {
    "order_id": 80,
    "submit_time": 1624788915,
    "pair": "alg_ctr",
    "side": "Sell",
    "price": 4500,
    "amount": "100.0000000000000000",
    "remain": "100.0000000000000000"
  },
  {
    "order_id": 78,
    "submit_time": 1624788402,
    "pair": "alg_ctr",
    "side": "Buy",
    "price": 3000,
    "amount": "166.6666666600000000",
    "remain": "116.6666666600000000"
  }
]
```

#### Order History
This method gives the list of order history (buy and sell)

Request Body

| Name | Type | Mandatory | Description | Value | default |
|-|-|-|-|-|-|
|`method`| string |yes|Specify the method you want to call |orderHistory||
|`pair`|string|yes|Pair to get the information from|alg_ctr, btc_ctr, etc|alg_ctr|
|`count`|int|no||||
|`from`|int|no||||

Response
```json
[
  {
    "order_id": 88,
    "submit_time": 1625035516,
    "pair": "alg_ctr",
    "side": "Sell",
    "price": 4400,
    "amount": "20.0000000000000000",
    "remain": "20.0000000000000000",
    "canceled": false,
    "completed": false
  },
  {
    "order_id": 87,
    "submit_time": 1624936840,
    "pair": "alg_ctr",
    "side": "Buy",
    "price": 3050,
    "amount": "65.5737704900000000",
    "remain": "65.5737704900000000",
    "canceled": true,
    "completed": false
  }
]
```

#### Get Order Endpoints
Use getOrder to get specific order details.

Request Body

| Name | Type | Mandatory | Description | Value | default |
|-|-|-|-|-|-|
|`method`| string |yes|Specify the method you want to call |getOrder||
|`pair`|string|yes|Pair to get the information from|alg_ctr, btc_ctr, etc|alg_ctr|
|`order_id`|int|yes|Order ID|||

Response
```json
{
  "order_id": 80,
  "submit_time": 1624788915,
  "pair": "alg_ctr",
  "side": "Sell",
  "price": 4500,
  "amount": "100.0000000000000000",
  "remain": "100.0000000000000000",
  "canceled": false,
  "completed": false
}
```

#### Cancel Order Endpoints
This method is for canceling an existing open order.

Request Body

| Name | Type | Mandatory | Description | Value | default |
|-|-|-|-|-|-|
|`method`| string |yes|Specify the method you want to call |cancelOrder||
|`pair`|string|yes|Pair to get the information from|alg_ctr, btc_ctr, etc|alg_ctr|
|`order_id`|int|yes|Order ID|||

Response
```json
{
  "id": 88,
  "market_pair_id": "algctr",
  "member_id": 2,
  "price": 4400,
  "order_amount": 2000000000,
  "side": 2
}
```

#### Withdraw Fee Endpoints
This method is for check withdraw fee

To be able to use this method you need to enable withdraw permission when you generate the API Key. Otherwise you will get “Operation not allowed” error.

Request Body

| Name | Type | Mandatory | Description | Value | default |
|-|-|-|-|-|-|
|`method`| string |yes|Specify the method you want to call |withdrawFee||
|`currency`|string|yes|Currency for check withdraw fee |alg, btc, doge, eth, etc||

Response
```json
{
  "server_time": 1625036062,
  "withdraw_fee": "5.0000000000000000",
  "currency": "alg"
}
```

#### Withdraw Coin Endpoints
This method is for withdrawing assets (except CTR).

To be able to use this method you need to enable withdraw permission when you generate the API Key. Otherwise you will get “Operation not allowed” error.

You also need to prepare a Callback URL. Callback URL is a URL that our system will call to verify your withdrawal requests. Various parameters will be sent to Callback URL, make sure to check this information on your server side. If all the data is correct, print out a string “ok”  (without quotes). We will continue the request if only we receive “ok” (without quotes) response, otherwise the request will be failed.

Callback call will be sent through a POST request, with 5 seconds connection timeout.


Request Body

| Name | Type | Mandatory | Description | Value | default |
|-|-|-|-|-|-|
|`method`| string |yes|Specify the method you want to call |withdrawCoin||
|`currency`|string|yes|Currency to withdraw|alg, btc, doge, eth, etc||
|`withdraw_address`|string|yes|Receiver address|a valid address||
|`withdraw_amount`|numeric|yes|Amount to send|||

Response
```json
{
  "status": "On Review",
  "withdraw_currency": "alg",
  "withdraw_address": "safwrwerwfsdfsdfwewrewrwsdfdsfwerwsdfsdf",
  "withdraw_amount": "11",
  "fee": 5,
  "amount_after_fee": 6,
  "submit_time": 1625036217,
  "withdraw_id": "21",
  "txid": "-"
}
```
