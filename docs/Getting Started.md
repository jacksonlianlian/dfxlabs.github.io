###   Getting Started

!!! example  "Domain Environment"

    === "Sandbox Environment"

        ``` markdown
        * Restful URL: [https://api.dfx-inc.com]
        * WebSocket: [wss://api.dfx-inc.com]
        ```

    === "Production Environment"

        ``` markdown
        * Restful URL: [https://api.dfx-inc.com]
        * WebSocket: [wss://api.dfx-inc.com]
        ```


###  General API Information

!!! tip ""

    * All **responses** will return a JSON object or array.

    * Data is returned in **ascending** order with the earlier data displayed first and updates appear later

    * All time or timestamp-related variables are measured in milliseconds (ms)

    * `HTTP 4XX` error code indicates that the content of the request is incorrect. This usually occurs on the originator's side

    * `HTTP 5XX` indicates an internal system error. This signifies that the issue originates within the trading system. When addressing this error, it's important not to immediately categorise it as a failed task. The execution status is uncertain, it could potentially be either successful or unsuccessful.

    * For **GET** endpoints, parameters must be sent as query strings.

    * Request Parameters can be sent in any order.

### General Information on Endpoints

!!! info ""

    * For `GET` endpoints, parameters must be sent as a `query string`.
    * For `POST`, `PUT`, and `DELETE` endpoints, the parameters may be sent as a `query string` or in the `request body` with content type  `application/x-www-form-urlencoded`. You may mix parameters between both the  `query string` and `request body` if you wish to do so.
    * Parameters may be sent in any order.
    * If a parameter sent in both the `query string` and `request body`, the`query string` parameter will be used.

###  Access Restrictions

!!! tip ""

    * The `rateLimits` array within **GET** `/api/v1/exchangeInfo` contains objects related to the`REQUEST_WEIGHT` of the transactions and `ORDERS` rate limits for trading. These are further defined in the enumeration defintion section under the limit types `rateLimitType`

    * If any rate limit is violated, a `429` error code will be returned

    * Each API endpoint has an associated with a specific weight, and certain endpoints may possess varying weights based on different parameters. Endpoints that consume more resources will have a higher weight assigned to them.

    * Upon receiving `429` error code, please take precautionary steps to cease sending requests. API abuse is strictly prohibited

    * It is recommended to use Websocket API to obtain corresponding real-time data as much as possible to reduce the traffic of access restrictions caused by frequent API requests.


###  Order Rate Limiting


!!! info ""

    * Unless specified, each API Key has a rate limit of 100 requests per minute for query related and order related endpoints have 20 requests per 2 seconds

    * When the number of orders exceeds the set limit, a response with an HTTP CODE 429 will be received. Please refer to the order rate limits (rateLimitType = ORDERS) in GET `/api/v1/exchangeInfo`, and wait for the suspended period to expire.

    * Order rate limits are based on each EXCHANGE-API-KEY and will not be associated with other API keys created within the same account.


###  Endpoint Security Types

!!! tip ""

    * Each endpoint is assigned a security type that determines how you interact with it.

    * The API-KEY must be passed in the REST API header as **EXCHANGE-API-KEY**.

    * EXCHANGE-API-KEY and EXCHANGE-API-SIGN are case-sensitive.

    * By default, API-KEY have access to all secure endpoints.


###  API-KEY Management

!!! info ""

    * Users have to log in the exchange website and apply for an API-KEY, please make sure to remember the following information when creating an API key:

    * **EXCHANGE-API-KEY**: API access key
    * **Public Key**: The key used for signature authentication encryption, Uploaded by users themselves (visible to the application only)
    * Users have to assign permissions to API-KEY. There are two kinds of permissions,

    * **READ** read permission is used for data query interfaces such as order query, transaction query, etc.
    *   **TRADE** read-write permission is used for order placing, order cancelling, including transfer permission whereby user can transfer between subaccounts under the same main trading account
    * Users can set IP whitelist for API-KEY. If user set IP for the API-KEY, only the IPs in the whitelist can call the API. Each API-KEY will be bound to a maximum of 10 IPs. If the user has multiple API-KEYs, they have to set IP whitelist for each API-KEY respectively.

    * Both private REST and WebSocket modes require users to authenticate the transaction through the API-KEY passed in the API header. Refer to the following Authentication chapter for the signature algorithm of the API-KEY.


# Public Rest API for exchange

!!! example ""
    ## General API Information
    * The base endpoint is: **https://xxx.com**
    * All endpoints return either a JSON object or array.

    Sample Payload below:
	```
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
!!! info ""
    * For `GET` endpoints, parameters must be sent as a `query string`.
    * For `POST`, `PUT`, and `DELETE` endpoints, the parameters may be sent as a `query string` or in the `request body` with content type  `application/x-www-form-urlencoded`. You may mix parameters between both the  `query string` and `request body` if you wish to do so.
    * Parameters may be sent in any order.
    * If a parameter sent in both the `query string` and `request body`, the `query string` parameter will be used.

# Public API Endpoints
!!! tip ""
    ### Terminology

    These terms will be used throughout the documentation, so it is recommended especially for new users to read to help their understanding of the API.

    * `base asset` refers to the asset that is the `quantity` of a symbol. For the symbol BTCUSDT, BTC would be the `base asset`.
    * `quote asset` refers to the asset that is the `price` of a symbol. For the symbol BTCUSDT, USDT would be the `quote asset`.



## Kline/Candlestick chart intervals


!!! note annotate "The meaning of k-line units"

	m -> minutes;
	h -> hours;
	d -> days;
	w -> weeks;
	M -> months;


!!! example "ENUM Definitions"

    === "Order Status"

        ``` markdown
        1. `OPEN` | The order has been accepted by the engine.
        2. `FINISHED` | The order has been completed.
        ```

	=== "Order Types"

        ``` markdown
        1. LIMIT
        2. MARKET
        ```

	=== "Order Side"

        ``` markdown
        1. BUY
        2. SELL
        ```

	=== "Kline Types"

        ``` markdown
        *. 1m
        *. 3m
        *. 5m
        *. 15m
        *. 30m
        *. 1h
        *. 2h
        *. 4h
        *. 6h
        *. 8h
        *. 12h
        *. 1d
        *. 3d
        *. 1w
        *. 1M
        ```
