---
title: title

language_tabs: # must be one of https://git.io/vQNgJ
  - python
  - php
  - shell

toc_footers:
  - <a href='https://www.kucoin.com'>Sign Up for KuCoin</a>

includes:

search: true
---

# Quick Start

## Brief Introduction

Welcome to KuCoin’s developer documentation for high-frequency trading. This document provides an overview of high-frequency trading features, as well as APIs.

KuCoin API: **REST API**

- There are three types of REST APIs: **User (private), Trading (private) and Market Data (public)**



## Update Preview

**04/23/23**:

- 【Add】Add`POST /api/v1/hf/orders/dead-cancel-all`interface
- 【Add】Add`GET /api/v1/hf/orders/dead-cancel-all/query`interface

**04/01/23**:

- 【Add】Add`POST /api/v1/hf/orders/alter`interface
- 【Add】Add`DELETE /api/v1/hf/orders/sync/{orderId}`interface
- 【Add】Add`DELETE /api/v1/hf/orders/sync/client-order/{clientOid}`interface
- 【Add】Add`POST /api/v1/hf/orders/multi/sync`interface
- 【Add】Add`GET /api/v1/hf/orders/active/symbols`interface
- 【Modify】Modify`POST /api/v1/hf/orders/multi`interface，Input parameters: support batch orders for different trading pairs; output parameters: the return value has been optimized, only orderId and order results are returned


**11/08/22**:

- 【Deprecate】Deprecate `POST /api/v1/accounts` interface.

**10/18/22**:

- 【Modify】Modify the `limit` request parameter description in the `GET /api/v1/hf/orders/done` interface to "default 20, maximum 100".

# User

# Account

## Internal Funds Transfers in high-frequency trading accounts
```json
// request
{
    "amount":10,
    "currency":"USDT",
    "from":"trade",
    "to":"trade_hf",
    "clientOid":"b6ahe97ce32aed83g3c3f8f0"
}
```

```json
// response
{
    "code":"200000",
    "data": {
        "orderId":"6305cbf0bd13850001e625b4"
    }
}
```
Users can transfer funds between their main account, trading account, and high-frequency trading account free of charge.

### HTTP Request
`POST /api/v2/accounts/inner-transfer`

### Example
`POST /api/v2/accounts/inner-transfer`

### API Permissions
This API requires `Trade` permissions

### Parameters
Parameters | Type | Mandatory | Description | 
--------- | ------- | -----------| -----------| 
clientOid | String | Yes | Client Order Id，a unique identifier created by the user, using UUID is recommended | 
currency | String | Yes | currency | 
from | String | Yes | Payment account type: `main`(main account), `trade`(trading account), `trade_hf`(high-frequency trading account)
to | String | Yes | Receiving account type: `main`(main account), `trade`(trading account), `trade_hf`(high-frequency trading account)
amount | String | Yes | Transfer amount, the precision is the precision of the currency multiplied by a positive integer |

### Return Value
Field | Description 
--------- | ------- 
orderId | Order ID for internal funds transfer


## List high-frequency trading accounts
```json
// response
{
    "code": "200000",
    "data": [
        {
            "balance": "3027.25165335",
            "available": "3027.25165335",
            "holds": "0",
            "currency": "USDT",
            "id": "2560047104",
            "type": "trade_hf"
        }
    ]
}
```
Get a list of high-frequency trading accounts.

### HTTP Request
`GET /api/v1/accounts`

### Example
`GET /api/v1/accounts?currency=USDT&type=trade_hf`

### API Permissions
This API requires `General` permissions

### Parameters
Parameters | Type | Mandatory | Description 
| --------- | ------- | -----------| -----------| 
currency | String | No | currency 
type | String | No | Account type, `trade_hf`(high-frequency trading account)

### Return Value
Field | Description 
--------- | ------- 
id | accountId
currency | The currency the account is associated with
type | Account type ,`trade_hf`(high-frequency trading account) 
balance | Total funds 
available | Available funds 
holds | Funds frozen

## Detail of the high-frequency trading account
```json
// response
{
    "code": "200000",
    "data": {
        "balance": "9000000",
        "available": "9000000",
        "holds": "0",
        "currency": "YOP"
    }
}
```
Get the details of the high-frequency trading account

### HTTP Request
`GET /api/v1/accounts/{accountId}`

### Example
`GET /api/v1/accounts/2051232768`

### API Permissions
This API requires `General` permissions

### Parameters
Parameters | Type | Mandatory | Description 
| --------- | ------- | -----------| -----------| 
accountId | String | Yes | Path parameters, high-frequency trading account ID

### Return Value
Field | Description 
--------- | ------- 
currency | The currency the account is associated with
balance | Total funds 
available | Available funds 
holds | Funds frozen

## Get Transferrable Funds in high-frequency trading accounts
```json
// response
{
    "code": "200000",
    "data": {
        "balance": "990.28419461",
        "transferable": "983.64866777",
        "available": "983.64866777",
        "holds": "6.63552684",
        "currency": "USDT"
    }
}
```
This API can be used to obtain the amount of transferrable funds in high-frequency trading accounts.

### HTTP Request
`GET /api/v1/accounts/transferable`

### Example
`GET /api/v1/accounts/transferable?currency=USDT&type=TRADE_HF`

### API Permissions
This API requires `General` permissions

### REQUEST RATE LIMIT
This API limits request frequencies to `18 times/3s` for all accounts.

### Parameters
Parameters | Type | Mandatory | Description |
--------- | ------- | -----------| -----------| 
currency | String | Yes | currency | 
type | String | Yes | Account type, `TRADE_HF`(high-frequency trading account)|

### Return Value
Field | Description 
--------- | ------- 
currency | currency 
balance | Total funds 
available | Available funds 
holds | Funds on hold 
transferable | Transferrable funds


## Account Ledger in high-frequency trading accounts
```json
// response
{
    "code": "200000", 
    "data": [
        {
            "id": "42852417537", 
            "currency": "CSP", 
            "amount": "1.00000000", 
            "fee": "0.00000000", 
            "balance": "99999986.99999999", 
            "accountType": "TRADE_HF", 
            "bizType": "TRADE_EXCHANGE", 
            "direction": "in", 
            "createdAt": "1661347205743", 
            "context": "{'symbol':'CSP-USDT','orderId':'6306257dd9180300014c8d47','tradeId':'609522034689'}"
        }, 
        {
            "id": "42852401152", 
            "currency": "CSP", 
            "amount": "1.00000000", 
            "fee": "0.00000000", 
            "balance": "99999985.99999999", 
            "accountType": "TRADE_HF", 
            "bizType": "TRADE_EXCHANGE", 
            "direction": "out", 
            "createdAt": "1661347205743", 
            "context": "{'symbol':'CSP-USDT','orderId':'63062585d9180300014c8d50','tradeId':'609522034689'}"
        }
    ]
}
```
This API endpoint returns all transfer (in and out) records in  high-frequency trading account and supports multi-coin queries. The query results are sorted in descending order by createdAt and id.

### HTTP Request
`GET /api/v1/hf/accounts/ledgers`

### Example
`GET /api/v1/hf/accounts/ledgers?bizType=TRADE_EXCHANGE&currency=YOP,DAI&startAt=1601395200000`

### API Permissions
This API requires `General` permissions

### REQUEST RATE LIMIT
This API limits request frequencies to `18 times/3s` for all accounts.

