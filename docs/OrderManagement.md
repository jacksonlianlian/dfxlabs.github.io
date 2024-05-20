Order Management
===========




# 1、 Create Order
## POST `/api/v1/spot/order`



| **PARAMETER** | **TYPE** | **Mandatory** | **DESCRIPTION** |
| --- | --- | --- | --- |
| symbol | STRING | YES | Trading pair |
| accountId | STRING | YES | user's accountId |
| side | STRING | YES | order direction. eg: `BUY` or `SELL` |
| type | STRING | YES | orderType eg:`LIMIT`. `MARKET`.`STOP_LIMIT`.`STOP_MARKET` |
| price | BIGDECIMAL | NO | Order Price |
| quantity | BIGDECIMAL | NO | Order Quantity |
| amount | BIGDECIMAL | NO | Order Amount |
| clientOrderId | STRING | NO | Order id pass by api user |




``` java
{
	"code": "0",
	"msg": "success",
	"data": "2000000074758463"
}

```



# 2、 Cancel Order.
## DELETE `/api/v1/spot/order`

| **PARAMETER** | **TYPE** | **Mandatory** | **DESCRIPTION** |
| --- | --- | --- | --- |
| accountId | STRING | YES | user's accountId |
| orderId    | LONG    | NO | Order Id|
| clientOrderId | STRING | NO | Order id pass by api user |



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



# 4、 Get User Order List.
## GET `/api/v1/spot/order`


| **PARAMETER** | **TYPE** | **Mandatory** | **DESCRIPTION** |
| --- | --- | --- | --- |
| accountId | STRING | YES | user's accountId |
| orderId    | LONG    | YES | Order Id|
| clientOrderId | STRING | NO | Order id pass by api user |


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



# 5、 Get completed orders information.
## GET `/api/v1/spot/finish_order`


| **PARAMETER** | **TYPE** | **Mandatory** | **DESCRIPTION** |
| --- | --- | --- | --- |
| accountId | STRING | YES | user's accountId |
| symbol | STRING | NO | Trading pair |
| orderId | LONG | NO | order's id|
| fromId | LONG | NO | The order list is queried in reverse order. If you query the data of order number 10-1, and the parameters fromId=10, limit=5, the data of the order number in [10-5) will be queried.|
| limit | INTEGER | NO | The number of items returned by each query, default 10 |
| startTime    | LONG    | NO | StartTime in milliseconds ,eg:1678772870000
| endTime    | LONG    | NO | endTime in milliseconds ,eg:1678772870000





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
| limit | INTEGER | NO | The number of items returned by each query, default 10 |




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
