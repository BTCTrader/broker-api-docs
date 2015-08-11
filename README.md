# White label exchange API documentation
The documentation for BTCTrader's white label exchange platform API. Use this documentation to access the APIs of BTCTurk, BTCGreece, BTCExchange.PH and other BTCTrader partners.

## Usage
Append your exchange's URL to the beginning of the methods to use the API. For example, to get ticker info from BTCTurk, use [https://www.btcturk.com/api/ticker] (https://www.btcturk.com/api/ticker)

## Testing
You can use our testing platforms to test the APIs. The balances on the test sites are not real and do not represent any real value. The testing platforms work on Bitcoin TESTNET and you can deposit TESTNET coins to your account. The testing platforms are:

* [BTCTurk Test] (https://btctrader-broker-btcturk.azurewebsites.net/)
* [BTCGreece Test] (https://btctrader-broker-btcgreece.azurewebsites.net/)
* [BTCExchange Test] (http://btctrader-broker-btcph.azurewebsites.net/)

## Questions & Problems
Please use the issues on this github project to ask questions and report bugs.

## Request Limits

* .../api/ticker requests are limited to 10 requests per 100 miliseconds.
* Other requests are limited to 1 request per 100 miliseconds.
* If you make more than 50 consequent unauthorized requests your IP will be blocked for 10 minutes

## Sample API Clients

Here are some sample client implementations for our API: 

* [C# client] (https://github.com/BTCTrader/broker-api-csharp) 
* [Objective C client] (https://github.com/BTCTrader/broker-api-objectivec) 

**Important:** Please take a look at how the authorization is implemented in the samples. If you make too many unauthorized requests, your IP will be blocked.

## Ticker

<code>GET</code> .../api/ticker 

**Result:**
``` json
{
  "high":771.08,
  "last":767.49,
  "timestamp":1439279026.0,
  "bid":762.48,
  "volume":182.92,
  "low":752.00,
  "ask":767.49,
  "open":758.31
}
```
* **last:** Last BTC price
* **high:** Highest trade price in the last 24 hours
* **low:** Lowest trade price in the last 24 hours
* **volume:** Total volume in the last 24 hours
* **bid:** Highest current bid
* **ask:** Lowest current ask
* **open:** Price of the opening trade on the current day

## Order Book

 <code>GET</code> .../api/orderbook 

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

 <code>GET</code> .../api/trades 

OR

 <code>GET</code> .../api/trades?last=COUNT (Max. value for count parameter is 50)

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

* **date:** Unix time of the trade (In the exchange's local timezone)
* **tid:** Trade ID
* **price:** Price of the trade
* **amount:** Amount of the trade

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

A common practice is to use unix time for that parameter.

#### Signature

Signature is a HMAC-SHA256 encoded message. The HMAC-SHA256 code must be generated using a private key that contains a timestamp and your API key

Example (C#):
```c#
string message = yourAPIKey + unixTimeStamp;
using (HMACSHA256 hmac = new HMACSHA256(Convert.FromBase64String( yourPrivateKey )))
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

 <code>GET</code> .../api/balance 

**Result:**
``` json
{
  "money_balance": 1000.00,
  "bitcoin_balance": 1.00000000,
  "money_reserved": 250.00,
  "bitcoin_reserved": 0.25000000,
  "money_available": 750.00,
  "bitcoin_available": 0.75000000,
  "fee_percentage": 0.2
}
```
* **money_balance**: Total money balance including open orders and pending withdrawal requests
* **bitcoin_balance**: Total bitcoin balance including open orders and pending withdrawal requests
* **money_reserved**: Money reserved in open orders
* **bitcoin_reserved**: Bitcoin reserved in open orders
* **money_available**: Money available for trading
* **bitcoin_available**: Bitcoin available for trading
* **fee_percentage**: Market taker fee percentage

## User Transactions (Requires Authentication)

 <code>GET</code> .../api/userTransactions?offset=OFFSET&limit=LIMIT&sort=SORT

**Params:**

* offset: Skip that many transactions before beginning to return results.
* limit: Limit result to that many transactions.
* sort: Results are sorted by date and time. Provide "asc" for ascending results, "desc" for descending results.

***Important*** All three parameters are required.

**Result:**
``` json
[
  {
    "id":"55c9b4ea3fbe186b4c089d09",
    "date":"2015-08-11T11:40:17.278",
    "operation":"buy",
    "btc":1.9449023,
    "currency":-1428.57,
    "price":734.52
  },
  {
    "id":"55c9b4ea3fbe186b4c089d0a",
    "date":"2015-08-11T11:40:17.325",
    "operation":"commission",
    "btc":0.0,
    "currency":0.0,
    "price":0.0
  },
  {
    "id":"55c9b5d33fbe186b4c089dd9",
    "date":"2015-08-11T11:44:10.162",
    "operation":"sell",
    "btc":-2.18674928,
    "currency":1613.18,
    "price":737.71
  },
  {
    "id":"55c9b5d33fbe186b4c089dda",
    "date":"2015-08-11T11:44:10.209",
    "operation":"commission",
    "btc":0.0,
    "currency":0.0,
    "price":0.0
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

 <code>GET</code> .../api/openOrders
 
**Result:**
``` json
[
  {
    "id":"55b708549c8d054130d80d71",
    "datetime":"2015-07-28T04:43:00.271Z",
    "type":"SellBtc",
    "price":820.02,
    "amount":4.65915461
  },
  {
    "id":"55b9f6039c8d0530ac9926dd",
    "datetime":"2015-07-30T10:01:39.619Z",
    "type":"BuyBtc",
    "price":790.61,
    "amount":10.42124175
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

 <code>POST</code> .../api/buy
 
**Params:**

"IsMarketOrder": 1 for market order, 0 for limit order
If order is market order

"Price": Price field will be ignored. Market orders get filled with different prices until your order is completely filled. There is a 5% limit on the difference between the first price and the last price. İ.e. you can't buy at a price more than 5% higher than the best sell at the time of order submission
"Amount": Amount field will be ignored. Amount will be computed with market price and total money
"Total": The total amount you will spend with this order. You will buy from different prices until your order is filled as described above
If order is limit order

"Price": price of per BTC
"Amount": Amount BTC what you want to buy
"Total": Total field will be ignored

## Sell Order (Requires Authentication)

 <code>POST</code> .../api/sell 
Params:

"IsMarketOrder": 1 for market order, 0 for limit order
If order is market order.

"Price": Price field will be ignored. Market orders get filled with different prices until your order is completely filled. There is a 5% limit on the difference between the first price and the last price. İ.e. you can't sell at a price less than 5% lower than the best buy at the time of order submission
"Amount": The total amount you will sell with this order. You will sell at different prices until your order is filled as described above
"Total": Total field will be ignored
If order is limit order.

"Price": price of per BTC
"Amount": Amount BTC what you want to sell
"Total": Total field will be ignored
