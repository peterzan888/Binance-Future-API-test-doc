# Error codes for Binance (2018-11-13)
Errors consist of two parts: an error code and a message. Codes are universal,
 but messages can vary. Here is the error JSON payload:
 
```javascript
{
  "code":-1121,
  "msg":"Invalid symbol."
}
```


## 10xx - General Server or Network issues
#### -1000 UNKNOWN
 * An unknown error occured while processing the request.

#### -1001 DISCONNECTED
 * Internal error; unable to process your request. Please try again.

#### -1002 UNAUTHORIZED
 * You are not authorized to execute this request.

#### -1003 TOO_MANY_REQUESTS
 * Too many requests queued.
 * Too many requests; please use the websocket for live updates.
 * Too many requests; current limit is %s requests per minute. Please use the websocket for live updates to avoid polling the API.
 * Way too many requests; IP banned until %s. Please use the websocket for live updates to avoid bans.
 
#### -1004 DUPLICATE_IP
 * This IP is already on the white list

#### -1005 NO_SUCH_IP
 * No such IP has been white listed
 
#### -1006 UNEXPECTED_RESP
 * An unexpected response was received from the message bus. Execution status unknown.

#### -1007 TIMEOUT
 * Timeout waiting for response from backend server. Send status unknown; execution status unknown.

#### -1010 ERROR_MSG_RECEIVED
 * ERROR_MSG_RECEIVED.  
 
#### -1011 NON_WHITE_LIST
 * This IP cannot access this route. 
 
#### -1013 ILLEGAL_MESSAGE
* INVALID_MESSAGE.

#### -1014 UNKNOWN_ORDER_COMPOSITION
 * Unsupported order combination.

#### -1015 TOO_MANY_ORDERS
 * Too many new orders.
 * Too many new orders; current limit is %s orders per %s.

#### -1016 SERVICE_SHUTTING_DOWN
 * This service is no longer available.

#### -1020 UNSUPPORTED_OPERATION
 * This operation is not supported.

#### -1021 INVALID_TIMESTAMP
 * Timestamp for this request is outside of the recvWindow.
 * Timestamp for this request was 1000ms ahead of the server's time.

#### -1022 INVALID_SIGNATURE
 * Signature for this request is not valid.


## 11xx - Request issues
#### -1100 ILLEGAL_CHARS
 * Illegal characters found in a parameter.
 * Illegal characters found in parameter '%s'; legal range is '%s'.

#### -1101 TOO_MANY_PARAMETERS
 * Too many parameters sent for this endpoint.
 * Too many parameters; expected '%s' and received '%s'.
 * Duplicate values for a parameter detected.

#### -1102 MANDATORY_PARAM_EMPTY_OR_MALFORMED
 * A mandatory parameter was not sent, was empty/null, or malformed.
 * Mandatory parameter '%s' was not sent, was empty/null, or malformed.
 * Param '%s' or '%s' must be sent, but both were empty/null!

#### -1103 UNKNOWN_PARAM
 * An unknown parameter was sent.

#### -1104 UNREAD_PARAMETERS
 * Not all sent parameters were read.
 * Not all sent parameters were read; read '%s' parameter(s) but was sent '%s'.

#### -1105 PARAM_EMPTY
 * A parameter was empty.
 * Parameter '%s' was empty.

#### -1106 PARAM_NOT_REQUIRED
 * A parameter was sent when not required.
 * Parameter '%s' sent when not required.

#### -1108 BAD_ASSET
 * Invalid asset.

#### -1109 BAD_ACCOUNT
 * Invalid account.

#### -1110 BAD_INSTRUMENT_TYPE
 * Invalid symbolType.
 
#### -1111 BAD_PRECISION
 * Precision is over the maximum defined for this asset.

#### -1112 NO_DEPTH
 * No orders on book for symbol.
 
#### -1113 WITHDRAW_NOT_NEGATIVE
 * Withdrawal amount must be negative.
 
#### -1114 TIF_NOT_REQUIRED
 * TimeInForce parameter sent when not required.

#### -1115 INVALID_TIF
 * Invalid timeInForce.

#### -1116 INVALID_ORDER_TYPE
 * Invalid orderType.

#### -1117 INVALID_SIDE
 * Invalid side.

#### -1118 EMPTY_NEW_CL_ORD_ID
 * New client order ID was empty.

#### -1119 EMPTY_ORG_CL_ORD_ID
 * Original client order ID was empty.

