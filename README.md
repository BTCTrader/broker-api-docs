# Note: Please use the new version. [here.](https://github.com/BTCTrader/broker-api-docs/blob/master/README-pro.md)

# You can find the current API docs here. https://docs.btcturk.com/

# White label exchange API documentation
The documentation for BTCTrader's white label exchange platform API.

These docs are for the APIs of BTCTurk and other BTCTrader partners.

## Usage
Append your exchange's URL to the beginning of the methods to use the API. For example, to get ticker info from BTCTurk, use [Ticker endpoint](https://api.btcturk.com/api/v2/ticker)

## Questions & Problems
Please use the [issues](https://github.com/BTCTrader/broker-api-docs/issues) on this github project to ask questions and report bugs.

## Rate Limits
*For more information, follow the rate limits page. [Rate limits](https://docs.btcturk.com/rate-limits)

## Ticker

<code>GET</code> .../api/v2/ticker 

**Result:**
``` json
[
    {
      "pair": "BTCTRY",
      "pairNormalized": "BTC_TRY",
      "timestamp": 1653897800073,
      "last": 499979.00,
      "high": 502000.00,
      "low": 475074.00,
      "bid": 499992.00,
      "ask": 499996.00,
      "open": 476868.00,
      "volume": 151.46800143,
      "average": 489551.46,
      "daily": 23128.00,
      "dailyPercent": 4.85,
      "denominatorSymbol": "TRY",
      "numeratorSymbol": "BTC",
      "order": 1000
    },
    {
      "pair": "ETHBTC",
      "pairNormalized": "ETH_BTC",
      "timestamp": 1653897780329,
      "last": 0.06218,
      "high": 0.06231,
      "low": 0.06108,
      "bid": 0.06212,
      "ask": 0.06231,
      "open": 0.06108,
      "volume": 6.00868250,
      "average": 0.06158,
      "daily": 0.00123,
      "dailyPercent": 1.80,
      "denominatorSymbol": "BTC",
      "numeratorSymbol": "ETH",
      "order": 3024
    },
    {
      "pair": "ETHTRY",
      "pairNormalized": "ETH_TRY",
      "timestamp": 1653897799785,
      "last": 31159.00,
      "high": 31182.00,
      "low": 29107.00,
      "bid": 31159.00,
      "ask": 31160.00,
      "open": 29309.00,
      "volume": 1205.78869300,
      "average": 30145.76,
      "daily": 1851.00,
      "dailyPercent": 6.31,
      "denominatorSymbol": "TRY",
      "numeratorSymbol": "ETH",
      "order": 1042
    }
]
```
* last: Last BTC price
* high: Highest trade price in the last 24 hours
* low: Lowest trade price in the last 24 hours
* volume: Total volume in the last 24 hours
* bid: Highest current bid
* ask: Lowest current ask
* open: Price of the opening trade in the last 24 hours
* average: Average Price in the last 24 hours
* pair : Pair symbol

## Order Book

 <code>GET</code> .../api/v2/orderbook?pairSymbol=BTCTRY

**Result:**
``` json
{
  "data": {
    "timestamp": 1653897927565.0,
    "bids": [
      [
        "499852.00",
        "0.00572421"
      ],
      [
        "499851.00",
        "0.00466286"
      ],
      [
        "499850.00",
        "0.32438288"
      ]
    ],
    "asks": [
      [
        "500186.00",
        "0.00137921"
      ],
      [
        "500248.00",
        "0.20000000"
      ],
      [
        "500250.00",
        "0.00085424"
      ]
    ]
  },
  "success": true,
  "message": null,
  "code": 0
}
```
* **bids:** Array of current open bids on the orderbook.
* **asks:** Array of current open askss on the orderbook.

## Trades

 <code>GET</code> .../api/v2/trades?pairSymbol=BTCTRY 

OR

 <code>GET</code> .../api/v2/trades?pairSymbol=BTCTRY&last=COUNT (Max. value for count parameter is 50)

**Result:**
``` json
  {
  "success": true,
  "message": null,
  "code": 0,
  "data": [
    {
      "pair": BTCTRY,
      "pairNormalized": BTC_TRY,
      "numerator": BTC,
      "denominator": TRY,
      "date": 1533650242300,
      "tid": "636692470417865271",
      "price": "33490",
      "amount": "0.00032747"
    },
    {
      "pair": BTCTRY,
      "pairNormalized": BTC_TRY,
      "numerator": BTC,
      "denominator": TRY,
      "date": 1533650237143,
      "tid": "636692470367947749",
      "price": "33245",
      "amount": "0.00471901"
    }
    ]
  }
```

* date: Unix time of the trade (In the exchange's local timezone)
* tid: Trade ID
* rice: Price of the trade
* amount: Amount of the trade

## OHCL Data (Daily)

<code>GET</code> .../v1/ohlcs 

**Result:**
``` json
[
   {
    "pair": "BTCUSDT",
    "time": 1639526400,
    "open": 48250.0,
    "high": 49500.0,
    "low": 46601.0,
    "close": 48820.0,
    "volume": 199.490950394233,
    "total": 9634561.91977406,
    "average": 48295.73,
    "dailyChangeAmount": 570.0,
    "dailyChangePercentage": 1.18
  }
]
```
* Date: DateTime (In the exchange's local timezone and daily)
* Open: Price of the opening trade on the Date
* High: Highest trade price on the Date
* Low: Lowest trade price on the Date
* Close: Price of the closing trade on the Date
* Volume: Total volume on the Date
* Average: Average price on the Date
* DailyChangeAmount: Amount of difference between Close and Open on the Date
* DailyChangePercentage: Percentage of difference between Close and Open on the Date

## API Authentication

All API calls related to a user account require authentication.

You need to provide 3 parameters to authenticate a request:

* "X-PCK": API key
* "X-Stamp": Nonce
* "X-Signature": Signature

#### API key

You can create the API key from the Account > API Access page in your exchange account.

#### Nonce

Nonce is a regular integer number. It must be increasing with every request you make.

A common practice is to use unix timestamp for that parameter.

#### Signature

Signature is a HMAC-SHA256 encoded message. The HMAC-SHA256 code must be generated using a private key that contains a timestamp and your API key

Example (C#):
```c#
string message = yourAPIKey + nonce;
using (HMACSHA256 hmac = new HMACSHA256(Convert.FromBase64String(yourPrivateKey)))
{
   byte[] signatureBytes = hmac.ComputeHash(Encoding.UTF8.GetBytes(message));
   string X-Signature = Convert.ToBase64String(signatureBytes);
}
```
After creating the parameters, you have to send them in the HTML Header of your request with their name

Example (C#):
```c#
client.DefaultRequestHeaders.Add("X-PCK", yourAPIKey);
client.DefaultRequestHeaders.Add("X-Stamp", nonce.ToString());
client.DefaultRequestHeaders.Add("X-Signature", signature);
```

Warning: Your IP address can be blocked if you make too many unauthorized requests. Make sure you implement the authentication method properly.

## Account Balance (Requires Authentication)

 <code>GET</code> .../api/v1/users/balances

**Result:**
``` json
{
  "data": [
    {
      "asset": "TRY",
      "assetname": "Türk Lirası",
      "balance": "103158.9412490031968651",
      "locked": "1023.5699999896000000",
      "free": "102135.3712490135968651"
    },
    {
      "asset": "BTC",
      "assetname": "Bitcoin",
      "balance": "29.6027353000000000",
      "locked": "0.0010000000000000",
      "free": "29.6017353000000000"
    }
    ...
  ],
  "success": true,
  "message": null,
  "code": 0
}
```

## User Transactions (Requires Authentication)

 <code>GET</code> .../api/v1/users/transactions/trade

**Params:**

* orderId: long, Optional you can not combine this parameter with other parameters. So you should send this parameter alone.
* type: string array, {"buy", "sell"}
* symbol: string array, {"btc", "try", ...etc.}
* startDate: long, Optional timestamp if null will return last 30 days
* endDate: long, Optional timestamp if null will return last 30 days

**Result:**
``` json
{
    "success": true,
    "message": "SUCCESS",
    "code": 0,
    "data": [
        {
            "price": "313000.00",
            "numeratorSymbol": "BTC",
            "denominatorSymbol": "TRY",
            "orderType": "sell",
            "orderId": 22632304,
            "id": 5758565,
            "timestamp": 1628690323897,
            "amount": "-0.00040000",
            "fee": "-0.19098258",
            "tax": "-0.03437687"
        },
        {
            "price": "399742.00",
            "numeratorSymbol": "BTC",
            "denominatorSymbol": "TRY",
            "orderType": "buy",
            "orderId": 22632299,
            "id": 5758564,
            "timestamp": 1628690307310,
            "amount": "0.00024971",
            "fee": "-0.15226792",
            "tax": "-0.02740823"
        },
        {
            "price": "429715.00",
            "numeratorSymbol": "BTC",
            "denominatorSymbol": "TRY",
            "orderType": "buy",
            "orderId": 21075506,
            "id": 5754843,
            "timestamp": 1627556216413,
            "amount": "0.00023229",
            "fee": "-0.15226792",
            "tax": "-0.02740823"
        }
    ]
}
```

## Open Orders (Requires Authentication)

 <code>GET</code> .../api/v1/openOrders
 
**Result:**
``` json
{   "success": true,
    "message": null,
    "code": 0,
    "data": {
        "asks": [{
            "id": 16060235,
            "price": "66800.00",
            "amount": "0.09733687",
            "quantity": "0.09733687",
            "stopPrice": "0.00",
            "pairSymbol": "BTCTRY",
            "pairSymbolNormalized": "BTC_TRY",
            "type": "sell",
            "method": "limit",
            "orderClientId": "da593000-6eb3-4a1c-ba26-c616122a0210",
            "time": 0,
            "updateTime": 1591286401373,
            "status": "Untouched",
            "leftAmount": "0.09733687"
        }
],
        "bids": [{
            "id": 16071095,
            "price": "65817.00",
            "amount": "0.08956055",
            "quantity": "0.08956055",
            "stopPrice": "0.00",
            "pairSymbol": "BTCTRY",
            "pairSymbolNormalized": "BTC_TRY",
            "type": "buy",
            "method": "limit",
            "orderClientId": "da593000-6eb3-4a1c-ba26-c616122a0210",
            "time": 0,
            "updateTime": 1591352311273,
            "status": "Untouched",
            "leftAmount": "0.08956055"
        }
]
    }
}
```

* id: Order id
* datetime: Date and time the order was inserted at
* type: Type of order. BuyBtc or SellBtc
* price: Price of the order
* amount: Bitcoin amount of the order

## Cancel Order (Requires Authentication)

 <code>POST</code> .../api/v1/order
 
**Params:**
* id: order ID

**Result:**
``` json
{
  "success": true,
  "message": "string",
  "code": "",
  "data": {
  }
}
```

* result: True if the order cancellation succeeded. False if it failed.

## Buy Order (Requires Authentication)

 <code>POST</code> .../api/v1/order
 
**Params:**

* quantity: decimal, mandatory for market or limit orders.
* price: decimal, price field will be ignored for market orders. Market orders get filled with different prices until your order is completely filled. There is a 5% limit on the difference between the first price and the last price. İ.e. you can't buy at a price more than 5% higher than the best sell at the time of order submission and you can't sell at a price less than 5% lower than the best buy at the time of order submission.
* stopPrice: decimal, for stop orders
* newOrderClientId: string, GUID if user did not set.
* orderMethod: enum, "limit", "market" or "stoplimit"
* orderType: enum, "buy" or "sell"
* pairSymbol: BTCTRY, ETHTRY etc.


**Result:**
``` json
{
  "success": true,
  "message": "OK",
  "code": 0,
  "data": {
    "id": 9932534,
    "datetime": 1543996112263,
    "type": "Buy",
    "method": "Limit",
    "price": "20000.00",
    "stopPrice": "20000.00",
    "quantity": "0.001",
    "pairSymbol": "BTCTRY",
    "pairSymbolNormalized": "BTC_TRY",
    "newOrderClientId": "test"
  }
}
```

* The result is a JSON object containing your order details and order ID if the request succeeded.
