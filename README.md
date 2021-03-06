# Mytrust DAPP接口

## 概述

欢迎使用Mytrust DAPP接口，Mytrust提供SWTC、Ripple、Stellar、Callchain、Stream的钱包DAPP服务。Mytrust开放了第三方DAPP接口，让开放者可以开发自己的DAPP，为用户提供更多的DAPP构建能力。

## DAPP开发流程

 - DAPP开发者，只需要提供一个URL地址给Mytrust就行，就可以使用Mytrust APP里面用户钱包资产。
 - DAPP开发者不需要引以Mytrust的代码，也不需要开放任何代码给Mytrust。
 - 在DAPP加载完成之后，Mytrust钱包会在当前界面中注入DAPP SDK代码，当前DAPP需要监听`window.MTL`对象是否存在。当`window.MTL`对象存在时，即可以从中获得相应的当前钱包地址和接口等。
 - Mytrust提供测试版本的Mytrust便于用户进行自行添加、测试DAPP。

## DAPP开发参考案例

以下是DAPP的官方完整示例，可以参考开发
示例：[https://trust.coinpany.cn/dapp](https://trust.coinpany.cn/dapp)

## 使用

在使用Mytrust DAPP接口的第一步是判断在浏览器中是否已经存在了Mytrust DAPP接口，可以通过如下方式进行检测

```js
if (typeof window.MTL === 'undefined') {
    alert('请在Mytrust APP中打开DAPP');
} else {
    // TODO 业务处理
}
```

## 属性

Mytrust在DAPP启动之后提供如下一些属性，可以供用户进行DAP片开发：

1. `version`, 当前钱包版本号, 1.0.0
2. `platform`, 当前钱包的操作系统，`ios`或`android`
3. `system`, 当前钱包的钱包体系，`jingtum`, `ripple`, `stellar`, `callchain`, `stream`
4. `address`, 当前用户的钱包地址
5. `contracts`: [{ name: '名称', address: '地址'}], 当前钱包的联系人信息
6. `locale`, 当前钱包的语言设置, zh_cn

## 接口

接口提供异步的调用方式，返回信息格式为
```json
{
    "success": "boolean", 
    "msg": "string", 
    "data": "object"
}
```
其中`success`表示请求是否成功或者失败，`msg`表示请求回应的消息，`data`表示返回的接口。

接口定义如下两种特殊的类型，分别是`address`和`amount`，其中`address`是钱包地址的字符串，`amount`是金额的对象，其格式如下：
```json
{
    "value": "number",
    "currency": "string",
    "issuer": "string"
}
```

### MTL.getBalance(callback)

返回用户当前钱包的余额

例子：

```js
MTL.getBalance(function(data) {
    console.dir(data);
});
```

返回值：

字段名  | 字段值
  ------------- | -------------
 currency  | 币种英文名
 value  | 余额
 counterparty  | 网关地址
 
```json
[
    {
        "currency":"SWT",
        "value":"47.49699"
    },
    {
        "currency":"CNY",
        "value":"7.49699",
        "counterparty":"jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or"
    }
]
```

### MTL.getOrders(callback)

获取用户的当前挂单列表，返回的数据格式根据每个钱包体系的而定。

例子：

```js
MTL.getOrders(function(data) {
    console.dir(data);
});
```
返回值：

字段名  | 字段值
  ------------- | -------------
 type  | 类型buy买，sell卖
 pairs  | 交易对
 price  | 价格
 amount  | 数量
 seq  | orderId
 

```json
[
    {
        "type":"sell",
        "pairs":"SWT/CNY:jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or",
        "price":1,
        "amount":"1.000000",
        "seq":"106"
    }
]
```


### MTL.getTransaction(hash, callback)

获取用户的交易记录详情，返回的数据格式根据每个钱包体系的而定。

参数列表：

参数名  | 参数值
  ------------- | -------------
 hash  | 交易hash
 callback  | 回调函数
 
例子：

```js
MTL.getTransaction("353535BACD84B1428C48389CEF8E5B993301ABE17D5291E7C5C7410A272D5152", function(data) {
    console.dir(data);
});
```
返回值：

字段名  | 字段值
  ------------- | -------------
 result  | 请求状态
 hash  | 交易hash
 source  | 源地址
 sequence  | 订单号orderId
 memo  | 备注
 memo_type  | 备注类型
 time  | 时间
 operations  | 列表
 type  | 类型
 source  | 源地址
 destination  | 目标地址
 des  | 描述
 
```json
{
    "result":"tesSUCCESS",
    "hash":"364BC0EDF31ACF9232274CE4C65328CB946FB89594D923C41F270015EAA4BB07",
    "source":"jB9UKTLvxsZvNSn1bJW8N96usZLmWGYu6E",
    "operations":[
        {
            "type":"payment",
            "source":"jB9UKTLvxsZvNSn1bJW8N96usZLmWGYu6E",
            "destination":"jMQu5YzVaPMP8XpMqoHiG3fT3EoQbxu2tY",
            "des":"支付300HJT, issuer: jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or"
        }
    ],
    "sequence":"91",
    "memo":"",
    "memo_type":"",
    "time":"2019-01-25T06:51:00.000Z"
}
```


### MTL.getTransactions(options, callback)

获取用户的交易记录详情，返回的数据格式根据每个钱包体系而定。
其中options的格式基本为
```json
{
    "cursor": {},
    "limit": "number"
}
```
其中`jingtum`的`cursor`为{ledger: number, seq: number}，`ripple`的`cursor`为{ledger: number}，`stellar`的`cursor`为`paging_token`，`callchain`的`cursor`为{ledger: number}。

参数列表：

参数名  | 参数值
  ------------- | -------------
 cursor  | 起始位置数
 limit  | 条数限制
 
例子：

```js
MTL.getTransactions({}, function(data) {
    console.dir(data);
});
```
返回值：

字段名  | 字段值
  ------------- | -------------
 date  | 日期
 hash  | 交易hash
 type  | 交易类型，payment支付
 amount  | 数量
 currency  | 币种英文名
 issuer  | 网关地址
 result  | 请求状态
  
```json
{
    "txs":[
        {
            "date":"2019-01-25T06:51:00.000Z",
            "hash":"364BC0EDF31ACF9232274CE4C65328CB946FB89594D923C41F270015EAA4BB07",
            "type":"payment",
            "amount":1,
            "currency":"SWTC",
            "issuer":"jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or",
            "result":"tesSUCCESS"
        },
        {
            "date":"2019-01-25T06:51:00.000Z",
            "hash":"364BC0EDF31ACF9232274CE4C65328CB946FB89594D923C41F270015EAA4BB07",
            "type":"payment",
            "amount":1,
            "currency":"SWTC",
            "issuer":"jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or",
            "result":"tesSUCCESS"
        }
    ],
    "cursor":{
        "ledger":11880416,
        "seq":2
    }
}
```


### MTL.getTrusts(address, callback)

获取用户的授信列表

参数列表：

参数名  | 参数值
  ------------- | -------------
 address  | 钱包账号地址
 callback  | 回调函数
 
例子：

```js
MTL.getTrusts('jaKwVhmbcKdaXoJ7EoyZj4qzpwErgymszQ', function(data) {
    console.dir(data);
});
```
返回值：

字段名  | 字段值
  ------------- | -------------
 currency  | 币种英文名
 issuer  | 网关地址
 limit  | 授信额度
 balance  | 余额
 result  | 返回状态
 
```json
[
    {
        "currency":"HJT",
        "issuer":"jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or",
        "limit":10000000000,
        "balance":"38",
        "result":"tesSUCCESS"
    }
]
```


### MTL.getOrderbook(base, counter, callback)

获取base/counter交易对的账本信息
base和counter格式为{currency: string, issuer: address}

参数列表：

参数名  | 参数值
  ------------- | -------------
 base  | 基币信息，currency基币币种英文名，issuer基币币种网关
 counter  | 交易币种信息，currency交易币种英文名，issuer交易币种网关地址
 callback  | 回调函数
 
例子：

```js
MTL.getOrderbook(
    {currency: 'SWT', issuer: ''}, 
    {currency: 'CNY', issuer: 'jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or'}, function(data) {
    console.dir(data);
});
```
返回值：

字段名  | 字段值
  ------------- | -------------
 bids  | 买单，price买单价格，amount买单数量
 asks  | 卖单，price卖单价格，amount卖单数量
 

```json
{
    "bids":[
        {
            "price":2,
            "amount":"2.000000"
        },
        {
            "price":1,
            "amount":"1.000000"
        }
    ],
    "asks":[
        {
            "price":3,
            "amount":"3.000000"
        },
        {
            "price":4,
            "amount":"4.000000"
        }
    ]
}
```


### MTL.isValidAddress(address, callback)

提供工具类的方法，判断用户钱包地址是否有效

参数列表：

参数名  | 参数值
  ------------- | -------------
 address  | 钱包地址
 callback  | 回调函数
 
例子：

```js
MTL.isValidAddress('jaKwVhmbcKdaXoJ7EoyZj4qzpwErgymszQ', function(data) {
    console.dir(data);
});
```
返回值：

成功返回true

### MTL.pay(options, callback)

提交用户的支付请求，其中options的格式如下：
```json
{
    "dest": "address",
    "amount": "amount",
    "memo": "string"
}
```

参数列表：

参数名  | 参数值
  ------------- | -------------
 dest  | 目标地址
 amount  | 交易信息，value个数，currency币种符号，issuer币种网关地址
 memo  | 叙述
 callback  | 回调函数
 
例子：

```js
MTL.pay(
    {
        dest: '',
        amount: {value: 1.0, currency: 'SWT', issuer: ''},
        memo: 'hello'
    }, function(data) {
    console.dir(data);
})
```

返回值：

字段名  | 字段值
  ------------- | -------------
 resultCode  | 返回状态
 resultMessage  | 返回消息
 hash  | 交易hash
 
```json
{
    "resultCode":"telINSUF_FUND",
    "resultMessage":"The transaction was applied.Only final in a validated ledger",
    "hash":"364BC0EDF31ACF9232274CE4C65328CB946FB89594D923C41F270015EAA4BB07"
}
```

### MTL.trust(options, callback)

用户进行资产授信，其中options的格式如下：
```json
{
    "amount": "amount",
    "memo": "string"

}
```
参数列表：

参数名  | 参数值
  ------------- | -------------
 amount  | 交易信息，value个数，currency币种符号，issuer币种网关地址
 memo  | 叙述
 callback  | 回调函数
 

例子：

```js
MTL.trust({
    amount: {currency: 'CNY', issuer: 'jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or'},
    memo: 'trust you'
}, function(data) {
    console.dir(data);
});
```
返回值：

字段名  | 字段值
  ------------- | -------------
 resultCode  | 请求状态
 resultMessage  | 返回消息


```json
{
    "resultCode":"tesSUCCESS",
    "resultMessage":"The transaction was applied. Only final in a validated ledger"
}
```

### MTL.createOrder(options, callback)

用户进行挂单操作，其中options的格式如下：
```json
{
    "type": "sell/buy",
    "amount": "number",
    "price": "number",
    "base": {
        "currency": "string",
        "issuer": "address"
    },
    "counter": {
        "currency": "string",
        "issuer": "address"
    },
    "memo": "string"
}
```
参数列表：

参数名  | 参数值
  ------------- | -------------
 type  | 类型，buy买，sell卖
 amount  | 数量
 price  | 价格
 base  | 基币信息，currency币种符号，issuer网关地址
 counter  | 交易币信息，currency币种符号，issuer网关地址
 memo  | 叙述
 callback  | 回调函数
 
例子：

```js
MTL.createOrder({
    "type": "sell",
    "amount": 100,
    "price": 0.045,
    "base": {
        "currency": "SWT",
        "issuer": ""
    },
    "counter": {
        "currency": "CNY",
        "issuer": "jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or"
    },
    memo: "test"
}, function(data) {
    console.dir(data);
});
```
返回值：

字段名  | 字段值
  ------------- | -------------
 resultCode  | 请求状态
 resultMessage  | 返回消息
 

```json
{
    "resultCode":"telINSUF_FUND",
    "resultMessage":"Fund insufficient"
}
```

### MTL.cancelOrder(options, callback)

用户进行取消挂单操作，其中options的格式如下：

```json
{
    "order": "number"
}
```
参数列表：

参数名  | 参数值
  ------------- | -------------
 order  | 挂单时产生的订单号
 callback  | 回调函数
 

例子：

```js
MTL.cancelOrder({order: 101}, function(data) {
    console.dir(data);
});
```

返回值：

字段名  | 字段值
  ------------- | -------------
 resultCode  | 请求状态
 resultMessage  | 返回消息

```json
{
    "resultCode":"tesSUCCESS",
    "resultMessage":"The transaction was applied. Only final in a validated ledger"
}
```
