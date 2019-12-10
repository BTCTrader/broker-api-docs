# White label exchange API documentation
The documentation for BTCTrader's white label exchange platform API.

These docs are for the APIs of BtcTurk and other BTCTrader partners.

## Usage
Append [https://api.btcturk.com/] URL to the beginning of the methods to use the API. 

For example, to get ticker info from BTCTurk, use [https://api.btcturk.com/api/v2/ticker] (https://api.btcturk.com/api/v2/ticker)

## Testing
You can use our testing platforms to test the APIs. The balances on the test sites are not real and do not represent any real value. The testing platforms work on TESTNET and you can deposit TESTNET coins to your account. The testing platforms are:

* [BTCTurk Test] (https://pro-dev.btcturk.com/) to create your test API keys.
* [BTCTurk Test API] (https://api-dev.btcturk.com/)

**Important** Our mobile applications will not work with the testing platforms. They can only be synced with our live platforms.

## Questions & Problems
Please check the [F.A.Q](https://github.com/BTCTrader/broker-api-docs/blob/master/FAQ.md) for Frequently Asked Questions. You can use the [issues](https://github.com/BTCTrader/broker-api-docs/issues) on this github project to ask questions and report bugs.

# General API Information
* API Base: **https://api.btcturk.com**
* All timestamps are in miliseconds
* Authentication related requests must be send as `application/json`
* For `GET` endpoints, parameters must be sent as a `query string`.
* For `POST`, `PUT`, and `DELETE` endpoints, the parameters may be sent as a
  `query string` or in the `request body` with content type
  `application/x-www-form-urlencoded`. You may mix parameters between both the
  `query string` and `request body` if you wish to do so.
* Parameters may be sent in any order.
* If a parameter sent in both the `query string` and `request body`, the
  `query string` parameter will be used.
* You can send pair symbol parameter in two formats, ex: BTCTRY or BTC_TRY
* All endpoints successful requests return JSON object model
``` json
  "success": true,
  "message": string,
  "code": int,
  "data": []
```
* The "data" parameter including the response data information, could be null if the request is `DELETE`, just check the "success" parameter.
* Please use V2 endpoint for all endpoints that don't require authentication (/api/v2/ticker, /api/v2/ohlc etc.)
* Any endpoint could return an error as a JSON object.
``` json
  "success": false,
  "message": string,
  "code": int
```
* HTTP 400 return code for bad requests
* HTTP 429 return code for exceeded request rate limit.
* HTTP 422 return code for requests could not be processed. 
* HTTP 5xx return codes for internal server errors.


# Request Limits
* /api/v2/ticker requests are limited to 10 requests per 100 miliseconds.
* Other requests are limited to 1 request per 100 miliseconds.
* If you make more than 50 consequent unauthorized requests, your IP address will be blocked

## Pairs scale (Quantity/Price)

| Pair          | Quantity Scale      |  Price Scale    |
| :-------------|:-------------------:|:---------------:|
| BTC/TRY        | 8 				  | 0			    |
| EOS/TRY        | 2 				  | 2			    |
| ETH/TRY        | 8 				  | 0			    |
| LTC/TRY        | 8 				  | 2			    |
| NEO/TRY        | 4 				  | 2			    |
| USDT/TRY       | 2 				  | 2			    |
| XLM/TRY        | 4 				  | 4			    |
| XRP/TRY        | 4 				  | 2			    |
| BTC/USDT       | 8 				  | 0			    |
| EOS/USDT       | 2 				  | 3 		    |
| ETH/USDT       | 8 				  | 1			    |
| LTC/USDT       | 8 				  | 2			    |
| NEO/USDT       | 4 				  | 2			    |
| XLM/USDT       | 4 				  | 4			    |
| XRP/USDT       | 4 				  | 4			    |
| EOS/BTC        | 4 				  | 5			    |
| ETH/BTC        | 8 				  | 8			    |
| LTC/BTC        | 8 				  | 5			    |
| NEO/BTC        | 8 				  | 5			    |
| XLM/BTC        | 4 				  | 8			    |
| XRP/BTC        | 4 				  | 8			    |

* You can use https://api.btcturk.com/api/v2/server/exchangeinfo for all tradable pairs and their quantity or price scales and more.

# Public Endpoints
## Ticker

* If pairSymbol is not set, ticker for all pairs will be returned in a json array.

<code>GET</code> ../api/v2/ticker 

OR

<code>GET</code> .../api/v2/ticker?pairSymbol=BTC_TRY

OR

<code>GET</code> .../api/v2/ticker/currency?symbol=USDT

**Result:**
``` json
{
  "data": [
    {
      "pair": "BTCTRY",
      "pairNormalized": "BTC_TRY",
      "timestamp": 1570024156166,
      "last": 47500,
      "high": 48710,
      "low": 47000,
      "bid": 47472,
      "ask": 47670,
      "open": 47988,
      "volume": 304.41,
      "average": 47850.41,
      "daily": -318,
      "dailyPercent": -1.02,
      "denominatorSymbol": "TRY",
      "numeratorSymbol": "BTC"
    },
    {
      "pair": "ETHTRY",
      "pairNormalized": "ETH_TRY",
      "timestamp": 1570024156166,
      "last": 1020,
      "high": 1034,
      "low": 999,
      "bid": 1019,
      "ask": 1023,
      "open": 1032,
      "volume": 5038.27,
      "average": 1015.66,
      "daily": -9,
      "dailyPercent": -1.16,
      "denominatorSymbol": "TRY",
      "numeratorSymbol": "ETH"
    }
	...
  ],
  "success": true,
  "message": "",
  "code": 0,
}
```
* **pairSymbol**: Requested pair symbol
* **pairSymbolNormalized**: Requested pair symbol with "_" in between.
* **timestamp**: Current Unix time in milliseconds
* **last**: Last price
* **high**: Highest trade price in last 24 hours
* **low**: Lowest trade price in last 24 hours
* **bid**: Highest current bid
* **ask**: Lowest current ask
* **open**: Price of the opening trade in last 24 hours
* **volume**: Total volume in last 24 hours
* **average**: Average Price in last 24 hours
* **daily**: Price change in last 24 hours
* **dailyPercent**: Price change percent in last 24 hours
* **denominatorSymbol**: Denominator currency symbol of the pair 
* **numeratorSymbol**: Numerator currency symbol of the pair

## Order Book

 <code>GET</code> .../api/v2/orderbook?pairSymbol=BTC_TRY 
 
 OR
 
 <code>GET</code> .../api/v2/orderbook?pairSymbol=BTC_TRY&limit=100

**Parameters:**
 * pairSymbol: string Mandatory
 * limit: int Optional (default 100 max 1000)
 
**Result:**
``` json
{
  "success": true,
  "message": null,
  "code": 0,
  "data": {
    "timestamp": 1543836448605,
    "bids": [
      [
        "33245.00",
        "2.10695265"
      ],
      [
        "33209.00",
        "0.001"
      ]
    ],
    "asks": [
      [
        "33490.00",
        "0.03681877"
      ],
      [
        "33499.00",
        "1.00000000"
      ]
    ]
  }
}
```
* **timestamp:** Current Unix time in milliseconds
* **bids:** Array of current open bids on the orderbook.
* **asks:** Array of current open askss on the orderbook.

## Trades
 
 <code>GET</code> .../api/v2/trades?pairSymbol=BTC_TRY

OR

 <code>GET</code> .../api/v2/trades?pairSymbol=BTC_TRY&last=COUNT (Max. value for count parameter is 50)

**Parameters:**
 * **pairSymbol**: string Mandatory
 * **last**: int Optional (default 50 max 1000) 
 
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
* **pair**: Requested pair symbol
* **pairNormalized**: Request Pair symbol with "_" in between.
* **numerator**: Numerator currency for the requested pair
* **denominator**: Denominator currency for the requested pair
* **date**: Unix time of the trade in milliseconds
* **tid**: Trade ID
* **price**: Price of the trade
* **amount**: Amount of the trade

## OHCL Data (Daily)

<code>GET</code> .../api/v2/ohlc?pairSymbol=BTC_TRY

OR

<code>GET</code> .../api/v2/ohlc?pairSymbol=BTC_TRY&last=COUNT

**Parameters:**
 * **pairSymbol**: (string) Mandatory ex. BTC_TRY
 * **last**: (int) Optional ex. 10
 
**Result:**
``` json
{
  "data": [
    {
      "pairSymbol": "BTCTRY",
      "pairSymbolNormalized": "BTC_TRY",
      "time": 15334272,
      "open": "35999",
      "high": "35999",
      "low": "35999",
      "close": "35999",
      "volume": "0.00921193",
      "average": "35999",
      "dailyChangeAmount": "0",
      "dailyChangePercentage": "0"
    },
    {
      "pairSymbol": "BTCTRY",
      "pairSymbolNormalized": "BTC_TRY",
      "time": 15333408,
      "open": "35999",
      "high": "35999",
      "low": "35999",
      "close": "35999",
      "volume": "0.01659802",
      "average": "35999",
      "dailyChangeAmount": "0",
      "dailyChangePercentage": "0"
    }
  ],
  "success": true,
  "message": null,
  "code": 0
}
```
* **pairSymbol**: Requested pair symbol
* **pairSymbolNormalized**: Requested pair symbol with "_" in between.
* **timestamp**: Current Unix time in milliseconds
* **open**: Price of the opening trade on the time
* **high**: Highest trade price on the time
* **low**: Lowest trade price on the time
* **close**: Price of the closing trade on the time
* **volume**: Total volume on the time
* **average**: Average price on the time
* **DailyChangeAmount**: Amount of difference between Close and Open on the Date
* **DailyChangePercentage**: Percentage of difference between Close and Open on the Date

# Authentication Required Endpoints
## API Authentication V1

All API calls related to a user account, require authentication.

You need to provide 3 parameters to authenticate a request:

* "X-PCK": API Public Key
* "X-Stamp": Nonce
* "X-Signature": Signature

#### API Public key

You can create the API key from the Account > API Access page in your exchange account.

#### Nonce

Nonce is a regular integer number. It must be current timestamp in milliseconds.

### NOTE: It is a must to sync your current time with API server time which is in miliseconds format. Our servers are using UTC timezone.
### You can check the server time from here [https://api.btcturk.com/api/v2/server/time]

#### Signature

Signature is a HMAC-SHA256 encoded message. The HMAC-SHA256 code must be generated using a private key that contains a timestamp as nonce and your API key

Example (C#):
```c#
var apiKey = YOUR_API_PUBLIC_KEY;
var apiSecret = YOUR_API_SECRET;
var nonce = new DateTimeOffset(DateTime.UtcNow).ToUnixTimeMilliSeconds();
string message = apiKey + nonce;
using (HMACSHA256 hmac = new HMACSHA256(Convert.FromBase64String(apiSecret)))
{
   byte[] signatureBytes = hmac.ComputeHash(Encoding.UTF8.GetBytes(message));
   string X-Signature = Convert.ToBase64String(signatureBytes));
}
```
After creating the parameters, you have to send them in the HTML Header of your request with their name

Example (C#):
```c#
client.DefaultRequestHeaders.Add("X-PCK", apiKey);
client.DefaultRequestHeaders.Add("X-Stamp", nonce.ToString());
client.DefaultRequestHeaders.Add("X-Signature", signature);
client.DefaultRequestHeaders.Add("Content-Type", "application/json");
```

Example (Python):
```python
apiKey = YOUR_API_PUBLIC_KEY
apiSecret = YOUR_API_SECRET
apiSecret = base64.b64decode(apiSecret)
stamp = str(int(time.time())*1000)
data = "{}{}".format(apiKey, stamp).encode('utf-8')
signature = hmac.new(apiSecret, data, hashlib.sha256).digest()
signature = base64.b64encode(signature)

headers= {
  "X-PCK": apiKey,
  "X-Stamp": str(nonce),
  "X-Signature": str(base64.b64encode(signature).decode('utf-8')),
  "Content-Type: application/json"
}

```

Example (PHP):
```PHP
$apiKey = YOUR_API_PUBLIC_KEY;
$apiSecret = YOUR_API_SECRET;
$message = $apiKey.(time()*1000);
$signatureBytes = hash_hmac('sha256', $message, base64_decode($apiSecret), true);
$signature = base64_encode($signatureBytes);
$nonce = time()*1000;
$headers = array(
  'X-PCK: '.$apiKey,
  'X-Stamp: '.$nonce,
  'X-Signature: '.$signature,
  'Content-Type: application/json',
);
```

Warning: Your IP address can be blocked if you make too many unauthorized requests. Make sure you implement the authentication method properly. To prevent concurrent requests you may add 1 second sleep time before your authentication request.

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
* **asset**: Asset symbol
* **assetname**: Asset name
* **balance**: Total asset balance including open orders and pending withdrawal requests
* **locked**: Asset locked amount in open orders and withdrawal requests
* **free**: Asset available amount for trading

## User Transactions (Requires Authentication)

 <code>Get</code> .../api/v1/users/transactions/trade?type=buy&type=sell&symbol=btc&symbol=try&symbol=usdt

 **Params**
 
* **type**: string array, {"buy", "sell"}
* **symbol**: string array, {"btc", "try", ...etc.}
* **startDate**: long Optional timestamp if null will return last 30 days
* **endDate**: long Optional timestamp if null will return last 30 days

**Result:**
``` json
{
  "success": true,
  "message": "SUCCESS",
  "code": 0,
  "data": [
    {
      "price": 33700,
      "numeratorSymbol": "BTC",
      "denominatorSymbol": "TRY",
      "orderType": "sell",
      "id": 5553445,
      "timestamp": 1533314288287,
      "amount": -0.003,
      "fee": -0.0685423626,
      "tax": -0.012337625268
    },
    {
      "price": 33700,
      "numeratorSymbol": "BTC",
      "denominatorSymbol": "TRY",
      "orderType": "sell",
      "id": 5553444,
      "timestamp": 1533314254297,
      "amount": -0.001,
      "fee": -0.0514067888,
      "tax": -0.009253221984
    },
    {
      "price": 4.77,
      "numeratorSymbol": "USDT",
      "denominatorSymbol": "TRY",
      "orderType": "buy",
      "id": 5553442,
      "timestamp": 1533313928057,
      "amount": 2.09,
      "fee": -0.0152074094832,
      "tax": -0.002737333706976
    } ...
}
```

* **price**: Trade price
* **numeratorSymbol**: Trade pair numerator symbol
* **denominatorSymbol**: Trade pair denominator symbol
* **orderType**: Trade type (buy,sell)
* **id**: Trade id
* **timestamp**: Unix timestamp
* **amount**: Trade Amount (always negative if order type is sell)
* **fee**: Trade fee
* **tax**: Trade tax

 <code>POST</code> .../api/v1/users/transactions/crypto

 **Params**
 
* **type**: string array , {'deposit', 'withdrawal'}
* **symbol**: string array , {'btc', 'eth', 'xrp, ...etc.}
* **startDate**: long Optional timestamp if null will return last 30 days
* **endDate**: long Optional timestamp if null will return last 30 days

**Result:**
``` json
{
  "success": true,
  "message": "SUCCESS",
  "code": 0,
  "data": [
    {
      "balanceType": "Deposit",
      "currencySymbol": "XRP",
      "id": 676961,
      "timestamp": 1544079921450,
      "funds": 10.8,
      "amount": 0.9,
      "fee": 0,
      "tax": 0
    },
 
    {
      "balanceType": "Withdrawal",
      "currencySymbol": "XRP",
      "id": 516136,
      "timestamp": 1544079921450,
      "funds": 8.9,
      "amount": -1,
      "fee": 0,
      "tax": 0
    } ...
  ]
}
```

 <code>POST</code> .../api/v1/users/transactions/fiat
 
 **Params**
 
* **balanceTypes**: string array, {"deposit", "withdrawal"}
* **currencySymbols**: string array, {"try" ...etc.}
* **startDate**: long Optional timestamp if null will return last 30 days
* **endDate**: long Optional timestamp if null will return last 30 days

**Result:**
``` json
{
  "success": true,
  "message": "SUCCESS",
  "code": 0,
  "data": [
    {
      "balanceType": "Deposit",
      "currencySymbol": "TRY",
      "id": 93214,
      "timestamp": 1544079972687,
      "funds": 100000,
      "amount": 1000,
      "fee": 0,
      "tax": 0
    },
    {
      "balanceType": "Deposit",
      "currencySymbol": "TRY",
      "id": 919664,
      "timestamp": 1544079972687,
      "funds": 100000.04,
      "amount": 100000,
      "fee": 0,
      "tax": 0
    } ...
}
```
* **balanceType**: Type of transaction (deposit, withdrawal)
* **currencySymbol**: Transaction currency symbol 
* **id**: Transaction id
* **timestamp**: Unix timestamp
* **amount**: Transaction Amount
* **fee**: Transaction fee
* **tax**: Transaction tax


## Open Orders (Requires Authentication)

 <code>GET</code> .../api/v1/openOrders?pairSymbol=BTC_TRY
 
**Params:**
* **pairSymbol**: string optional, pair symbol if not set returns all pairs open orders
 
**Result:**
``` json
{
  "success": true,
  "message": null,
  "code": 0,
  "data": {
    "asks": [
      {
        "id": 9932533,
        "price": "21000.00",
        "amount": "0.001",
	"quantity": "0.001",
        "pairsymbol": "BTCTRY",
        "type": "Sell",
        "method": "Limit",
        "orderClientId": "test",
        "time": 1543994632920,
        "updateTime": 1543994632920,
        "status": "Untouched"
      }
    ],
    "bids": [
      {
        "id": 9932534,
        "price": "20000.00",
        "amount": "0.001",
	"quantity": "0.001",
        "pairsymbol": "BTCTRY",
        "type": "Buy",
        "method": "Limit",
        "orderClientId": "test",
        "time": 1543996112263,
        "updateTime": 1543996112263,
        "status": "Untouched"
      }
    ]
  }
}
```

* **id**: Order id
* **price**: Price of the order
* **amount**: Amount of the order
* **quantity**: quantity of the order
* **pairsymbol**: Pair of the order
* **pairSymbolNormalized**: Pair of the order with "_" in between.
* **type**: Type of order. Buy or Sell
* **method**: Method of order. Limit, Stop Limit
* **orderClientId**: Order client id created with (GUID if not set by user)
* **time**: Unix time the order was inserted at
* **updateTime**: Unix time last updated 
* **status**: Status of the order. Untouched (not matched), Partial (matched partially)


## Cancel Order (Requires Authentication)

 <code>DELETE</code> .../api/v1/order?id=1
 
**Params:**
* **id**: order ID

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

* **Result**: Success true if the order cancellation succeeded. False if it failed.

## Submit Order (Requires Authentication)

 <code>POST</code> .../api/v1/order
 
**Params:**

* **quantity**: "decimal", Mandatory for market or limit orders.
* **price**: "decimal", Price field will be ignored for market orders. Market orders get filled with different prices until your order is completely filled. There is a 5% limit on the difference between the first price and the last price. İ.e. you can't buy at a price more than 5% higher than the best sell at the time of order submission and you can't sell at a price less than 5% lower than the best buy at the time of order submission.
* **stopPrice**: "decimal", For stop orders
* **newOrderClientId**: "string", GUID if user did not set.
* **orderMethod**: "enum", "limit", "market" or "stoplimit"
* **orderType**: "enum", "buy", "sell"
* **pairSymbol**: "string", ex: "BTCTRY", "ETHTRY"


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

**Expected Errors:**

* **PARAMETERS_ERROR:** One of the giving parameters is not correct.
* **BALANCE_NOT_ENOUGH_WITHOUT_OPEN_ORDERS:** You do not have enough funds for this operation.
* **BALANCE_NOT_ENOUGH:** The available funds is not enough for this operation.
* **MIN_TOTAL:** The order quantity is less than the minimum required.
* **STOP_PRICE_GREATER_THAN_MARKET:** Stop buy price must be above current price.
* **STOP_PRICE_LESS_THAN_MARKET:** Stop sell price must be bellow current price.

## All Orders (Requires Authentication)

 <code>GET</code> .../api/v1/allOrders?pairSymbol=BTC_TRY

**Params:**

* **orderId**: integer optional, If orderId set, it will return all orders greater than or equals to this order id
* **pairSymbol**: pair symbol
* **startTime**: integer optional, start time
* **endTime**: integer optional, end time
* **page**: integer optional, page number
* **limit**: integer optional, default 100 max 1000

``` json
{
  "success": true,
  "message": null,
  "code": 0,
  "data": [
    {
      "id": 9932534,
      "price": "20000.00",
      "amount": "0.001",
      "quantity": "0.001",
      "pairsymbol": "BTCTRY",
      "pairsymbolnormalized":"BTC_TRY",
      "type": "Buy",
      "method": "Limit",
      "orderClientId": "test",
      "time": 1543996112263,
      "updateTime": 1543996112263,
      "status": "Untouched"
    },
    {
      "id": 9932533,
      "price": "21000.00",
      "amount": "0.001",
      "quantity": "0.001",
      "pairsymbol": "BTCTRY",
      "pairsymbolnormalized":"BTC_TRY",
      "type": "Buy",
      "method": "Limit",
      "orderClientId": "test",
      "time": 1543994632920,
      "updateTime": 1543994632920,
      "status": "Untouched"
    },
    {
      "id": 9932523,
      "price": "2000.00",
      "amount": "0.01",
      "quantity": "0.01",
      "pairsymbol": "BTCTRY",
      "pairsymbolnormalized":"BTC_TRY",
      "type": "Buy",
      "method": "Limit",
      "orderClientId": "test",
      "time": 1543500891493,
      "updateTime": 1543501769613,
      "status": "Canceled"
    }
  ]
}
```

* **id**: Order id
* **price**: Price of the order
* **amount**: Amount of the order
* **quantity**: quantity of the order
* **pairsymbol**: Pair of the order
* **pairSymbolNormalized**: Pair of the order with "_" in between.
* **type**: Type of order. Buy or Sell
* **method**: Method of order.
* **orderClientId**: Order client id created with (GUID if not set by user)
* **time**: Unix time the order was inserted at
* **updateTime**: Unix time last updated 
* **status**: Status of the order. Untouched, Matched partialy, Canceled
