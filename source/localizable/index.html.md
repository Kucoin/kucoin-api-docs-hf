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

Welcome to KuCoin’s developer documentation for high-frequency trading. This document provides an overview of high-frequency trading features, the high-frequency trading market, as well as APIs.

KuCoin API: **REST API**

- There are three types of REST APIs: **User (private), Trading (private) and Market Data (public)**



## Update Preview


# Account

## Create an Account
```json
{
    "id":"5bd6e9286d99522a52e458de" //accountId
}
```

This API can be used to create an account.

### HTTP Request
`POST /api/v1/accounts`

### API Permissions
This API requires `General` permissions

### Parameters
Parameters | Data Type | Compulsory? | Description 
--------- | ------- | -----------| -----------| 
type | String | Yes | Account type:`MAIN`, `TRADE`, `MARGIN`, or `TRADE_HF`
currency | String | Yes | currency

### Return Value
Field | Description 
--------- | ------- 
id | Account ID -- accountId



## List Accounts
```json
[
    {
        "id":"5bd6e9286d99522a52e458de", //accountId
        "currency":"BTC",  //currency
        "type":"TRADE_HF", //Account type, high-frequency trading (trade_hf)
        "balance":"237582.04299",//Total funds
        "available":"237582.032", //Amount available
        "holds":"0.01099" //Amount frozen
    },
    {
        "id":"5bd6e9216d99522a52e458d6",
        "currency":"BTC",
        "type":"trade_hf",
        "balance":"1234356",
        "available":"1234356",
        "holds":"0"
    }
]
```

List Account Details

Before making a trade, first deposit funds into the main account, and then transfer funds into the high-frequency trading account via internal funds transfer.

### HTTP Request
`GET /api/v1/accounts`

### API Permissions
This API requires `General` permissions


### Parameters
Parameters | Type | Compulsory? | Description 
| --------- | ------- | -----------| -----------| 
currency | String | No | currency 
type | String | No | Account type:`MAIN`, `TRADE`, `MARGIN`, or `TRADE_HF`

### Return Value
Field | Description 
--------- | ------- 
id | accountId Account ID 
currency | The currency the account is associated with 
type |Account type , `MAIN` (main account) , `TRADE` (trading account), `MARGIN` (margin account), `TRADE_HF` (high-frequency trading account) balance | Total funds 
available | Available funds 
holds | Funds frozen

## Get Transferrable Funds
```json
{
    "currency":"KCS",
    "balance":"0",
    "available":"0",
    "holds":"0",
    "transferable":"0"
}
```
This API can be used to obtain the amount of transferrable funds pertaining to the specified currency for the specified account.

### HTTP Request
`GET /api/v1/accounts/transferable`

### API Permissions
This API requires `General` permissions

### Frequency Limits
This API limits request frequencies to `18 times/3s` for all accounts.

### Parameters

Parameters | Type | Compulsory? | Description |
--------- | ------- | -----------| -----------| 
currency | String | No | currency | 
type | String | No | Account type: `MAIN`, `TRADE`, `MARGIN`, `TRADE_HF` |


### Return Value
Field | Description 
--------- | ------- 
currency | currency 
balance | Total funds 
available | Available funds 
holds | Funds on hold 
transferable | Transferrable funds


## Account Ledger
```json
[
    {
        "id": 12,//Unique key
        "currency": "USDT", //currency
        "amount": "10.00059547", //Amount of change in funds
        "fee": "0", //Deposit for withdrawal fee
        "balance": "0", //Change in balance
        "accountType": "TRADE_HF", //Account type
        "bizType": "TRADE_EXCHANGE", //Transaction type
        "direction": "in", //Direction of transfer (in or out)
        "createdAt": 1629101692950, //Created
        "context": "{\"symbol\":\"1001\",\"orderId\":\"611a1e7cc913d000066cf7ec\",\"tradeId\":\"283742\"}" //Business core parameters
    },
    {
        "id": 10,
        "currency": "USDT",
        "amount": "10.00059547",
        "fee": "0",
        "balance": "0",
        "accountType": "TRADE_HF",
        "bizType": "TRADE_EXCHANGE",
        "direction": "in",
        "createdAt": 1629101692950,
        "context": "{\"symbol\":\"1001\",\"orderId\":\"611a1e7cc913d000066cf7ec\",\"tradeId\":\"283742\"}"
    }
]
```
This API endpoint returns all transfer (in and out) records for the account and supports multi-coin queries. Get Parameter

