
# Rest API endpoints

## Test connectivity
```
GET /fapi/v1/ping
```
Test connectivity to the Rest API.

**Weight:**
1

**Parameters:**
NONE

**Response:**

```javascript
{}
```

## Check server time
```
GET /fapi/v1/time
```
Test connectivity to the Rest API and get the current server time.

**Weight:**
1

**Parameters:**
NONE

**Response:**

```javascript
{
  "serverTime": 1499827319559
}
```

## Exchange information
```
GET /fapi/v1/exchangeInfo
```
Current exchange trading rules and symbol information

**Weight:**
1

**Parameters:**
NONE

**Response:**

```javascript
{
	"exchangeFilters": [],
 	"rateLimits": [
 		{
 			"interval": "MINUTE",
   			"intervalNum": 1,
   			"limit": 6000,
   			"rateLimitType": "REQUEST_WEIGHT"
   		},
  		{
  			"interval": "MINUTE",
   			"intervalNum": 1,
   			"limit": 6000,
   			"rateLimitType": "ORDERS"
   		}
   	],
 	"serverTime": 1565613908500,
 	"symbols": [
 		{
 			"filters": [
 				{
 					"filterType": "PRICE_FILTER",
     				"maxPrice": "10000000",
     				"minPrice": "0.00000100",
     				"tickSize": "0.00000100"
     			},
    			{
    				"filterType": "LOT_SIZE",
     				"maxQty": "10000000",
     				"minQty": "0.00100000",
     				"stepSize": "0.00100000"
     			},
    			{
    				"filterType": "MARKET_LOT_SIZE",
     				"maxQty": "10000000",
     				"minQty": "0.00100000",
     				"stepSize": "0.00100000"
     			},
     			{
    				"filterType": "MAX_NUM_ORDERS",
    				"limit": 100
  				}
    		],
   			"maintMarginPercent": "2.5000",
   			"pricePrecision": 2,
   			"quantityPrecision": 3,
   			"requiredMarginPercent": "5.0000",
   			"status": "TRADING",
   			"OrderType": [
   				"LIMIT", 
   				"MARKET", 
   				"STOP"
   			],
   			"symbol": "BTCUSDT",
   			"timeInForce": [
   				"GTC",    // Good Till Cancel 
   				"IOC",    // Immediate or Cancel
   				"FOK",    // Fill or Kill
   				"GTX"     // Good Till Crossing
   			]
   		}
   	],
	"timezone": "UTC"
}

```


## Order book
```
GET /fapi/v1/depth
```

**Weight:**
Adjusted based on the limit:


Limit | Weight
------------ | ------------
5, 10, 20, 50, 100 | 1
500 | 5
1000 | 10

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
limit | INT | NO | Default 100; max 1000. Valid limits:[5, 10, 20, 50, 100, 500, 1000]



**Response:**

```javascript
{
  "lastUpdateId": 1027024,
  "bids": [
    [
      "4.00000000",     // PRICE
      "431.00000000"    // QTY
    ]
  ],
  "asks": [
    [
      "4.00000200",
      "12.00000000"
    ]
  ]
}
```

## Recent trades list
```
GET /fapi/v1/trades
```
Get recent trades (up to last 500).

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
limit | INT | NO | Default 500; max 1000.

**Response:**

```javascript
[
  {
    "id": 28457,
    "price": "4.00000100",
    "qty": "12.00000000",
    "quoteQty": "8000.00",
    "time": 1499865549590,
    "isBuyerMaker": true,
  }
]
```

## Old trades lookup
```
GET /fapi/v1/historicalTrades
```
Get older market historical trades.

**Weight:**
5

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
limit | INT | NO | Default 500; max 1000.
fromId | LONG | NO | TradeId to fetch from. Default gets most recent trades.

**Response:**

```javascript
[
  {
    "id": 28457,
    "price": "4.00000100",
    "qty": "12.00000000",
    "quoteQty": "8000.00",
    "time": 1499865549590,
    "isBuyerMaker": true,
  }
]
```

## Compressed/Aggregate trades list
```
GET /fapi/v1/aggTrades
```
Get compressed, aggregate trades. Trades that fill at the time, from the same
order, with the same price will have the quantity aggregated.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
fromId | LONG | NO | ID to get aggregate trades from INCLUSIVE.
startTime | LONG | NO | Timestamp in ms to get aggregate trades from INCLUSIVE.
endTime | LONG | NO | Timestamp in ms to get aggregate trades until INCLUSIVE.
limit | INT | NO | Default 500; max 1000.

* If both startTime and endTime are sent, time between startTime and endTime must be less than 1 hour.
* If fromId, startTime, and endTime are not sent, the most recent aggregate trades will be returned.

**Response:**

```javascript
[
  {
    "a": 26129,         // Aggregate tradeId
    "p": "0.01633102",  // Price
    "q": "4.70443515",  // Quantity
    "f": 27781,         // First tradeId
    "l": 27781,         // Last tradeId
    "T": 1498793709153, // Timestamp
    "m": true,          // Was the buyer the maker?
  }
]
```