### Parameters
Parameters | Type | Mandatory | Description | 
--------- | ------- | -----------| -----------| 
currency | String | No | currency, optional，can select more than one，separate with commas，select no more than `10` currencys，the default will be to query for all currencys if left empty | 
direction | String | No | Direction of transaction (in or out): `in`-transfer in, `out`-transfer out | 
bizType | String | No | Transaction type: `TRANSFER`-transfer funds,`TRADE_EXCHANGE`-Trade | 
lastId | long | No | The id of the last set of data from the previous batch of data. By default, the latest information is given. | 
limit | int | No | Default`100`，Max`200` | 
startAt | long | No | Start time (ms), the conditional limit createdAt| 
endAt | long | No | End time (ms), the conditional limit createdAt|

<aside class="notice">If <code>lastId</code> is configured, the information obtained < <code>lastId</code>. Otherwise, it will go back to the latest information. You can only obtain data from within a <code>3 * 24</code> hour time range (i.e., from <code>3 * 24</code> hours ago up to now) If you specify a time range that exceeds this limit, the system will default to data from within <code>3 * 24</code> hours.</aside>

#### Return Value
Field | Description | 
--------- | ------- | 
id | Unique key | 
currency | currency | 
amount | Change in funds balance | 
fee | Deposit or withdrawal fee | 
balance | Total balance of funds after change | 
accountType | Master account type `TRADE_HF` | 
bizType | Trnasaction type，such as `TRANSFER`, `TRADE_EXCHANGE`, etc. | 
direction | Direction of transfer( `out` or `in`) | 
createdAt | Created | 
context | Core transaction parameter |

- `context` description
    * If the `bizType` is `TRADE_EXCHANGE`, the context field will include additional transaction information (order id, transaction id, and trading pair).

# Trade

The following requires signature verification.


# Order

## Place hf order
```json
// response
{
    "code": "200000",
    "data": {
        "orderId": "5bd6e9286d99522a52e458de"
    }
}
```

There are two types of orders: 
(`limit`) order: set price and quantity for the transaction. 
(`market`) order : set amount or quantity for the transaction.

Before placing an order, make sure there are enough funds in your high-frequency trading account. Once the order is successfully placed, the amount of funds required for the order will be frozen. The amount frozen will depend on the type of order placed as well as the request parameters set.

Please note that once your order enters the order book, the system will freeze the handling fees for the order ahead of time.

Before placing orders, please be sure to fully understand the meaning of the parameters for each trading pair.

<aside class="notice">For each account, the maximum number of <strong>ACTIVE</strong> orders is <code>200</code> for each trading pair.</aside>


### HTTP Request
`POST /api/v1/hf/orders`

### Example
`POST /api/v1/hf/orders`

### API Permissions
This API requires `Trade` permissions

### REQUEST RATE LIMIT
The request frequency for this API is limited to `150 times/3s` for each account

### Parameters

Public order placement request parameters

Parameters | Type | Mandatory | Description |
 --------- | ------- | -----------| -----------| 
 clientOid | String | No | Client Order Id，unique identifier created by the user, the use of UUID is recommended | 
 symbol | String | Yes | Trading pair, such as, `ETH-BTC` | 
 type | String | Yes | Order type `limit` and `market` | 
 side | String | Yes | `buy` or `sell` | 
 stp | String | No | Self trade prevention (`self trade prevention`) is divided into four strategies: `CN`, `CO`, `CB` , and `DC` | 
 tags | String | No | Order tag, cannot exceed `20` characters (ASCII) in length| 
 remark | String | No | Order placement remarks, length cannot exceed `20` characters (ASCII) in length|

#### Additional Request Parameters Required by `limit` Orders

Parameters | Type | Mandatory | Description | 
--------- | ------- | -----------| -----------| 
price | String | Yes | Specify price for currency | 
size | String | Yes | Specify quantity for currency | 
timeInForce | String | No | \[Optional] Order timing strategy `GTC`, `GTT`, `IOC`, `FOK` (The default is `GTC`) | 
cancelAfter | long | No | \[Optional] Cancel after `n` seconds，the order timing strategy is `GTT` | 
postOnly | boolean | No | \[Optional] passive order labels, this is disabled when the order timing strategy is `IOC` or `FOK` | 
hidden | boolean | No | \[Optional] Hidden or not (not shown in order book) | 
iceberg | boolean | No | \[Optional] Whether or not only visible portions of orders are shown in iceberg orders | 
visibleSize | String | No | \[Optional] Maximum visible quantity in iceberg orders |

#### Additional request parameters required by `market` orders
<aside class="notice">When placing a market order, the <code>size</code> or <code>funds</code> must be set.</aside>
Parameters | Type | Mandatory | Description | 
--------- | ------- | -----------| -----------| 
size | String | No | (Select one out of two: `size` or `funds`) | 
funds | String | No | (Select one out of two: `size` or `funds`) |



### Terms

### Trading pair (Symbol)
The trading pair must be a trading pair supported by KuCoin.

#### Client Order Id(clientOid)
The ClientOid field is a unique ID created by the user（we recommend using a UUID）, and can only contain numbers, letters, underscores （\_）, and hyphens （-）. This field is returned when order information is obtained. You can use clientOid to tag your orders. ClientOid is different from the order ID created by the service provider. Please do not initiate requests using the same clientOid. The maximum length for the ClientOid is 40 characters.

Please remember the orderId created by the service provider, it used to check for updates in order status.

#### Order Type (type):
The type of order you specify when you place your order determines whether or not you need to request other parameters and also affects the execution of the matching engine.

When placing a limit order, you must specify a price and size. The system will try to match the order according to market price or a price better than market price. If the order cannot be immediately matched, it will stay in the order book until it is matched or the user cancels.

Unlike limit orders, the price for market orders fluctuates with market prices. When placing a market order, you do not need to specify a price, you only need to specify a quantity. Market orders are filled immediately and will not enter the order book. All market orders are takers and a taker fee will be charged.

#### Trade Type (tradeType)
The platform currently supports spot （TRADE） asset trading orders, the system will default to freezing funds in your trading account according to spot trading rules.

#### Price (Price)
When placing a limit order, the price must be based on price increments (priceIncrement) for the trading pair. The price increment (priceIncrement) is the price precision for the trading pair. For example, for the BTC-USDT trading pair, the priceIncrement is 0.00001000. So the price for your orders cannot be less than 0.00001000 and must be a multiple of priceIncrement. Otherwise, the order will return an invalid priceIncrement error.

#### Size (Size)
When placing a limit order, size refers to the amount of trading targets (the asset name written in front) for the trading pair. Size must be based on the baseIncrement. The baseIncrement represents the precision for the trading pair. The size of an order must be a positive-integer multiple of baseIncrement and must be between baseMinSize and baseMaxSize.

#### Funds (Funds)
When placing a market order, the funds field refers to the funds for the priced asset (the asset name written latter) of the trading pair. The funds must be based on the quoteIncrement of the trading pair. The quoteIncrement represents the precision of the trading pair. The funds value for an order must be a multiple of quoteIncrement and must be between quoteMinSize and quoteMaxSize.

#### Time In Force (TimeInForce)
Time in force is a special strategy used during trading. It is used to specify how long an order shall remain active before being executed or expiring. There are four types of TimeInForce:

