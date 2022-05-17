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

# 快速开始

## 简介

欢迎使用KuCoin开发者高频交易文档。 此文档概述了高频交易功能、高频交易市场行情和其他应用开发接口。

KuCoin API：**REST API**

- REST API 包含三个类别：**用户（私有）、交易（私有）、市场数据（公共）**



## 更新预告


# 账户

## 创建账户

此接口可以用于创建账户

```json
{
    "id":"5bd6e9286d99522a52e458de" //accountId
}
```

### HTTP请求
`POST /api/v1/accounts`

### API权限
此接口需要`通用权限`。

### 请求参数
请求参数 | 数据类型 | 是否必需 | 含义
--------- | ------- | -----------| -----------|
type | String | 是 | 账户类型:`MAIN`、`TRADE`、`MARGIN`或`TRADE_HF`
currency | String | 是 | 币种

### 返回值
字段 | 含义
--------- | -------
id | 账户ID -- accountId



## 账户列表
```json
[
    {
        "id":"5bd6e9286d99522a52e458de", //accountId
        "currency":"BTC",  //币种
        "type":"trade_hf", //账户类型，高频交易（trade_hf）账户
        "balance":"237582.04299",//资金总额
        "available":"237582.032", //可用金额
        "holds":"0.01099" //冻结金额
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

获取账号下账户详情列表。

交易前请先充值到储蓄账户，再使用内部资金划转将资金从储蓄账户划转到高频交易账户。

### HTTP请求
`GET /api/v1/accounts`

### API权限
此接口需要`通用权限`。


### 请求参数
请求参数 | 类型 | 是否必须 | 含义 |
--------- | ------- | -----------| -----------|
currency | String | 否 | 币种
type | String | 否 | 账户类型:`MAIN`、`TRADE`、`MARGIN`或`TRADE_HF`

### 返回值
字段 | 含义
--------- | -------
id | accountId 账户ID
currency | 账户对应的币种
type |账户类型 ，`MAIN`（储蓄账户）、`TRADE`（交易账户）、`MARGIN`(杠杆账户)、`TRADE_HF`（高频交易账户）
balance | 账户资金总额
available | 账户可用的资金
holds | 账户冻结的资金

## 获取可划转资金

```json
{
    "currency":"KCS",
    "balance":"0",
    "available":"0",
    "holds":"0",
    "transferable":"0"
}
```
此接口可获取指定账户和币种下的可划转的资金。

### HTTP请求
`GET /api/v1/accounts/transferable`

### API权限
此接口需要`通用权限`。

### 频率限制
此接口针对每个账号请求频率限制为`18次/3s`

### 请求参数

请求参数 | 类型 | 是否必须 | 含义 |
--------- | ------- | -----------| -----------|
currency | String | 否 | 币种 |
type | String | 否 | 账户类型: `MAIN`、`TRADE`、`MARGIN`、`TRADE_HF` |


### 返回值
字段 | 含义
--------- | -------
currency | 币种
balance | 资金总额
available | 可用资金
holds | 冻结资金
transferable | 可划转资金


## 账户流水记录
```json
[
    {
        "id": 12,//唯一键
        "currency": "USDT", //币种
        "amount": "10.00059547", //资金变动值
        "fee": "0", //充值或提现费率
        "balance": "0", //金额变动
        "accountType": "TRADE_HF", //账户类型
        "bizType": "TRADE_EXCHANGE", //业务类型
        "direction": "in", //出入账方向入账或出账（in or out）
        "createdAt": 1629101692950, //创建时间
        "context": "{\"symbol\":\"1001\",\"orderId\":\"611a1e7cc913d000066cf7ec\",\"tradeId\":\"283742\"}" //Business core parameters
    },
    {
        "id": 10,//唯一键
        "currency": "USDT", //币种
        "amount": "10.00059547", //资金变动值
        "fee": "0", //充值或提现费率
        "balance": "0", //金额变动
        "accountType": "TRADE_HF", //账户类型
        "bizType": "TRADE_EXCHANGE", //业务类型
        "direction": "in", //出入账方向入账或出账（in or out）
        "createdAt": 1629101692950, //创建时间
        "context": "{\"symbol\":\"1001\",\"orderId\":\"611a1e7cc913d000066cf7ec\",\"tradeId\":\"283742\"}" //Business core parameters
    }
]
```
此接口返回所有账户的出入账流水记录，支持多币种查询。 查询参数

### HTTP请求
`GET /api/v1/accounts/ledgers/hf`

### API权限
此接口需要`通用权限`。

### 频率限制
此接口针对每个账号请求频率限制为`18次/3s`

### 请求参数
请求参数 | 类型 | 是否必须 | 含义 |
--------- | ------- | -----------| -----------|
currency | String | 否 | [可选] 币种，选填，可多选，以逗号分隔，最多支持选择10个币种，若不填写，默认查询所有币种 |
direction | String | 否 | [可选] 出入账方向: `in`-入账, `out`-出账 |
bizType | String | 否 | [可选] 业务类型: `TRANSFER`-转账,`TRADE_EXCHANGE`-交易 |
lastId | long | 否 | [可选] 前一批次数据最后一条数据的id。 默认获取最新流水。|
limit | int | 否 | [可选] 默认`100`，最大`200` |
startAt | long | 否 | [可选] 开始时间（毫秒）|
endAt | long | 否 | [可选] 截止时间（毫秒）|

注意:

如果设定 `lastId` , 获取流水 < `lastId`. 否则返回最近流水。
您只能获取 3 * 24 小时时间范围内的数据（即：从当前时间起至3 * 24 小时前）。若超出时间范围，系统会默认查询 3 * 24 小时时间范围内的数据。

#### 返回值

字段 | 含义 |
--------- | ------- |
id | 唯一键 |
currency | 币种 |
amount | 资金变动值 |
fee | 充值或提现费率 |
balance | 变动后的资金总额 |
accountType | 母账号账户类型TRADE_HF |
bizType | 业务类型，比如交易，提现，推荐关系奖，借贷等 |
direction | 出入账方向 out 或 in |
createdAt | 创建时间 |
context | 业务核心参数 |

#### context

如果 bizType 是trade exchange，那么 context 字段会包含交易的额外信息（订单id，交易id，交易对）。

## 内部资金划转

```json
{
    "orderId":"5bd6e9286d99522a52e458de"
}
```
此接口用于平台内部账户资金划转，用户可以将资金在储蓄账户、交易账户、杠杆账户、高频账户之间免费划转。同时支持从其他账户划转资金至合约账户，但不支持从合约账户转出资金至其他账户。

### HTTP请求
`POST /api/v2/accounts/inner-transfer`

### API权限
此接口需要`交易权限`。

### 请求参数
请求参数 | 类型 | 是否必须 | 含义 |
--------- | ------- | -----------| -----------|
clientOid | String | 是 | Client Order Id，客户端创建的唯一标识，建议使用UUID |
currency | String | 是 | 币种 |
from | String | 是 |  付款账户类型: `MAIN`、`TRADE`、`MARGIN`、`TRADE_HF`
to | String | 是 |  收款账户类型: `MAIN`、`TRADE`、`MARGIN`、`TRADE_HF`、`CONTRACT`
amount | String | 是 | 转账金额，精度为币种精度正整数倍 |

### 返回值
字段 | 含义
--------- | -------
orderId | 内部资金划转的订单ID

# 交易模块

以下请求需要校验[签名](#8ba46c43fe)。


# 订单

## 下单

```json
{
    "orderId":"5bd6e9286d99522a52e458de"
}
```

订单有两种类型： 限价单（`limit`）: 指定价格和数量进行交易。 市价单(`market`) : 指定资金或数量进行交易。

在下单前，请确保您的高频交易账户有足够的资金。一旦下单成功，您下单的金额会被冻结。冻结金额的多少取决于您下单的类型和具体的请求参数。

请悉知，当您的订单进入买卖盘，系统会提前冻结订单的手续费。

在下单之前，请充分了解每一个交易对的参数含义。

###下单限制

对于一个账号，每一个交易对最大撮合订单数量`200`（包含止损单）。

### HTTP 请求

`POST /api/v1/orders/hf`

### API权限
此接口需要`交易权限`。

### 频率限制
此接口针对每个账号请求频率限制为`45次/3s`

### 请求参数

下单公有的请求参数

请求参数 | 类型 | 是否必须 | 含义 |
--------- | ------- | -----------| -----------|
clientOid | String | 否 | Client Order Id，客户端创建的唯一标识，建议使用UUID |
symbol | String | 是 | 交易对 比如，ETH-BTC |
type | String | 是 | 订单类型 limit 和 market |
side | String | 是 | buy（买） 或 sell（卖）|
stp | String | 否 | 自成交保护（`self trade prevention`）分为`CN`, `CO`, `CB` , `DC`四种策略 |
tags | String | 否 | 订单标签，长度不超过20个字符（UTF-8）|
remark | String | 否 | 下单备注，长度不超过20个字符（UTF-8）|

#### **limit** 限价单额外所需请求参数

请求参数 | 类型 | 是否必须 | 含义 |
--------- | ------- | -----------| -----------|
price | String | 是 | 指定币种的价格 |
size | String | 是 | 指定币种的数量 |
timeInForce | String | 否 | [可选] 订单时效策略 `GTC`, `GTT`, `IOC`, `FOK` (默认为`GTC`) |
cancelAfter | long | 否 | [可选] `n`秒之后取消，订单时效策略为`GTT` |
postOnly | boolean | 否 | [可选] 被动委托的标识, 当订单时效策略为`IOC`或`FOK`时无效 |
hidden | boolean | 否 | [可选] 是否隐藏（买卖盘中不展示） |
iceberg | boolean | 否 | [可选] 冰山单中是否仅显示订单的可见部分 |
visibleSize | String | 否 | [可选] 冰山单最大的展示数量 |

#### **market** 市价单额外所需请求参数

请求参数 | 类型 | 是否必须 | 含义 |
--------- | ------- | -----------| -----------|
size | String | 否 | （`size`和`funds` 二选一） |
funds | String | 否 | （`size`和`funds` 二选一） |

- 下市价单，需定买卖数量或资金。

### 术语解释

### 交易对(Symbol)

交易对必须是KuCoin支持的交易对。

#### Client Order Id(clientOid)

ClientOid字段是客户端创建的唯一ID（推荐使用UUID），只能包含数字、字母、下划线（_）和 分隔线（-）。这个字段会在获取订单信息时返回。您可使用clientOid来标识您的订单。ClientOid不同于服务端创建的订单ID。请不要使用同一个clientOid发起请求。ClientOid最长不得超过40个字符。

请妥善记录服务端创建的orderId，以用于查询订单状态的更新。

#### 订单类型(type)

您在下单时指定的订单类型，决定了您是否需要请求其他参数，同时还会影响到撮合引擎的执行。如果您在下单时未指定订单类型，系统将默认按照限价单执行。

下限价单时，您需指定限价单的价格（price）和数量（size）。系统将根据市场行情以指定或更优价格撮合该订单。如果订单未能被立即撮合，将继续留买卖盘中，直至被撮合或被用户取消。

与限价单不同，市价单价格会随着市场价格波动而变化。下市价单时，您无需指定价格，只需指定数量。市价单会立即成交，不会进入买卖盘。所有市价单都是taker，需支付taker费用。

#### 交易类型(tradeType)

目前平台支持现货（TRADE）资产交易下单。系统根据您的参数类型，将对指定账户资金进行冻结。若未传递该参数，将默认按照现货冻结您交易账户资金。

#### 价格(Price)

下限价单时，price 必须以交易对的价格增量 priceIncrement为基准，价格增量是交易对的价格的精度。比如，对BTC-USDT这个交易对, 它的 priceIncrement 为0.00001000。那么你下单的 price 不可以小于0.00001000，且为 priceIncrement 的正整数倍，否则下单时会报错，invalid priceIncrement。

#### 数量(Size)

下限价单时，size 是指交易对的交易对象(即写在靠前部分的资产名)的数量。size 必须以交易对中的数量增量 baseIncrement为基准，数量增量是交易对的数量的精度。下单的 size 为 baseIncrement 的正整数倍并且必须在 baseMinSize 和 baseMaxSize 之间。

#### 资金(Funds)

下市价单时，funds 字段是指交易对的定价资产(即写在靠后部分资产名)的资金。funds 必须以交易对中的资金增量quoteIncrement为基准，资金增量是交易对的资金的精度。下单的 funds 为 quoteIncrement 的正整数倍且必须在 quoteMinSize 和 quoteMaxSize 之间。

#### 订单时效策略(TimeInForce)

订单时效是一种交易时使用的特殊策略，用于设定订单在被撮合或取消前的生效时间。订单时效策略分为四种：

缩写 | 全称 | 含义 |
--------- | ------- | -----------|
GTC | Good Till Canceled | 主动取消才过期 |
GTT | Good Till Time | 指定时间后过期 |
IOC | Immediate Or Cancel | 立即成交可成交的部分，然后取消剩余部分，不进入买卖盘 |
FOK | Fill Or Kill | 如果下单不能全部成交，则取消 |

- 注意，成交也包含自成交。

#### 被动委托(PostOnly)

PostOnly只是一个标识，如果下单有能立即成交的对手方，则取消。 * 当用户所下订单是postOnly订单时，如果订单进入撮合引擎后遇到冰山单和隐藏单可以立即成交，postOnly 订单收maker手续费，冰山单和隐藏单收taker手续费。

#### 隐藏单和冰山单(Hidden & Iceberg)

您可在高级设置中设置隐藏单和冰山单（冰山单是一种特殊形式的隐藏单）。进行限价单和限价止损单交易时，您可选择按照隐藏单或冰山单执行。

隐藏单不会展示在买卖盘上。

与隐藏单不同，冰山单分为可见和隐藏两部分。进行冰山单交易时，需设置可见订单数量。冰山单最小可见数量是总订单量的1/20。

进行撮合时，冰山单的可见部分会首先被撮合，当可见部分被全部撮合后，另一部分隐藏订单将浮出，直至订单全部成交。

##### 注意

- 系统将对隐藏和冰山单收取taker费用。
- 如果您同时设定了冰山单和隐藏单，您的订单将默认作为冰山单处理。

#### 冻结策略(Hold)

对于限价买单，我们会从您的资金里面冻结您买单的金额(price * size)。同样，对于限价卖单，我们也会冻结您卖单的资产。在成交那一刻评估实际的手续费。如果您取消了一个部分成交或未成交的订单，那么剩余金额会解冻会退到您的账户。 对于市价买/卖单，需要指定funds(资金)，我们会根据funds来冻结您账户里的资金。如果只指定了size，在成交或取消之前，您的账户所有资金会被冻结（通常冻结时间非常短）。

#### 自成交保护(SelfTradePrevention)

您可在高级设置中设置自成交保护策略，您的订单将不会发生自成交。如果您在下单时没有指定STP，您的订单可能会被自己的订单成交。市价单现不支持DC策略。

市价单现不支持 DC，当timeInForce 为FOK， 那么stp会指定为CN。

缩写 | 全称 | 含义 |
--------- | ------- | -----------|
GTC | Good Till Canceled | 主动取消才过期 |
GTT | Good Till Time | 指定时间后过期 |
IOC | Immediate Or Cancel | 立即成交可成交的部分，然后取消剩余部分，不进入买卖盘 |
FOK | Fill Or Kill | 如果下单不能全部成交，则取消 |

缩写 | 全称 | 含义 |
--------- | ------- | -----------|
DC | Decrease and Cancel | 取消数量少的一方的订单，并将数量多的一方数量改为新旧差值 |
CO | Cancel old | 取消旧的订单 |
CN | Cancel new | 取消新的订单 |
CB | Cancel both | 双方都取消 |

#### 订单生命周期(ORDER LIFECYCLE)

当下单请求因请求成功（撮合引擎已收到订单）或（因余额不足、参数不合法等原因）被拒绝时，HTTP 请求会进行响应。下单成功，返回订单ID，订单将被撮合，可能会部分或全部成交。部分成交后，订单剩余为未成交部分会变成等待撮合（Active）状态（不包括使用立即成交或取消[IOC]的订单）。已完全成交的订单会变成“已完成”（Done）状态。

订阅市场数据频道的用户可使用订单ID（orderId）和用户订单ID（clientOid）来识别消息。

#### 价格保护机制

下单将启用价格保护机制。具体规则如下

- 若用户在币币交易所下的市价单/限价单可以与当前买卖盘内订单直接成交，那么系统会判断成交深度对应的价格与同方向盘口价的偏差是否超出阈值（阈值可根据API symbol接口获取）；
- 若超过，当您是限价单时，该订单会被直接取消；
- 若是市价单则此订单将被系统部分执行，执行上限为阈值对应的价格内的订单数量，其他剩余订单将不被成交。

举例说明：若阈值为10%，当某用户在KCS/USDT交易区下了10,000 USDT的市价买单时(此时卖一价为1.20000)，系统会判断订单完全成交后最新成交价为1.40000。(1.40000-1.20000)/1.20000=16.7%>10%，而阈值价格为1.32000，此时，用户的这笔市价买单将最多被成交至1.32000，其他剩余订单则不会和买卖盘内订单进行撮合。 请注意：该功能对深度的探测可能存在偏差，若您的订单未被完全成交有可能是因为超出了阈值的部分未成交。


### 返回值

| 字段                                | 含义   |
| --------------------------------- | ---- |
| orderId                           | 订单Id,下单成功后，会返回一个`orderId`字段，意味这订单进入撮合引擎。 |

## 批量下单
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

该接口支持在一个接口中批量下单，每次最多可同时下`5`个订单，订单类型必须为相同交易对的限价单（目前该接口只支持现货交易，不支持杠杆交易）

此接口只提交下单请求，实际下单结果需要通过查询订单状态或订阅websocket获取订单消息。

### HTTP 请求
`POST /api/v1/orders/hf/multi`

### API权限

此接口需要`交易权限`。

### 频率限制
此接口针对每个账号请求频率限制为`3次/3s`

### 请求参数

请求参数 | 类型 | 是否必须 | 含义 |
--------- | ------- | -----------| -----------|
clientOid | String | 否 | Client Order Id，客户端创建的唯一标识，建议使用UUID |
symbol | String | 是 | 交易对 比如，ETH-BTC |
type | String | 是 | 订单类型 limit 和 market |
timeInForce | String | 否 | [可选] 订单时效策略 GTC, GTT, IOC, FOK (默认为GTC) |
stp | String | 否 | [可选] 自成交保护（self trade prevention）分为CN, CO, CB , DC四种策略 |
side | String | 是 | buy（买） 或 sell（卖）|
price | String | 是 | 指定币种的价格 |
size | String | 是 | 指定币种的数量 |
cancelAfter | long | 否 | [可选] n 秒之后取消，订单时效策略为 GTT |
postOnly | boolean | 否 | [可选] 被动委托的标识, 当订单时效策略为 IOC 或 FOK 时无效 |
hidden | boolean | 否 | [可选] 是否隐藏（买卖盘中不展示） |
iceberg | boolean | 否 | [可选] 冰山单中是否仅显示订单的可见部分 |
visibleSize | String | 否 | [可选] 冰山单最大的展示数量 |
tags | String | 否 | [可选] 订单标签，长度不超过20个字符（UTF-8）|
remark | String | 否 | [可选] 下单备注，长度不超过20个字符（UTF-8）|


### 返回值
| 字段    | 含义                        |
| -------| --------------------------- |
|status  |  订单创建结果 (`success`, `fail`) |
|failMsg |  失败原因                    |


## 单个撤单

```json
{
    "cancelledOrderIds":[
        "5bd6e9286d99522a52e458de"
    ]
}
```

此端点可以取消单笔订单。

### HTTP请求
`DELETE /api/v1/orders/hf/{orderId}?symbol={symbol}`

### API权限
此接口需要`交易权限`。

### 频率限制
此接口针对每个账号请求频率限制为`60次/3s`

### 请求参数
请求参数 | 类型 | 是否必须 | 含义 |
--------- | ------- | -----------| -----------|
orderId | String | 是 | 路径参数，订单Id 唯一标识 |
symbol | String | 是 | 交易对 比如，ETH-BTC |

<aside class="notice">输入参数orderId是服务器生成的订单唯一标识，不是客户端生成的clientOId</aside>

### 返回值

| 字段                | 含义      |
| ----------------- | ------- |
| cancelledOrderIds | 取消的订单id |

### 撤单被拒
如果订单不能撤销（已经成交或已经取消），会返回错误信息，可根据返回的`msg`获取原因。


## 基于clientOid 单个撤单
```json
{
    "clientOid": "6d539dc614db3"
}
```

此接口发送一个通过clientOid撤销订单的请求。

### HTTP请求
`DELETE /api/v1/orders/hf/client-order/{clientOid}?symbol={symbol}`
### API权限
此接口需要`交易权限`。
### 请求参数
请求参数 | 类型 | 是否必须 | 含义 |
--------- | ------- | -----------| -----------|
clientOid | String | 是 | 路径参数，客户端生成的标识 |
symbol | String | 是 | 交易对 比如，ETH-BTC |
#### 返回值
字段	| 含义 |
--------- | ------- |
clientOid | 客户端生成的标识 |

## 全部撤单

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

此接口，可以取消指定交易对所有状态为`open`的订单（包含高频交易账户和非高频交易账户的所有订单），返回值是是已取消订单的ID列表。

此接口只提交取消请求，实际取消结果需要通过查询订单状态或订阅websocket获取订单消息。

### HTTP请求
`DELETE /api/v1/orders/hf`
### API权限
此接口需要`交易权限`。
### 频率限制
此接口针对每个账号请求频率限制为`3次/3s`
### 请求参数
请求参数 | 类型 | 是否必须 | 含义 |
--------- | ------- | -----------| -----------|
symbol | String | 是 | 取消指定交易对的open订单 |
### 返回值
| 字段                | 含义      |
| ----------------- | ------- |
| cancelledOrderIds | 取消的订单id |

## 获取活跃订单列表

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

该接口是获取所有活跃订单列表，返回值是分页后的数据。返回数据都根据订单最新更新时间降序排序。


### HTTP请求
`GET /api/v1/orders/hf/active`

### API权限
此接口需要`通用权限`。

### 频率限制
此接口针对每个账号请求频率限制为`30次/3s`

### 请求参数
请求参数 | 类型 | 是否必须 | 含义 |
--------- | ------- | -----------| -----------|
symbol | String | 是 | 只返回指定交易对的订单信息 |
side | String | 否 | [可选] buy（买） 或 sell（卖）|
startAt | long | 否 | [可选] 开始时间（毫秒），限制订单最新更新时间|
endAt | long | 否 | [可选] 截止时间（毫秒），限制订单最新更新时间|

<aside class="notice"><code>lastId</code>用来过滤数据和分页，如果不输入<code>lastId</code>，默认返回最多100条最新数据，返回结果中有<code>lastId</code>，使用它作为查询条件，可以查询下一页次新数据</aside>

### 返回值

字段	| 含义 |
--------- | ------- |
id | 订单id，订单唯一标识 |
symbol | 交易对 |
opType | 操作类型: DEAL |
type | 订单类型 |
side | 买或卖 |
price | 订单价格 |
size | 下单数量 |
funds | 下单金额 |
dealFunds | 成交额 |
dealSize | 成交数量 |
fee | 手续费 |
feeCurrency | 计手续费币种 |
stp | 自成交保护 |
timeInForce | 订单时效策略 |
postOnly | 是否为被动委托 |
hidden | 是否为隐藏单 |
iceberg | 是否为冰山单 |
visibleSize | 冰山单在买卖盘可见数量 |
cancelAfter | timeInForce 为 GTT n秒后过期 |
channel | 下单来源 |
clientOid | 客户端生成的标识 |
remark | 订单说明 |
tags | 订单标签 |
isActive | 订单状态: `true`-订单状态为`open`; `false`-订单已成交或取消 |
cancelExist | 订单是否存在取消记录 |
createdAt | 创建时间 |
tradeType | 交易类型: TRADE（现货交易）|

#### 订单状态和结算

在买卖盘上，所有委托都处于活跃（Active）状态，从买卖盘上移除的订单则被标记为已完成（Done）状态。

订单被成交后到入账，因系统清算可能会有毫秒级别的延迟。

查询“active”状态的订单，没有时间限制。但查询“已完成”状态的订单时，您只能获取 `3 * 24` 小时时间范围内的数据（即：从当前时间起至`3 * 24` 小时前）。若超出时间范围，系统会默认查询 `3 * 24` 小时时间范围内的数据。

取消订单的历史记录仅保留`3`天。您将无法查询`3`天以前已取消的订单。

#### 订单轮询(Polling)

对于高频交易的用户，建议您在本地缓存和维护一份自己的活动委托列表，并使用市场数据流实时更新自己的订单信息。

## 获取成交订单列表

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

该接口是获取已成交订单列表，返回值是分页后的数据。返回数据都根据订单最新更新时间降序排序。


### HTTP请求
`GET /api/v1/orders/hf/done`

### API权限
此接口需要`通用权限`。

### 频率限制
此接口针对每个账号请求频率限制为`30次/3s`

### 请求参数
请求参数 | 类型 | 是否必须 | 含义 |
--------- | ------- | -----------| -----------|
symbol | String | 是 | 只返回指定交易对的订单信息 |
side | String | 否 | [可选] `buy`（买）或 `sell`（卖）|
type | String | 否 | [可选] 订单类型: `limit`（限价单）, `market`(市价单) |
startAt | long | 否 | [可选] 开始时间（毫秒），限制订单最新更新时间|
endAt | long | 否 | [可选] 截止时间（毫秒），限制订单最新更新时间|
lastId | long | 否 | [可选] 前一批次数据最后一条数据的id，默认获取最新数据 |
limit | int | 否 | [可选] 默认`100`，最大`200` |

<aside class="notice">您只能获取<code>3 * 24</code>小时时间范围内的数据（即：从当前时间起至<code>3 * 24</code>小时前），若超出时间范围，系统会默认查询<code>3 * 24</code>小时时间范围内的数据。</aside>

<aside class="notice"><code>lastId</code>用来过滤数据和分页，如果不输入<code>lastId</code>，默认返回最多100条最新数据，返回结果中有<code>lastId</code>，使用它作为查询条件，可以查询下一页次新数据</aside>

### 返回值
字段	| 含义 |
--------- | ------- |
id | 订单id，订单唯一标识 |
symbol | 交易对 |
opType | 操作类型: DEAL |
type | 订单类型 |
side | 买或卖 |
price | 订单价格 |
size | 下单数量 |
funds | 下单金额 |
dealFunds | 成交额 |
dealSize | 成交数量 |
fee | 手续费 |
feeCurrency | 计手续费币种 |
stp | 自成交保护 |
timeInForce | 订单时效策略 |
postOnly | 是否为被动委托 |
hidden | 是否为隐藏单 |
iceberg | 是否为冰山单 |
visibleSize | 冰山单在买卖盘可见数量 |
cancelAfter | timeInForce 为 GTT n秒后过期 |
channel | 下单来源 |
clientOid | 客户端生成的标识 |
remark | 订单说明 |
tags | 订单标签 |
isActive | 订单状态 true: 订单状态为 open; false: 订单已成交或取消 |
cancelExist | 订单是否存在取消记录 |
createdAt | 创建时间 |
tradeType | 交易类型: TRADE（现货交易）|


## 单个订单详情
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

此接口，可以通过订单id获取单个订单信息。

### HTTP请求
`GET /api/v1/orders/hf/{orderId}`

### API权限
此接口需要`通用权限`。
### 请求参数
请求参数 | 类型 | 是否必须 | 含义 |
--------- | ------- | -----------| -----------|
orderId | String | 是 | 路径参数，订单Id 唯一标识 |
symbol | String | 是 | 交易对 比如，ETH-BTC |

<aside class="notice">如果订单是已完结订单，您只能获取<code>3 * 24</code>小时时间范围内的数据（即：从当前时间起至<code>3 * 24</code>小时前）</aside>


#### 返回值
字段	| 含义 |
--------- | ------- |
id	| 订单id，订单唯一标识 |
symbol | 交易对 |
opType | 操作类型: DEAL |
type | 订单类型 |
side | 买或卖 |
price | 订单价格 |
size | 订单数量 |
funds | 下单金额 |
dealFunds | 成交额 |
dealSize | 成交数量 |
fee | 手续费 |
feeCurrency | 计手续费币种 |
stp | 自成交保护 |
timeInForce | 订单时效策略 |
postOnly | 是否为被动委托 |
hidden | 是否为隐藏单 |
iceberg | 是否为冰山单 |
visibleSize | 冰山单在买卖盘可见数量 |
cancelAfter | timeInForce 为 GTT n秒后触发 |
channel | 下单来源 |
clientOid | 客户端生成的标识 |
remark | 订单说明 |
tags | 订单标签 |
isActive | 订单状态 true: 订单状态为 open; false: 订单已成交或取消 |
cancelExist | 订单是否存在取消记录 |
createdAt | 创建时间 |
tradeType | 交易类型: TRADE（现货交易）|


## 基于clientOid 获取单个订单详情
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

此接口，可以通过clientOid查询单个订单的信息，若订单不存在则提示订单不存在。

### HTTP请求
`GET /api/v1/orders/hf/client-order/{clientOid}`
### API权限
此接口需要`通用权限`。
### 频率限制
此接口针对每个账号请求频率限制为`30次/3s`
### 请求参数
请求参数 | 类型 | 是否必须 | 含义 |
--------- | ------- | -----------| -----------|
clientOid | String | 是 | 路径参数，客户端生成的标识 |
symbol | String | 是 | 交易对 比如，ETH-BTC |

<aside class="notice">如果订单是已完结订单，您只能获取<code>3 * 24</code>小时时间范围内的数据（即：从当前时间起至<code>3 * 24</code>小时前）</aside>

#### 返回值
字段	| 含义 |
--------- | ------- |
id	| 订单id，订单唯一标识 |
symbol | 交易对 |
opType | 操作类型: DEAL |
type | 订单类型 |
side | 买或卖 |
price | 订单价格 |
size | 订单数量 |
funds | 下单金额 |
dealFunds | 成交额 |
dealSize | 成交数量 |
fee | 手续费 |
feeCurrency | 计手续费币种 |
stp | 自成交保护 |
timeInForce | 订单时效策略 |
postOnly | 是否为被动委托 |
hidden | 是否为隐藏单 |
iceberg | 是否为冰山单 |
visibleSize | 冰山单在买卖盘可见数量 |
cancelAfter | timeInForce 为 GTT n秒后触发 |
channel | 下单来源 |
clientOid | 客户端生成的标识 |
remark | 订单说明 |
tags | 订单标签 |
isActive | 订单状态 true: 订单状态为 open; false: 订单已成交或取消 |
cancelExist | 订单是否存在取消记录 |
createdAt | 创建时间 |
tradeType | 交易类型: TRADE（现货交易）|

# 成交明细

## 成交记录
```json
{
    "lastId": 10002,
    "items":[
        {
            "symbol":"BTC-USDT",
            "tradeId":"5c35c02709e4f67d5266954e",
            "orderId":"5c35c02703aa673ceec2a168",
            "counterOrderId":"5c1ab46003aa676e487fa8e3",
            "side":"buy",
            "liquidity":"taker",
            "forceTaker":true,
            "price":"0.083",
            "size":"0.8424304",
            "funds":"0.0699217232",
            "fee":"0",
            "feeRate":"0",
            "feeCurrency":"USDT",
            "type":"limit",
            "createdAt":1547026472000,
            "tradeType": "TRADE"
        }
    ]
}
```

此接口，可获取最近的成交明细列表 返回值是分页后的数据，根据时间降序排序。

### HTTP请求
`GET /api/v1/fills/hf`

### API权限
此接口需要`通用权限`。

### 频率限制
此接口针对每个账号请求频率限制为`9次/3s`

### 请求参数
请求参数 | 类型 | 是否必须 | 含义 |
--------- | ------- | -----------| -----------|
orderId | String | 否 | [可选] 查询该订单Id的成交明细（如果指定了`orderId`，请忽略其他查询条件）|
symbol | String | 否 | [可选] 只返回指定交易对的订单信息，如果没有指定`orderId`，则该参数是必填参数 |
side | String | 否 | [可选] `buy`（买） 或 `sell`（卖）|
type | String | 否 | [可选] 订单类型: `limit`（限价单）, `market`(市价单) |
startAt | long | 否 | [可选] 开始时间（毫秒），限制成交记录成交时间|
endAt | long | 否 | [可选] 截止时间（毫秒），限制成交记录成交时间|
lastId | long | 否 | [可选] 前一批次数据最后一条数据的id，默认获取最新数据 |
limit | int | 否 | [可选] 默认`100`，最大`200` |

<aside class="notice"><code>lastId</code>用来过滤数据和分页，如果不输入<code>lastId</code>，默认返回最多100条最新数据，返回结果中有<code>lastId</code>，使用它作为查询条件，可以查询下一页次新数据</aside>


### 返回值

字段 | 含义 |
--------- | ------- |
symbol | 交易对 |
tradeId | 交易Id |
orderId	| 订单Id |
counterOrderId | 对手方订单Id |
side | 买或卖 |
forceTaker | 是否强制作为taker处理 |
liquidity | 流动性类型: taker 或 maker |
price | 订单价格 |
size | 订单数量 |
funds | 成交额 |
fee | 手续费 |
feeRate | 手续费率 |
feeCurrency | 计手续费币种 |
type | 订单类型limit 或 market |
createdAt | 创建时间 |
tradeType | 交易类型: TRADE（现货交易）|

##### 查询时间范围

您只能获取 `3 * 24` 小时时间范围内的数据（即：从当前时间起至`3 * 24` 小时前）。若超出时间范围，系统会默认查询 `3 * 24` 小时时间范围内的数据。

##### 结算
结算分为两部分，一部分是成交结算，一部分是费用结算。当撮合完成后，这些数据将立即更新到我们的数据存储区，系统将启动结算并从您的预冻结资金中进行扣除。

##### 手续费

KuCoin平台上的订单分为两种类型：Taker 和 Maker。Taker单会与买卖盘上的已有订单立即成交，而Maker单则相反，会一直留在买卖盘中等待撮合。Taker单消耗了市场的流动性，因此会被收取taker费用，而Maker单增加了市场的流动性，会被收取较低的手续费甚至获得手续费补贴。请注意：市价单、冰山单和隐藏单都会被扣除taker手续费。

下单时，系统会预冻结您账户中的taker费用。流动性（liquidity）字段中的参数说明了订单将会被收取taker还是maker费用。

假设您的订单是限价单，当您下单后在撮合引擎中被立即撮合，我们将收取您taker费用，而如果您的订单没有被立即撮合或有部分剩余未被撮合都会进入买卖盘，进入买卖盘的订单在未被取消前成交都会收取您maker手续费。

进入撮合后与对手盘订单撮合，当指令订单剩余金额为0，交易完成，如果剩余资金不足以购买最低数量（0.00000001）的商品，则取消指令订单。

如果您的订单作为maker被成交，我们会将剩余预冻结的taker费用返还给您。

但需要注意的是:

- 当您创建了一个隐藏委托/冰山委托订单时，即使它未被撮合引擎立即成交而被被动成交，仍然会收取taker费用
- 被动委托收取maker费用。如果该委托下单后会立刻与市场已有委托(除冰山/隐藏订单外)撮合，那么该委托将被取消。如果被动委托下单后与冰山/隐藏订单立即成交，被动委托订单将收取maker费用

举例：

以BTC/USDT为例，假设您想市价买入1BTC，手续费率为0.1%，市场买卖盘数据如下：

Price（USDT）| Size（BTC）| Side |
--------- | ------- | ------- |
4200.00 | 0.18412309 | sell |
4015.60	| 0.56849308 | sell |
4011.32 | 0.24738383 | sell |
3995.64	| 0.84738383 | buy |
3988.60	| 0.20484000 | buy |
3983.85	| 1.37584908 | buy |

当您下一个买入市价单时，市场会立即成交，成交明细将分为3笔，如下图所示：

Price（USDT）| Size（BTC）| Fee（BTC）|
--------- | ------- | ------- |
4011.32 | 0.24738383 | 0.00024738 |
4015.60	| 0.56849308 | 0.00056849 |
4200.00	| 0.18312409 | 0.00018312 |