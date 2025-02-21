
Subscribe Websocket
===========


### This article mainly explains the precautions related to websocket subscription and the return example after a successful subscription.


* Currently, the data format returned by DFX Labs' websocket supports two forms: string and byte stream. You only need to change the format parameter in the subscription link `wss://host/ws/market?format=JSON` to `JSON` and `PROTOBUF` to achieve the conversion


#### Type Status

| **VALUE** | **DESCRIPTION** |
| --- | --- |
|  PROTOBUF| The data is binary and will be compressed.|
|  JSON| Normally readable json string.|

* If you subscribe successfully, the server will return the following content indicating that the socket connection has been successfully established.
```javascript

  {"id":"2cc7870a-50d9-46e6-bce4-44b442fde96e","type":"WELCOME"}

```



### You may have noticed the `type` field in the above example, which represents the business subscription type. Currently, there are four types, the specific meanings are as follows:


* After the above steps, you have successfully established a socket connection. Next, the supported channels will be specifically introduced. Topics are divided into public channels and private channels. Public channels do not require authentication, while private channels require you to carry the `EXCHANGE-API-KEY` parameter in the header when requesting a socket connection. The specific value needs to be obtained from the DFX Labs API page, for details refer [here](https://github.com/dfxlabs/dfxlabs.github.io/blob/main/docs/Open%20Api.md#step-1)


### How to subscribe to a private channel (you need to add the 'EXCHANGE-API-KEY' field in the header)
![subscribe to a private channel](https://dfx-test-public.s3.ap-east-1.amazonaws.com/image/dfx-socket-example-1.png)

`Here is an example of connecting to a private channel using Python`

``` Python
import asyncio
import websockets

async def connect_to_websocket():
    uri = "wss://api.dfx.hk/ws/market?format=JSON"
    headers = {
        "EXCHANGE-API-KEY": "Your API KEY"
    }

    async with websockets.connect(uri, extra_headers=headers) as websocket:
        response = await websocket.recv()
        print(f"Received message: {response}")

asyncio.run(connect_to_websocket())

```


#### Operation Type

| **VALUE** | **DESCRIPTION** |
| --- | --- |
|  WELCOME|Establish connection response |
|  ACK| Subscription result response|
|  PONG| heartbeat response|
|  ERROR|error response |
|  MESSAGE|Message response after subscription |
|  SUBSCRIBE| subscribe topic|
|  UNSUBSCRIBE| Cancel subscribe |
|  PING|Send heartbeat |
|  BYE|disconnect |




# Subscribe to topics

## Private topic (Credentials required)

| **TOPIC** | **SUBJECT** | EXCHANGE-API-KEY | **Push frequency** | **DESCRIPTION** |
| --- | --- | --- | --- |  --- |
| SPOT | ORDER | YES | immediately |  Order update |
| SPOT | TRADE | YES | immediately |  Trade update |


### Public topic


| **TOPIC** | **SUBJECT** | **Mandatory LOGIN** | **Push frequency** | **DESCRIPTION** |
| --- | --- | --- | --- |  --- |
| spot_kline_${cycle} | SYMBOL.eg:`BTC-USDT` | NO | 1S | cycle。eg：`1m、3,、5m、15m、30m、1h、2h、4h、6h、8h、12h、1d、3d、1w、1M`, and Topic eg: spot_kline_cycle |
| spot_trade | SYMBOL.eg:`BTC-USDT` | NO | immediately |  Symbol trade update |
| spot_book_update | SYMBOL.eg:`BTC-USDT` | NO | immediately |  Symbol depth update(Incremental depth push) |
| spot_depth5 | SYMBOL.eg:`BTC-USDT` | NO | immediately |  Symbol depth update, (local depth, first 5 levels)|
| spot_depth10 | SYMBOL.eg:`BTC-USDT` | NO | immediately |  Symbol depth update, (local depth, first 10 levels)|
| spot_depth20 | SYMBOL.eg:`BTC-USDT` | NO | immediately |  Symbol depth update, (local depth, first 20 levels)|
| spot_depth50 | SYMBOL.eg:`BTC-USDT` | NO | immediately |  Symbol depth update, (local depth, first 50 levels)|
| spot_24hr_ticker | SYMBOL.eg:`BTC-USDT` | NO | immediately |  24 hours market update |



## How to subscribe Topic？
`Now let's explore how to establish subscriptions for public and private channels, as well as the return formats after successful subscription.`

## Format and Explanation of Subscription Fields

| **COLUMN** | **DESCRIPTION** |
| --- |  --- |
| id | Message Identifier |
| type | Subscription Type |
| topic | Subscription Channel |
| subjects | Subscription Topics |

### Subscribe symbol latest trade data


``` javascript
{
  "id":"af1183cd-f04c-4517-b563-2d3d3d7edbcd",
  "type":"SUBSCRIBE",  
  "topic":"spot_trade",
  "subjects":["BTC-USDT"]
}

```

`Response format`

```javascript
{"id":"af1183cd-f04c-4517-b563-2d3d3d7edbcd","type":"ACK"}
```

``` javascript

{
		"id": "9f4b9d0eb5a74a40b76bc3cdca05cb4e",
		"type": "MESSAGE",                       
		"topic": "spot_trade",                   
		"subjects": ["BTC-USDT"],                
		"sn": 0,
		"body": {
				"e": "trade",              //事件类型
				"E": 1707126585893,        //事件时间
				"s": "BTC-USDT",           //交易对
				"t": 208,                  //成交ID
				"p": "42533",              //成交价格
				"q": "0.001",              //成交数量
				"T": 1707126585679,        //成交时间
        "b": 2000000275523028,     //买方订单id
        "a": 2000000275531886,     //卖方订单id
				"m": true                  //是否是maker买, isBuyMaker = trade.getTakerOrderSide() == OrderSide.SELL.getCode()
		}
}

```


### Subscribe symbol latest depth data

``` javascript
{
  "id":"af1183cd-f04c-4517-b563-2d3d3d7edbcd",
  "type":"SUBSCRIBE",  
  "topic":"spot_depth5",
  "subjects":["BTC-USDT"]
}

```

`You can subscribe to other topics, such as spot_depth10 and spot_depth20, which will return the corresponding amount of depth information.`

`Response format`

```javascript
{"id":"af1183cd-f04c-4517-b563-2d3d3d7edbcd","type":"ACK"}
```

``` javascript
{
	"id": "d3e096a94bf44c989815590f354c2e3d",  
	"type": "MESSAGE",
	"topic": "spot_depth5",
	"subject": "BTC-USDT",
	"sn": 0,
	"body": {
		"lastUpdateId": 211754,
		"symbol": "BTC-USDT",
		"asks": [ //卖盘[价格，数量]
			["64114.95", "0.00312"],
			["64130.98", "0.00468"],
			["64150.21", "0.0078"],
			["64166.24", "0.01559"],
			["64179.06", "0.01559"]
		],
		"bids": [ //买盘[价格，数量]
			["64102.13", "0.00305"],
			["64086.1", "0.00469"],
			["64066.87", "0.00781"],
			["64050.84", "0.01562"],
			["64038.02", "0.01562"]
		]
	}
}
```

### Subscribe k-line

``` javascript
{
  "id":"af1183cd-f04c-4517-b563-2d3d3d7edbcd",
  "type":"SUBSCRIBE",  
  "topic":"spot_kline_1m",
  "subjects":["BTC-USDT"]
}

```

`Response format`

```javascript
{"id":"af1183cd-f04c-4517-b563-2d3d3d7edbcd","type":"ACK"}
```

`Recevie data format`
``` javascript
{
		"id": "a42824af27dd4f79bf200e552c3f7b91",
		"type": "MESSAGE",
		"topic": "spot_kline_1m",  
		"subject": "BTC-USDT",
		"sn": 0,
		"body": {  
				"e": "kline",   //事件类型
				"E": 1707118140000,  //事件时间
				"s": "BTC-USDT",   //交易对
				"k": {   //k线数据
						"t": 1707118080000,   //k线开始时间
						"T": 1707118139999,   //k线结束时间
						"s": "BTC-USDT",   //交易对
						"i": "1m",   //周期
            "f":0, //first tradeId during the kline startTime and closeTime
            "L":0, //last tradeId during the kline startTime and closeTime
						"o": "1.1",  //开盘价
						"c": "1.1",  //收盘价
						"h": "1.1",  //最高价
						"l": "1.1",  //最低价
						"v": "0",    //成交量
						"n": 0,      //交易笔数
						"x": true,   //k线是否结束
						"q": "0",    //成交额
						"V": "0",    //taker 买入成交量
						"Q": "0"     //taker 买入成交额
				}
		}
}
```

### Subscribe lately 24 Hour ticker

``` javascript
{
  "id":"af1183cd-f04c-4517-b563-2d3d3d7edbcd",
  "type":"SUBSCRIBE",  
  "topic":"spot_24hr_ticker",
  "subjects":["BTC-USDT"]
}

```

`Response format`

```javascript
{"id":"af1183cd-f04c-4517-b563-2d3d3d7edbcd","type":"ACK"}
```

`Recevie data format`
``` javascript
{
	"id": "6f8d9b9ea7124d1f86a20800cbc22fac",
	"type": "MESSAGE",
	"topic": "spot_24hr_ticker",
	"subject": "BTC-USDT",
	"sn": 0,
	"body": {
		"e": "24hrTicker",  //eventType
		"E": 1727244748599, //current time milli
		"s": "BTC-USDT",  //symbol
		"c": "813.26",  //price change value
		"cp": "1.28", //price change percentage
		"w": "64660.7611913419913419",  //weighted avg price
		"p": "64081.59",  //last trade price
		"r": "64094.41",  //current reference price
		"o": "63268.33",  //open price
		"h": "70000", //highest price
		"l": "62760.42",  //lowest price
		"v": "0.3465",  //Trading Volume
		"V": "22404.9537528", //Trading Amount
		"t": 1727158348000, //open time
		"T": 1727244748000, //close time
		"n": 3585 //count of trades happened during the kline startTime and closeTime
	}
}
```

### Subscribe User Order Data

``` javascript
{
  "id":"af1183cd-f04c-4517-b563-2d3d3d7edbcd",
  "type":"SUBSCRIBE",  
  "topic":"SPOT",
  "subjects":["ORDER"]
}

```

`Response format`

```javascript
{"id":"af1183cd-f04c-4517-b563-2d3d3d7edbcd","type":"ACK"}
```


``` javascript
{
		"id": "c6e7becf00654002bd6b4e6ee5c61d17",  
		"type": "MESSAGE",  
		"topic": "SPOT",   
		"subject": "ORDER",
		"sn": 0,
		"body": {
				"e": "orderUpdate",   //事件类型
				"E": 1707122929996,   //事件时间
				"s": "BTC-USDT",      //交易对
				"se": 1680,           //序号
				"i": "2000000001250133",   //订单ID
				"c": " ",    //自定义订单ID
				"u": 24956691,    //用户ID
				"ai": "2",        //账户ID
				"ci": "1",        //客户ID
				"S": "BUY",       //方向
				"p": "42532",     //价格
				"q": "0.001",     //数量
				"a": "42.532",    //金额
				"X": "NEW",       //状态
				"z": "0",         //成交量
				"x": "0",         //成交额
				"o": "LIMIT",     //订单类型
				"st": " ",        //触发方向
				"ss": " ",        //计划委托状态
				"sp": " ",        //触发价
				"f": " ",         //备注
				"ct": 1707122929778,    //创建时间
				"mt": 1707122929996     //修改时间
		}
}
```

### Subscribe User Trade Data

``` javascript
{
  "id":"af1183cd-f04c-4517-b563-2d3d3d7edbcd",
  "type":"SUBSCRIBE",  
  "topic":"SPOT",
  "subjects":["TRADE"]
}

```

`Response format`

```javascript
{"id":"af1183cd-f04c-4517-b563-2d3d3d7edbcd","type":"ACK"}
```

``` javascript
{
		"id": "19d645cc3d4b47c19e3f034097fc56e1",   
		"type": "MESSAGE",  
		"topic": "SPOT",    
		"subjects": ["TRADE"],
		"sn": 0,
		"body": {
				"e": "tradeUpdate",      //事件类型
				"E": 1707125415443,      //事件时间
				"s": "BTC-USDT",         //交易对
				"i": "207",              //成交ID
				"u": 24956691,           //用户ID
				"ai": "2",               //账户ID
				"oi": "2000000001250131",  //订单ID
				"p": "42534",            //成交价
				"q": "0.001",            //成交量
				"S": "BUY",              //方向
				"o": "LIMIT",            //类型
				"t": 1707125415336,      //时间
				"T": "42.534",           //成交额
				"r": "MAKER",            //角色
				"f": "0.000001",         //手续费
				"fa": "BTC"              //手续费币种
		}
}
```



## How to unSubscribe Topic？

`If you need to unsubscribe from a topic, you need to specify the topic. If you want to unsubscribe all users at once, neither the topic nor the subject needs to be provided.`
``` javascript
  {
  	"id": "e6b46d0e-cda3-48e4-9cc9-abb5e27e71bf",    //前端自定义一个uuid，用于接收识别响应结果
  	"type": "UNSUBSCRIBE",  
  	"topic":"spot_kline_1m",
  	"subjects": ["BTC-USDT"]
  }

```
`Then, the server returns the following message`
```javascript

  {
  	"id":"e6b46d0e-cda3-48e4-9cc9-abb5e27e71bf",  
  	"type":"ACK"  
  }
```




## How to disConnect？

`If you need to tell the server that it needs to disconnect the current socket, you can send the following message`
``` javascript
{
	"id": "e6b46d0e-cda3-48e4-9cc9-abb5e27e71bc",
	"type": "BYE"
}

```
`Then, the server returns the following message`
```javascript

{
	"id":"e6b46d0e-cda3-48e4-9cc9-abb5e27e71bc",  
	"type":"ACK"
}
```
