# Websocket账户接口

# 基本信息
* 本篇所列出REST接口的baseurl **https://api.binance.com**
* 用于订阅账户数据的 `listenKey` 从创建时刻起有效期为30分钟
* 可以通过`PUT`一个`listenKey`延长30分钟有效期
* `DELETE`一个 `listenKey` 立即关闭当前数据流
* 本篇所列出的websocket接口baseurl: **wss://stream.binance.com:9443**
* 订阅账户数据流的stream名称为 **/ws/\<listenKey\>**
* 每个到stream.binance.com的链接有效期不超过24小时，请妥善处理断线重连。
* 账户数据流的消息**不保证**严格时间序; **请使用 E 字段进行排序**

## 用户数据流

### 生成listenKey
```
POST /api/v1/listenKey (HMAC SHA256)
```
创建一个新的user data stream，返回值为一个listenKey，即websocket订阅的stream名称。

**权重:**
1

**参数:**
NONE

**响应:**
```javascript
{
  "listenKey": "pqia91ma19a5s61cv6a81va65sdf19v8a65a1a5s61cv6a81va65sdf19v8a65a1"
}
```

### 延长listenKey有效期
```
PUT /api/v1/listenKey (HMAC SHA256)
```
有效期延长至本次调用后30分钟

**权重:**
1

**参数:**

NONE

**响应:**
```javascript
{}
```

### 关闭listenKey
```
DELETE /api/v1/listenKey (HMAC SHA256)
```
关闭某账户数据流

**权重:**
1

**参数:**

NONE

**响应:**
```javascript
{}
```

# websocket推送事件

## balance和position更新
账户更新事件的 event type 固定为 `ACCOUNT_UPDATE`
当账户信息有变动时，会推送此事件

**Payload:**
```javascript
{
  "e": "ACCOUNT_UPDATE",        // 事件类型
  "E": 1564745798939            // 事件时间
  "a": [                        // 账户更新事件
    {
      "B":[                     // 余额信息
        {
          "a":"USDT",           // 资产名称
          "wb":"122624"         // 可用余额
        },
        {
          "a":"BTC",           
          "wb":"0"         
        }
      ],
      "P":[
        {
          "s":"BTCUSDT",          // 交易对
          "pa":"1",               // 仓位
          "ep":"9000",            // 入仓价格
          "up":"1974.15"          // 未实现利润
        }
      ]
    }
  ]
}
```

## 订单/交易 更新
当有新订单创建、订单有新成交或者新的状态变化时会推送此类事件
event type统一为 `ORDER_TRADE_UPDATE`

**Payload:**
```javascript
{
  "e": "ORDER_TRADE_UPDATE",     // 事件类型
  "E": 1564745798939             // 事件时间
  "o":
  {
      "s": "ETHBTC",                 // 交易对
      "c": "211",                    // 客户端自定订单ID
      "S": "BUY",                    // 订单方向
      "o": "LIMIT",                  // 订单类型
      "f": "GTC",                    // Time in force
      "q": "1.00000000",             // 订单原始数量
      "p": "0.10264410",             // 订单原始价格
      "ap": "0.10264410",            // 订单平均价格
      "sp": "0.10264410",            // 订单停止价格
      "x": "NEW",                    // 本次事件的具体执行类型
      "X": "NEW",                    // 订单的当前状态
      "i": 4293153,                  // 订单ID
      "l": "0.00000000",             // 订单末次成交数量
      "z": "0.00000000",             // 订单累计已成交数量
      "L": "0.00000000",             // 订单末次成交价格
      "n": "0",                      // 手续费数量
      "T": 1499405658657,            // 成交时间
      "t": -1,                       // 成交ID
      "b": 100,                      // 买单净值
      "a": 100                       // 卖单净值
  }
}
```
**订单方向**
* BUY 买入
* SELL 卖出

**订单类型**
* MARKET 
* LIMIT
* STOP

**本次事件的具体执行类型**

* NEW
* PARTIAL_FILL
* FILL
* DONE_FOR_DAY
* CANCELED
* REPLACE
* PENDING_CANCEL
* STOPPED
* REJECTED
* SUSPENDED
* PENDING_NEW
* CALCULATED
* EXPIRED
* RESTATED
* PENDING_REPLACE
* TRADE
* TRADE_CORRECT
* TRADE_CANCEL
* ORDER_STATUS

**订单状态**
* NEW
* PARTIALLY_FILLED
* FILLED
* DONE_FOR_DAY
* CANCELED
* REPLACED
* PENDING_CANCEL
* STOPPED
* REJECTED
* SUSPENDED
* PENDING_NEW
* CALCULATED
* EXPIRED
* ACCEPTED_FOR_BIDDING
* PENDING_REPLACE
