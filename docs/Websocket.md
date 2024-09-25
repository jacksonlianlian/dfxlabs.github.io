
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


### Subscribe symbol latest trade data

``` javascript
{
		"id": "9f4b9d0eb5a74a40b76bc3cdca05cb4e",     //消息ID
		"type": "MESSAGE",                         //消息类型
		"topic": "spot_trade",                     //主题
		"subjects": ["BTC-USDT"],                    //科目
		"sn": 0,
		"body": {
				"e": "trade",              //事件类型
				"E": 1707126585893,        //事件时间
				"s": "BTC-USDT",           //交易对
				"t": 208,                  //成交ID
				"p": "42533",              //成交价格
				"q": "0.001",              //成交数量
				"T": 1707126585679,        //成交时间
				"m": true                  //是否是maker买, isBuyMaker = trade.getTakerOrderSide() == OrderSide.SELL.getCode()
		}
}

```


### Subscribe symbol latest depth data

``` javascript
{
		"id": "ed7e4e0d9c5b4303ada167658d6bd03f",   //消息ID
		"type": "MESSAGE",       //消息类型
		"topic": "spot_depth",   //主题
		"subjects": ["BTC-USDT"],   //科目
		"sn": 0,
		"body": {    //正文数据
				"e": "depthUpdate",  //事件类型
				"E": 1707122929997,  //事件时间
				"s": "BTC-USDT",     //交易对
				"U": 314,            //第一个更新ID
				"u": 314,            //最后更新ID
				"a": [],             //卖盘[价格，数量]
				"b": [               //卖盘[价格，数量]
						["42532", "0.001"]
				]
		}
}
```

### Subscribe k-line

``` javascript
{
  "id":"af1183cd-f04c-4517-b563-2d3d3d7edbcd",  //消息ID
  "type":"SUBSCRIBE",   //操作类型
  "topic":"spot_kline_1m", //操作主题
  "subjects":["BTC-USDT"] //操作科目
}

```

* Response format

``` javascript
{
		"id": "a42824af27dd4f79bf200e552c3f7b91", //消息ID
		"type": "MESSAGE",   //响应类型
		"topic": "spot_kline_1m",   //订阅主题
		"subject": "BTC-USDT",   //订阅的科目
		"sn": 0,
		"body": {   //正文数据
				"e": "kline",   //事件类型
				"E": 1707118140000,  //事件时间
				"s": "BTC-USDT",   //交易对
				"k": {   //k线数据
						"t": 1707118080000,   //k线开始时间
						"T": 1707118139999,   //k线结束时间
						"s": "BTC-USDT",   //交易对
						"i": "1m",   //周期
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


### Subscribe User Order Data

``` javascript
{
		"id": "c6e7becf00654002bd6b4e6ee5c61d17",   //消息ID
		"type": "MESSAGE",   //消息类型
		"topic": "SPOT",     //主题
		"subjects": ["ORDER"],  //科目
		"sn": 0,
		"body": { //正文
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
		"id": "19d645cc3d4b47c19e3f034097fc56e1",     //消息ID
		"type": "MESSAGE",     //消息类型
		"topic": "SPOT",      //主题
		"subjects": ["TRADE"],   //科目
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
  	"type": "UNSUBSCRIBE",    //操作类型
  	"topic":"spot_kline_1m"  //取消订阅主题
  	"subjects": ["BTC-USDT"]   //取消订阅的科目
  }

```
`Then, the server returns the following message`
```java

  {
  	"id":"e6b46d0e-cda3-48e4-9cc9-abb5e27e71bf",  //订阅操作ID
  	"type":"ACK"  //响应类型
  }
```




## How to disConnect？

`If you need to tell the server that it needs to disconnect the current socket, you can send the following message`
``` javascript
{
	"id": "e6b46d0e-cda3-48e4-9cc9-abb5e27e71bc",    //前端自定义一个uuid，用于接收识别响应结果
	"type": "BYE"
}

Then, the server returns the following message

{
	"id":"e6b46d0e-cda3-48e4-9cc9-abb5e27e71bc",  //订阅操作ID
	"type":"ACK"  //响应类型
}
```
