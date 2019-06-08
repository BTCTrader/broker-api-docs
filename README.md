# White label exchange API documentation
The documentation for BTCTrader's white label exchange platform API.

These docs are for the APIs of BTCTurk and other BTCTrader partners.

## Usage
Append your exchange's URL to the beginning of the methods to use the API. For example, to get ticker info from BTCTurk, use [https://www.btcturk.com/api/ticker] (https://www.btcturk.com/api/ticker)

## Testing
You can use our testing platforms to test the APIs. The balances on the test sites are not real and do not represent any real value. The testing platforms work on Bitcoin TESTNET and you can deposit TESTNET coins to your account. The testing platforms are:

* [BTCTurk Test] (https://test.btcturk.com)

**Important** Our mobile applications will not work with the testing platforms. They can only be synced with our live platforms.

## Questions & Problems
Please use the [issues](https://github.com/BTCTrader/broker-api-docs/issues) on this github project to ask questions and report bugs.

## Request Limits

* .../api/ticker requests are limited to 10 requests per 100 miliseconds.
* Other requests are limited to 1 request per 100 miliseconds.
* If you make more than 50 consequent unauthorized requests your IP address will be blocked


## Ticker

<code>GET</code> .../api/ticker 

**Result:**
``` json
[
	{
	pair: "BTCTRY",
	high: 20950,
	last: 20659.95,
	timestamp: 1508242980,
	bid: 20556.51,
	volume: 142.95,
	low: 20500,
	ask: 20659.95,
	open: 20830,
	average: 20761.68
	},
	{
	pair: "ETHBTC",
	high: 0,
	last: 0,
	timestamp: 1508242980,
	bid: 0.06,
	volume: 0,
	low: 0,
	ask: 0.0635,
	open: 0,
	average: 0
	},
	{
	pair: "ETHTRY",
	high: 1274,
	last: 1227.99,
	timestamp: 1508242980,
	bid: 1210,
	volume: 757.85,
	low: 1210,
	ask: 1227.99,
	open: 1265.99,
	average: 1238.73
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

 <code>GET</code> .../api/orderbook?pairSymbol=BTCTRY 

**Result:**
``` json
{
  "timestamp":1439279819.0,
  "bids":[["761.68","1.17474867"],["761.67","0.23928215"]],
  "asks":[["767.48","0.12456411"],["767.49","4.07185043"]]
}
```
* **bids:** Array of current open bids on the orderbook.
* **asks:** Array of current open askss on the orderbook.

## Trades

 <code>GET</code> .../api/trades?pairSymbol=BTCTRY 

OR

 <code>GET</code> .../api/trades?pairSymbol=BTCTRY&last=COUNT (Max. value for count parameter is 50)

**Result:**
``` json

  [
    {
     "date": 1439280491.0,
     "tid": "55c9ad6b1ac4dc12b06131dc",
     "price":767.47,
     "amount":0.06486361
    },
    {
     "date": 1439280491.0,
     "tid": "55c9ad6b1ac4dc12b06131dc",
     "price":767.47,
     "amount":0.06486361
    } 
  ]
```

* date: Unix time of the trade (In the exchange's local timezone)
* tid: Trade ID
* rice: Price of the trade
* amount: Amount of the trade

## OHCL Data (Daily)

<code>GET</code> .../api/ohlcdata 

OR

<code>GET</code> .../api/ohlcdata?last=COUNT
 
**Result:**
``` json
[
    {
     "Date":"2016-05-18T00:00:00",
     "Open":1367.01,
     "High":1375.61,
     "Low":1362.61,
     "Close":1375.61,
     "Volume":125.17664557,
     "Average":1371.06,
     "DailyChangeAmount":0.63,
     "DailyChangePercentage":8.6
    },
    {
     "Date":"2016-05-17T00:00:00",
     "Open":1365.13,
     "High":1379.8,
     "Low":1365.01,
     "Close":1371.0,
     "Volume":541.47093778,
     "Average":1373.57,
     "DailyChangeAmount":0.43,
     "DailyChangePercentage":5.87
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

 <code>GET</code> .../api/balance 

**Result:**
``` json
{
    "try_balance": 1000,
    "btc_balance": 0.84053883,
    "eth_balance": 0.0660681,
    "try_reserved": 1003.54,
    "btc_reserved": 0,
    "eth_reserved": 0,
    "try_available": 1000,
    "btc_available": 0.84053883,
    "eth_available": 0.0660681,
    "btctry_taker_fee_percentage": 0.005,
    "btctry_maker_fee_percentage": 0.002,
    "ethtry_taker_fee_percentage": 0.005,
    "ethtry_maker_fee_percentage": 0.002,
    "ethbtc_taker_fee_percentage": 0.004,
    "ethbtc_maker_fee_percentage": 0.001
}
```
* try_balance: Total money balance including open orders and pending withdrawal requests
* btc_balance: Total bitcoin balance including open orders and pending withdrawal requests
* eth_balance: Total ethereum balance including open orders and pending withdrawal requests
* try_reserved: Money reserved in open orders
* btc_reserved: Bitcoin reserved in open orders
* eth_reserved: Ethereum reserved in open orders
* try_available: Money available for trading
* btc_available: Bitcoin available for trading
* eth_available: Ethereum available for trading
* btctry_taker_fee_percentage: Market taker fee percentage
* btctry_maker_fee_percentage: Market maker fee percentage
* ethtry_taker_fee_percentage: Market taker fee percentage
* ethtry_maker_fee_percentage: Market maker fee percentage
* ethbtc_taker_fee_percentage: Market taker fee percentage
* ethbtc_maker_fee_percentage: Market maker fee percentage


## User Transactions (Requires Authentication)

 <code>GET</code> .../api/userTransactions?offset=OFFSET&limit=LIMIT&sort=SORT

**Params:**

* offset: Skip that many transactions before beginning to return results. Default value is 0.
* limit: Limit result to that many transactions. Default value is 25.
* sort: Results are sorted by date and time. Provide "asc" for ascending results, "desc" for descending results. Default value is "desc".


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

 <code>GET</code> .../api/openOrders?pairSymbol=BTCTRY
 
**Result:**
``` json
[
  {
    "id":"123654",
    "datetime":"2015-07-28T04:43:00.271Z",
    "type":"SellBtc",
    "price":820.02,
    "amount":4.65915461,
	"PairSymbol" : "BTCTRY"
  },
  {
    "id":"123657",
    "datetime":"2015-07-30T10:01:39.619Z",
    "type":"BuyBtc",
    "price":790.61,
    "amount":10.42124175,
	"PairSymbol" : "BTCTRY"
  },
]
```

* id: Order id
* datetime: Date and time the order was inserted at
* type: Type of order. BuyBtc or SellBtc
* price: Price of the order
* amount: Bitcoin amount of the order

## Cancel Order (Requires Authentication)

 <code>POST</code> .../api/cancelOrder
 
**Params:**
* id: order ID

**Result:**
``` json
{
  "result":true
}
```

* result: True if the order cancellation succeeded. False if it failed.

## Buy Order (Requires Authentication)

 <code>POST</code> .../api/exchange
 
**Params:**

* OrderMethod: 1 for market order, 0 for limit order, 2 for Stop limit order
* OrderType: must be set as 0

For market orders:

* Price: Price field will be ignored for market orders. Market orders get filled with different prices until your order is completely filled. There is a 5% limit on the difference between the first price and the last price. İ.e. you can't buy at a price more than 5% higher than the best sell at the time of order submission
* PricePrecision : Precision of the price (.001)
* Amount: Amount field will be ignored for buy market orders. The amount will be calculated according to the total value that you send.
* AmountPrecision : Precision of the amount (.001)
* Total: The total amount you will spend with this order. You will buy from different prices until your order is filled as described above
* TotalPrecision : Precision of the Total (.001)
* TriggerPrice : For stop orders
* TriggerPricePrecision : Precision of the TriggerPrice (.001)
* PairSymbol : BTCTRY, ETHTRY 

**Result:**
``` json
{
  "id":"123753",
  "datetime":"2015-08-11T10:37:44.4786271Z",
  "type":"Buy",
  "price":739.16,
  "amount":2.77891473
}
```

* The result is a JSON object containing your order details and order ID if the request succeeded.

## Sell Order (Requires Authentication)

 <code>POST</code> .../api/exchange 
 
**Params:**

* OrderMethod: 1 for market order, 0 for limit order, 3 for Stop market order
* OrderType: must be set as 1

For market orders:

* Price: Price field will be ignored for market orders. Market orders get filled with different prices until your order is completely filled. There is a 5% limit on the difference between the first price and the last price. İ.e. you can't buy at a price more than 5% higher than the best sell at the time of order submission
* PricePrecision : Precision of the price (.001)
* Amount: Amount field will be ignored for buy market orders. The amount will be calculated according to the total value that you send.
* AmountPrecision : Precision of the amount (.001)
* Total: The total amount you will spend with this order. You will buy from different prices until your order is filled as described above
* TotalPrecision : Precision of the Total (.001)
* TriggerPrice : For stop orders
* TriggerPricePrecision : Precision of the TriggerPrice (.001)
* PairSymbol : BTCTRY, ETHTRY

**Result:**
``` json
{
  "id":"147852",
  "datetime":"2015-08-11T10:37:44.4786271Z",
  "type":"Sell",
  "price":739.16,
  "amount":2.77891473
}
```

* The result is a JSON object containing your order details and order ID if the request succeeded.