### HTTP Request
`GET /api/v1/accounts/ledgers/hf`

### API Permissions
This API requires `General` permissions

### Frequency Limits
This API limits request frequencies to `18 times/3s` for all accounts.

### Parameters
Parameters | Type | Compulsory? | Description | 
--------- | ------- | -----------| -----------| 
currency | String | No | currency, optional，can select more than one，separate with commas，select no more than `10` currencys，the default will be to query for all currencys if left empty | 
direction | String | No | Direction of transaction (in or out): `in`-transfer in, `out`-transfer out | 
bizType | String | No | Transaction type: `TRANSFER`-transfer funds,`TRADE_EXCHANGE`-Trade | 
lastId | long | No | The id of the last set of data from the previous batch of data. By default, the latest information is given. | 
limit | int | No | Default`100`，Max`200` | 
startAt | long | No | Start time (ms)| 
endAt | long | No | End time (ms)|

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
bizType | Trnasaction type，such as trade, withdraw，referral bonus, loan, etc. | 
direction | Direction of transfer( `out` or `in`) | 
createdAt | Created | 
context | Core transaction parameter |

- `context` description
    * If the `bizType` is `TRADE_EXCHANGE`, the context field will include additional transaction information (order id, transaction id, and trading pair).

## Internal Funds Transfers
```json
{
    "orderId":"5bd6e9286d99522a52e458de"
}
```

This API endpoint can be used to transfer funds between accounts internally. Users can transfer funds between their main account, trading account, margin account, and high-frequency trading account free of charge. **Transfer of funds from other accounts to futures accounts is supported, but transfer of funds from futures accounts to other accounts is not supported.**

### HTTP Request
`POST /api/v2/accounts/inner-transfer`

### API Permissions
This API requires `Trade` permissions

### Parameters
Parameters | Type | Compulsory? | Description | 
--------- | ------- | -----------| -----------| 
clientOid | String | Yes | Client Order Id，a unique identifier created by the user, using UUID is recommended | 
currency | String | Yes | currency | 
from | String | Yes | Payment account type: `MAIN`, `TRADE`, `MARGIN`, `TRADE_HF` 
to | String | Yes | Receiving account type: `MAIN`, `TRADE`, `MARGIN`, `TRADE_HF`, `CONTRACT` 
amount | String | Yes | Transfer amount, the precision is the precision of the currency multiplied by a positive integer |

### Return Value
Field | Description 
--------- | ------- 
orderId | Order ID for internal funds transfer

# Trade

The following requires signature verification.


# Order

## Order placement
```json
{
    "orderId":"5bd6e9286d99522a52e458de"
}
```

There are two types of orders: 
(`limit`) order: set price and quantity for the transaction. 
(`market`) order : set amount or quantity for the transaction.

Before placing an order, make sure there are enough funds in your high-frequency trading account. Once the order is successfully placed, the amount of funds required for the order will be frozen. The amount frozen will depend on the type of order placed as well as the request parameters set.

Please note that once your order enters the order book, the system will freeze the handling fees for the order ahead of time.

Before placing orders, please be sure to fully understand the meaning of the parameters for each trading pair.

<aside class="notice">For each account, the maximum number of <strong>ACTIVE</strong> orders is <code>200</code> (including stop-loss orders) for each trading pair.</aside>


### HTTP Request
`POST /api/v1/orders/hf`

### API Permissions
This API requires `Trade` permissions

### Frequency Limits
The request frequency for this API is limited to `45 times/3s` for each account

### Parameters

Public order placement request parameters

