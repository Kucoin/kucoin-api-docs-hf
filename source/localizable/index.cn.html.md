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

# 快速開始

## 簡介

歡迎使用KuCoin開發者高頻交易文檔。 此文檔概述了高頻交易功能等應用開發接口。

KuCoin API：**REST API**

- REST API 包含三個類別：**用戶（私有）、交易（私有）、市場數據（公共）**



## 更新預告

**11/08/22**:

- 【廢棄】廢棄`POST /api/v1/accounts`接口

**10/18/22**:

- 【修改】`GET /api/v1/hf/orders/done`接口中`limit`請求參數修改爲：默認20，最大100

# 用戶模塊

# 賬戶

## 高頻交易賬戶內部資金劃轉
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
用戶可以將資金在儲蓄賬戶、交易賬戶和高頻交易賬戶之間免費劃轉。

### HTTP請求
`POST /api/v2/accounts/inner-transfer`

### 請求示例
`POST /api/v2/accounts/inner-transfer`

### API權限
此接口需要`交易權限`。

### 請求參數
請求參數 | 類型 | 是否必須 | 含義 |
--------- | ------- | -----------| -----------|
clientOid | String | 是 | Client Order Id，客戶端創建的唯一標識，建議使用UUID |
currency | String | 是 | 幣種 |
from | String | 是 | 付款賬戶類型：`main`(儲蓄賬戶)、`trade`(交易賬戶)、`trade_hf`(高頻交易賬戶) |
to | String | 是 | 收款賬戶類型：`main`(儲蓄賬戶)、`trade`(交易賬戶)、`trade_hf`(高頻交易賬戶) |
amount | String | 是 | 轉賬金額，精度爲幣種精度正整數倍 |

### 返回值
字段 | 含義
--------- | -------
orderId | 內部資金劃轉的訂單ID

## 高頻交易賬戶列表
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
獲取高頻交易賬戶列表。

### HTTP請求
`GET /api/v1/accounts`

### 請求示例
`GET /api/v1/accounts?currency=USDT&type=trade_hf`

### API權限
此接口需要`通用權限`。

### 請求參數
請求參數 | 類型 | 是否必須 | 含義 |
--------- | ------- | -----------| -----------|
currency | String | 否 | 幣種
type | String | 否 | 賬戶類型:`trade_hf`（高頻交易賬戶）

### 返回值
字段 | 含義
--------- | -------
id | accountId 賬戶ID
currency | 賬戶對應的幣種
type |賬戶類型 ，trade_hf（高頻交易賬戶）
balance | 賬戶資金總額
available | 賬戶可用的資金
holds | 賬戶凍結的資金

## 單個高頻交易賬戶詳情
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
獲取單個高頻交易賬戶詳情。

### HTTP請求
`GET /api/v1/accounts/{accountId}`

### 請求示例
`GET /api/v1/accounts/2051232768`

### API權限
此接口需要`通用權限`。

### 請求參數
請求參數 | 類型 | 是否必須 | 含義 |
--------- | ------- | -----------| -----------|
accountId | String | 是 | 路徑參數，高頻交易賬戶Id

### 返回值
字段 | 含義
--------- | -------
currency | 賬戶對應的幣種 |
balance | 賬戶資金總額 |
available | 賬戶可用的資金 |
holds | 賬戶凍結的資金 |

## 高頻交易賬戶可劃轉的資金

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
獲取高頻交易賬戶可劃轉的資金。

### HTTP請求
`GET /api/v1/accounts/transferable`

### 請求示例
`GET /api/v1/accounts/transferable?currency=USDT&type=TRADE_HF`

### API權限
此接口需要`通用權限`。

### 頻率限制
此接口針對每個賬號請求頻率限制爲`18次/3s`

### 請求參數

請求參數 | 類型 | 是否必須 | 含義 |
--------- | ------- | -----------| -----------|
currency | String | 否 | 幣種 |
type | String | 否 | 賬戶類型: `TRADE_HF`|


### 返回值
字段 | 含義
--------- | -------
currency | 幣種
balance | 資金總額
available | 可用資金
holds | 凍結資金
transferable | 可劃轉資金


## 高頻交易賬戶流水記錄
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
獲取高頻交易賬戶的出入賬流水記錄，支持多幣種查詢。查詢結果按流水創建時間和Id降序排序。

### HTTP請求
`GET /api/v1/hf/accounts/ledgers`

### 請求示例
`GET /api/v1/hf/accounts/ledgers?bizType=TRADE_EXCHANGE&currency=YOP,DAI&startAt=1601395200000`

### API權限
此接口需要`通用權限`。

### 頻率限制
此接口針對每個賬號請求頻率限制爲`18次/3s`

### 請求參數
請求參數 | 類型 | 是否必須 | 含義 |
--------- | ------- | -----------| -----------|
currency | String | 否 | 幣種，選填，可多選，以逗號分隔，最多支持選擇`10`個幣種，若不填寫，默認查詢所有幣種 |
direction | String | 否 | 出入賬方向: `in`-入賬, `out`-出賬 |
bizType | String | 否 | 業務類型: `TRANSFER`-轉賬,`TRADE_EXCHANGE`-交易 |
lastId | long | 否 | 前一批次數據最後一條數據的id。 默認獲取最新流水。|
limit | int | 否 | 默認`100`，最大`200` |
startAt | long | 否 | 開始時間（毫秒），限制流水創建時間|
endAt | long | 否 | 截止時間（毫秒），限制流水創建時間|

