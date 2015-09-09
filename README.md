# White label exchange API documentation
The documentation for BTCTrader's white label exchange platform API. Use this documentation to access the APIs of BTCTurk, BTCGreece, BTCExchange.PH and other BTCTrader partners.

## Usage
Append your exchange's URL to the beginning of the methods to use the API. For example, to get ticker info from BTCTurk, use [https://www.btcturk.com/api/ticker] (https://www.btcturk.com/api/ticker)

## Testing
You can use our testing platforms to test the APIs. The balances on the test sites are not real and do not represent any real value. The testing platforms work on Bitcoin TESTNET and you can deposit TESTNET coins to your account. The testing platforms are:

* [BTCTurk Test] (https://btctrader-broker-btcturk.azurewebsites.net/)
* [BTCGreece Test] (https://btctrader-broker-btcgreece.azurewebsites.net/)
* [BTCExchange Test] (http://btctrader-broker-btcph.azurewebsites.net/)

**Important** Our mobile applications will not work with the testing platforms. They can only be synced with our live platforms.

## Questions & Problems
Please use the [issues](https://github.com/BTCTrader/broker-api-docs/issues) on this github project to ask questions and report bugs.

## Request Limits

* .../api/ticker requests are limited to 10 requests per 100 miliseconds.
* Other requests are limited to 1 request per 100 miliseconds.
* If you make more than 50 consequent unauthorized requests your IP address will be blocked

## Sample API Clients

Here are some sample client implementations for our API: 

* [C# client] (https://github.com/BTCTrader/broker-api-csharp). A nuget package is also [available](https://www.nuget.org/packages/BTCTrader.APIClient/)
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
  "open":758.31,
  "average":765.25
}
```
* last: Last BTC price
* high: Highest trade price in the last 24 hours
* low: Lowest trade price in the last 24 hours
* volume: Total volume in the last 24 hours
* bid: Highest current bid
* ask: Lowest current ask
* open: Price of the opening trade on the current day
* average: Average Price in the last 24 hours

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

* date: Unix time of the trade (In the exchange's local timezone)
* tid: Trade ID
* rice: Price of the trade
* amount: Amount of the trade

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
* money_balance: Total money balance including open orders and pending withdrawal requests
* bitcoin_balance: Total bitcoin balance including open orders and pending withdrawal requests
* money_reserved: Money reserved in open orders
* bitcoin_reserved: Bitcoin reserved in open orders
* money_available: Money available for trading
* bitcoin_available: Bitcoin available for trading
* fee_percentage: Market taker fee percentage

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

* IsMarketOrder: 1 for market order, 0 for limit order
* Type: must be set as "BuyBtc"

For market orders:

* Price: Price field will be ignored for market orders. Market orders get filled with different prices until your order is completely filled. There is a 5% limit on the difference between the first price and the last price. İ.e. you can't buy at a price more than 5% higher than the best sell at the time of order submission
* Amount: Amount field will be ignored for buy market orders. The amount will be calculated according to the total value that you send.
* Total: The total amount you will spend with this order. You will buy from different prices until your order is filled as described above

For limit orders:

* Price: Order price
* Amount": Order amount
* Total: Will be ignored for limit orders.

**Result:**
``` json
{
  "id":"55c9d0783fbe186b4c08b831",
  "datetime":"2015-08-11T10:37:44.4786271Z",
  "type":"BuyBtc",
  "price":739.16,
  "amount":2.77891473
}
```

* The result is a JSON object containing your order details and order ID if the request succeeded.

## Sell Order (Requires Authentication)

 <code>POST</code> .../api/sell 
 
**Params:**

* IsMarketOrder: 1 for market order, 0 for limit order
* Type: must be set as "SelBtc"

For market orders:

* Price: Price field will be ignored. Market orders get filled with different prices until your order is completely filled. There is a 5% limit on the difference between the first price and the last price. İ.e. you can't sell at a price less than 5% lower than the best buy at the time of order submission
* Amount: The total amount you will sell with this order. You will sell at different prices until your order is filled as described above
* Total: Total field will be ignored. The total amount will depent on the amount value you send.

For limit orders:

* Price: Order price
* Amount": Order amount
* Total: Will be ignored for limit orders.

**Result:**
``` json
{
  "id":"55c9d0783fbe186b4c08b831",
  "datetime":"2015-08-11T10:37:44.4786271Z",
  "type":"SellBtc",
  "price":739.16,
  "amount":2.77891473
}
```

* The result is a JSON object containing your order details and order ID if the request succeeded.

## Get Money Deposit Info (Requires Authentication)

Use this method to check if the user has a pending local currency(USD, TRY, PHP, EUR etc.) deposit request.

<code>GET</code> .../api/FundingMoney/DepositMoney

**Result:** (null if the user does not have a deposit request)
``` json
{
  "id":"55db2279ba3d9d2f14b81f34",
  "deposit_code":"AVYM71",
  "banks":
    [
      {
        "bank_name":"YAPI VE KREDI BANKASI A.S.",
        "iban":"TR123456789"
      },
      {
        "bank_name":"T.IŞ BANKASI A.S.",
        "iban":"TRxxxxxxxxxxxxxxx"
      }
    ],
  "currency_type":"TL",
  "amount":14.0,
  "first_name":"Huseyin",
  "last_name":"Gurkan",
  "account_owner":"Bitcoin Trading Ltd."
}
```

* The result is a JSON object containing your request details and deposit code if the request succeeded.

## Deposit Money (Requires Authentication)

Use this method to submit a local currency(USD, TRY, PHP, EUR etc.) deposit request.

<code>POST</code> .../api/FundingMoney/DepositMoney

**Params:**

* Amount: Amount of money, integer places
* AmountPrecision: Amount of money, decimal places

**Result:**
``` json
{
  "id":"55db2279ba3d9d2f14b81f34",
  "deposit_code":"AVYM71",
  "banks":
    [
      {
        "bank_name":"YAPI VE KREDI BANKASI A.S.",
        "iban":"TR123456789"
      },
      {
        "bank_name":"T.IŞ BANKASI A.S.",
        "iban":"TRxxxxxxxxxxxxxxx"
      }
    ],
  "currency_type":"TL",
  "amount":14.0,
  "first_name":"Huseyin",
  "last_name":"Gurkan",
  "account_owner":"Bitcoin Trading Ltd."
}
```

* The result is a JSON object containing your request details and deposit code if the request succeeded.

## Get Money Withdrawal Info (Requires Authentication)

Use this method to check if the user has a pending local currency(USD, TRY, PHP, EUR etc.) withdrawal request.

<code>GET</code> .../api/FundingMoney/WithdrawalMoney

**Result:**
``` json
{
  "iban":null,
  "bank_list":[{"key":"ADABANK A.S.                       ","value":"51c41f6449ede8108423f015"},{"key":"AKBANK T.A.S.                      ","value":"51c41f6349ede8108423f00a"},{"key":"AKTIF YATIRIM BANKASI A.S.","value":"51c41f6649ede8108423f02a"},{"key":"ALBARAKA TURK KATILIM BANKASI A.S.","value":"51c41f6649ede8108423f02c"},{"key":"ALTERNATIFBANK A.S.","value":"51c41f6549ede8108423f020"},{"key":"ANADOLUBANK A.S.                   ","value":"51c41f6649ede8108423f025"},{"key":"ARAP TÜRK BANKASI A.S.             ","value":"51c41f6449ede8108423f010"},{"key":"ASYA KATILIM BANKASI A.S.","value":"51c41f6649ede8108423f02f"},{"key":"BANKPOZITIF KREDI VE KALK.BANK.A.S.","value":"51c41f6649ede8108423f029"},{"key":"BIRLESIK FON BANKASI A.S.","value":"51c41f6349ede8108423f008"},{"key":"BURGAN BANK A.S.","value":"51c41f6549ede8108423f021"},{"key":"CITIBANK A.S.","value":"51c41f6449ede8108423f011"},{"key":"DENIZ BANK A.S.","value":"51c41f6549ede8108423f024"},{"key":"DEUTSCHE BANK A.S.                 ","value":"51c41f6549ede8108423f01b"},{"key":"DİLER YATIRIM BANKASI A.S.         ","value":"51c41f6649ede8108423f026"},{"key":"FIBABANKA A.S.","value":"51c41f6449ede8108423f016"},{"key":"FINANSBANK A.S.                    ","value":"51c41f6549ede8108423f01a"},{"key":"GSD YATIRIM BANKASI A.S.","value":"51c41f6649ede8108423f027"},{"key":"HALK BANKASI                       ","value":"51c41f6349ede8108423f003"},{"key":"HSBC BANK A.S.                     ","value":"51c41f6549ede8108423f01f"},{"key":"İLLER BANKASI                      ","value":"51c41f6349ede8108423f001"},{"key":"ING BANK A.S.","value":"51c41f6449ede8108423f014"},{"key":"ISTANBUL TAKAS VE SAKLAMA BANK.A.S.","value":"51c41f6549ede8108423f023"},{"key":"JP MORGAN CHASE BANK NA MERK COL.OH","value":"51c41f6449ede8108423f013"},{"key":"KUVEYT TURK KATILIM BANKASI A.S.","value":"51c41f6649ede8108423f02d"},{"key":"MERKEZI KAYIT KURULUSU","value":"51c41f6649ede8108423f030"},{"key":"MERRILL LYNCH YATIRIM BANK A.S.","value":"51c41f6549ede8108423f022"},{"key":"NUROL YATIRIM BANKASI A.S.         ","value":"51c41f6649ede8108423f028"},{"key":"ODEA BANK A.S.","value":"51c41f6649ede8108423f02b"},{"key":"PORTIGON AG MER.DUSSELD.IST.MER.SB.","value":"51c41f6449ede8108423f017"},{"key":"POS","value":"51c41f6649ede8108423f031"},{"key":"ŞEKERBANK T.A.S.                   ","value":"51c41f6349ede8108423f00b"},{"key":"SOCIETE GENERALE                   ","value":"51c41f6549ede8108423f01e"},{"key":"STANDARD CHARTERED YATIRIM BANKASI","value":"51c41f6549ede8108423f01d"},{"key":"T.C.MERKEZ BANKASI A.Ş.","value":"51c41f6249ede8108423f000"},{"key":"T.EKONOMİ BANKASI A.S.             ","value":"51c41f6349ede8108423f009"},{"key":"T.GARANTİ BANKASI A.S.             ","value":"51c41f6349ede8108423f00c"},{"key":"T.IHRACAT KREDI BANKASI A.S.       ","value":"51c41f6349ede8108423f006"},{"key":"T.IŞ BANKASI A.S.","value":"51c41f6449ede8108423f00d"},{"key":"T.KALKINMA BANKASI A.S.            ","value":"51c41f6349ede8108423f007"},{"key":"T.SINAİ KALK. BANKASI A.S.         ","value":"51c41f6349ede8108423f004"},{"key":"T.VAKIFLAR BANKASI T.A.O.          ","value":"51c41f6349ede8108423f005"},{"key":"TAIB YATIRIMBANK A.S.              ","value":"51c41f6549ede8108423f01c"},{"key":"TC ZIRAAT BANKASI                  ","value":"51c41f6349ede8108423f002"},{"key":"TEKSTIL BANKASI A.S.               ","value":"51c41f6449ede8108423f019"},{"key":"THE ROYAL BANK OF SCOT.PLC MRK.EDIN","value":"51c41f6449ede8108423f00f"},{"key":"TURKISHBANK                        ","value":"51c41f6449ede8108423f012"},{"key":"TURKIYE FINANS KATILIM BANKASI A.S.","value":"51c41f6649ede8108423f02e"},{"key":"TURKLAND BANK A.S.","value":"51c41f6449ede8108423f018"},{"key":"YAPI VE KREDI BANKASI A.S.","value":"51c41f6449ede8108423f00e"}],
  "friendly_name_list":[{"key":"Test","value":"TR920011100000000049638020,AKBANK T.A.S.                      "}],
  "bank_name":null,
  "amount":0.0,
  "has_balance_request":false,
  "balance_request_id":null}
```

* The result is a JSON object containing your request details and has balance request return true if the request succeeded.
* Otherwise if there is no request, it is return to list of bank and friendly bank name list.

## Withdrawal Money (Requires Authentication)

Use this method to submit a local currency(USD, TRY, PHP, EUR etc.) withdrawal request.

<code>POST</code> .../api/FundingMoney/WithdrawalMoney

**Params:**

* Amount: Amount of money, integer places
* AmountPrecision: Amount of money, decimal places
* BankId: Selected Bank Id (bank_list)
* BankName: Selected Bank Name (bank_list)
* FriendlyNameSave: If you want to set a friendly record name for your bank account, set to true
* FriendlyName: Set a friendly record name for bank account if FriendlyNameSave is true, otherwise this is not required.
* Iban: IBAN number

**Result:**
``` json
{
  "iban":"TR920011100000000049638020",
  "bank_list":null,
  "friendly_name_list":null,
  "bank_name":"AKBANK T.A.S.                      ",
  "amount":15.0,
  "has_balance_request":true,
  "balance_request_id":"55dc62a7ba3d9d2978085bfa"
}
```

* The result is a JSON object containing your request details and balance request id if the request succeeded.

## Cancel Operation (Deposit/Withdrawal) (Requires Authentication)

<code>POST</code> .../api/FundingMoney/CancelOperation?balanceRequestId=55dc73adba3d9d32d8f8c56e

**Params:**

* balanceRequestId: For deposit, id field value / For withdrawal, balance_request_id field value

**Result:**
``` json
true
```

* The result is true if the request succeeded.
