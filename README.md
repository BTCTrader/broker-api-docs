# White label exchange API documentation
The documentation for BTCTrader's white label exchange platform API. Use this documentation to access the APIs of BTCTurk, BTCGreece, BTCExchange.PH and other BTCTrader partners.

## Usage
Append your exchange's URL to the beginning of the methods to use the API. For example, to get ticker info from BTCTurk, use [https://www.btcturk.com/api/ticker] (https://www.btcturk.com/api/ticker)

## Request Limits

* .../api/ticker requests are limited to 10 requests per 100 miliseconds.
* Other requests are limited to 1 request per 100 miliseconds.
* If you make more than 50 consequent unauthorized requests your IP will be blocked for 10 minutes

## Sample API Clients

Here are some sample client implementations for our API: 

**Important:** Please take a look at how the authorization is implemented. If you make too many unauthorized requests, your IP will be blocked.

## Ticker

<code>GET</code> .../api/ticker 

Returns JSON dictionary:

last - last BTC price
high - last 24 hours price high
low - last 24 hours price low
volume - last 24 hours volume
bid - highest buy order
ask - lowest sell order
open - opening trade of the current day
Order Book

 <code>GET</code> .../api/orderbook 
Returns JSON dictionary:

"timestamp": UNIXTIME
"bids": [
"price": PRICE
"amount": AMOUNT
]
"asks": [
"price": PRICE
"amount": AMOUNT
]
Trades

 <code>GET</code> .../api/trades 
OR

 <code>GET</code> .../api/trades?last=COUNT
Max. value for count parameter is 50. Returns JSON dictionary:

"date": UNIXTIME
"price": PRICE
"amount": AMOUNT
"tid": TRADE_ID
API Authentication

All API calls related to a specific account require authentication. You need to provide 3 parameters to authenticate a request:

"X-PCK": API key
"X-Stamp": Nonce
"X-Signature": Signature
API key

You can create the API key from the Account > API Access page

Nonce

Nonce is a regular integer number. It must be increasing with every request you make.

A common practice is to use unix time for that parameter.

Signature

Signature is a HMAC-SHA256 encoded message. The HMAC-SHA256 code must be generated using a private key that contains a timestamp and your API key

Example (C#):
string message = yourAPIKey + unixTimeStamp;
using (HMACSHA256 hmac = new HMACSHA256(Convert.FromBase64String( yourPrivateKey )))
{
byte[] signatureBytes = hmac.ComputeHash(Encoding.UTF8.GetBytes(message));
string X-Signature = Convert.ToBase64String(signatureBytes));
}
After creating the parameters, you have to send them in the HTML Header of your request with their name

Example (C#):
client.DefaultRequestHeaders.Add("X-PCK", yourAPIKey);
client.DefaultRequestHeaders.Add("X-Stamp", stamp.ToString());
client.DefaultRequestHeaders.Add("X-Signature", signature);
Warning: Your IP address can be blocked if you make too many unauthorized requests. Make sure you implement the authentication method properly.

Account Balance (Requires Authentication)

 <code>GET</code> .../api/balance 
Returns JSON dictionary:

"money_balance": TL balance
"bitcoin_balance": BTC balance
"money_reserved": TL reserved in open orders
"bitcoin_reserved": BTC reserved in open orders
"money_available": TL available for trading
"bitcoin_available": BTC available for trading
"fee_percentage": Fee Percentage
User Transactions (Requires Authentication)

 <code>GET</code> .../api/userTransactions 
Params:

"offset": Skip that many transactions before beginning to return results.(mandatory)
"limit": Limit result to that many transactions.(mandatory)
"sort": Sorting by date and time (asc - ascending; desc - descending).(mandatory)
Returns JSON dictionary:

"id": Transaction id
"date": Date and time
"operation": Name of operation (sell,buy,deposit,commission,withdrawal)
"btc": BTC amount
"currency": Currency amount
"price": The price of the trade. Only for buy and sell transactions
Open Orders (Requires Authentication)

 <code>GET</code> .../api/openOrders 
Returns JSON dictionary:

"id": Order id
"datetime": Date and time
"type": Type of order. BuyBtc or SellBtc
"price": Per BTC price
"amount": BTC amount
Cancel Order (Requires Authentication)

 <code>POST</code> .../api/cancelOrder 
Params:

"id": order ID
Returns Json dictionary:

"result": result of the cancellation (boolean)
Buy Order (Requires Authentication)

 <code>POST</code> .../api/buy 
Params:

"IsMarketOrder": 1 for market order, 0 for limit order
If order is market order

"Price": Price field will be ignored. Market orders get filled with different prices until your order is completely filled. There is a 5% limit on the difference between the first price and the last price. İ.e. you can't buy at a price more than 5% higher than the best sell at the time of order submission
"Amount": Amount field will be ignored. Amount will be computed with market price and total money
"Total": The total amount you will spend with this order. You will buy from different prices until your order is filled as described above
If order is limit order

"Price": price of per BTC
"Amount": Amount BTC what you want to buy
"Total": Total field will be ignored
Sell Order (Requires Authentication)

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