Abbreviation | Full name | Description | 
--------- | ------- | -----------| 
GTC | Good Till Canceled | Expires only when cancelled | 
GTT | Good Till Time | Expires at a specified time | 
IOC | Immediate Or Cancel | Execute the portions that can be executed immediately and cancel the rest; this does not enter the order book. | 
FOK | Fill Or Kill | Cancel if the order cannot be completely filled. |

- Note: order fills include self-fills. Market order does not support the TimeInForce strategy

#### Post Only (PostOnly)
PostOnly is just a label. If an order can be immediately filled, then it is cancelled. When the user places a postOnly order, if the order encounters an iceberg order or hidden order after entering the matching engine, the order can be filled immediately. The postOnly order will charge maker fees and the iceberg order and hidden order will charge taker fees.

#### Hidden Orders and Iceberg Orders (Hidden & Iceberg)

Hidden orders and iceberg orders can be set in advanced settings (iceberg orders are a special type of hidden orders). When placing limit orders or stop limit orders, you can choose to execute according to hidden orders or iceberg orders.

Hidden orders are not shown in order books.

Unlike hidden orders, iceberg orders are divided into visible and hidden portions. When engaging in iceberg orders, visible order sizes must be set. The minimum visible size for an iceberg order is 1/20 of the total order size.

When matching, the visible portions of iceberg orders are matched first. Once the visible portions are fully matched, hidden portions will emerge. This will continue until the order is fully filled.

##### Note:
- The system will charge taker fees for hidden orders and iceberg orders.
- If you simultaneously set iceberg orders and hidden orders, your order will default to an iceberg order for execution.

#### Hold (Hold)

For limit price purchase orders, we will hold the amount of funds (price * size) required for your purchase order. Similarly, for limit price sell orders, we will also hold you sell order assets. When the transaction is executed, the service fees will be calculated. If you cancel a portion of a filled or unfilled order, then the remaining funds or amounts will be released and returned to your account. For market price buy/sell orders that require specific funds, we will hold the required funds in from your account. If only the size is specified, we may freeze (usually for a very short amount of time) all of the funds in your account prior to the order being filled or cancelled.

#### Self Trade Prevention (SelfTradePrevention)
Self trade prevention can be set in advanced settings, which will prevent self trades from occurring in your orders. If you do not set STP when placing an order, your order could be filled with another one of your orders. Market orders currently do not support DC strategies.

Market orders currently do not support DC. When timeInForce is FOK, stp will specify CN.

Abbreviation | Full name | Description | 
--------- | ------- | -----------| 
GTC | Good Till Canceled | Expires only when cancelled | 
GTT | Good Till Time | Expires at a specified time | 
IOC | Immediate Or Cancel | Execute the portions that can be executed immediately and cancel the rest; this does not enter the order book. | 
FOK | Fill Or Kill | Cancel if the order cannot be completely filled. |

Abbreviation | Full name | Description | 
--------- | ------- | -----------| 
DC | Decrease and Cancel | Cancel the order with smaller size and change the order with larger size to the difference between old and new. | 
CO | Cancel old | Cancel the old order. | 
CN | Cancel new | Cancel the new order. | 
CB | Cancel both | Cancel both sides |

#### Order Lifecycle (ORDER LIFECYCLE)
When an order placement request is successful (the matching engine receives the order) or denied (due to there being insufficient funds or illegal parameter values, etc.), the system will respond to the HTTP request. When an order is successfully placed, the order ID is returned. The order will be matched, which could result in it being fully or partially filled. When an order is fully or partially filled, the remaining portions of the order will be in an active state awaiting to be matched (this does not include IOC orders). Orders that are fully or partially filled(already cancelled) will be put into the “done” state.

Users that have subscribed to the Market Data Channel can use the order ID （orderId） and client ID （clientOid） to identify messages.

#### Price Protection Mechanisms
Price protection mechanisms ae enabled for order placements. Rules are detailed below:

- If the spot trading market order/limit order placed by the user can be directly matched with an order in the current order book, the system will judge whether deviation between the price corresponding to the transaction depth and the spread exceeds the threshold value （the threshold value can be obtained using the symbol API endpoint）；
- If it is exceeded, for limit orders, the order will be directly cancelled;
- If it is a market order, then the system will partially execute the order. The execution limit will be the order size within the price range corresponding to the threshold value. The remaining orders will not be filled.

For example: if the threshold value is 10%, when a user places a market price purchase order in the KCS/USDT trading market for 10,000 USDT (the selling price is currently 1.20000), the system will determine that after the order is completely filled, the final price will be 1.40000. (1.40000-1.20000)/1.20000=16.7%>10%. The threshold value is 1.32000\. The user’s market price purchase order will be filled only to a maximum of 1.32000\. The remaining order portions will not be matched with orders in the order book. Note: this feature may not be able to determine depth with complete accuracy. If your order is not completely filled, it may be because the portion exceeding the threshold value was not filled.


### Return Value

| Field | Description | 
| --------------------------------- | ---- | 
| orderId | An order Id is returned once an order is successfully placed.|





## Sync place hf order 
```json
{
    "code": "200000", 
    "data": {
    "orderId": "6d539dc614db3",
    "orderTime": "1507725176595",//order time
    "originSize": "10.01", 
    "dealSize": "2.01",
    "remainSize": "8",
    "canceledSize": "0",
    "status": "open", //open: the order is active: the order has been completed
    "matchTime": "1507725176595" //begin match time
  }
}
```

The request parameters of this interface are the same as those of the "Place hf order" interface

The difference between this interface and "Place hf order" is that this interface will synchronously return the order information after the order matching is completed.

For higher latency requirements, please select the "Place hf order" interface. If there is a requirement for returning data integrity, please select this interface

### HTTP Request

`POST /api/v1/hf/orders/sync`

### Example

`POST /api/v1/hf/orders/sync`

### API Permissions

This API requires `Trade` permissions

### REQUEST RATE LIMIT

The request frequency of this API is limited to `45 times/3s` for each account

### Parameters

The request parameters of this interface are the same as those of the "Place hf order" interface

### Return Value

| Field | Description | 
--------- |------------|
orderId | An order Id is returned once an order is successfully placed.      |
orderTime | order time       |
originSize | original order size    |
originFunds | Order original funds - market order |
dealSize | deal size       |
dealFunds | deal funds - market order  |
remainSize | remain size       |
remainFunds | remain funds - market order   |
canceledSize | Cumulative number of cancellations     |
canceledFunds | Cumulative funds of cancellations - market order |
status | Order Status. open：order is active; done：order has been completed |
matchTime | matching time       |





<aside class="spacer4"></aside>
<aside class="spacer4"></aside>
<aside class="spacer2"></aside>




## Place multiple hf orders

```json
//request
{
  "orderList": [
	  {
		  "clientOid": "3d07008668054da6b3cb12e432c2b13a",
		  "side": "buy",
		  "type": "limit",
		  "price": "0.01",
		  "size": "1",
		  "symbol": "ETH-USDT"
	  },
	  {
		  "clientOid": "37245dbe6e134b5c97732bfb36cd4a9d",
		  "side": "buy",
		  "type": "limit",
		  "price": "0.01",
		  "size": "1",
		  "symbol": "ETH-USDT"
	  }
  ]
}
```