<aside class="notice">如果設定<code>lastId</code>，獲取流水id < lastId，否則返回最近流水。lastId的值取流水id，可以從返回結果裏獲取。

您只能獲取<code>3 * 24</code>小時時間範圍內的數據（即：從當前時間起至<code>3 * 24</code>小時前）。若超出時間範圍，系統會默認查詢<code>3 * 24</code>小時時間範圍內的數據。</aside>

#### 返回值
字段 | 含義 |
--------- | ------- |
id | 唯一鍵 |
currency | 幣種 |
amount | 資金變動值 |
fee | 充值或提現費率 |
balance | 變動後的資金總額 |
accountType | 母賬號賬戶類型`TRADE_HF` |
bizType | 業務類型，比如`TRANSFER`-轉賬，`TRADE_EXCHANGE` -交易 |
direction | 出入賬方向 `out` 或 `in` |
createdAt | 創建時間 |
context | 業務核心參數 |

- `context`說明
    * 如果 `bizType` 是`TRADE_EXCHANGE`，那麼 context 字段會包含交易的額外信息（訂單id，交易id，交易對）。

# 交易模塊

以下請求需要校驗簽名。


# 訂單

## 高頻交易下單
```json
// response
{
    "code": "200000",
    "data": {
        "orderId": "5bd6e9286d99522a52e458de"
    }
}
```

訂單有兩種類型： 限價單（`limit`）: 指定價格和數量進行交易。 市價單(`market`) : 指定資金或數量進行交易。

在下單前，請確保您的高頻交易賬戶有足夠的資金。一旦下單成功，您下單的金額會被凍結。凍結金額的多少取決於您下單的類型和具體的請求參數。

請悉知，當您的訂單進入買賣盤，系統會提前凍結訂單的手續費。

在下單之前，請充分了解每一個交易對的參數含義。

<aside class="notice">對於一個賬號，每一個交易對最大<strong>活躍</strong>委託訂單數量<code>200</code>。</aside>

### HTTP 請求
`POST /api/v1/hf/orders`

### 請求示例
`POST /api/v1/hf/orders`

### API權限
此接口需要`交易權限`。

### 頻率限制
此接口針對每個賬號請求頻率限制爲`45次/3s`

### 請求參數

下單公有的請求參數

請求參數 | 類型 | 是否必須 | 含義 |
--------- | ------- | -----------| -----------|
clientOid | String | 否 | Client Order Id，客戶端創建的唯一標識，建議使用UUID |
symbol | String | 是 | 交易對 比如，ETH-BTC |
type | String | 是 | 訂單類型 limit 和 market |
side | String | 是 | `buy`（買） 或 `sell`（賣）|
stp | String | 否 | 自成交保護（`self trade prevention`）分爲`CN`, `CO`, `CB` , `DC`四種策略 |
tags | String | 否 | 訂單標籤，長度不超過`20`個字符（編碼只支持ASCII）|
remark | String | 否 | 下單備註，長度不超過`20`個字符（編碼只支持ASCII）|

#### **limit** 限價單額外所需請求參數

請求參數 | 類型 | 是否必須 | 含義 |
--------- | ------- | -----------| -----------|
price | String | 是 | 指定幣種的價格 |
size | String | 是 | 指定幣種的數量 |
timeInForce | String | 否 | [可選] 訂單時效策略 `GTC`, `GTT`, `IOC`, `FOK` (默認爲`GTC`) |
cancelAfter | long | 否 | [可選] `n`秒之後取消，訂單時效策略爲`GTT` |
postOnly | boolean | 否 | [可選] 被動委託的標識, 當訂單時效策略爲`IOC`或`FOK`時無效 |
hidden | boolean | 否 | [可選] 是否隱藏（買賣盤中不展示） |
iceberg | boolean | 否 | [可選] 冰山單中是否僅顯示訂單的可見部分 |
visibleSize | String | 否 | [可選] 冰山單最大的展示數量 |

#### **market** 市價單額外所需請求參數
<aside class="notice">下市價單，需定買賣數量(size)或資金(funds)。</aside>
請求參數 | 類型 | 是否必須 | 含義 |
--------- | ------- | -----------| -----------|
size | String | 否 | （`size`和`funds` 二選一） |
funds | String | 否 | （`size`和`funds` 二選一） |


### 術語解釋

### 交易對(Symbol)
交易對必須是KuCoin支持的交易對。

#### Client Order Id(clientOid)
ClientOid字段是客戶端創建的唯一ID（推薦使用UUID），只能包含數字、字母、下劃線（_）和 分隔線（-）。這個字段會在獲取訂單信息時返回。您可使用clientOid來標識您的訂單。ClientOid不同於服務端創建的訂單ID。請不要使用同一個clientOid發起請求。ClientOid最長不得超過40個字符。