Parameters | Type | Compulsory? | Description |
 --------- | ------- | -----------| -----------| 
 clientOid | String | No | Client Order Id，unique identifier created by the user, the use of UUID is recommended | 
 symbol | String | Yes | Trading pair, such as, `ETH-BTC` | 
 type | String | Yes | Order type `limit` and `market` | 
 side | String | Yes | `buy` or `sell` | 
 stp | String | No | Self trade prevention (`self trade prevention`) is divided into four strategies: `CN`, `CO`, `CB` , and `DC` | 
 tags | String | No | Order tag, cannot exceed `20` characters (UTF-8) in length| 
 remark | String | No | Order placement remarks, length cannot exceed `20` characters (UTF-8) in length|

#### Additional Request Parameters Required by `limit` Orders

Parameters | Type | Compulsory? | Description | 
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
Parameters | Type | Compulsory? | Description | 
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
The type of order you specify when you place your order determines whether or not you need to request other parameters and also affects the execution of the matching engine. If you do not specify an order type when placing the order, the system will default to a limit order.

When placing a limit order, you must specify a price and size. The system will try to match the order according to market price or a price better than market price. If the order cannot be immediately matched, it will stay in the order book until it is matched or the user cancels.

Unlike limit orders, the price for market orders fluctuates with market prices. When placing a market order, you do not need to specify a price, you only need to specify a quantity. Market orders are filled immediately and will not enter the order book. All market orders are takers and a taker fee will be charged.

#### Trade Type (tradeType)
The platform currently supports spot （TRADE） asset trading orders. Based on your parameter types, the system will freeze funds in the specified accounts. If that parameter is not set, the system will default to freezing funds in your trading account according to spot trading rules.

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

- Note: order fills include self-fills.

#### Post Only (PostOnly)
PostOnly is just a label. If an order can be immediately filled, then it is cancelled. \* When the user places a postOnly order, if the order encounters an iceberg order or hidden order after entering the matching engine, the order can be filled immediately. The postOnly order will charge maker fees and the iceberg order and hidden order will charge taker fees.

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
When an order placement request is successful (the matching engine receives the order) or denied (due to there being insufficient funds or illegal parameter values, etc.), the system will respond to the HTTP request. When an order is successfully placed, the order ID is returned. The order will be matched, which could result in it being fully or partially filled. When an order is partially filled, the remaining portions of the order will be in an active state awaiting to be matched (this does not include IOC orders). Orders that are fully filled will be put into the “done” state.

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
| orderId | An order Id is returned once an order is successfully placed. The `orderId` field，represents that the order has entered the matching engine. |

## Batch Order Placement
```json
//request
{
    "symbol": "KCS-USDT",
    "orderList": [
        {
            "clientOid": "3d07008668054da6b3cb12e432c2b13a",
            "side": "buy",
            "type": "limit",
            "price": "0.01",
            "size": "0.01"
        },
        {
            "clientOid": "37245dbe6e134b5c97732bfb36cd4a9d",
            "side": "buy",
            "type": "limit",
            "price": "0.01",
            "size": "0.01"
        }
    ]
}
```

```json
//response
{
    "data": [
        {
            "symbol": "KCS-USDT",
            "type": "limit",
            "side": "buy",
            "price": "0.01",
            "size": "0.01",
            "funds": null,
            "stp": "",
            "timeInForce": "GTC",
            "cancelAfter": 0,
            "postOnly": false,
            "hidden": false,
            "iceberge": false,
            "iceberg": false,
            "visibleSize": null,
            "channel": "API",
            "id": "611a6a309281bc000674d3c0",
            "status": "success",
            "failMsg": null,
            "clientOid": "552a8a0b7cb04354be8266f0e202e7e9"
        },
        {
            "symbol": "KCS-USDT",
            "type": "limit",
            "side": "buy",
            "price": "0.01",
            "size": "0.01",
            "funds": null,
            "stp": "",
            "timeInForce": "GTC",
            "cancelAfter": 0,
            "postOnly": false,
            "hidden": false,
            "iceberge": false,
            "iceberg": false,
            "visibleSize": null,
            "channel": "API",
            "id": "611a6a309281bc000674d3c1",
            "status": "success",
            "failMsg": null,
            "clientOid": "bd1e95e705724f33b508ed270888a4a9"
        }
    ]
}
```

