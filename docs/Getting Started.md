#   Getting Started


## Production Environment

 ``` java
  Restful URL: [https://api.dfx.hk]
  WebSocket: [wss://api.dfx.hk]
  ```


##  General API Information

* All **responses** will return a JSON object or array.

* Data is returned in **ascending** order with the earlier data displayed first and updates appear later

* All time or timestamp-related variables are measured in milliseconds (ms)

* `HTTP 4XX` error code indicates that the content of the request is incorrect. This usually occurs on the originator's side

* `HTTP 5XX` indicates an internal system error. This signifies that the issue originates within the trading system. When addressing this error, it's important not to immediately categorise it as a failed task. The execution status is uncertain, it could potentially be either successful or unsuccessful.

* Request Parameters can be sent in any order.


###  API-KEY Management

* Users must log in to the exchange website and apply for an API key. Please ensure you remember the following information when creating an API key:

* **EXCHANGE-API-KEY**: API access key
* **Public Key**: The key used for signature authentication encryption, Uploaded by users themselves (visible to the application only)
* Users have to assign permissions to API-KEY. There are two kinds of permissions:

  * **READ** read permission is used for data query interfaces such as order query, transaction query, etc.
  * **TRADE** read-write permission is used for order placing, order cancelling, including transfer permission whereby user can transfer between subaccounts under the same main trading account
* Users need to set IP whitelist for API-KEY, only the IPs in the whitelist can call the API. Each API-KEY will be bound to maximum of 30 IPs.

* Both private REST and WebSocket modes require users to authenticate the transaction through the API-KEY passed in the API header. Refer to the following Authentication chapter for the signature algorithm of the API-KEY.


###  Access Restrictions

* If any rate limit is violated, a `429` error code will be returned

* Upon receiving `429` error code, please take precautionary steps to cease sending requests. API abuse is strictly prohibited

* It is recommended to use the WebSocket API to obtain corresponding real-time data as much as possible to reduce the traffic caused by frequent API requests.



## Public Rest API for exchange


## General API Information
* The base endpoint is: **https://api.dfx.hk**
* All endpoints return either a JSON object or array.

    Sample Payload below:
``` java
	{
  	"code": "0",
  	"msg": "success",
  	"data": {
            "id": "85",
            "price": "17",
            "qty": "0.1",
            "quoteQty": "1.7",
            "time": "1681873639235",
            "buyerMaker": false
  	}
	}
```
* Data is returned in **ascending** order. Oldest first, newest last.
* All time and timestamp related fields are in **milliseconds**.


## General Information on Endpoints

* For `GET` endpoints, parameters must be sent as a `query string`.
* For `POST`, `PUT`, and `DELETE` endpoints, the parameters may be sent as a `query string` or in the `request body` with content type  `application/x-www-form-urlencoded`. You may mix parameters between both the  `query string` and `request body` if you wish to do so.
* Parameters may be sent in any order.
* If a parameter sent in both the `query string` and `request body`, the `query string` parameter will be used.

## Public API Endpoints

### Terminology

These terms will be used throughout the documentation, so it is recommended especially for new users to read to help their understanding of the API.

* `base asset` refers to the asset that is the `quantity` of a symbol. For the symbol BTCUSDT, BTC would be the `base asset`.
* `quote asset` refers to the asset that is the `price` of a symbol. For the symbol BTCUSDT, USDT would be the `quote asset`.



### Kline/Candlestick chart intervals

| **VALUE** | **DESCRIPTION** |
| --- | --- |
|  1m| every minute|
|  3m| every 3 minutes|
|  5m| every 5 minutes|
|  15m| every 15 minutes|
|  30m| every 30 minutes|
|  1h| every 1 hour|
|  2h| every 2 hours|
|  4h| every 4 hours|
|  6h| every 6 hours|
|  8h| every 8 hours|
|  12h| every 12 hours|
|  1d| every 1 day|
|  1w| every 1 week|
|  1M| every 1 month|


### Order Status

| **VALUE** | **DESCRIPTION** |
| --- | --- |
|  PENDING_NEW| Conditional order waiting to be triggered. The order is not on the orderbook.|
|  NEW| New order, pending to be filled.|
|  PARTIAL_FILLED| Partially filled, the remaining part pending to be filled.|
|  FILLED| Completed filled. This is the final status of the order.|
|  CANCELED| Order cancelled. This is the final status of the order.|
|  REJECT| Order refers to the rejection of an order during the order creating or matching process. This is the final status of the order.|

### Order Types

| **VALUE** | **DESCRIPTION** |
| --- | --- |
|  LIMIT| limit order|
|  MARKET| market order|


### Order Side
| **VALUE** | **DESCRIPTION** |
| --- | --- |
|  BUY| Buy order|
|  SELL| Sell order|