請妥善記錄服務端創建的orderId，以用於查詢訂單狀態的更新。

#### 訂單類型(type)
您在下單時指定的訂單類型，決定了您是否需要請求其他參數，同時還會影響到撮合引擎的執行。

下限價單時，您需指定限價單的價格（price）和數量（size）。系統將根據市場行情以指定或更優價格撮合該訂單。如果訂單未能被立即撮合，將繼續留買賣盤中，直至被撮合或被用戶取消。

與限價單不同，市價單價格會隨着市場價格波動而變化。下市價單時，您無需指定價格，只需指定數量。市價單會立即成交，不會進入買賣盤。所有市價單都是taker，需支付taker費用。

#### 交易類型(tradeType)
目前平臺只支持現貨（TRADE）資產交易下單，系統將默認按照現貨凍結您交易賬戶資金。

#### 價格(Price)
下限價單時，price 必須以交易對的價格增量 priceIncrement爲基準，價格增量是交易對的價格的精度。比如，對BTC-USDT這個交易對, 它的 priceIncrement 爲0.00001000。那麼你下單的 price 不可以小於0.00001000，且爲 priceIncrement 的正整數倍，否則下單時會報錯，invalid priceIncrement。

#### 數量(Size)
下限價單時，size 是指交易對的交易對象(即寫在靠前部分的資產名)的數量。size 必須以交易對中的數量增量 baseIncrement爲基準，數量增量是交易對的數量的精度。下單的 size 爲 baseIncrement 的正整數倍並且必須在 baseMinSize 和 baseMaxSize 之間。

#### 資金(Funds)
下市價單時，funds 字段是指交易對的定價資產(即寫在靠後部分資產名)的資金。funds 必須以交易對中的資金增量quoteIncrement爲基準，資金增量是交易對的資金的精度。下單的 funds 爲 quoteIncrement 的正整數倍且必須在 quoteMinSize 和 quoteMaxSize 之間。

#### 訂單時效策略(TimeInForce)
訂單時效是一種交易時使用的特殊策略，用於設定訂單在被撮合或取消前的生效時間。訂單時效策略分爲四種：

縮寫 | 全稱 | 含義 |
--------- | ------- | -----------|
GTC | Good Till Canceled | 主動取消才過期 |
GTT | Good Till Time | 指定時間後過期 |
IOC | Immediate Or Cancel | 立即成交可成交的部分，然後取消剩餘部分，不進入買賣盤 |
FOK | Fill Or Kill | 如果下單不能全部成交，則取消 |

- 注意，成交也包含自成交。市價單並不支持訂單時效策略(TimeInForce)

#### 被動委託(PostOnly)
PostOnly只是一個標識，如果下單有能立即成交的對手方，則取消。當用戶所下訂單是postOnly訂單時，如果訂單進入撮合引擎後遇到冰山單和隱藏單可以立即成交，postOnly 訂單收maker手續費，冰山單和隱藏單收taker手續費。

#### 隱藏單和冰山單(Hidden & Iceberg)
您可在高級設置中設置隱藏單和冰山單（冰山單是一種特殊形式的隱藏單）。進行限價單和限價止損單交易時，您可選擇按照隱藏單或冰山單執行。

隱藏單不會展示在買賣盤上。

與隱藏單不同，冰山單分爲可見和隱藏兩部分。進行冰山單交易時，需設置可見訂單數量。冰山單最小可見數量是總訂單量的1/20。

進行撮合時，冰山單的可見部分會首先被撮合，當可見部分被全部撮合後，另一部分隱藏訂單將浮出，直至訂單全部成交。

##### 注意
- 系統將對隱藏和冰山單收取taker費用。
- 如果您同時設定了冰山單和隱藏單，您的訂單將默認作爲冰山單處理。

#### 凍結策略(Hold)
對於限價買單，我們會從您的資金裏面凍結您買單的金額(price * size)。同樣，對於限價賣單，我們也會凍結您賣單的資產。在成交那一刻評估實際的手續費。如果您取消了一個部分成交或未成交的訂單，那麼剩餘金額會解凍會退到您的賬戶。 對於市價買/賣單，需要指定funds(資金)，我們會根據funds來凍結您賬戶裏的資金。如果只指定了size，在成交或取消之前，您的賬戶所有資金會被凍結（通常凍結時間非常短）。

#### 自成交保護(SelfTradePrevention)
您可在高級設置中設置自成交保護策略，您的訂單將不會發生自成交。如果您在下單時沒有指定STP，您的訂單可能會被自己的訂單成交。市價單現不支持DC策略。

市價單現不支持 DC，當timeInForce 爲FOK， 那麼stp會指定爲CN。

縮寫 | 全稱 | 含義 |
--------- | ------- | -----------|
GTC | Good Till Canceled | 主動取消才過期 |
GTT | Good Till Time | 指定時間後過期 |
IOC | Immediate Or Cancel | 立即成交可成交的部分，然後取消剩餘部分，不進入買賣盤 |
FOK | Fill Or Kill | 如果下單不能全部成交，則取消 |