```json
//response
{
  "success": true,
  "code": "200",
  "msg": "success",
  "retry": false,
  "data": [
	  {
		  "orderId": "641d669c9ca34800017a2a3c",
		  "success": true
	  },
	  {
		  "orderId": "641d669c9ca34800017a2a45",
		  "success": true
	  }
  ]
}
```

This endpoint supports sequential batch order placement from a single endpoint. A maximum of `5`orders can be placed simultaneously. The order types must be limit orders of the same trading pair （this endpoint currently only supports spot trading and does not support margin trading）.

This endpoint only supports order placement requests. To obtain the results of the order placement, you will need to check the order status or subscribe to websocket to obtain information about he order. 

### HTTP Request

`POST /api/v1/hf/orders/multi`

### Example

`POST /api/v1/hf/orders/multi`

### API Permissions
This API requires `Trade` permissions

### REQUEST RATE LIMIT
The request frequency of this API endpoint is limited to `3 times/3s` for each account

### Parameters
Parameters | Type | Mandatory  | Description | 
--------- | ------- | -----------| -----------| 
clientOid | String | No | Client Order Id，a unique identifier created by the user，the use of UUID is recommended | 
symbol | String | Yes | trading pairs such as，ETH-BTC | 
type | String | Yes | Order type limit and market | 
timeInForce | String | No | \[Optional] Time in force `GTC`, `GTT`, `IOC`, `FOK` (defaults to `GTC`) | 
stp | String | No | \[Optional] Self trade prevention（self trade prevention）is divided into `CN`, `CO`, `CB` , and `DC` strategies | 
side | String | Yes | buy（buy） or sell（sell）| 
price | String | Yes | Specify price for currency | 
size | String | Yes | Specify quantity of currency | 
cancelAfter | long | No | \[Optional] Cancels in `n` seconds, with `GTT` as the time in force strategy | 
postOnly | boolean | No | \[Optional] Post only identifier, invalid when the time in force strategy is `IOC` or `FOK` | 
hidden | boolean | No | \[Optional] Hidden or not（not shown in order book） | 
iceberg | boolean | No | \[Optional] Whether iceberg orders only show visible portions of orders | 
visibleSize | String | No | \[Optional] The maximum visible size for iceberg orders | 
tags | String | No | \[Optional] The order identifier length cannot exceed `20` characters（ASCII）| 
remark | String | No | \[Optional] Order placement remarks cannot exceed a length of `20` characters（ASCII）|


### Return Value
| Field | Description | 
| -------| --------------------------- | 
|status | Order creation results (`success`, `fail`) | |fail
Msg | Reason of failure |


## Sync place multiple hf orders

```json
//request
{
  "orderList": [
	  {
		  "clientOid": "3d07008668054da6b3cb12e432c2b13a",
		  "side": "buy",
		  "type": "limit",
		  "price": "0.01",
		  "size": "1",
		  "symbol": "ETH-USDT"
	  },
	  {
		  "clientOid": "37245dbe6e134b5c97732bfb36cd4a9d",
		  "side": "buy",
		  "type": "limit",
		  "price": "0.01",
		  "size": "1",
		  "symbol": "ETH-USDT"
	  }
  ]
}
```


```json
//response
 {
  "success": true,
  "code": "200",
  "msg": "success",
  "retry": false,
  "data": [
	  {
		  "orderId": "641d67ea162d47000160bfb8",
		  "orderTime": 1679648746796,
		  "originSize": "1",
		  "dealSize": "0",
		  "remainSize": "1",
		  "canceledSize": "0",
		  "status": "open",
		  "matchTime": 1679648746443,
		  "success": true
	  },
	  {
		  "orderId": "641d67eb162d47000160bfc0",
		  "orderTime": 1679648747369,
		  "originSize": "1",
		  "dealSize": "0",
		  "remainSize": "1",
		  "canceledSize": "0",
		  "status": "open",
		  "matchTime": 1679648746644,
		  "success": true
	  }
  ]
}
```


The request parameters of this interface are the same as those of the "Sync place multiple hf orders" interface

The difference between this interface and "Sync place multiple hf orders" is that this interface will synchronously return the order information after the order matching is completed.

For higher latency requirements, please select the "Sync place multiple hf orders" interface. If there is a requirement for returning data integrity, please select this interface


### HTTP Request

`POST /api/v1/hf/orders/multi/sync`

### Example

`POST /api/v1/hf/orders/multi/sync`

### API Permissions

This API requires `Trade` permissions

### REQUEST RATE LIMIT

The request frequency of this API endpoint is limited to `3 times/3s` for each account


### Parameters

The request parameters of this interface are the same as those of the "Sync place multiple hf orders" interface, Maximum support for 20 orders

### Return Value
Field | Description | 
--------- | ------- | 
orderId | order Id is returned once an order is successfully placed.       |
orderTime | order time     |
originSize | original order size     |
dealSize | deal size      |
remainSize | remain size       |
canceledSize | Cumulative number of cancellations     |
status | Order Status. open：order is active; done：order has been completed |
matchTime | matching time     |
success | Whether the order was placed successfully.     |


<aside class="spacer2"></aside>








## Modify order

This interface can modify the price and quantity of the order according to orderId or clientOid.

The implementation of this interface is: cancel the order and place a new order on the same trading pair, and return the modification result to the client synchronously

When the quantity of the new order updated by the user is less than the filled quantity of this order, the order will be considered as completed, and the order will be cancelled, and no new order will be placed


### HTTP Request

`POST /api/v1/hf/orders/alter`

### Example

`POST /api/v1/hf/orders/alter`

### API Permissions

This API requires `Trade` permissions

### REQUEST RATE LIMIT

The request frequency of this API endpoint is limited to `60 times/3s` for each account


### Parameters
Parameters | Type | Mandatory | Description | 
--------- | ------- | -----------| -----------| 
symbol | String | Yes | symbol      |
clientOid | String | No | clientOid     |
orderId | String | No | other id          |
newPrice | String | No | The modified price of the new order |
newSize | String | No | The modified size of the new order   |

orderId and clientOid must choose one

newPrice and newSize must choose one

### Return Value
Field | Description | 
--------- | ------- | 
newOrderId | The order id of the new order |


```json
//response
{
    "code": "200000", 
    "data": {
    "newOrderId": "6d539dc614db3"
  }
}
```

<aside class="spacer4"></aside>






## Cancel orders by orderId
```json
// response
{   
    "code": "200000", 
    "data": {
        "orderId": "630625dbd9180300014c8d52"
    }
}
```

This endpoint can be used to cancel a high-frequency order by orderId.

### HTTP Request
`DELETE /api/v1/hf/orders/{orderId}?symbol={symbol}`

### Example
`DELETE /api/v1/hf/orders/5bd6e9286d99522a52e458de?symbol=ETH-BTC`

### API Permissions
This API requires `Trade` permissions

### REQUEST RATE LIMIT
The request frequency of this API is limited to `150 times/3s` for each account

### Parameters
Parameters | Type | Mandatory | Description | 
--------- | ------- | -----------| -----------| 
orderId | String | Yes | Path parameter，Order Id unique identifier | 
symbol | String | Yes | Trading pair, such as `ETH-BTC` |

<aside class="notice">The input parameter orderId is a unique identifier of the order created by the server and is different from the clientOId created by the user.</aside>

<aside class="notice">If an order cannot be cancelled （if it has already been filled or cancelled），an error message will be returned, and you can judge what the reason was based on the returned <code>msg</code>.</aside>