## Kline/Candlestick data
```
GET /fapi/v1/klines
```
Kline/candlestick bars for a symbol.
Klines are uniquely identified by their open time.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
interval | ENUM | YES |
startTime | LONG | NO |
endTime | LONG | NO |
limit | INT | NO | Default 500; max 1000.

* If startTime and endTime are not sent, the most recent klines are returned.

**Response:**

```javascript
[
  [
    1499040000000,      // Open time
    "0.01634790",       // Open
    "0.80000000",       // High
    "0.01575800",       // Low
    "0.01577100",       // Close
    "148976.11427815",  // Volume
    1499644799999,      // Close time
    "2434.19055334",    // Quote asset volume
    308,                // Number of trades
    "1756.87402397",    // Taker buy base asset volume
    "28.46694368",      // Taker buy quote asset volume
    "17928899.62484339" // Ignore.
  ]
]
```


## Mark price
```
GET /fapi/v1/premiumIndex
```
mark price and funding rate

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |

**Response:**

```javascript
{
    "symbol": "BTCUSDT",
    "markPrice": "11012.80409769",
    "lastFundingRate": "-0.03750000",
    "nextFundingTime": 1562569200000,
    "time": 1562566020000
}
```

## 24hr ticker price change statistics
```
GET /fapi/v1/ticker/24hr
```
24 hour rolling window price change statistics. **Careful** when accessing this with no symbol.

**Weight:**
1 for a single symbol; **40** when the symbol parameter is omitted

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | NO |

* If the symbol is not sent, tickers for all symbols will be returned in an array.

**Response:**
```javascript
{
  "symbol": "BTCUSDT",
  "priceChange": "-94.99999800",
  "priceChangePercent": "-95.960",
  "weightedAvgPrice": "0.29628482",
  "prevClosePrice": "0.10002000",
  "lastPrice": "4.00000200",
  "lastQty": "200.00000000",
  "openPrice": "99.00000000",
  "highPrice": "100.00000000",
  "lowPrice": "0.10000000",
  "volume": "8913.30000000",
  "quoteVolume": "15.30000000",
  "openTime": 1499783499040,
  "closeTime": 1499869899040,
  "firstId": 28385,   // First tradeId
  "lastId": 28460,    // Last tradeId
  "count": 76         // Trade count
}
```


## Symbol price ticker
```
GET /fapi/v1/ticker/price
```
Latest price for a symbol or symbols.

**Weight:**
1 for a single symbol; **2** when the symbol parameter is omitted

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |

* If the symbol is not sent, prices for all symbols will be returned in an array.

**Response:**

```javascript
{
  "symbol": "BTCUSDT",
  "price": "6000.01"
}
```

### Symbol order book ticker
```
GET /fapi/v1/ticker/bookTicker
```
Best price/qty on the order book for a symbol or symbols.

**Weight:**
1 for a single symbol; **2** when the symbol parameter is omitted

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |

* If the symbol is not sent, bookTickers for all symbols will be returned in an array.

**Response:**

```javascript
{
  "symbol": "BTCUSDT",
  "bidPrice": "4.00000000",
  "bidQty": "431.00000000",
  "askPrice": "4.00000200",
  "askQty": "9.00000000"
}
```



# Web-Socket Streams

* The base endpoint is: **wss://testnet.binancefuture.com**
* Streams can be access either in a single raw stream or a combined stream
* Combined streams are accessed at **/stream?streams=<streamName1\>/<streamName2\>/<streamName3\>**

	for example:
	
```shell
	wss://testnet.binancefuture.com/stream?streams=btcusdt@markPrice/btcusdt@aggTrade/btcusdt@kline_1m
```
* Combined stream events are wrapped as follows: **{"stream":"<streamName\>","data":<rawPayload\>}**
* All symbols for streams are **lowercase**
* The websocket server will send a `ping frame` every 3 minutes. If the websocket server does not receive a `pong frame` back from the connection within a 10 minute period, the connection will be disconnected. Unsolicited `pong frames` are allowed.

## Aggregate Trade Streams
The Aggregate Trade Streams push trade information that is aggregated for a single taker order.

**Stream Name:** 

`<symbol\>@aggTrade`

**Payload:**

```javascript
{
  "e": "aggTrade",  // Event type
  "E": 123456789,   // Event time
  "s": "BTCUSDT",    // Symbol
  "p": "0.001",     // Price
  "q": "100",       // Quantity
  "f": 100,         // First trade ID
  "l": 105,         // Last trade ID
  "T": 123456785,   // Trade time
  "m": true,        // Is the buyer the market maker?
}
```

## Mark Price Stream
Mark price for a single symbol pushed every minute. 

**Stream Name:** `<symbol\>@markPrice`

**Payload:**