This endpoint supports batch order placement from a single endpoint. A maximum of `5`orders can be placed simultaneously. The order types must be limit orders of the same trading pair （this endpoint currently only supports spot trading and does not support margin trading）.

This endpoint only supports order placement requests. To obtain the results of the order placement, you will need to check the order status or subscribe to websocket to obtain information about he order. 

### HTTP Request
`POST /api/v1/orders/hf/multi`

### API Permissions

This API requires `Trade` permissions

### Frequency Limits
The request frequency of this API endpoint is limited to `3 times/3s` for each account

### Parameters

Parameters | Type | Compulsory?  | Description | 
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
tags | String | No | \[Optional] The order identifier length cannot exceed `20` characters（UTF-8）| 
remark | String | No | \[Optional] Order placement remarks cannot exceed a length of `20` characters（UTF-8）|


### Return Value
| Field | Description | 
| -------| --------------------------- | 
|status | Order creation results (`success`, `fail`) | |fail
Msg | Reason of failure |


## Single Order Cancellation
```json
{
    "cancelledOrderIds":[
        "5bd6e9286d99522a52e458de"
    ]
}
```

This endpoint can be used to cancel a single order.

### HTTP Request
`DELETE /api/v1/orders/hf/{orderId}?symbol={symbol}`

### API Permissions
This API requires `Trade` permissions

### Frequency Limits
The request frequency of this API is limited to `60 times/3s` for each account

### Parameters
Parameters | Type | Compulsory? | Description | 
--------- | ------- | -----------| -----------| 
orderId | String | Yes | Path parameter，Order Id unique identifier | 
symbol | String | Yes | Trading pair, such as `ETH-BTC` |

<aside class="notice">The input parameter orderId is a unique identifier of the order created by the server and is different from the clientOId created by the user.</aside>

<aside class="notice">If an order cannot be cancelled （if it has already been filled or cancelled），an error message will be returned, and you can judge what the reason was based on the returned <code>msg</code>.</aside>

### Return Value

| Field | Description | 
| ----------------- | ------- | 
| cancelledOrderIds | Order id of the cancelled order |


## Cancel Single Order Using clientOid 
```json
{
    "clientOid": "6d539dc614db3"
}
```

This endpoint sends out a request to cancel an order using clientOid.

### HTTP Request
`DELETE /api/v1/orders/hf/client-order/{clientOid}?symbol={symbol}`
### API Permissions
This API requires `Trade` permissions
### Parameters
Parameters  | Type | Compulsory? | Description | 
--------- | ------- | -----------| -----------| 
clientOid | String | Yes | Path parameter，an identifier created by the 
client | symbol | String | Yes | Trading pair such as `ETH-BTC` |
#### Return Value
Field | Description | 
--------- | ------- | 
clientOid | Identifier created by the client |

## Cancel All Orders
```json
{
    "cancelledOrderIds":[
        "5c52e11203aa677f33e493fb",
        "5c52e12103aa677f33e493fe",
        "5c52e12a03aa677f33e49401",
        "5c52e1be03aa677f33e49404",
        "5c52e21003aa677f33e49407",
        "5c6243cb03aa67580f20bf2f",
        "5c62443703aa67580f20bf32",
        "5c6265c503aa676fee84129c",
        "5c6269e503aa676fee84129f",
        "5c626b0803aa676fee8412a2"
    ]
}
```

This endpoint allows cancellation of all high-frequency orders related to a specific trading pair with a status of `open`, the return value is a list of the IDs of the cancelled orders.

This endpoint only sends cancellation requests. The results of the requests must be obtained by checking the order status or subscribing to websocket. 

### HTTP Request
`DELETE /api/v1/orders/hf?symbol={symbol}`
### API Permissions
This API requires `Trade` permissions
### Frequency Limits
The request frequency of this API endpoint is limited to `3 times/3s` for each account
### Parameters
Parameters | Type | Compulsory? | Description | 
--------- | ------- | -----------| -----------| 
symbol | String | Yes | Cancel open orders pertaining to the specified trading pair |
### Return Value
| Field | Description | 
| ----------------- | ------- | 
| cancelledOrderIds | Order id of the cancelled order |