#### -1120 BAD_INTERVAL
 * Invalid interval.

#### -1121 BAD_SYMBOL
 * Invalid symbol.

#### -1125 INVALID_LISTEN_KEY
 * This listenKey does not exist.

#### -1127 MORE_THAN_XX_HOURS
 * Lookup interval is too big.
 * More than %s hours between startTime and endTime.

#### -1128 OPTIONAL_PARAMS_BAD_COMBO
 * Combination of optional parameters invalid.

#### -1130 INVALID_PARAMETER
 * Invalid data sent for a parameter.
 * Data sent for paramter '%s' is not valid.

#### -2008 BAD_API_ID
 * Invalid Api-Key ID
 
#### -2010 NEW_ORDER_REJECTED
 * NEW_ORDER_REJECTED

#### -2011 CANCEL_REJECTED
 * CANCEL_REJECTED

#### -2013 NO_SUCH_ORDER
 * Order does not exist.

#### -2014 BAD_API_KEY_FMT
 * API-key format invalid.

#### -2015 REJECTED_MBX_KEY
 * Invalid API-key, IP, or permissions for action.

#### -2016 NO_TRADING_WINDOW
 * No trading window could be found for the symbol. Try ticker/24hrs instead.

#### -4000 INVALID_ORDER_STATUS
 * Invalid order status.

#### -4001 PRICE_LESS_THAN_ZERO
 * Price less than 0.

#### -4002 PRICE_GREATER_THAN_MAX_PRICE
 * Price greater than max price.
 
#### -4003 QTY_LESS_THAN_ZERO
 * Quantity less than zero.

#### -4004 QTY_LESS_THAN_MIN_QTY
 * Quantity less than min quantity.
 
#### -4005 QTY_GREATER_THAN_MAX_QTY
 * Quantity greater than max quantity. 

#### -4006 STOP_PRICE_LESS_THAN_ZERO
 * Stop price less than zero. 
 
#### -4006 STOP_PRICE_GREATER_THAN_MAX_PRICE
 * Stop price greater than max price. 
 
## Messages for -1010 ERROR_MSG_RECEIVED, -2010 NEW_ORDER_REJECTED, and -2011 CANCEL_REJECTED
This code is sent when an error has been returned by the matching engine.
The following messages which will indicate the specific error:


Error message | Description
------------ | ------------
"Unknown order sent." | The order (by either `orderId`, `clOrdId`, `origClOrdId`) could not be found
"Duplicate order sent." | The `clOrdId` is already in use
"Market is closed." | The symbol is not trading
"Account has insufficient balance for requested action." | Not enough funds to complete the action
"Market orders are not supported for this symbol." | `MARKET` is not enabled on the symbol
"Iceberg orders are not supported for this symbol." | `icebergQty` is not enabled on the symbol
"Stop loss orders are not supported for this symbol." | `STOP_LOSS` is not enabled on the symbol
"Stop loss limit orders are not supported for this symbol." | `STOP_LOSS_LIMIT` is not enabled on the symbol
"Take profit orders are not supported for this symbol." | `TAKE_PROFIT` is not enabled on the symbol
"Take profit limit orders are not supported for this symbol." | `TAKE_PROFIT_LIMIT` is not enabled on the symbol
"Price * QTY is zero or less." | `price` * `quantity` is too low
"IcebergQty exceeds QTY." | `icebergQty` must be less than the order quantity
"This action disabled is on this account." | Contact customer support; some actions have been disabled on the account.
"Unsupported order combination" | The `orderType`, `timeInForce`, `stopPrice`, and/or `icebergQty` combination isn't allowed.
"Order would trigger immediately." | The order's stop price is not valid when compared to the last traded price.
"Cancel order is invalid. Check origClOrdId and orderId." | No `origClOrdId` or `orderId` was sent in.
"Order would immediately match and take." | `LIMIT_MAKER` order type would immediately match and trade, and not be a pure maker order.

## -9xxx Filter failures
Error message | Description
------------ | ------------
"Filter failure: PRICE_FILTER" | `price` is too high, too low, and/or not following the tick size rule for the symbol.
"Filter failure: LOT_SIZE" | `quantity` is too high, too low, and/or not following the step size rule for the symbol.
"Filter failure: MARKET_LOT_SIZE" | `MARKET` order's `quantity` is too high, too low, and/or not following the step size rule for the symbol.
"Filter failure: MAX_NUM_ORDERS" | Account has too many open orders on the symbol.