```javascript
  {
    "e": "markPriceUpdate",  // Event type
    "E": 1562305380000,      // Event time
    "s": "BTCUSDT",          // Symbol
    "p": "11185.87786614",   // Mark price
    "r": "0.00030000",       // Funding rate
    "T": 1562306400000       // Next funding time
  }
```


### Kline/Candlestick Streams
The Kline/Candlestick Stream push updates to the current klines/candlestick every second.

**Kline/Candlestick chart intervals:**

m -> minutes; h -> hours; d -> days; w -> weeks; M -> months

* 1m
* 3m
* 5m
* 15m
* 30m
* 1h
* 2h
* 4h
* 6h
* 8h
* 12h
* 1d
* 3d
* 1w
* 1M

**Stream Name:** 

`<symbol\>@kline_<interval\>`

**Payload:**

```javascript
{
  "e": "kline",     // Event type
  "E": 123456789,   // Event time
  "s": "BTCUSDT",    // Symbol
  "k": {
    "t": 123400000, // Kline start time
    "T": 123460000, // Kline close time
    "s": "BTCUSDT",  // Symbol
    "i": "1m",      // Interva\
    "f": 100,       // First trade ID
    "L": 200,       // Last trade ID
    "o": "0.0010",  // Open price
    "c": "0.0020",  // Close price
    "h": "0.0025",  // High price
    "l": "0.0015",  // Low price
    "v": "1000",    // Base asset volume
    "n": 100,       // Number of trades
    "x": false,     // Is this kline closed?
    "q": "1.0000",  // Quote asset volume
    "V": "500",     // Taker buy base asset volume
    "Q": "0.500",   // Taker buy quote asset volume
    "B": "123456"   // Ignore
  }
}
```

## Individual Symbol Mini Ticker Stream
24hr rolling window mini-ticker statistics for a single symbol pushed every second. These are NOT the statistics of the UTC day, but a 24hr rolling window from requestTime to 24hrs before.

**Stream Name:** <symbol\>@miniTicker

**Payload:**

```javascript
  {
    "e": "24hrMiniTicker",  // Event type
    "E": 123456789,         // Event time
    "s": "BTCUSDT",          // Symbol
    "c": "0.0025",          // Close price
    "o": "0.0010",          // Open price
    "h": "0.0025",          // High price
    "l": "0.0010",          // Low price
    "v": "10000",           // Total traded base asset volume
    "q": "18"               // Total traded quote asset volume
  }
```


## Individual Symbol Ticker Streams
24hr rollwing window ticker statistics for a single symbol pushed every second. These are NOT the statistics of the UTC day, but a 24hr rolling window from requestTime to 24hrs before.

**Stream Name:** <symbol\>@ticker

**Payload:**

```javascript
{
  "e": "24hrTicker",  // Event type
  "E": 123456789,     // Event time
  "s": "BTCUSDT",      // Symbol
  "p": "0.0015",      // Price change
  "P": "250.00",      // Price change percent
  "w": "0.0018",      // Weighted average price
  "c": "0.0025",      // Last price
  "Q": "10",          // Last quantity
  "o": "0.0010",      // Open price
  "h": "0.0025",      // High price
  "l": "0.0010",      // Low price
  "v": "10000",       // Total traded base asset volume
  "q": "18",          // Total traded quote asset volume
  "O": 0,             // Statistics open time
  "C": 86400000,      // Statistics close time
  "F": 0,             // First trade ID
  "L": 18150,         // Last trade Id
  "n": 18151          // Total number of trades
}
```



## Partial Book Depth Streams
Bids and asks, pushed every second (if existing)

**Stream Name:** <symbol\>@depth

**Payload:**

```javascript
{
  "e": "depthUpdate", // Event type
  "E": 123456789,     // Event time
  "s": "BTCUSDT",      // Symbol
  "U": 157,           // first update Id from last stream
  "u": 160,           // last update Id from last stream
  "pu": 149,          // last update Id in last stream（ie ‘u’ in last stream）
  "b": [              // Bids to be updated
    [
      "0.0024",       // Price level to be updated
      "10"            // Quantity
    ]
  ],
  "a": [              // Asks to be updated
    [
      "0.0026",       // Price level to be updated
      "100"          // Quantity
    ]
  ]
}
```


## How to manage a local order book correctly
1. Open a stream to **wss://testnet.binancefuture.com/stream?streams=btcusdt@depth**
2. Buffer the events you receive from the stream
3. Get a depth snapshot from **https://testnet.binancefuture.com/fapi/v1/depth?symbol=BTCUSDT&limit=1000**
4. Drop any event where `u` is <= `lastUpdateId` in the snapshot
5. The first processed should have `U` <= `lastUpdateId`+1 **AND** `u` >= `lastUpdateId`+1
6. While listening to the stream, each new event's `U` should be equal to the previous event's `u`+1
7. The data in each event is the **absolute** quantity for a price level
8. If the quantity is 0, **remove** the price level
9. Receiving an event that removes a price level that is not in your local order book can happen and is normal.