### Return Value

| Field | Description | 
| ----------------- | ------- | 
| orderId | Order id of the cancelled order |

<aside class="spacer4"></aside>





## Sync cancel orders by orderId

The request parameters of this interface are the same as those of the "Cancel orders by orderId" interface

The difference between this interface and "Cancel orders by orderId" is that this interface will synchronously return the order information after the order canceling is completed.

For higher latency requirements, please select the "Cancel orders by orderId" interface. If there is a requirement for returning data integrity, please select this interface


### HTTP Request

`DELETE /api/v1/hf/orders/sync/{orderId}`

### Example

`DELETE /api/v1/hf/orders/sync/{orderId}`

### API Permissions

This API requires `Trade` permissions

### REQUEST RATE LIMIT

The request frequency of this API endpoint is limited to `150 times/3s` for each account


### Parameters
Parameters | Type | Mandatory | Description | 
--------- | ------- | -----------| -----------| 
orderId | String | Yes | Path parameter，Order Id unique identifier | 
symbol | String | Yes | Trading pair, such as `ETH-BTC` |

### Return Value
Field | Description | 
--------- | ------- | 
orderId | order Id       |
originSize | original order size     |
originFunds | Order original funds - market order |
dealSize | deal size      |
remainSize | remain size       |
canceledSize | Cumulative number of cancellations     |
status | Order Status. open：order is active; done：order has been completed |


```json
//response
 {
    "orderId": "641d67ea162d47000160bfb8",
    "originSize": "1",
    "dealSize": "0",
    "remainSize": "1",
    "canceledSize": "0",
    "status": "done"
}
```

<aside class="spacer4"></aside>







## Cancel order by clientOid
```json
// response
{
    "code": "200000", 
    "data": {
        "clientOid": "6d539dc614db3"
    }
}
```

This endpoint sends out a request to cancel a high-frequency order using clientOid.

### HTTP Request
`DELETE /api/v1/hf/orders/client-order/{clientOid}?symbol={symbol}`

### Example
`DELETE /api/v1/hf/orders/client-order/6d539dc614db3?symbol=ETH-BTC`

### API Permissions
This API requires `Trade` permissions

### REQUEST RATE LIMIT
The request frequency of this API is limited to `150 times/3s` for each account

### Parameters
Parameters  | Type | Mandatory | Description | 
--------- | ------- | -----------| -----------| 
clientOid | String | Yes | Path parameter，an identifier created by the 
symbol | String | Yes | Trading pair such as `ETH-BTC` |

### Return Value
Field | Description | 
--------- | ------- | 
clientOid | Identifier created by the client |

<aside class="spacer4"></aside>






## Sync cancel orders by clientOid

The request parameters of this interface are the same as those of the "Cancellation of order by clientOid" interface

The difference between this interface and "Cancellation of order by clientOid" is that this interface will synchronously return the order information after the order canceling is completed.

For higher latency requirements, please select the "Cancellation of order by clientOid" interface. If there is a requirement for returning data integrity, please select this interface


### HTTP Request

`DELETE /api/v1/hf/orders/sync/client-order/{clientOid}`

### Example

`DELETE /api/v1/hf/orders/sync/client-order/{clientOid}`

### API Permissions

This API requires `Trade` permissions

### REQUEST RATE LIMIT

The request frequency of this API endpoint is limited to `150 times/3s` for each account


### Parameters
Parameters | Type | Mandatory | Description | 
--------- | ------- | -----------| -----------| 
clientOid | String | Yes | Path parameter，an identifier created by the 
symbol | String | Yes | Trading pair such as `ETH-BTC` |

### Return Value
Field | Description | 
--------- | ------- | 
orderId | order Id       |
originSize | original order size     |
originFunds | Order original funds - market order |
dealSize | deal size      |
remainSize | remain size       |
canceledSize | Cumulative number of cancellations     |
status | Order Status. open：order is active; done：order has been completed |

```json
//response
 {
    "orderId": "641d67ea162d47000160bfb8",
    "originSize": "1",
    "dealSize": "0",
    "remainSize": "1",
    "canceledSize": "0",
    "status": "done"
}
```

<aside class="spacer4"></aside>

## Cancel specified number of orders by orderId
```json
{
    "code": "200000", 
    "data": {
    "orderId": "6d539dc614db3",
    "cancelSize": "10.01"
  }
}
```

This interface can cancel the specified quantity of the order according to the orderId.

### HTTP Request

`DELETE /api/v1/hf/orders/cancel/{orderId}`

### Example

`DELETE /api/v1/hf/orders/cancel/1?symbol=BTC-USDT&cancelSize=10.01`

### API Permissions

This API requires `Trade` permissions


### REQUEST RATE LIMIT
The request frequency of this API is limited to `60 times/3s` for each account


### Parameters

Parameters | Type | Mandatory | Description | 
--------- | ------- | -----------|------|
orderId | String | Yes | Order id of the cancelled order |
symbol | String | Yes | symbol  |
cancelSize | String | Yes | canceled size |

#### Return Value
Field | Description | 
--------- |------|
orderId | Canceled orderId |
cancelSize | canceled size |


<aside class="spacer4"></aside>



## Cancel all HF orders by symbol
```json 
// response
{
    "code": "200000", 
    "data": "success"
}
```

This endpoint allows cancellation of all orders related to a specific trading pair with a status of `open` (including all orders pertaining to high-frequency trading accounts and non-high-frequency trading accounts). If the HTTP status of the interface is `200`, it can be considered that the cancellation request has been submitted successfully.

This endpoint only sends cancellation requests. The results of the requests must be obtained by checking the order status or subscribing to websocket. 

### HTTP Request
`DELETE /api/v1/hf/orders?symbol={symbol}`

### Example
`DELETE /api/v1/hf/orders?symbol=ETH-BTC`

### API Permissions
This API requires `Trade` permissions

### REQUEST RATE LIMIT
The request frequency of this API endpoint is limited to `3 times/3s` for each account

### Parameters
Parameters | Type | Mandatory | Description | 
--------- | ------- | -----------| -----------| 
symbol | String | Yes | Cancel open orders pertaining to the specified trading pair |

### Return Value
Since this interface is a batch cancellation, the http status of the interface is `200`, which means that the cancellation request has been submitted successfully, so no return value is required. The actual cancellation result can be obtained by query order status through API or subscribing to websocket to get the order status.

## Obtain List of Active HF Orders
```json
// response
{
    "code" : "200000",
    "data" : [
        "id": "5c35c02703aa673ceec2a168",
        "symbol": "BTC-USDT",
        "opType": "DEAL",
        "type": "limit",
        "side": "buy",
        "price": "10",
        "size": "2",
        "funds": "0",
        "dealFunds": "0.166",
        "dealSize": "2",
        "fee": "0",
        "feeCurrency": "USDT",
        "stp": "",
        "timeInForce": "GTC",
        "postOnly": false,
        "hidden": false,
        "iceberg": false,
        "visibleSize": "0",
        "cancelAfter": 0,
        "channel": "IOS",
        "clientOid": "",
        "remark": "",
        "tags": "",
        "active": true,
        "inOrderBook": true,
        "cancelExist": false,
        "createdAt": 1547026471000,
        "lastUpdatedAt": 1547026471001,
        "tradeType": "TRADE"
        }        
    ]
}
```
This endpoint obtains a list of all active HF orders. The return data is sorted in descending order based on the latest update times.