## Obtain List of Active Orders
```json
{
    "currentPage": 1,
    "pageSize": 1,
    "totalNum": 153408,
    "totalPage": 153408,
    "items": [
        {
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
            "stop": "",
            "stopTriggered": false,
            "stopPrice": "0",
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
            "isActive": false,
            "cancelExist": false,
            "createdAt": 1547026471000,
            "tradeType": "TRADE"
        }
    ]
 }
```

This endpoint obtains a list of all active orders. The return data is sorted in descending order based on the latest update times.


### HTTP Request
`GET /api/v1/orders/hf/active`

### API Permissions
This API requires `General` permissions

### Frequency Limits
The request frequency of this API is limited to `30 times/3s` for each account

### Parameters
Parameters | Type | Compulsory? | Description | 
--------- | ------- | -----------| -----------| 
symbol | String | Yes | Only returns order information for the specified 
trading pair | side | String | No | \[Optional] `buy` or `sell`| 
startAt | long | No | \[Optional] Start time (ms)，latest update time of limit orders| 
endAt | long | No | \[Optional] End time (ms), latest update time of limit order|

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
isActive | Order status: `true`-The status of the order is`open`; `false`-the order has been filled or cancelled | 
cancelExist | Are there any cancellation records pertaining to the order? | 
createdAt | Creation time | 
tradeType | Trade type: TRADE (Spot Trading)|

#### Order Status and Settlement

All orders in the order book are in the “active” state. Orders removed from the order book are put in the “done” state.

There could be a delay in the order of milliseconds from the time an order is filled and the time funds are credited due to the system conducting settlement operations.

There are no time limits for looking up orders in the “active” statue. However, when looking up orders in the “completed” state, you can only obtain data from within  the `3 * 24` hour time range. (i.e., `3 * 24` hours ago to the current time). If the time range limit is exceeded, the system will default to looking up data from within the `3 * 24` hour time range.

Records for cancelled orders are only kept for `3`days. You will not be able to look up orders cancelled more than `3` days ago.

#### Order Polling (Polling)

For high-frequency trading users, we recommend locally caching, maintaining your own order records, and using market data streams to update your order information in real time. 

## Obtain List of Filled Orders
```json
{
    "currentPage": 1,
    "pageSize": 1,
    "totalNum": 153408,
    "totalPage": 153408,
    "items": [
        {
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
            "stop": "",
            "stopTriggered": false,
            "stopPrice": "0",
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
            "isActive": false,
            "cancelExist": false,
            "createdAt": 1547026471000,
            "tradeType": "TRADE"
        }
    ]
 }
```

This endpoint obtains a list of filled orders and returns paginated data. The returned data is sorted in descending order based on the latest order update times.


### HTTP Request
`GET /api/v1/orders/hf/done`

### API Permissions
This API requires `General` permissions

### Frequency Limits
The request frequency of this API is limited to `30 times/3s` for each account

### Parameters
Parameters | Type | Compulsory? | Description | 
--------- | ------- | -----------| -----------| 
symbol | String | Yes | Only returns order information for the specified trading pair | 
side | String | No | \[Optional] `buy` (Buy) or`sell` (Sell)| 
type | String | No | \[Optional] Order type: `limit` (limit order), `market`(market order) | 
startAt | long | No | \[Optional] Start time (ms)，last update time of the limit order| 
endAt | long | No | \[Optional] End time (ms)，last update time of limit order| 
lastId | long | No | \[Optional] The id of the last data item from the previous batch，defaults to obtaining the latest data | 
limit | int | No | \[Optional] Default`100`，maximum`200` |


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
isActive | Order status `true`: the status of the order is open; `false`: the order has been filled or cancelled | 
cancelExist | Are there any cancellation records pertaining to the order? | 
createdAt | Creation time | 
tradeType | Trade type: TRADE (Spot Trading)|


