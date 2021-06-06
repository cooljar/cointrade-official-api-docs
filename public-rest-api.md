# Cointrade Public REST API  
These are open data for public. It doesn't need an API key to call these methods. You can call simple GET request or open it directly from the browser.
#### Table of Contents
 - [General API Information](#general-api-information)
 - [Limit](#limit)
 - [API](#api)
     - [/api/server_time](#server-time) 
     - [/api/pairs](#pairs)  
     - [/api/price_increments](#price-increments)
     - [/api/summaries](#summaries)
     - [/api/ticker/$pair_id](#ticker)
     - [/api/ticker_all](#ticker-all)
     - [/api/trades/$pair_id](#trades)
     - [/api/depth/$pair_id](#depth)


## General API Information
 - The base endpoint is: https://cointrade.id
 - All endpoints return either a JSON object or array.
 - All time and timestamp related fields are in milliseconds.

## Limit
Public API rate limited to 100 request/minute.  

## Server Time
Provide server time on cointrade exchange
#### Request
```
/api/v1/server_time
```
#### Response
```json
{
    "timezone": "UTC",
    "server_time": 1571205969552
}
```

## Pairs
Provide available pairs on cointrade exchange
#### Request
```
/api/v1/pairs
```
#### Response
```json
[
    {
        "id": "btcctr",
        "symbol": "BTCCTR",
        "base_currency": "ctr",
        "traded_currency": "btc",
        "traded_currency_unit": "BTC",
        "description": "BTC/CTR",
        "ticker_id": "btc_ctr",
        "volume_precision": 0,
        "price_precision": 1000,
        "price_round": 8,
        "pricescale": 1000,
        "trade_min_base_currency": 50000,
        "trade_min_traded_currency": 0.0001
    }   
]
```

## Price Increments
Provide price increments of each pairs on cointrade exchange
#### Request
```
/api/v1/price_increments
```
#### Response
```json
{
    "increments": {
        "btc_ctr": "1000",
        "alg_ctr": "1",
        "mrc_ctr": "1",
        "dash_ctr": "1",
        "eth_ctr": "500"
    }
}
```

## Summaries
#### Request
```
/api/v1/summaries
```
#### Response
```json
{
    "tickers": {
        "btc_ctr": {
            "high": "120009000",
            "low": "116735000",
            "vol_btc": "218.31103295",
            "vol_ctr": "25831203178",
            "last": "117136000",
            "buy": "116938000",
            "sell": "117136000",
            "server_time": 1571206340
        }
    },
    "prices_24h": {
        "btcctr": "120002000",
        "algctr": "4500",
        "mrcctr": "4500"
    },
    "prices_7d": {
        "btcctr": "116001000",
        "algctr": "4500",
        "mrcctr": "4500"
    },
}
```

## Ticker
Provide single ticker price on each pair in cointrade exchange
#### Request
```
/api/v1/ticker/$pair_id
```
##### Parameter 
| Type | Mandatory | Description |
| ------ | ------ | ------ |
| String | Optional| Example: `algctr`, `mrcctr`, `ethctr`. For specific pair id please use Response from API [`/api/v1/pairs`](#pairs). Default `$pair_id` is `algctr`. |

#### Response
```json
{
    "ticker": {
        "high": "5500",
        "low": "4500",
        "vol_alg": "153588.49847928",
        "vol_ctr": "78884203",
        "last": "5600",
        "buy": "5700",
        "sell": "4400",
        "server_time": 1571207668
    }
}
```

## Ticker All
Provide all ticker prices in cointrade exchange
#### Request
```
/api/v1/ticker_all
```
#### Response
```json
{
    "tickers": {
        "btc_ctr": {
            "high": "120009000",
            "low": "116735000",
            "vol_btc": "218.13777777",
            "vol_ctr": "25800033297",
            "last": "117088000",
            "buy": "117002000",
            "sell": "117078000",
            "server_time": 1571207881
        }  
    }
}
```

## Trades
Provide Order Book trade on each pair in cointrade exchange
#### Request
```
/api/v1/trades/$pair_id
```
##### Parameter 
| Type | Mandatory | Description |
| ------ | ------ | ------ |
| String | Optional| Example: `algctr`, `btcctr`, `ethctr`. For specific pair id please use Response from API [`/api/v1/pairs`](#pairs). Default `$pair_id` is `algctr`. |

#### Response
```json
[
    {
        "date": "1571207255",
        "price": "4700",
        "amount": "123.19523759",
        "tid": "7847563",
        "type": "sell"
    },
    {
        "date": "1571207236",
        "price": "4100",
        "amount": "121.42187500",
        "tid": "7893645",
        "type": "buy"
    }
]
```

## Depth
Provide Volume price Buy and Sell on each pair in cointrade exchange
#### Request
```
/api/v1/depth/$pair_id
```
##### Parameter 
| Type | Mandatory | Description |
| ------ | ------ | ------ |
| String | Optional| Example: `algctr`, `btcctr`, `ethctr`. For specific pair id please use Response from API [`/api/v1/pairs`](#pairs). Default `$pair_id` is `algctr`. |
#### Response
```json
{
    "buy": [
        [
            4500,
            "165.65364758"
        ],
        [
            4400,
            "212.36598735"
        ]
    ],
   "sell": [
        [
            4700,
            "2763.76356453"
        ],
        [
            4750,
            "0.66536542"
        ]
    ]
}
```
