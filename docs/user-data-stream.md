# User Data Streams for Binance (2018-11-13)
# General WSS information
* The base API endpoint is: **https://testnet.binancefuture.com**
* A User Data Stream `listenKey` is valid for 30 minutes after creation.
* Doing a `PUT` on a `listenKey` will extend its validity for 30 minutes.
* Doing a `DELETE` on a `listenKey` will close the stream.
* The base websocket endpoint is: **wss://testnet.binancefuture.com**
* User Data Streams are accessed at **/stream?stream=\<listenKey\>**
* User data stream payloads are **not guaranteed** to be in order during heavy periods; **make sure to order your updates using E**


## User data stream endpoints

### Start user data stream (USER_STREAM)
```
POST /fapi/v1/listenKey (HMAC SHA256)
```
Start a new user data stream. The stream will close after 30 minutes unless a keepalive is sent.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
recvWindow | LONG | NO |
timestamp | LONG | YES |

**Response:**
```javascript
{
  "listenKey": "pqia91ma19a5s61cv6a81va65sdf19v8a65a1a5s61cv6a81va65sdf19v8a65a1"
}
```

### Keepalive user data stream (USER_STREAM)
```
PUT /fapi/v1/listenKey (HMAC SHA256)
```
Keepalive a user data stream to prevent a time out. User data streams will close after 30 minutes. It's recommended to send a ping about every 30 minutes.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
recvWindow | LONG | NO |
timestamp | LONG | YES |


**Response:**
```javascript
{}
```

### Close user data stream (USER_STREAM)
```
DELETE /fapi/v1/listenKey (HMAC SHA256)
```
Close out a user data stream.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
recvWindow | LONG | NO |
timestamp | LONG | YES |


**Response:**

```javascript
{}
```

# websocket event

## balance and position update
event type is `ACCOUNT_UPDATE`.
When balance or position get updated, will push this event.

**Payload:**

```javascript
{
  "e": "ACCOUNT_UPDATE",        // event type
  "E": 1564745798939            // event time
  "a": [                        
    {
      "B":[                     // balances
        {
          "a":"USDT",           // asset
          "wb":"122624"         // wallet balance
        },
        {
          "a":"BTC",           
          "wb":"0"         
        }
      ],
      "P":[                       // positions 
        {
          "s":"BTCUSDT",          // symbol
          "pa":"1",               // position amount
          "ep":"9000",            // entry price
          "up":"1974.15"          // unrealized profit
        }
      ]
    }
  ]
}
```

## order update
When new order created, order status changed will push such event.
event type is `ORDER_TRADE_UPDATE`.

**Payload:**

```javascript
{
  "e": "ORDER_TRADE_UPDATE",     // event type
  "E": 1564745798939             // event time
  "o":
  {
    "s": "BTCUSDT",                 // symbol
    "c": "211",                    // client order id
    "S": "BUY",                    // side
    "o": "LIMIT",                  // order type
    "f": "GTC",                    // Time in force
    "q": "1.00000000",             // Original quantity
    "p": "0.10264410",             // Price
    "ap": "0.10264410",            // average price
    "sp": "0.10264410",            // stop price
    "x": "NEW",                    // execution type
    "X": "NEW",                    // order status
    "r": "NONE",                   // order reject reason
    "i": 4293153,                  // order id 
    "l": "0.00000000",             // order last filled quantity
    "z": "0.00000000",             // order filled accumulated quantity
    "L": "0.00000000",             // last filled price
    "n": "0",                      // commission
    "T": 1499405658657,            // order trade time
    "t": -1,                       // trade id
    "b": 100,                      // bis notional
    "a": 100                       // ask notional
  }
  
}
```
**Side**
* BUY 
* SELL 

**order type**
* MARKET 
* LIMIT
* STOP

**execution type**

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

**order status**

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
