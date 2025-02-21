Order Management
===========




# 1、 Create Order
## POST `/api/v1/spot/order`




| **PARAMETER** | **TYPE** | **Mandatory** | **DESCRIPTION** |
| --- | --- | --- | --- |
| symbol | STRING | YES | Trading pair |
| accountId | STRING | YES | user's accountId |
| side | STRING | YES | order direction. eg: `BUY` or `SELL` |
| type | STRING | YES | orderType eg:`LIMIT`. `MARKET`. |
| price | BIGDECIMAL | NO | Order Price |
| quantity | BIGDECIMAL | NO | Order Quantity |
| amount | BIGDECIMAL | NO | Order Amount |
| clientOrderId | STRING | NO | Order id pass by api user |


**The following example will demonstrate how to create a limit order**
``` java
{
	"symbol": "BTC-USDT",
	"accountId": "1403134775",
	"side": "BUY"
	"type": "LIMIT",
	"price": 66666.88,
	"quantity": 0.01
}

```
**And the following example will demonstrate how to create a market order**
``` java
{
	"symbol": "BTC-USDT",
	"accountId": "1403134775",
	"side": "BUY"
	"type": "MARKET",
	"amount": 100.00
}

```

**If the order is created successfully, the server will return the following example, The returned data content is orderId.**

``` java
{
	"code": "0",
	"msg": "success",
	"data": "2000000074758463"
}

```



# 2、 Cancel a single order.
## DELETE `/api/v1/spot/order`

| **PARAMETER** | **TYPE** | **Mandatory** | **DESCRIPTION** |
| --- | --- | --- | --- |
| accountId | STRING | YES | user's accountId |
| orderId    | LONG    | NO | Order Id|
| clientOrderId | STRING | NO | Order id pass by api user |

```javascript
The parameters orderId and clientOrderId require at least one to be selected.
The single cancel order interface currently supports 3 parameters, among which accountId is mandatory.
If either orderId or clientOrderId is to be chosen, the parameter rules are as follows:

1. Cancellation is prioritized based on orderId. If both orderId and clientOrderId are provided,
the order will be cancelled based only on the orderId.
```

``` markdown
{
	"code": "0",
	"msg": "success",
	"orderId": "2000000074758463"
}

```


# 3、 Batch Cancel Order.
DELETE `/api/v1/spot/batch/order`


| **PARAMETER** | **TYPE** | **Mandatory** | **DESCRIPTION** |
| --- | --- | --- | --- |
| accountId | STRING | YES | user's accountId |
| symbol    | STRING    | NO | symbol|
| orderIds    | STRING    | NO | Multiple order numbers, separated by commas. eg:2000000074758463,2000000074758464|


```javascript
Supports cancelling orders in batches, either based on symbol or specific orderIds specified by the user. If a symbol
is specified, all orders for that symbol will be canceled. If order IDs are specified, up to 5 orders can be canceled
at once.At least one of the two parameters, symbol or orderId, must be specified. If both parameters are specified,
orders will be canceled based on the orderId first.

The batch cancel order interface currently supports 3 parameters, with accountId being mandatory. Either orderIds
or symbol must be provided. If both symbol and orderIds are specified, cancellation will only proceed based on orderIds.
 The order of operations is as follows:

1. If both orderIds and symbol are provided, the orders will be cancelled based solely on the orderIds.
```


``` java
{
	"code": "0",
	"msg": "success",
	"data": [
		{
			"msg": "success",
			"code": "0",
			"orderId": "2000000074758463,2000000074758464"//Successfully canceled OrderIds
		}
	],
	"success": true
}

```



# 4、 Get Single User Order.
## GET `/api/v1/spot/order`


| **PARAMETER** | **TYPE** | **Mandatory** | **DESCRIPTION** |
| --- | --- | --- | --- |
| accountId | STRING | YES | user's accountId |
| orderId    | LONG    | NO | Order Id|
| clientOrderId | STRING | NO | Order id pass by api user |