縮寫 | 全稱 | 含義 |
--------- | ------- | -----------|
DC | Decrease and Cancel | 取消數量少的一方的訂單，並將數量多的一方數量改爲新舊差值 |
CO | Cancel old | 取消舊的訂單 |
CN | Cancel new | 取消新的訂單 |
CB | Cancel both | 雙方都取消 |

#### 訂單生命週期(ORDER LIFECYCLE)

當下單請求因請求成功或（因餘額不足、參數不合法等原因）被拒絕時，HTTP請求會進行響應。下單成功，返回訂單ID，訂單將被撮合，可能會部分或全部成交。被取消或者已完全成交的訂單狀態爲“已完成”（DONE），否則訂單就是“活躍”（ACTIVE）狀態。訂閱市場數據頻道的用戶可使用訂單ID（orderId）和用戶訂單ID（clientOid）來識別消息。

#### 價格保護機制

下單將啓用價格保護機制。具體規則如下

- 若用戶在幣幣交易所下的市價單/限價單可以與當前買賣盤內訂單直接成交，那麼系統會判斷成交深度對應的價格與同方向盤口價的偏差是否超出閾值（閾值可根據API symbol接口獲取）；
- 若超過，當您是限價單時，該訂單會被直接取消；
- 若是市價單則此訂單將被系統部分執行，執行上限爲閾值對應的價格內的訂單數量，其他剩餘訂單將不被成交。

舉例說明：若閾值爲10%，當某用戶在KCS/USDT交易區下了10,000 USDT的市價買單時(此時賣一價爲1.20000)，系統會判斷訂單完全成交後最新成交價爲1.40000。(1.40000-1.20000)/1.20000=16.7%>10%，而閾值價格爲1.32000，此時，用戶的這筆市價買單將最多被成交至1.32000，其他剩餘訂單則不會和買賣盤內訂單進行撮合。 請注意：該功能對深度的探測可能存在偏差，若您的訂單未被完全成交有可能是因爲超出了閾值的部分未成交。


### 返回值

| 字段                                | 含義   |
| --------------------------------- | ---- |
| orderId                           | 訂單Id,下單成功後，會返回一個`orderId`字段 |

## 高頻交易批量下單
```json
//request
{
    "symbol": "BTC-USDT",
    "orderList": [
        {
            "clientOid": "3d07008668054da6b3cb12e432c2b13a",
            "side": "buy",
            "type": "limit",
            "price": "0.01",
            "size": "0.01",
            "symbol": "BTC-USDT"
        },
        {
            "clientOid": "37245dbe6e134b5c97732bfb36cd4a9d",
            "side": "buy",
            "type": "limit",
            "price": "0.01",
            "size": "0.01",
            "symbol": "BTC-USDT"
        }
    ]
}
```

