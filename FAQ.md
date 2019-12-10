# White label exchange API Frequently Asked Questions
The documentation for frequently asked questions of BTCTrader's white label exchange platform API.

## Why do I receive 401 - Unauthorized error ?

You need to authorize before sending your request on all user related operations like buy/sell/cancel orders, user balances, open orders etc.

Please check and confirm that you've created the API key from PRO website. Also, check the API Key's permissions. If you don't have the necessary permissions, your requests will be unauthorized. Also if you had limited API with an IP address and your server has a different address, your API requests won't be authorized.

## Why do I receive Invalid nonce error ?

Nonce is a regular integer number. It must be Unix Timestamp in milliseconds format. We do have a 5 seconds tolerance in timestamps within the requests but your server’s current time must be synced with the  API server time which is in milliseconds format. Our servers are using UTC timezone.

You can check the server time from [here](https://api.btcturk.com/api/v2/server/time)

## Why do I receive 502 - Not enough balance error ?

For TRY pairs, you need to calculate the commission and deduct from the total amount. For example, if you would like to order 1.00000000 BTC for 50,000TRY, your free balance must be at least 50,090TRY (Maker/Taker commissions are being calculated after your order executed. If your order matches immediately you'll be charged as Taker. Otherwise, you'll be charged as Maker and the commission difference will be added to your balance)

## What is the difference between API versions and which one is suitable for me?

There are two versions v1 and v2.

V1 is used for all authentication required requests like checking balances, listing open orders, creating or canceling orders etc. with HMAC authentication.

V2 is used for all authentication required requests like checking balances, listing open orders, creating or canceling orders etc. with OAUTH2 authentication. V2 is also used for all public requests like ticker, OHLC (open, high, low, close) data, orderbook etc.

If you would like to request a data, related to your user account, you should use v1 with HMAC authentication or v2 with OAUTH2 authentication.

All public data is being served on v2.

## 401 Unauthorized - Invalid Signature

You might compute a wrong signature. 
You might not set the signature in the headers.
You might use this signature before so the time is changed and therefore you cannot use it anymore. 
Please check your public/private keys are correct. You may need to encode the key as UTF-8 in some languages.

## 401 Unauthorized - Invalid Public/Private key
Your public or private keys are missing.

## 401 Unauthorized - Unauthorized - Headers are missing
Your request does not have requested headers.

## 401 Unauthorized - Invalid Request

Your current account could be unverified yet you should contact support.
Your private key (API Secret) might be wrong. Please check your private key is correct. You may need to decode the key as base64 in some languages.

## 401 Unauthorized - Invalid Nonce

Nonce should not be different than the current timestamp we are giving a tolerance ±5 seconds.

Please check your server's timezone and timestamp output. It should be in milliseconds format and you need to declare your server's timezone to match UTC.

You can check the server time from [here](https://api.btcturk.com/api/v2/server/time)

## Have more questions?

If you have more questions please feel free to send your questions to [Issues](https://github.com/BTCTrader/broker-api-docs/issues) section