### HTTP Request
`GET /api/v1/hf/orders/active`

### Example
`GET /api/v1/hf/orders/active?symbol=BTC-ETH`

### API Permissions
This API requires `General` permissions

### REQUEST RATE LIMIT
The request frequency of this API is limited to `30 times/3s` for each account

### Parameters
Parameters | Type | Mandatory | Description | 
--------- | ------- | -----------| -----------| 
symbol | String | Yes | Only returns order information for the specified trading pair 

### Return Value
Field | Description | 
--------- | ------- | 
id | Order id，a unique identifier pertaining to the order | 
symbol | Trading pair | 
opType | Operation type: DEAL | 
type | Order type | 
side | Buy or sell | 
price | Order price | 
size | Order size | 
funds | Order amount | 
dealFunds | Turnover | 
dealSize | Number of filled transactions | 
fee | Service fee | 
feeCurrency | currency used to calculate fees | 
stp | Self trade protection | 
timeInForce | Time in force | 
postOnly | Is it post only? | 
hidden | Is it a hidden order? | 
iceberg | Is it an iceberg order? | 
visibleSize | Visible size of iceberg order in order book. | 
cancelAfter | A GTT timeInForce that expires in n seconds | 
channel | Source of orders | 
clientOid | Identifier created by the client | 
remark | Order description | 
tags | Order identifier | 
active | Order status: `true`-The status of the order is`active`; `false`-The status of the order is`done` | 
inOrderBook | Whether to enter the orderbook: `true`: enter the orderbook; `false`: not enter the orderbook |  
cancelExist | Are there any cancellation records pertaining to the order? | 
createdAt | order creation time | 
lastUpdatedAt | Last update time of order |
tradeType | Trade type: TRADE (Spot Trading)|

#### Order Polling (Polling)

For high-frequency trading users, we recommend locally caching, maintaining your own order records, and using market data streams to update your order information in real time. 


##Obtain List of symbol with active HF orders

This interface can query all trading pairs that the user has active orders

### HTTP Request

`GET /api/v1/hf/orders/active/symbols`

### Example

`GET /api/v1/hf/orders/active/symbols`

### API Permissions

This API requires `Trade` permissions

### REQUEST RATE LIMIT

The request frequency of this API endpoint is limited to `3 times/3s` for each account

### Parameters
`N/A`


### Return Value
Field | Description | 
--------- | ------- | 
symbols | List of trading pairs with active orders       |


```json
//response
{

    "success": true,
    "code": "200",
    "msg": "success",
    "retry": false,
    "data": {
        "symbols": ["BTC-USDT"]
    }

}

```

<aside class="spacer4"></aside>
<aside class="spacer4"></aside>


## Obtain List of Filled HF Orders
```json
// response
{
   "code":"200000",
   "data":{
      "lastId":2682265600,
      "items":[
         {
            "id":"63074a5a27ecbe0001e1f3ba",
            "symbol":"CSP-USDT",
            "opType":"DEAL",
            "type":"limit",
            "side":"sell",
            "price":"0.1",
            "size":"0.1",
            "funds":"0.01",
            "dealSize":"0",
            "dealFunds":"0",
            "fee":"0",
            "feeCurrency":"USDT",
            "stp":"",
            "timeInForce":"GTC",
            "postOnly":false,
            "hidden":false,
            "iceberg":false,
            "visibleSize":"0",
            "cancelAfter":0,
            "channel":"API",
            "clientOid":"",
            "remark":"",
            "tags":"",
            "cancelExist":true,
            "createdAt":1661422170924,
            "lastUpdatedAt":1661422196926,
            "tradeType":"TRADE",
            "inOrderBook":false,
            "active":false
         }
      ]
   }
}
```
This endpoint obtains a list of filled HF orders and returns paginated data. The returned data is sorted in descending order based on the latest order update times.


### HTTP Request
`GET /api/v1/hf/orders/done`

### Example
`GET /api/v1/hf/orders/done?symbol=BTC-ETH&type=limit&side=buy`

### API Permissions
This API requires `General` permissions

### REQUEST RATE LIMIT
The request frequency of this API is limited to `30 times/3s` for each account

### Parameters
Parameters | Type | Mandatory | Description | 
--------- | ------- | -----------| -----------| 
symbol | String | Yes | Only returns order information for the specified trading pair | 
side | String | No | `buy` (Buy) or`sell` (Sell)| 
type | String | No | Order type: `limit` (limit order), `market`(market order) | 
startAt | long | No | Start time (ms)，last update(filled) time of the limit order| 
endAt | long | No | End time (ms)，last update(filled) time of limit order| 
lastId | long | No | The id of the last data item from the previous batch，defaults to obtaining the latest data | 
limit | int | No | Default`20`，maximum`100` |


<aside class="notice">You can only obtain data from within a <code>3 * 24</code> hour time range（i.e., from now up to <code>3 * 24</code> hours ago）. If the time range limit is exceeded, the system will default to looking up data from within the <code>3 * 24</code> hour time range.</aside>

<aside class="notice"><code>lastId</code>is used to filter data and paginate. If <code>lastId</code>is not entered, the default is a maximum of 100 returned data items. The return results include<code>lastId</code>，which can be used as a query parameter to look up new data from the next page.</aside>

### Return Value
Field | Description | 
--------- | ------- | 
id | Order id，a unique identifier of the order  | 
symbol | Trading pair | 
opType | Operation type: DEAL | 
type | Order type | 
side | Buy or sell | 
price | Order price | 
size | Order size | 
funds | Order amount | 
dealFunds | Turnover | 
dealSize | Number of filled transactions | 
fee | Service fee | 
feeCurrency | currency used to calculate fees | 
stp | Self trade protection | 
timeInForce | Time in force | 
postOnly | Is it post only? | 
hidden | Is it a hidden order? | 
iceberg | Is it an iceberg order? | 
visibleSize | Visible size of iceberg order in order book. | 
cancelAfter | A GTT timeInForce that expires in n seconds | 
channel | Source of orders | 
clientOid | Identifier created by the client | 
remark | Order description | 
tags | Order identifier | 
active | Order status: `true`-The status of the order is`active`; `false`-The status of the order is`done` | 
inOrderBook | Whether to enter the orderbook: `true`: enter the orderbook; `false`: not enter the orderbook |  
cancelExist | Are there any cancellation records pertaining to the order? | 
createdAt | order creation time | 
lastUpdatedAt | Last update time of order |
tradeType | Trade type: TRADE (Spot Trading)|


## Details of a Single HF Order
```json
// response
{
   "code":"200000",
   "data": {
        "id": "5f3113a1c9b6d539dc614dc6",
        "symbol": "KCS-BTC",
        "opType": "DEAL",
        "type": "limit",
        "side": "buy",
        "price": "0.00001",
        "size": "1",
        "funds": "0",
        "dealFunds": "0",
        "dealSize": "0",
        "fee": "0",
        "feeCurrency": "BTC",
        "stp": "",
        "timeInForce": "GTC",
        "postOnly": false,
        "hidden": false,
        "iceberg": false,
        "visibleSize": "0",
        "cancelAfter": 0,
        "channel": "API",
        "clientOid": "6d539dc614db312",
        "remark": "",
        "tags": "",
        "active": true,
        "inOrderBook": false,
        "cancelExist": false,
        "createdAt": 1547026471000,
        "lastUpdatedAt": 1547026471001,
        "tradeType": "TRADE"
    }
}
```