```json
//response
{
   "code":"200000",
   "data":[
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

該接口支持在一個接口中批量下單，每次最多可同時下`5`個訂單，訂單類型必須爲相同交易對的限價單（目前該接口只支持現貨交易，不支持槓桿交易）

此接口只提交下單請求，實際下單結果需要通過查詢訂單狀態或訂閱websocket獲取訂單消息。

### HTTP 請求
`POST /api/v1/hf/orders/multi`

### 請求示例
`POST /api/v1/hf/orders/multi`

### API權限
此接口需要`交易權限`。

### 頻率限制
此接口針對每個賬號請求頻率限制爲`3次/3s`

### 請求參數

請求參數 | 類型 | 是否必須 | 含義 |
--------- | ------- | -----------| -----------|
clientOid | String | 否 | Client Order Id，客戶端創建的唯一標識，建議使用UUID |
symbol | String | 是 | 交易對 比如，`ETH-BTC` |
type | String | 是 | 訂單類型 limit 和 market |
timeInForce | String | 否 | [可選] 訂單時效策略 `GTC`, `GTT`, `IOC`, `FOK` (默認爲`GTC`) |
stp | String | 否 | [可選] 自成交保護（self trade prevention）分爲CN, CO, CB , DC四種策略 |
side | String | 是 | buy（買） 或 sell（賣）|
price | String | 是 | 指定幣種的價格 |
size | String | 是 | 指定幣種的數量 |
cancelAfter | long | 否 | [可選] `n`秒之後取消，訂單時效策略爲 GTT |
postOnly | boolean | 否 | [可選] 被動委託的標識, 當訂單時效策略爲 IOC 或 FOK 時無效 |
hidden | boolean | 否 | [可選] 是否隱藏（買賣盤中不展示） |
iceberg | boolean | 否 | [可選] 冰山單中是否僅顯示訂單的可見部分 |
visibleSize | String | 否 | [可選] 冰山單最大的展示數量 |
tags | String | 否 | [可選] 訂單標籤，長度不超過`20`個字符（編碼只支持ASCII）|
remark | String | 否 | [可選] 下單備註，長度不超過`20`個字符（編碼只支持ASCII）|


### 返回值
| 字段    | 含義                        |
| -------| --------------------------- |
|status  |  訂單創建結果 (`success`, `fail`) |
|failMsg |  失敗原因                    |


## 高頻交易通過orderId撤單
```json
// response
{   
    "code": "200000", 
    "data": {
        "orderId": "630625dbd9180300014c8d52"
    }
}
```
通過orderId取消單筆訂單。

### HTTP請求
`DELETE /api/v1/hf/orders/{orderId}?symbol={symbol}`

### 請求示例
`DELETE /api/v1/hf/orders/5bd6e9286d99522a52e458de?symbol=ETH-BTC`

### API權限
此接口需要`交易權限`。

### 頻率限制
此接口針對每個賬號請求頻率限制爲`60次/3s`

### 請求參數
請求參數 | 類型 | 是否必須 | 含義 |
--------- | ------- | -----------| -----------|
orderId | String | 是 | 路徑參數，訂單Id 唯一標識 |
symbol | String | 是 | 交易對 比如，ETH-BTC |

<aside class="notice">輸入參數orderId是服務器生成的訂單唯一標識，不是客戶端生成的clientOId</aside>

<aside class="notice">如果訂單不能撤銷（已經成交或已經取消），會返回錯誤信息，可根據返回的<code>msg</code>獲取原因。</aside>

### 返回值
| 字段                | 含義      |
| ----------------- | ------- |
| orderId | 取消的訂單id |

## 高頻交易通過clientOid單個撤單
```json
// response
{
    "code": "200000", 
    "data": {
        "clientOid": "6d539dc614db3"
    }
}
```
通過clientOid取消單筆訂單。

### HTTP請求
`DELETE /api/v1/hf/orders/client-order/{clientOid}?symbol={symbol}`

### 請求示例
`DELETE /api/v1/hf/orders/client-order/6d539dc614db3?symbol=ETH-BTC`

### API權限
此接口需要`交易權限`。

### 請求參數
請求參數 | 類型 | 是否必須 | 含義 |
--------- | ------- | -----------| -----------|
clientOid | String | 是 | 路徑參數，客戶端生成的標識 |
symbol | String | 是 | 交易對 比如，`ETH-BTC` |

#### 返回值
字段	| 含義 |
--------- | ------- |
clientOid | 客戶端生成的標識 |

## 高頻交易全部撤單
```json 
// response
{
    "code": "200000", 
    "data": "success"
}
```
此接口，可以取消所有還沒有完成的高頻訂單（通過`/api/v1/hf/orders`創建的訂單）。

此接口只提交取消請求，實際取消結果需要通過查詢訂單狀態或訂閱websocket獲取訂單消息。

### HTTP請求
`DELETE /api/v1/hf/orders?symbol={symbol}`

### 請求示例
`DELETE /api/v1/hf/orders?symbol=ETH-BTC`

### API權限
此接口需要`交易權限`。

### 頻率限制
此接口針對每個賬號請求頻率限制爲`3次/3s`

### 請求參數
請求參數 | 類型 | 是否必須 | 含義 |
--------- | ------- | -----------| -----------|
symbol | String | 是 | 取消指定交易對的open訂單 |

### 返回值
由於此接口爲批量撤單，接口http狀態爲`200`即可視爲取消請求提交成功，所以不需要返回值，實際取消結果可根據“獲取訂單列表（新）“查詢訂單或訂閱websocket獲取訂單消息。

### 獲取高頻交易訂單列表

包含獲取活躍訂單(Active)和已完成訂單(Done)兩個接口，活躍訂單接口返回值是所有還沒有完成的訂單列表，已完成訂單接口獲取歷史訂單列表，返回值是分頁後的數據。兩個接口的返回數據都根據訂單最新更新時間降序排序。用戶成功下單後，訂單就處於活躍（Active）狀態，用戶可以通過 inOrderBook 來判斷委託是否進入買賣盤。被取消或者已完全成交的訂單則被標記爲已完成（Done）狀態。

查詢“活躍”狀態的訂單，沒有時間限制。但查詢“已完成”狀態的訂單時，您只能獲取 3 * 24 小時時間範圍內的數據（即：從當前時間起至3 * 24 小時前）。若超出時間範圍，系統會默認查詢 3 * 24 小時時間範圍內的數據。

## 獲取活躍訂單列表
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
該接口是獲取所有活躍訂單列表。返回數據都根據訂單最新更新時間降序排序。

### HTTP請求
`GET /api/v1/hf/orders/active`

### 請求示例
`GET /api/v1/hf/orders/active?symbol=BTC-ETH`

### API權限
此接口需要`通用權限`。

### 頻率限制
此接口針對每個賬號請求頻率限制爲`30次/3s`

### 請求參數
請求參數 | 類型 | 是否必須 | 含義 |
--------- | ------- | -----------| -----------|
symbol | String | 是 | 只返回指定交易對的訂單信息 |

### 返回值
字段	| 含義 |
--------- | ------- |
id | 訂單id，訂單唯一標識 |
symbol | 交易對 |
opType | 操作類型: DEAL |
type | 訂單類型 |
side | 買或賣 |
price | 訂單價格 |
size | 下單數量 |
funds | 下單金額 |
dealFunds | 成交額 |
dealSize | 成交數量 |
fee | 手續費 |
feeCurrency | 計手續費幣種 |
stp | 自成交保護 |
timeInForce | 訂單時效策略 |
postOnly | 是否爲被動委託 |
hidden | 是否爲隱藏單 |
iceberg | 是否爲冰山單 |
visibleSize | 冰山單在買賣盤可見數量 |
cancelAfter | timeInForce 爲 GTT n秒後過期 |
channel | 下單來源 |
clientOid | 客戶端生成的標識 |
remark | 訂單說明 |
tags | 訂單標籤 |
active | 訂單狀態 true: 訂單狀態爲 active; false: 訂單訂單狀態爲 done |
inOrderBook | 是否進入買賣盤 true: 進入買賣盤; false: 沒有進入買賣盤 |  
cancelExist | 訂單是否存在取消記錄 |
createdAt | 訂單創建時間 |
lastUpdatedAt | 訂單最新更新時間 |
tradeType | 交易類型: TRADE（現貨交易）|

#### 訂單輪詢(Polling)

對於高頻交易的用戶，建議您在本地緩存和維護一份自己的活動委託列表，並使用市場數據流實時更新自己的訂單信息。

## 已完成訂單列表
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
該接口是獲取已成交高頻訂單列表，返回值是分頁後的數據。返回數據都根據訂單最新更新時間降序排序。


### HTTP請求
`GET /api/v1/hf/orders/done`

### 請求示例
`GET /api/v1/hf/orders/done?symbol=BTC-ETH&type=limit&side=buy`

### API權限
此接口需要`通用權限`。

### 頻率限制
此接口針對每個賬號請求頻率限制爲`30次/3s`

### 請求參數
請求參數 | 類型 | 是否必須 | 含義 |
--------- | ------- | -----------| -----------|
symbol | String | 是 | 只返回指定交易對的訂單信息 |
side | String | 否 | `buy`（買）或 `sell`（賣）|
type | String | 否 | 訂單類型: `limit`（限價單）, `market`(市價單) |
startAt | long | 否 | 開始時間（毫秒），限制訂單最新更新(完成)時間|
endAt | long | 否 | 截止時間（毫秒），限制訂單最新更新(完成)時間|
lastId | long | 否 | 前一批次數據最後一條數據的id，默認獲取最新數據 |
limit | int | 否 | 默認`20`，最大`100` |

<aside class="notice">您只能獲取<code>3 * 24</code>小時時間範圍內的數據（即：從當前時間起至<code>3 * 24</code>小時前），若超出時間範圍，系統會默認查詢<code>3 * 24</code>小時時間範圍內的數據。</aside>

<aside class="notice"><code>lastId</code>用來過濾數據和分頁，如果不輸入<code>lastId</code>，默認返回最多100條最新數據，返回結果中有<code>lastId</code>，使用它作爲查詢條件，可以查詢下一頁次新數據</aside>

### 返回值
字段	| 含義 |
--------- | ------- |
id | 訂單id，訂單唯一標識 |
symbol | 交易對 |
opType | 操作類型: DEAL |
type | 訂單類型 |
side | 買或賣 |
price | 訂單價格 |
size | 下單數量 |
funds | 下單金額 |
dealFunds | 成交額 |
dealSize | 成交數量 |
fee | 手續費 |
feeCurrency | 計手續費幣種 |
stp | 自成交保護 |
timeInForce | 訂單時效策略 |
postOnly | 是否爲被動委託 |
hidden | 是否爲隱藏單 |
iceberg | 是否爲冰山單 |
visibleSize | 冰山單在買賣盤可見數量 |
cancelAfter | timeInForce 爲 GTT n秒後過期 |
channel | 下單來源 |
clientOid | 客戶端生成的標識 |
remark | 訂單說明 |
tags | 訂單標籤 |
active | 訂單狀態 true: 訂單狀態爲 active; false: 訂單訂單狀態爲 done |
inOrderBook | 是否進入買賣盤 true: 進入買賣盤; false: 沒有進入買賣盤 |  
cancelExist | 訂單是否存在取消記錄 |
createdAt | 訂單創建時間 |
lastUpdatedAt | 訂單最新更新時間 |
tradeType | 交易類型: TRADE（現貨交易）|


## 獲取高頻交易訂單詳情
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

此接口，可以通過訂單id獲取單個訂單信息。

### HTTP請求
`GET /api/v1/hf/orders/{orderId}?symbol={symbol}`

### 請求示例
`GET /api/v1/hf/orders/5c35c02703aa673ceec2a168?symbol=ETH-BTC`

### API權限
此接口需要`通用權限`。

### 頻率限制
此接口針對每個賬號請求頻率限制爲`30次/3s`

### 請求參數
請求參數 | 類型 | 是否必須 | 含義 |
--------- | ------- | -----------| -----------|
orderId | String | 是 | 路徑參數，訂單Id 唯一標識 |
symbol | String | 是 | 交易對 比如，ETH-BTC |

<aside class="notice">如果訂單是已完結訂單，您只能獲取<code>3 * 24</code>小時時間範圍內的數據（即：從當前時間起至<code>3 * 24</code>小時前）</aside>


#### 返回值
字段	| 含義 |
--------- | ------- |
id	| 訂單id，訂單唯一標識 |
symbol | 交易對 |
opType | 操作類型: DEAL |
type | 訂單類型 |
side | 買或賣 |
price | 訂單價格 |
size | 訂單數量 |
funds | 下單金額 |
dealFunds | 成交額 |
dealSize | 成交數量 |
fee | 手續費 |
feeCurrency | 計手續費幣種 |
stp | 自成交保護 |
timeInForce | 訂單時效策略 |
postOnly | 是否爲被動委託 |
hidden | 是否爲隱藏單 |
iceberg | 是否爲冰山單 |
visibleSize | 冰山單在買賣盤可見數量 |
cancelAfter | timeInForce 爲 GTT n秒後觸發 |
channel | 下單來源 |
clientOid | 客戶端生成的標識 |
remark | 訂單說明 |
tags | 訂單標籤 |
active | 訂單狀態 true: 訂單狀態爲 active; false: 訂單訂單狀態爲 done |
inOrderBook | 是否進入買賣盤 true: 進入買賣盤; false: 沒有進入買賣盤 | 
cancelExist | 訂單是否存在取消記錄 |
createdAt | 訂單創建時間 |
lastUpdatedAt | 訂單最新更新時間 |
tradeType | 交易類型: TRADE（現貨交易）|


## 基於clientOid 通過clientOid獲取高頻交易訂單詳情
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
此接口，可以通過clientOid查詢單個訂單的信息，若訂單不存在則提示訂單不存在。

### HTTP請求
`GET /api/v1/hf/orders/client-order/{clientOid}?symbol={symbol}`

### 請求示例
`GET /api/v1/hf/orders/client-order/6d539dc614db312?symbol=ETH-BTC`

### API權限
此接口需要`通用權限`。

### 頻率限制
此接口針對每個賬號請求頻率限制爲`30次/3s`
### 請求參數
請求參數 | 類型 | 是否必須 | 含義 |
--------- | ------- | -----------| -----------|
clientOid | String | 是 | 路徑參數，客戶端生成的標識 |
symbol | String | 是 | 交易對 比如，`ETH-BTC` |

<aside class="notice">如果訂單是已完結訂單，您只能獲取<code>3 * 24</code>小時時間範圍內的數據（即：從當前時間起至<code>3 * 24</code>小時前）</aside>

#### 返回值
字段	| 含義 |
--------- | ------- |
id	| 訂單id，訂單唯一標識 |
symbol | 交易對 |
opType | 操作類型: DEAL |
type | 訂單類型 |
side | 買或賣 |
price | 訂單價格 |
size | 訂單數量 |
funds | 下單金額 |
dealFunds | 成交額 |
dealSize | 成交數量 |
fee | 手續費 |
feeCurrency | 計手續費幣種 |
stp | 自成交保護 |
timeInForce | 訂單時效策略 |
postOnly | 是否爲被動委託 |
hidden | 是否爲隱藏單 |
iceberg | 是否爲冰山單 |
visibleSize | 冰山單在買賣盤可見數量 |
cancelAfter | timeInForce 爲 GTT n秒後觸發 |
channel | 下單來源 |
clientOid | 客戶端生成的標識 |
remark | 訂單說明 |
tags | 訂單標籤 |
active | 訂單狀態 true: 訂單狀態爲 active; false: 訂單訂單狀態爲 done |
inOrderBook | 是否進入買賣盤 true: 進入買賣盤; false: 沒有進入買賣盤 |
cancelExist | 訂單是否存在取消記錄 |
createdAt | 訂單創建時間 |
lastUpdatedAt | 訂單最新更新時間 |
tradeType | 交易類型: TRADE（現貨交易）|

# 成交明細

## 獲取高頻交易成交記錄
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
獲取最近的成交明細列表 返回值是分頁後的數據，根據成交(創建)時間降序排序。

### HTTP請求
`GET /api/v1/hf/fills`

### 請求示例
`GET /api/v1/hf/fills?symbol=BTC-USDT`

### API權限
此接口需要`通用權限`。

### 頻率限制
此接口針對每個賬號請求頻率限制爲`9次/3s`

### 請求參數
請求參數 | 類型 | 是否必須 | 含義 |
--------- | ------- | -----------| -----------|
orderId | String | 否 | 查詢該訂單Id的成交明細（如果指定了`orderId`，請忽略其他查詢條件）|
symbol | String | 是 | 只返回指定交易對的訂單信息 |
side | String | 否 | `buy`（買） 或 `sell`（賣）|
type | String | 否 | 訂單類型: `limit`（限價單）, `market`(市價單) |
startAt | long | 否 | 開始時間（毫秒），限制成交記錄成交(創建)時間|
endAt | long | 否 | 截止時間（毫秒），限制成交記錄成交(創建)時間|
lastId | long | 否 | 前一批次數據最後一條數據的id，默認獲取最新數據 |
limit | int | 否 | 默認`100`，最大`200` |

<aside class="notice"><code>lastId</code>用來過濾數據和分頁，如果不輸入<code>lastId</code>，默認返回最多100條最新數據，返回結果中有<code>lastId</code>，使用它作爲查詢條件，可以查詢下一頁次新數據</aside>


### 返回值
字段 | 含義 |
--------- | ------- |
id | 成交明細Id |
symbol | 交易對 |
tradeId | 交易Id |
orderId	| 訂單Id |
counterOrderId | 對手方訂單Id |
side | 買或賣 |
liquidity | 流動性類型: taker 或 maker |
forceTaker | 是否強製作爲taker處理 |
price | 成交價格 |
size | 成交數量 |
funds | 成交額 |
fee | 手續費 |
feeRate | 手續費率 |
feeCurrency | 計手續費幣種 |
type | 訂單類型limit 或 market |
stop | 止盈止損類型，目前高頻交易還未支持止盈止損類型，所以爲空 |
createdAt | 成交(創建)時間 |
tradeType | 交易類型: TRADE（現貨交易）|

##### 查詢時間範圍

您只能獲取 `3 * 24` 小時時間範圍內的數據（即：從當前時間起至`3 * 24` 小時前）。若超出時間範圍，系統會默認查詢 `3 * 24` 小時時間範圍內的數據。

##### 結算
結算分爲兩部分，一部分是成交結算，一部分是費用結算。當撮合完成後，這些數據將立即更新到我們的數據存儲區，系統將啓動結算並從您的預凍結資金中進行扣除。

##### 手續費

KuCoin平臺上的訂單分爲兩種類型：Taker 和 Maker。Taker單會與買賣盤上的已有訂單立即成交，而Maker單則相反，會一直留在買賣盤中等待撮合。Taker單消耗了市場的流動性，因此會被收取taker費用，而Maker單增加了市場的流動性，會被收取較低的手續費甚至獲得手續費補貼。請注意：市價單、冰山單和隱藏單都會被扣除taker手續費。

下單時，系統會預凍結您賬戶中的taker費用。流動性（liquidity）字段中的參數說明瞭訂單將會被收取taker還是maker費用。

假設您的訂單是限價單，當您下單後在撮合引擎中被立即撮合，我們將收取您taker費用，而如果您的訂單沒有被立即撮合或有部分剩餘未被撮合都會進入買賣盤，進入買賣盤的訂單在未被取消前成交都會收取您maker手續費。

進入撮合後與對手盤訂單撮合，當指令訂單剩餘金額爲0，交易完成，如果剩餘資金不足以購買最低數量（0.00000001）的商品，則取消指令訂單。

如果您的訂單作爲maker被成交，我們會將剩餘預凍結的taker費用返還給您。

但需要注意的是:

- 當您創建了一個隱藏委託/冰山委託訂單時，即使它未被撮合引擎立即成交而被被動成交，仍然會收取taker費用
- 被動委託收取maker費用。如果該委託下單後會立刻與市場已有委託(除冰山/隱藏訂單外)撮合，那麼該委託將被取消。如果被動委託下單後與冰山/隱藏訂單立即成交，被動委託訂單將收取maker費用

舉例：

以BTC/USDT爲例，假設您想市價買入1BTC，手續費率爲0.1%，市場買賣盤數據如下：

Price（USDT）| Size（BTC）| Side |
--------- | ------- | ------- |
4200.00 | 0.18412309 | sell |
4015.60	| 0.56849308 | sell |
4011.32 | 0.24738383 | sell |
3995.64	| 0.84738383 | buy |
3988.60	| 0.20484000 | buy |
3983.85	| 1.37584908 | buy |

當您下一個買入市價單時，市場會立即成交，成交明細將分爲3筆，如下圖所示：

Price（USDT）| Size（BTC）| Fee（BTC）|
--------- | ------- | ------- |
4011.32 | 0.24738383 | 0.00024738 |
4015.60	| 0.56849308 | 0.00056849 |
4200.00	| 0.18312409 | 0.00018312 |

# Websocket

REST API的使用受到了訪問頻率的限制，因此推薦您使用websocket獲取實時數據。Websocket的相關設置請參考線上文檔`https://docs.kucoin.com/#websocket-feed `

# 公共頻道
參考線上文檔`https://docs.kucoin.com/#public-channels`

# 私有頻道

## 高頻交易賬戶餘額變更事件
```json
```
Topic: `/account/balance`

- 推送頻率: `實時推送`

當您的高頻交易賬戶餘額變更時，您會收到詳細的賬戶變更信息。

Relation Event

類型 | 描述
--------- | -------
| trade_hf.hold | 高頻交易賬戶凍結 |
| trade_hf.setted | 高頻交易賬戶入賬 |
| trade_hf.transfer | 高頻交易賬戶轉賬 |
| trade_hf.other | 高頻交易賬戶其他操作 |

## 高頻交易私有訂單變更事件
參考線上文檔 `https://docs.kucoin.com/#private-order-change-events`

## 其他部分
其他部分參考線上文檔 `https://docs.kucoin.com/#private-channels`