```
The get User order list interface currently supports 3 parameters, with accountId being mandatory. Either orderId
or clientOrderId must be provided. If both clientOrderId and orderId are specified, The get User order will only proceed based on orderId.
 The order of operations is as follows:

1. If both orderId and clientOrderId are provided, the order will be searched based solely on the orderId.
```

``` java
{
	"code": "0",
	"msg": "success",
	"data": {
		"clientOid": " ",
		"orderId": 2000000074758463,
		"accountId": "2",
		"side": "BUY",
		"symbol": "BTC-USDT",
		"price": "69000",
		"quantity": "0.001",
		"amount": "69",
		"status": "NEW",
		"tradeQty": "0",
		"tradeAmount": "0",
		"orderType": "LIMIT",
		"stopStatus": "PENDING",
		"failedReason": " "
	}
}

```



# 5、 Get completed orders information.
## GET `/api/v1/spot/finish_order`


| **PARAMETER** | **TYPE** | **Mandatory** | **DESCRIPTION** |
| --- | --- | --- | --- |
| accountId | STRING | YES | user's accountId |
| symbol | STRING | NO | Trading pair |
| orderId | LONG | NO | order's id|
| limit | INTEGER | NO | The number of items returned per query is 10 by default, with a maximum limit of 500 items.|
| startTime    | LONG    | NO | StartTime in milliseconds ,eg:1678772870000。Based on order creation time.
| endTime    | LONG    | NO | endTime in milliseconds ,eg:1678772870000。Based on order creation time.





``` java
{
	"code": "0",
	"msg": "success",
	"data": [{
		"clientOid": " ",
		"orderId": 2000000074758461,
		"accountId": "2",
		"side": "BUY",
		"symbol": "BTC-USDT",
		"price": "70745.48",
		"quantity": "0.0002",
		"amount": "14.149096",
		"status": "FILLED",
		"tradeQty": "0.0002",
		"tradeAmount": "14.149096",
		"orderType": "LIMIT",
		"stopType": "UP",
		"stopStatus": "PENDING",
		"averagePrice": "70745.48",
		"failedReason": " "
	}, {
		"clientOid": " ",
		"orderId": 2000000074758404,
		"accountId": "2",
		"side": "BUY",
		"symbol": "BTC-USDT",
		"price": "70799.48",
		"quantity": "0.00075",
		"amount": "53.09961",
		"status": "FILLED",
		"tradeQty": "0.00075",
		"tradeAmount": "53.09961",
		"orderType": "LIMIT",
		"stopType": "UP",
		"stopStatus": "PENDING",
		"averagePrice": "70799.48",
		"failedReason": " "
	}]
}

```



# 6、 Query unfinished orders information.
## GET `/api/v1/spot/open_order`


| **PARAMETER** | **TYPE** | **Mandatory** | **DESCRIPTION** |
| --- | --- | --- | --- |
| accountId | STRING | YES | user's accountId |
| symbol | STRING | NO | Trading pair |
| orderId | LONG | NO | order's id|
| limit | INTEGER | NO | The number of items returned per query is 10 by default, with a maximum limit of 100 items. |




``` java
{
	"code": "0",
	"msg": "success",
	"data": [{
		"clientOid": " ",
		"orderId": 2000000074758463,
		"accountId": "2",
		"side": "BUY",
		"symbol": "BTC-USDT",
		"price": "69000",
		"quantity": "0.001",
		"amount": "69",
		"status": "NEW",
		"tradeQty": "0",
		"tradeAmount": "0",
		"orderType": "LIMIT",
		"stopStatus": "PENDING",
		"failedReason": " "
	}, {
		"clientOid": " ",
		"orderId": 2000000074758423,
		"accountId": "2",
		"side": "BUY",
		"symbol": "BTC-USDT",
		"price": "70613.48",
		"quantity": "0.00036",
		"amount": "25.4208528",
		"status": "NEW",
		"tradeQty": "0",
		"tradeAmount": "0",
		"orderType": "LIMIT",
		"stopStatus": "PENDING",
		"failedReason": " "
	}]
}

```