This endpoint can be used to obtain information for a single HF order using the order id.

### HTTP Request

`GET /api/v1/hf/orders/{orderId}?symbol={symbol}`

### Example

`GET /api/v1/hf/orders/5c35c02703aa673ceec2a168?symbol=ETH-BTC`

### API Permissions

This API requires `General` permissions

### Parameters
Parameters | Type | Mandatory | Description | 
--------- | ------- | -----------| -----------| 
orderId | String | Yes | Path parameter，Order Id unique identifier | 
symbol | String | Yes | Trading pair, such as `ETH-BTC` |

<aside class="notice">If the order is not an active order, you can only get data within the time frame of <code>3 * 24</code> hours (ie: from the current time to <code>3 * 24</code> hours ago)</aside>

#### Return Value
Field | Description | 
--------- | ------- | 
id | Order id，a unique identifier of the order  | 
symbol | Trading pair | 
opType | Operation type: DEAL | 
type | Order type | 
side | Buy or sell | 
price | Order price | 
size | Order size | 
funds | Order amount | 
dealFunds | Turnover | 
dealSize | Number of filled transactions | 
fee | Service fee | 
feeCurrency | currency used to calculate fees | 
stp | Self trade protection | 
timeInForce | Time in force | 
postOnly | Is it post only? | 
hidden | Is it a hidden order? | 
iceberg | Is it an iceberg order? | 
visibleSize | Visible size of iceberg order in order book. | 
cancelAfter | A GTT timeInForce that expires in `n` seconds | 
channel | Source of orders | 
clientOid | Identifier created by the client | 
remark | Order description | 
tags | Order identifier | 
active | Order status: `true`-The status of the order is`active`; `false`-The status of the order is`done` | 
inOrderBook | Whether to enter the orderbook: `true`: enter the orderbook; `false`: not enter the orderbook |  
cancelExist | Are there any cancellation records pertaining to the order? | 
createdAt | order creation time | 
lastUpdatedAt | Last update time of order |
tradeType | Trade type: TRADE (Spot Trading)|


## Obtain details of a single HF order using clientOid 
```json
// response
{
   "code":"200000",
   "data": {
        "id": "5f3113a1c9b6d539dc614dc6",
        "symbol": "KCS-BTC",
        "opType": "DEAL",
        "type": "limit",
        "side": "buy",
        "price": "0.00001",
        "size": "1",
        "funds": "0",
        "dealFunds": "0",
        "dealSize": "0",
        "fee": "0",
        "feeCurrency": "BTC",
        "stp": "",
        "timeInForce": "GTC",
        "postOnly": false,
        "hidden": false,
        "iceberg": false,
        "visibleSize": "0",
        "cancelAfter": 0,
        "channel": "API",
        "clientOid": "6d539dc614db312",
        "remark": "",
        "tags": "",
        "active": true,
        "inOrderBook": false,
        "cancelExist": false,
        "createdAt": 1547026471000,
        "lastUpdatedAt": 1547026471001,
        "tradeType": "TRADE"
    }
}
```
The endpoint can be used to obtain information about a single order using clientOid. If the order does not exist, then there will be a prompt saying that the order does not exist.

### HTTP Request
`GET /api/v1/hf/orders/client-order/{clientOid}?symbol={symbol}`
### Example
`GET /api/v1/hf/orders/client-order/6d539dc614db312?symbol=ETH-BTC`
### API Permissions
This API requires `General` permissions
### REQUEST RATE LIMIT
The request frequency of this API is limited to `30 times/3s` for each account
### Parameters
Parameters  | Type | Mandatory | Description | 
--------- | ------- | -----------| -----------| 
clientOid | String | Yes | Path parameter，an identifier created by the client | 
symbol | String | Yes | Trading pair such as `ETH-BTC` |

<aside class="notice">If the order is not an active order, you can only get data within the time frame of <code>3 * 24</code> hours (ie: from the current time to <code>3 * 24</code> hours ago)</aside>

### Return Value
Field | Description | 
--------- | ------- | 
id | Order id，a unique identifier of the order  | 
symbol | Trading pair | 
opType | Operation type: DEAL | 
type | Order type | 
side | Buy or sell | 
price | Order price | 
size | Order size | 
funds | Order amount | 
dealFunds | Turnover | 
dealSize | Number of filled transactions | 
fee | Service fee | 
feeCurrency | currency used to calculate fees | 
stp | Self trade protection | 
timeInForce | Time in force | 
postOnly | Is it post only? | 
hidden | Is it a hidden order? | 
iceberg | Is it an iceberg order? | 
visibleSize | Visible size of iceberg order in order book. | 
cancelAfter | A GTT timeInForce that expires in `n` seconds | 
channel | Source of orders | 
clientOid | Identifier created by the client | 
remark | Order description | 
tags | Order identifier | 
active | Order status: `true`-The status of the order is`active`; `false`-The status of the order is`done` | 
inOrderBook | Whether to enter the orderbook: `true`: enter the orderbook; `false`: not enter the orderbook |  
cancelExist | Are there any cancellation records pertaining to the order? | 
createdAt | order creation time | 
lastUpdatedAt | Last update time of order |
tradeType | Trade type: TRADE (Spot Trading)|


## HF auto cancel setting

```json
// response
{
    "code": "200000", 
    "data": {
    "currentTime": 1682010526,
    "triggerTime": 1682010531 
  }
}
```

Call this interface to automatically cancel all orders of the set trading pair after the specified time. If this interface is not called again for renewal or cancellation before the set time, the system will help the user to cancel the order of the corresponding trading pair. Otherwise it will not.

Note: The order cancellation delay is between 0 and 10 seconds, and the order will not be canceled in real time. When the system cancels the order, if the transaction pair status is no longer operable to cancel the order, it will not cancel the order

### HTTP Request
`POST /api/v1/hf/orders/dead-cancel-all`

### Example
`POST /api/v1/hf/orders/dead-cancel-all`

### API Permissions
This API requires `Trade` permissions

### REQUEST RATE LIMIT
The request frequency of this API is limited to `1 times/3s` for each account

### Parameters
Parameters | Type | Mandatory | Description
--------- | ------- | -----------| -----------
timeout | Integer| Yes | Auto cancel order trigger setting time, the unit is second. range: timeout=-1 (meaning unset) or 5 <= timeout <= 86400. For example, timeout=5 means that the order will be automatically canceled if no user request is received for more than 5 seconds.
symbols | String | No | List of trading pairs. Separated by commas, empty means all trading pairs

### Return Value
Field | Description 
--------- |----------
currentTime | System current time (in seconds)
triggerTime | Trigger cancellation time (in seconds) 




## HF auto cancel order setting query

```json
// response
 {
    "timeout": 5,
    "symbols": "BTC-USDT",
    "currentTime": 1682010526,
    "triggerTime": 1682010531
}
```

Through this interface, you can query the settings of automatic order cancellation

### HTTP Request
`GET /api/v1/hf/orders/dead-cancel-all/query`

### Example
`GET /api/v1/hf/orders/dead-cancel-all/query`

### API Permissions
This API requires `Trade` permissions