## Details of a Single Order
```json
{
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
    "isActive": false,
    "cancelExist": false,
    "createdAt": 1547026471000,
    "tradeType": "TRADE"
}
```

This endpoint can be used to obtain information for a single order using the order id.

### HTTP Request
`GET /api/v1/orders/hf/{orderId}?symbol={symbol}`

### API Permissions
This API requires `General` permissions
### Parameters
Parameters | Type | Compulsory? | Description | 
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
isActive | Order status `true`: the status of the order is open; `false`: the order has been filled or cancelled | 
cancelExist | Are there any cancellation records pertaining to the order? | 
createdAt | Creation time | 
tradeType | Trade type: `TRADE`(Spot Trading)|


## Obtain details of a single order using clientOid 
```json
{
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
    "isActive": true,
    "cancelExist": false,
    "createdAt": 1597051810000,
    "tradeType": "TRADE"
}
```

The endpoint can be used to obtain information about a single order using clientOid. If the order does not exist, then there will be a prompt saying that the order does not exist.

### HTTP Request
`GET /api/v1/orders/hf/client-order/{clientOid}?symbol={symbol}`
### API Permissions
This API requires `General` permissions
### Frequency Limits
The request frequency of this API is limited to `30 times/3s` for each account
### Parameters
Parameters  | Type | Compulsory? | Description | 
--------- | ------- | -----------| -----------| 
clientOid | String | Yes | Path parameter，an identifier created by the client | 
symbol | String | Yes | Trading pair such as `ETH-BTC` |

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
isActive | Order status `true`: the status of the order is open; `false`: the order has been filled or cancelled | 
cancelExist | Are there any cancellation records pertaining to the order? | 
createdAt | Creation time | 
tradeType | Trade type: `TRADE` (Spot Trading)|

# Transaction details

## Transaction records
```json
{
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
    "isActive": true,
    "cancelExist": false,
    "createdAt": 1597051810000,
    "tradeType": "TRADE"
}
```

This endpoint can be used to obtain a list of the latest transaction details. The returned results are paginated. The data is sorted in descending order according to time.

### HTTP Request
`GET /api/v1/fills/hf`

### API Permissions
This API requires `General` permissions

### Frequency Limits
The request frequency of this API is limited to `9 times/3s` for each account

### Parameters
Parameters | Type | Compulsory?  | Description | 
--------- | ------- | -----------| -----------| 
orderId | String | No | \[Optional] Look up the transaction details pertaining to the order id（If`orderId` is specified，please ignore the other query parameters）| 
symbol | String | No | \[Optional] Only returns order information for the specified trading pair, if `orderId`is specified，then this parameter is required | 
side | String | No | \[Optional] `buy`（Buy） or `sell`（Sell）| 
type | String | No | \[Optional] Order type: `limit`（limit order）, `market`(market order) | 
startAt | long | No | \[Optional] Start time（ms），puts a restriction on the transaction time for the transaction records| 
endAt | long | No | \[Optional] End time（ms），puts a restriction on the transaction time of the transaction records| 
lastId | long | No | \[Optional] The id of the last data item from the previous batch, defaults to obtaining the latest data | 
limit | int | No | \[Optional] Default`100`，maximum `200` |

<aside class="notice"><code>lastId</code>is used to filter data and paginate. If <code>lastId</code>is not entered, the default is a maximum of 100 returned data items. The return results include<code>lastId</code>，which can be used as a query parameter to look up new data from the next page.</aside>


### Return Value

Field | Description | 
--------- | ------- | 
symbol | Trading pair | 
tradeId | Trade Id | 
orderId | Order Id | 
counterOrderId | Counterparty order Id | 
side | Buy or sedd | 
forceTaker | Whether or not to forcefully process as taker | 
liquidity | Liquidity type: taker or maker | 
price | Order price | 
size | Order size | 
funds | Turnover | 
fee | Service fee | 
feeRate | Fee rate | 
feeCurrency | currency used to calculate fees | 
type | Order type: limit or market | 
createdAt | Creation time | 
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