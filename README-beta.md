# White label exchange API documentation
The documentation for BTCTrader's white label exchange platform API.

These docs are for the APIs of BTCTurk and other BTCTrader partners.

## Usage
Append your exchange's URL to the beginning of the methods to use the API. For example, to get ticker info from BTCTurk, use [https://api.btcturk.com/api/v2/ticker] (https://api.btcturk.com/api/v2/ticker)

## Testing
You can use our testing platforms to test the APIs. The balances on the test sites are not real and do not represent any real value. The testing platforms work on TESTNET and you can deposit TESTNET coins to your account. The testing platforms are:

* [BTCTurk Test] (https://broker-api-dev.azurewebsites.net/)

**Important** Our mobile applications will not work with the testing platforms. They can only be synced with our live platforms.

## Questions & Problems
Please use the [issues](https://github.com/BTCTrader/broker-api-docs/issues) on this github project to ask questions and report bugs.

## General Information

* For GET endpoints, parameters must be sent as a query string.
* For POST, PUT, and DELETE endpoints, the parameters should be sent as a request body with content type application/x-www-form-urlencoded. 
* All timestamp parameters are in milliseconds.
* All endpoints successful requests return JSON object model 
``` json
  "success": true,
  "message": string,
  "code": int,
  "data": []
```
* The "data" parameter including the response data information, could be null if the request is DELETE, just check the "success" parameter.
* All public endpoints could be reached in V2.
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

## Request Limits

* .../api/v2/ticker requests are limited to 10 requests per 100 miliseconds.
* Other requests are limited to 1 request per 100 miliseconds.
* If you make more than 50 consequent unauthorized requests your IP address will be blocked


## Ticker

* If pairSymbol is not set, ticker for all pairs will be returned in a json array.

<code>GET</code> .../api/v2/ticker 

OR

<code>GET</code> .../api/v2/ticker?pairSymbol=""

OR

<code>GET</code> .../api/v2/ticker/currency?symbol=""

**Result:**
``` json
{
  "success": true,
  "message": "",
  "code": 0,
  "data": [
    {
      "pair": "BTCTRY",
      "timestamp": 1543828434676,
      "last": 20800,
      "high": 22000,
      "low": 20525,
      "bid": 20792,
      "ask": 20800,
      "open": 21849,
      "volume": 273.658077,
      "average": 21188.51,
      "daily": 0,
      "dailyPercent": 0,
      "denominatorSymbol": "TRY",
      "numeratorSymbol": "BTC"
    },
    {
      "pair": "ETHTRY",
      "timestamp": 1543828434678,
      "last": 590,
      "high": 618,
      "low": 580,
      "bid": 590,
      "ask": 593,
      "open": 613,
      "volume": 2466.148129,
      "average": 598.79,
      "daily": 0,
      "dailyPercent": 0,
      "denominatorSymbol": "TRY",
      "numeratorSymbol": "ETH"
    }
	...
  ]
}
```
* **pair**: Pair symbol
* **timestamp**: Current Unix time in milliseconds
* **last**: Last BTC price
* **high**: Highest trade price in the last 24 hours
* **low**: Lowest trade price in the last 24 hours
* **bid**: Highest current bid
* **ask**: Lowest current ask
* **open**: Price of the opening trade in the last 24 hours
* **volume**: Total volume in the last 24 hours
* **average**: Average Price in the last 24 hours
* **daily**: Price change in the last 24 hours
* **dailyPercent**: Price change percent in the last 24 hours
* **denominatorSymbol**: Denominator currency symbol of the pair 
* **numeratorSymbol**: Numerator currency symbol of the pair

## Ticker Orders:
* Current total open orders for each pair

<code>GET</code> .../api/v2/ticker/orders

**Result:**
``` json
{
  "success": true,
  "message": "",
  "code": 0,
  "data": [
    {
      "pairSymbol": "BTCTRY",
      "bidsCount": 588,
      "totalBidsPrice": "14372349.44",
      "totalBidsAmount": "2241.447684423",
      "asksCount": 1958,
      "totalAsksPrice": "12491962231.87",
      "totalAsksAmount": "281.98567826"
    },
    {
      "symbol": "ETHTRY",
      "bidsCount": 471,
      "totalBidsPrice": "848020.43",
      "totalBidsAmount": "1743.97498774",
      "asksCount": 2242,
      "totalAsksPrice": "1022121574153.97",
      "totalAsksAmount": "4573.46976244"
    },...
}
```
* **pair**: Pair symbol
* **bidsCount**: Count of bid orders
* **totalBidsPrice**: Sum of bid orders prices
* **totalBidsAmount**: Sum of bid orders amount
* **asksCount**: Count of ask orders
* **totalAsdsPrice**: Sum of ask orders prices
* **totalAsksAmount**: Sum of ask orders amount

## Order Book

 <code>GET</code> .../api/v2/orderbook?pairSymbol="" 
 
 OR
 
  <code>GET</code> .../api/v2/orderbook?pairSymbol=""&limit=100


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
 
 <code>GET</code> .../api/v2/trades?pairSymbol=""

OR

 <code>GET</code> .../api/v2/trades?pairSymbol=""?last=COUNT (Max. value for count parameter is 50)

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
      "numerator": BTC,
      "denominator": TRY,
      "date": 1533650242300,
      "tid": "636692470417865271",
      "price": "33490",
      "amount": "0.00032747"
    },
    {
      "pair": BTCTRY,
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
* **pair**: Requested pair sybmol
* **numerator**: Numerator currency for the requested pair
* **denominator**: Denominator currency for the requested pair
* **date**: Unix time of the trade in milliseconds
* **tid**: Trade ID
* **price**: Price of the trade
* **amount**: Amount of the trade

## OHCL Data (Daily)

<code>GET</code> .../api/v2/ohlc?pairSymbol=""

OR

<code>GET</code> .../api/ohlc?pairSymbol=""&last=COUNT

**Parameters:**
 * **pairSymbol**: string Mandatory
 * **last**: int Optional 
 
**Result:**
``` json
{
  "success": true,
  "message": null,
  "code": 0,
  "data": [
    {
      "pairSymbol": "BTCTRY",
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
  ]
}
```
* **pair**: Requested pair sybmol
* **time**: Unix time 
* **open**: Price of the opening trade on the time
* **high**: Highest trade price on the time
* **low**: Lowest trade price on the time
* **close**: Price of the closing trade on the time
* **volume**: Total volume on the time
* **average**: Average price on the time
* **DailyChangeAmount**: Amount of difference between Close and Open on the Date
* **DailyChangePercentage**: Percentage of difference between Close and Open on the Date

## API Authentication V1

All API calls related to a user account require authentication.

You need to provide 3 parameters to authenticate a request:

* "X-PCK": API key
* "X-Stamp": Nonce
* "X-Signature": Signature

#### API key

You can create the API key from the Account > API Access page in your exchange account.

#### Nonce

Nonce is a regular integer number. It must be increasing with every request you make.

A common practice is to use unix time for that parameter.

#### Signature

Signature is a HMAC-SHA256 encoded message. The HMAC-SHA256 code must be generated using a private key that contains a timestamp and your API key

Example (C#):
```c#
string message = yourAPIKey + unixTimeStamp;
using (HMACSHA256 hmac = new HMACSHA256(Convert.FromBase64String(yourPrivateKey)))
{
   byte[] signatureBytes = hmac.ComputeHash(Encoding.UTF8.GetBytes(message));
   string X-Signature = Convert.ToBase64String(signatureBytes));
}
```
After creating the parameters, you have to send them in the HTML Header of your request with their name

Example (C#):
```c#
client.DefaultRequestHeaders.Add("X-PCK", yourAPIKey);
client.DefaultRequestHeaders.Add("X-Stamp", stamp.ToString());
client.DefaultRequestHeaders.Add("X-Signature", signature);
```

Warning: Your IP address can be blocked if you make too many unauthorized requests. Make sure you implement the authentication method properly.

## Account Balance (Requires Authentication)

 <code>GET</code> .../api/v1/users/balances

**Result:**
``` json
{
  "success": true,
  "message": "string",
  "code": "dbError",
  "data": [
    {
      "asset": "string",
      "assetname": "string",
      "balance": "string",
      "locked": "string",
      "free": "string"
    }
  ]
}
```
* **asset**: Asset symbol
* **assetname**: Asset name
* **balance**: Total asset balance including open orders and pending withdrawal requests
* **locked**: Asset locked amount in open orders and withdrawal requests
* **free**: Asset available amount for trading


## User Transactions (Requires Authentication)

 <code>POST</code> .../api/v1/users/transactions/trade

 **Params**
 
* **orderTypes**: string [] , 'buy', 'sell'
* **currencySymbols**: string [] , 'btc', 'try', ...etc.
* **startDate**: long Optional
* **endDate**: long Optional

 OR

 <code>POST</code> .../api/v1/users/transactions/crypto

 **Params**
 
* **balanceTypes**: string [] , 'deposit', 'withdrawal'
* **currencySymbols**: string [] , 'btc', 'eth', ...etc.
* **startDate**: long Optional
* **endDate**: long Optional

 OR
 
 <code>POST</code> .../api/v1/users/transactions/fiat
 
 **Params**
 
* **balanceTypes**: string [] , 'deposit', 'withdrawal'
* **currencySymbols**: string [] , 'try' ...etc.
* **startDate**: long Optional
* **endDate**: long Optional

**Result:**
``` json
[
  {
    "id":"123456",
    "date":"2015-08-11T11:40:17.278",
    "operation":"buy",
    "amount":1.9449023,
    "currency":"TRY",
    "price":734.52,
	"funds":10,
	"fee" : 0.005,
	"tax" : 0.18
  },
  {
    "id":"123456",
    "date":"2015-08-11T11:40:17.325",
    "operation":"commission",
    "amount":0.0,
    "currency":"TRY",
    "price":0.0,
	"funds":10,
	"fee" : 0.005,
	"tax" : 0.18
  },
  {
    "id":"123456",
    "date":"2015-08-11T11:44:10.162",
    "operation":"sell",
    "amount":-2.18674928,
    "currency":"BTC",
    "price":737.71,
	"funds":10,
	"fee" : 0.005,
	"tax" : 0.18
  },
  {
    "id":"123456",
    "date":"2015-08-11T11:44:10.209",
    "operation":"commission",
    "amount":0.0,
    "currency":"BTC",
    "price":0.0,
	"funds":10,
	"fee" : 0.005,
	"tax" : 0.18
  }
]
```

* id: Transaction id
* date: Date and time
* operation: Type of transaction (sell,buy,deposit,commission,withdrawal)
* btc: Bitcoin amount of transaction
* currency: Money amount of transaction
* price: The price of the trade if the transaction is a buy or sell

## Open Orders (Requires Authentication)

 <code>GET</code> .../api/v1/openOrders?pairSymbol=""
 
**Params:**
* **pairsymbol**: string optional, pair symbol if not set returns all pairs open orders
 
**Result:**
``` json
{
  "success": true,
  "message": "string",
  "code": "",
  "data": {
    "asks": [
      {
        "id": 0,
        "price": "string",
        "amount": "string",
        "pairsymbol": "string",
        "type": "string",
        "method": "string",
        "orderClientId": "string",
        "time": 0,
        "updateTime": 0,
        "status": "string"
      }
    ],
    "bids": [
      {
        "id": 0,
        "price": "string",
        "amount": "string",
        "pairsymbol": "string",
        "type": "string",
        "method": "string",
        "orderClientId": "string",
        "time": 0,
        "updateTime": 0,
        "status": "string"
      }
    ]
  }
}
```

* **id**: Order id
* **price**: Price of the order
* **amount**: Amount of the order
* **pairsymbol**: Pair of the order
* **type**: Type of order. Buy or Sell
* **method**: Method of order. Limit, Stop Limit
* **orderClientId**: Order client id created with (GUID if not set by user)
* **time**: Unix time the order was inserted at
* **updateTime**: Unix time last updated 
* **status**: Status of the order. Untouched, Matched partialy


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

* **result**: Success true if the order cancellation succeeded. False if it failed.

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
  "message": "string",
  "code": "",
  "data": {
    "id": 0,
    "datetime": 0,
    "type": "string",
    "method": "string",
    "price": "string",
    "amount": "string",
    "pairSymbol": "string",
    "newOrderClientId": "string"
  }
}
```

* The result is a JSON object containing your order details and order ID if the request succeeded.

## All Orders (Requires Authentication)

 <code>GET</code> .../api/v1/allOrders

**Params:**

* **orderId**: integer optional, If orderId set, it will return all orders greater than or equals to this order id
* **pairSymbol**: pair symbol
* **startTime**: integer optional, start time
* **endTime**: integer optional, end time
* **page**: integer optional, page number
* **limit**: integer optional, default 100 max 1000