### REQUEST RATE LIMIT
The request frequency of this API is limited to `1 times/3s` for each account

### Parameters
`N/A`

### Return Value
Field | Description    
--------- |------------
timeout | Auto cancel order trigger setting time, the unit is second. range: timeout=-1 (meaning unset) or 5 <= timeout <= 86400
symbols | List of trading pairs. Separated by commas, empty means all trading pairs
currentTime | System current time (in seconds)） 
triggerTime | Trigger cancellation time (in seconds)


# Transaction details

## HF transaction records
```json
// response
{
   "code":"200000",
   "data":{
      "items":[
         {
            "id":2678765568,
            "symbol":"BTC-ETC",
            "tradeId":616179312641,
            "orderId":"6306cf6e27ecbe0001e1e03a",
            "counterOrderId":"6306cf4027ecbe0001e1df4d",
            "side":"buy",
            "liquidity":"taker",
            "forceTaker":false,
            "price":"1",
            "size":"1",
            "funds":"1",
            "fee":"0.00021",
            "feeRate":"0.00021",
            "feeCurrency":"USDT",
            "stop":"",
            "tradeType":"TRADE",
            "type":"limit",
            "createdAt":1661390702919
         }
      ],
      "lastId":2678765568
   }
}
```

This endpoint can be used to obtain a list of the latest HF transaction details. The returned results are paginated. The data is sorted in descending order according to time.

### HTTP Request
`GET /api/v1/hf/fills`

### Example
`GET /api/v1/hf/fills?symbol=BTC-USDT`

### API Permissions
This API requires `General` permissions

### REQUEST RATE LIMIT
The request frequency of this API is limited to `9 times/3s` for each account

### Parameters
Parameters | Type | Mandatory  | Description | 
--------- | ------- | -----------| -----------| 
orderId | String | No | Look up the transaction details pertaining to the order id（If`orderId` is specified，please ignore the other query parameters）| 
symbol | String | Yes | Only returns order information for the specified trading pair | 
side | String | No | `buy`（Buy） or `sell`（Sell）| 
type | String | No | Order type: `limit`（limit order）, `market`(market order) | 
startAt | long | No | Start time（ms），puts a restriction on the transaction(creation) time for the transaction records| 
endAt | long | No | End time（ms），puts a restriction on the transaction(creation) time of the transaction records| 
lastId | long | No | The id of the last data item from the previous batch, defaults to obtaining the latest data | 
limit | int | No | Default`100`，maximum `200` |

<aside class="notice"><code>lastId</code>is used to filter data and paginate. If <code>lastId</code>is not entered, the default is a maximum of 100 returned data items. The return results include<code>lastId</code>，which can be used as a query parameter to look up new data from the next page.</aside>


### Return Value

Field | Description | 
--------- | ------- | 
id | Id of transaction detail |
symbol | Trading pair | 
tradeId | Trade Id | 
orderId | Order Id | 
counterOrderId | Counterparty order Id | 
side | Buy or sell | 
liquidity | Liquidity type: taker or maker | 
forceTaker | Whether or not to forcefully process as taker | 
price | Order price | 
size | Order size | 
funds | Turnover | 
fee | Service fee | 
feeRate | Fee rate | 
feeCurrency | currency used to calculate fees | 
type | Order type: limit or market | 
stop | Take Profit and Stop Loss type, currently HFT does not support the Take Profit and Stop Loss type, so it is empty |
createdAt | Transaction(Creation) time | 
tradeType | Trade type: `TRADE`(Spot Trading)|

##### Show time range

You can only obtain data from within a `3 * 24` hour time range（i.e., from now up to `3 * 24` hours ago）. If the time range limit is exceeded, the system will default to looking up data from within the `3 * 24` hour time range.

##### Settlement
Settlement is divided into two parts: transaction settlement and fee settlement. When matching is complete, the data is immediately updated into our data storage and the system initiates the settlement to deduct funds from the funds that are on hold.

##### Handling fees

Orders on the KuCoin platform are divided into two types: Taker and Maker. Taker orders can be filled with existing orders in the order book. On the contrary, Maker orders will stay in the order book and await matching. Taker orders decrease market liquidity and therefore charge a taker fee. Maker orders increase market liquidity and therefore only charge a lower handling fee or may even pay out rewards. Note: market orders, iceberg orders, and hidden orders all charge taker fees.

When an order is placed, the system puts a hold on a portion of the funds in your account for paying the taker fee. Parameter values in the liquidity field determine whether taker fees or maker fees will be charged for the order.

If your order is a limit order, when the order is placed it will immediately be matched in the matching engine and we will charge a taker fee. If your order is not immediately matched or is only partially matched, the remaining portions will enter the order book. Maker fees will be charged for orders that have entered the order book before they are cancelled or filled.

After entering the matching stage, matching will be conducted with counterparty orders. When the remaining amount for the order is 0，the transaction is complete. If the remaining funds are not enough to purchase the minimum amount（0.00000001）, the order is cancelled.

If your order is filled as a maker, we will refund the remaining amount of withheld taker fees. 

Please note:

- When you create a hidden/iceberg order, even if it is not immediately matched by the matching engine and is filled as post only, a taker fee will still be charged.
- Maker fees will be charged for post only orders. If the order is immediately matched with existing orders (with the exception of iceberg/hidden orders) on the market after order placement, then the order will be cancelled.  If a post only order is immediately filled with iceberg/hidden orders, a maker fee will be charged for the post only order. 

<b>For Example:</b>

Using BTC/USDT as an example, if you want to 1BTC at market price，the handling fee would be 0.1%，with the market order book data as follows：

Price（USDT）| Size（BTC）| Side | 
--------- | ------- | ------- | 
4200.00 | 0.18412309 | sell | 
4015.60 | 0.56849308 | sell | 
4011.32 | 0.24738383 | sell | 
3995.64 | 0.84738383 | buy | 
3988.60 | 0.20484000 | buy | 
3983.85 | 1.37584908 | buy |

When you place a buy market order, it will be filled immediately, and the transaction detailed will show 3 transactions, as shown below:

Price（USDT）| Size（BTC）| Fee（BTC）| 
--------- | ------- | ------- | 
4011.32 | 0.24738383 | 0.00024738 | 
4015.60 | 0.56849308 | 0.00056849 | 
4200.00 | 0.18312409 | 0.00018312 |

# Websocket

While there is a strict access frequency control for REST API, we highly recommend that API users utilize Websocket to get the real-time data. For Websocket related settings, please refer to the documentation `https://docs.kucoin.com/#websocket-feed`

# Public Channels
please refer to the documentation `https://docs.kucoin.com/#public-channels`

# Private Channels

## HF Account Balance Notice
```json
```
Topic: `/account/balance`

* Push frequency: `real-time`

You will receive this message when an HF account balance changes. The message contains the details of the change.

Relation Event

類型 | 描述
--------- | -------
| trade_hf.hold | Hold (high frequency account) |
| trade_hf.setted | Settlement (high frequency account) |
| trade_hf.transfer | Transfer (high frequency account) |
| trade_hf.other | Other operations (high frequency account) |

## Private HF Order Change Events
please refer to the documentation `https://docs.kucoin.com/#private-order-change-events`
or `https://docs.kucoin.com/#private-order-change-v2`

## Other
Other parts please refer to the documentation `https://docs.kucoin.com/#private-channels`