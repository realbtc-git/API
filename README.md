# API_Docs

欢迎有优秀做市策略且交易量大的用户参与做市商项目，如果您有疑问请发送邮件至：support@realbtc.io。

目前关于apikey申请和修改，请在“API设置”页面进行相关操作。<br>
[签名认证(重要，请仔细阅读)](https://github.com/ontrade-research/API_Docs/wiki/REST_authentication)



# 永续合约
## REST 行情、交易 API<br>
[永续合约REST接口示例](https://documenter.getpostman.com/view/14201442/TVzVgF4M)

## WebSocket 行情 API<br>

认证签名：
鉴权-Authentication
用户自⼰在火币网⽣成Access Key和Secret Key，Secret Key由用户自⼰保存，⽤户需提供Access Key。目前关于 apikey 申请和修改，请在“账户 - API 管理 ” 创建新API Key 填写备注(可选择绑定 ip)点击创建。其中 Access Key 为 API 访问密钥，Secret Key 为用户对请求进⾏签名的密钥(仅申请时可见)。用户按规则生成签名(Signature)。

交易功能 websocket 版本接⼝建立连接时首先要做鉴权操作，具体格式如下，

重要提示：这两个密钥与账号安全紧密相关，无论何时都请勿向其它人透露。

鉴权请求数据格式
{

"op": "auth",

"type": "api",

"AccessKeyId": "e2xxxxxx-99xxxxxx-84xxxxxx-7xxxx",

"SignatureMethod": "HmacSHA256",

"SignatureVersion": "2",

"Timestamp": "2017-05-11T15:19:30",

"Signature": "4F65x5A2bLyMWVQj3Aqp+B4w+ivaA7n5Oi2SuYtCJ9o=",

}

鉴权请求数据格式说明

|**字段名称**|   **类型**   |**说明** |
|---------|---------------|-----------------|
|op|string|必填；操作名称，鉴权固定值为auth|
|type|string|必填；认证方式 api表示接口认证，ticket 表示终端认证|
|cid	|string	|选填；Client请求唯一ID|
|AccessKeyId	|string|	type的值为api时必填；API 访问密钥, 您申请的 APIKEY 中的 AccessKey|
|SignatureMethod|	string|	type的值为api时必填；签名方法, 用户计算签名的基于哈希的协议，此处使用 HmacSHA256|
|SignatureVersion|	string	|type的值为api时必填；签名协议的版本，此处使用 2|
|Timestamp|	string|	type的值为api时必填；时间戳, 您发出请求的时间 (UTC 时区) 。在查询请求中包含此值有助于防止第三方截取您的请求。如:2017-05-11T16:22:06。再次强调是 (UTC 时区)|
|Signature	|string|	type的值为api时必填；签名, 计算得出的值，用于确保签名有效和未被篡改|

注意：
为了减少已有用户的接入工作量，此处使用了与REST接口同样的签名算法进行鉴权。

请注意大小写

当type为api时，参数op，type，cid，Signature不参加签名计算

此处签名计算中请求方法固定值为GET,其余值请参考REST接口签名算法文档

步骤：
示例例参数签名(Signature)计算过程如下，

规范要计算签名的请求 因为使用 HMAC 进⾏签名计算时，使⽤不同内容计算得到的结果会完全 不同。所以在进⾏签名计算前，请先对请求进⾏规范化处理。

请求方法(GET 或 POST)，后面添加换行符 \n 。

GET\n

添加小写的访问地址，后面添加换行符\n。
api.hbdm.com\n

访问方法的路径，后面添加换行符\n。
/notification\n

按照ASCII码的顺序对参数名进行排序(使⽤ UTF-8 编码，且进⾏了 URI 编码，十六进制字符必须 大写，如‘:’会被编码为'%3A'，空格被编码为'%20')。例如，下面是请求参数的原始顺序，进⾏过 编码后。
AccessKeyId=e2xxxxxx-99xxxxxx-84xxxxxx- 7xxxx&SignatureMethod=HmacSHA256&SignatureVersion=2&Timestamp=2017-05- 11T15%3A19%3A30

按照以上顺序，将各参数使用字符’&’连接。

组成最终的要进行签名计算的字符串如下:

计算签名，将以下两个参数传入加密哈希函数: 要进行签名计算的字符串，进行签名的密钥(SecretKey)

得到签名计算结果并进行 Base64编码

将上述值作为参数Signature的取值添加到 API 请求中。 将此参数添加到请求时，必须将该值进⾏URI编码。


|**接口类型**|   **接口数据类型**   |**请求方法** |**类型**   |**描述**                     |**需要验签**        |
|----------- |------------------ |------------------------------------------------------------------------------------------------------------------------------------------------------------------- |---------- |---------------------------- |--------------|
|Websocket   |市场行情接口 |         market.$symbol.kline.$period|                                                                                                            sub        |订阅 KLine 数据              |否|
|Websocket   |市场行情接口|           market.$symbol.kline.$period|                                                                                                                    req        |请求 KLine 数据              |否|
|Websocket   |市场行情接口           |market.$symbol.depth.$type |                                                                                                                     sub        |订阅 Market Depth 数据       |否|
|Websocket   |市场行情接口           |market.$symbol.detail|                                                                                                                     sub        |订阅 Market detail 数据       |否|
|Websocket   |市场行情接口           |market.$symbol.trade.detail |          req        |请求Trade detail 数据     |否|
|Websocket   |市场行情接口           |market.$symbol.trade.detail|        sub |订阅 Trade Detail 数据  | 否  |
|Websocket   |订单和用户数据接口      |orders.$symbol             |            sub|    订阅订单成交数据 | 是|
|Websocket   |订单和用户数据接口      |matchOrders.$symbol             |            sub|    订阅订单撮合数据 | 是|
|Websocket   |订单和用户数据接口      |accounts.$symbol             |            sub|    订阅资产变动数据 | 是|
|Websocket   |订单和用户数据接口      |positions.$symbol             |            sub|    订阅持仓变动更新数据 | 是|
|Websocket   |指数价格数据接口       |indexs.$symbol               |            sub|    订阅指数价格数据 | 否|
|Websocket   |订单和用户数据接口       |liquidationprice.$symbol        |            sub|    预估爆仓价格数据 | 是|

## C++ API
|**业务类型**|   **业务名称**   |**请求接口** |**响应接口**   |**数据流**   |
|---------|---------------|---------------------------------|---------------------------------|-----------|
|登录|登录|ReqUserLogin|OnRspUserLogin|对话流|
|登录|登出|ReqUserLogout|OnRspUserLogout|对话流|
|交易|报单录入|ReqOrderInsert|OnRspOrderInsert|对话流|
|交易|报单操作|ReqOrderAction|OnRspOrderAction|对话流|
|私有回报|成交回报|N/A|OnRtnTrade|私有流|
|私有回报|报单回报|N/A|OnRtnOrder|私有流|
|私有回报|会员资金通知|N/A|OnRtnTradingAccount|私有流|
|私有回报|客户持仓通知|N/A|OnRtnClientPosition|私有流|
|私有回报|报单录入错误回报|N/A|OnErrRtnOrderInsert|私有流|
|私有回报|报单操作错误回报|N/A|OnErrRtnOrderAction|私有流|
|公共通知|合约交易状态通知|N/A|OnRtnInstrumentStatus|公共流|
|公共通知|增加合约通知|N/A|OnRtnInsInstrument|公共流|
|公共通知|删除合约通知|N/A|OnRtnDelInstrument|公共流|
|查询|会员资金查询|ReqQryTradingAccount|OnRspQryTradingAccount|查询流|
|查询|报单查询|ReqQryOrder|OnRspQryOrder|查询流|
|查询|成交单查询|ReqQryTrade|OnRspQryTrade|查询流|
|查询|会员客户查询|ReqQryClient|OnRspQryClient|查询流|
|查询|客户持仓查询|ReqQryClientPosition|OnRspQryClientPosition|查询流|
|查询|合约查询|ReqQryInstrument|OnRspQryInstrument|查询流|
|查询|合约交易状态查询|ReqQryInstrumentStatus|OnRspQryInstrumentStatus|查询流|
|查询|普通行情查询|ReqQryMarketData|OnRspQryMarketData|查询流|
|查询|客户手续费查询|ReqQryPartClientFee|OnRspQryPartClientFee|查询流|
|行情|主题查询|ReqQryTopic|OnRspQryTopic|查询流|
|行情|订阅主题|ReqSubscribeTopic|OnRspSubscribeTopic|对话流|
|行情|深度行情通知|N/A|OnRtnDepthMarketData|私有流|

#### 订阅 KLine 数据

成功建立和 WebSocket API 的连接之后，向 Server发送如下格式的数据来订阅数据：

    {
    "sub": "market.$symbol.kline.$period",
    "id": "id generate by client"
    }

  |**参数名称**|   **是否必须**  | **类型**  | **描述** |  **默认值**  | **取值范围**
  |--------------| -----------------| ---------- |----------| ------------| --------------------------------------------------------------------------------|
  |symbol  |       true         |  string  |   交易对   |               |币本位："BTC","ETH"...； USDT本位："BTCUSDT","ETHUSDT"..|
  |period    |     true          | string   |  K线周期     |            |1min, 5min, 15min, 30min, 1hour,4hour,1day, 1mon|

正确订阅请求参数的例子：

    {
    "sub": "market.BTC.kline.1min",
    "id": "id1"
    }

订阅成功返回数据的例子

    {
    "id": "id1",
    "status": "ok",
    "subbed": "market.BTC.kline.1min",
    "ts": 1489474081631
    }

之后每当 KLine 有更新时，client 会收到数据，例子

    {
     "ch": "market.BTC.kline.1min",
     "ts": 1489474082831,
     "tick":
        {
         "id": 1489464480,
         "mrid": 268168237,
         "vol": 100,
         "count": 0,
         "open": 7962.62,
         "close": 7962.62,
         "low": 7962.62,
         "high": 7962.62,
         "amount": 0.3 //单位BTC 币种的数量
        }
    }
    
    tick 说明
    
    "tick":
      {
       "id": K线id,
       "mrid": 268168237,
       "vol": 成交量张数,
       "count": 成交笔数,
       "open": 开盘价,
       "close": 收盘价,当K线为最晚的一根时，是最新成交价
       "low": 最低价,
       "high": 最高价,
       "amount": BTC, 即 sum(每一笔 该合约面值* 该笔的成交量/成交价)
    }

**错误订阅的列子**

错误订阅(错误的 symbol)

    {
     "sub": "market.invalidsymbol.kline.1min",
     "id": "id2"
    }

订阅失败返回数据的例子

    {
     "id": "id2",
     "status": "error",
     "err-code": "bad-request",
     "err-msg": "invalid topic market.invalidsymbol.kline.1min",
     "ts": 1494301904959
    }

错误订阅(错误的 topic)

    {
     "sub": "market.BTC.kline.3min",
     "id": "id3"
    }

订阅失败返回数据的例子

    {
     "id": "id3",
     "status": "error",
     "err-code": "bad-request",
     "err-msg": "invalid topic market.BTC.kline.3min",
     "ts": 1494310283622
    }

#### 请求 KLine 数据 </a>

成功建立和 WebSocket API 的连接之后，向Server发送如下格式的数据来请求数据：

    {
     "req": "market.$symbol.kline.$period",
     "id": "id generated by client",
     "from": "optional, type: long, 2017-07-28T00:00:00+08:00 至2050-01-01T00:00:00+08:00 之间的时间点，单位：秒",
      "to": "optional, type: long, 2017-07-28T00:00:00+08:00 至2050-01-01T00:00:00+08:00 之间的时间点，单位：秒，必须比 from 大"}

| **参数名称** | **是否必须** | **类型** | **描述** | **默认值** |**取值范围** |
| -------- | -------- | ------ | ------ | ------- |---------------------------------------- |
| symbol | true | string |交易对 | |币本位："BTC","ETH"...； USDT本位："BTCUSDT","ETHUSDT"..|
| period | true | string | K线周期 | | 1min, 5min, 15min, 30min, 1hour,4hour,1day, 1mon|

    [t1, t5] 假设有 t1  ~ t5 的K线：
    
    from: t1, to: t5, return [t1, t5].
    from: t5, to: t1, which t5  > t1, return [].
    from: t5, return [t5].
    from: t3, return [t3, t5].
    to: t5, return [t1, t5].
    from: t which t3  < t  <t4, return [t4, t5].
    to: t which t3  < t  <t4, return [t1, t3].
    from: t1 and to: t2, should satisfy 1325347200  < t1  < t2  < 2524579200.

**备注**：一次最多300条

请求 KLine 数据请求参数的例子：

    {
    "req": "market.BTC_CQ.kline.1min",
    "id": "id4"
    }

请求成功返回数据的例子：

    {
     "rep": "market.BTC.kline.1min",
     "status": "ok",
     "id": "id4",
     "tick": [
       {
        "vol": 100,
        "count": 27,
        "id": 1494478080,
        "open": 10050.00,
        "close": 10058.00,
        "low": 10050.00,
        "high": 10058.00,
        "amount": 175798.757708
       },
       {
        "vol": 300,
        "count": 28,
        "id": 1494478140,
        "open": 10058.00,
        "close": 10060.00,
        "low": 10056.00,
        "high": 10065.00,
        "amount": 158331.348600
       }
     ]
    }

#### 订阅 Market Depth 数据 </a>

成功建立和 WebSocket API 的连接之后，向 Server发送如下格式的数据来订阅数据：

    {
    "sub": "market.$symbol.depth.$type",
    "id": "id generated by client"
    }

|**参数名称**   |**是否必须**   |**类型**   |**描述**     |**默认值**   |**取值范围**|
|-------------- |-------------- |---------- |------------ |------------ |---------------------------------------------------------------------------------|
|symbol         |true           |string     |交易对            |        |币本位："BTC","ETH"...； USDT本位："BTCUSDT","ETHUSDT"..|
|type           |true           |string     |Depth 类型        |        |(150档数据)  step0, step1, step2, step3, step4, step5（合并深度1-5）,step0时，不合并深度;(20档数据)  step6, step7, step8, step9, step10, step11（合并深度7-11）；step6时，不合并深度|

**备注**：用户选择“合并深度”时，一定报价精度内的市场挂单将予以合并显示。合并深度仅改变显示方式，不改变实际成交价格。

正确订阅请求参数的例子：

    {
    "sub": "market.BTC.depth.step0",
    "id": "id5"
    }

订阅成功返回数据的例子：

    {
      "id": "id5",
      "status": "ok",
      "subbed": "market.BTC.depth.step0",
      "ts": 1489474081631
    }

之后每当 depth 有更新时，client 会收到数据，例子：

    {
     "ch": "market.BTC.depth.step0",
     "ts": 1489474082831,
     "tick":
       {
        "mrid": 269073229,
         "id": 1539843937,
            "bids": [
             [9999.9101,1],
             [9992.3089,2]
                    ],
             "asks": [
              [10010.9800,10],
              [10011.3900,15]
                     ],
          "ts": 1539843937417,
           "version": 1539843937,
           "ch": "market.BTC.depth.step0"
        }
    }
    
    tick 说明：
    "tick": {
      "bids": [
        [买1价,买1量]
        [买2价,买2量]
         //more data here
       ],
       "asks": [
        [卖1价,卖1量]
        [卖2价,卖2量]
        //more data here
       ]
    }

#### 订阅 Market Detail 数据 </a>

成功建立和 WebSocket API 的连接之后，向 Server发送如下格式的数据来请求数据:

    {
     "sub": "market.$symbol.detail",
     "id": "id generated by client"
    }

|**参数名称**   |**是否必须**   |**类型**   |**描述**     |**默认值**   |**取值范围**|
|-------------- |-------------- |---------- |------------ |------------ |--------------------------------------------------------------------------------|
|symbol         |true           |string     |交易对      |              |币本位："BTC","ETH"...； USDT本位："BTCUSDT","ETHUSDT"..|

订阅 Market Detail 数据请求参数的例子：

    {
     "sub": "market.BTC.detail",
     "id": "id6"
    }

请求成功返回数据的例子：

    {
    "ch": "market.BTC.detail",
    "ts": 1539842340724,
    "tick": {
    	"id": 1539842340,
    	"mrid": 268041138,
    	"open": 6740.47,
    	"close": 7800,
    	"high": 7800,
    	"low": 6726.13,
    	"amount": 477.1200312075244664773339914558562673572,
    	"vol": 32414,
    	"count": 1716
            }
       }

tick数据说明：

    "tick":
         {
           "id": id,
           "mrid": 1494496390000,
           "vol": 成交量(张),
           "count": 成交笔数,
           "open": 开盘价,
           "close": 收盘价,当K线为最晚的一根时，是最新成交价
           "low": 最低价,
           "high": 最高价,
           "amount": 成交量(币), 即 sum(每一笔成交量(张)*单张合约面值/该笔成交价)
         }

#### 请求 Trade Detail 数据

成功建立和 WebSocket API 的连接之后，向 Server 发送如下格式的数据来请求数据：

    {
     "req": "market.$symbol.trade.detail",
     "id": "id generated by client"
    }

仅返回当前 Trade Detail

请求 Market Detail 数据请求参数的例子：

    {
     "req": "market.BTC.trade.detail",
     "id": "id8"
    }

请求成功返回数据的例子：



    {
     "ch": "market.BTC.trade.detail",
     "ts": 1489474082831,
     "data": [
              {
               "id":601595424,
               "price":10195.64,
               "amount":100,
               "direction":"buy",
               "ts":1494495766000
               },
              {
              "id":601595423,
              "price":10195.64,
              "amount":200,
              "direction":"buy",
              "ts":1494495711000
              }
            ]
     }

tick数据说明：

    "data": [
      {
       "id": 消息ID,
       "price": 成交价,
       "amount": 成交量（张）,
       "direction": 成交方向,
       "ts": 时间戳
      }
     ]

#### 订阅 Trade Detail 数据 </a>

成功建立和 WebSocket API 的连接之后，向 Server发送如下格式的数据来订阅数据：

    {
    "sub": "market.$symbol.trade.detail",
    "id": "id generated by client"
    }

**备注**：仅能获取最近 300 个 Trade Detail 数据。

|**参数名称**   |**是否必须**   |**类型**   |**描述**   |**默认值**   |**取值范围**|
|-------------- |-------------- |---------- |---------- |------------ |--------------------------------------------------------------------------------|
|symbol         |true           |string     |合约名称    |            |币本位："BTC","ETH"...； USDT本位："BTCUSDT","ETHUSDT"..|

正确订阅请求参数的例子：

    {
     "sub": "market.BTC.trade.detail",
     "id": "id7"
    }

订阅成功返回数据的例子：

    {
     "id": "id7",
     "status": "ok",
     "subbed": "market.BTC.trade.detail",
     "ts": 1489474081631
    }

之后每当 Trade Detail 有更新时，client 会收到数据，例子：

    {
    "ch": "market.BTC.trade.detail",
    "ts": 1539831709042,
    "tick": {
    	"id": 265842227,
    	"ts": 1539831709001,
    	"data": [{
    		"amount": 20,
    		"ts": 1539831709001,
    		"id": 265842227259096443,
    		"price": 6742.25,
    		"direction": "buy"
    	        }]
              }
     }

data 说明：

    "data": [
      {
       "id": 消息ID,
       "price": 成交价,
       "amount": 成交量（张）,
       "direction": 成交方向,
       "ts": 时间戳
      }
     ]

#### 订阅订单成交数据（sub） </a>
成功建立和 WebSocket API 的连接之后，向 Server发送如下格式的数据来订阅数据：

    {
      "op": "sub",
      "cid": "id generated by client",
      "topic": "orders.btc"
    }

|**参数名称**   |**是否必须**   |**类型**   |**描述**   |**默认值**   |**取值范围**|
|-------------- |-------------- |---------- |---------- |------------ |--------------------------------------------------------------------------------|
|op         |true           |string     |必填；操作名称，订阅固定值为sub    |            |     &nbsp;  |
|cid         |false           |string     |选填;Client 请求唯一 ID    |            |     &nbsp;  |
|topic         |true           |string     |订阅主题名称，orders.$symbol ,$symbol可以为*，即订阅所有品种，symbol有：币本位："BTC","ETH"...； USDT本位："BTCUSDT","ETHUSDT"..   |            |     &nbsp;  |

正确订阅请求参数的例子：

    {
     "op": "sub",
     "cid": "1",
     "topic": "orders.btc"
    }

订阅成功返回数据的例子：

    {
        "op": "notify",
        "topic": "orders.btc",
        "ts": 1489474082831,
        "symbol": "BTC",
        "contract_type": "",
        "contract_code": "BTCPERP",
        "volume": 111,
        "price": 1111,
        "order_price_type": "limit",
        "direction": "buy",
        "offset": "open",
        "status": 6,
        "lever_rate": 10,
        "order_id": 633989207806582784,
        "order_id_str": "633989207806582784",
        "client_order_id": 10683,
        "order_source": "web",
        "order_type": 1,
        "created_at": 1408076414000,
        "trade_volume": 1,
        "trade_turnover": 1200,
        "fee": 0,
        "trade_avg_price": 10,
        "margin_frozen": 10,
        "profit": 2,
        "trade": [{
            "id": "2131234825-6124591349-1",
            "trade_id": 112,
            "trade_volume": 1,
            "trade_price": 123.4555,
            "trade_fee": 0.234,
            "trade_turnover": 34.123,
            "created_at": 1490759594752,
            "role": "maker"
        }]
    }

  订阅成功返回数据的说明：

|**字段**   |**类型**   |**说明**   |
|-------------- |-------------- |---------- |
|op|string|必填;操作名称，推送固定值为 notify;|
|topic|string|必填;推送的主题|
|ts|long|服务端应答时间戳|
|symbol|string|品种ID|
|contract_type|string|合约类型|
|contract_code|string|合约代码|
|volume|decimal|委托数量|
|price|decimal|委托价格|
|order_price_type|string|订单报价类型 "limit":限价 "opponent":对手价 "post_only":只做maker单,post only下单只受用户持仓数量限制|
|direction|string|"buy":买 "sell":卖|
|offset|string|"open":开 "close":平|
|status|int|订单状态(1准备提交 2准备提交 3已提交 4部分成交 5部分成交已撤单 6全部成交 7已撤单)|
|lever_rate|int|杠杆倍数|
|order_id|bigint|订单ID|
|order_id_str|string|订单ID|
|client_order_id|long|客户订单ID|
|order_source|int|订单来源（system:系统 web:用户网页 api:用户API m:用户M站 risk:风控系统）|
|order_type|int|订单类型 1:报单 、 2:撤单 、 3:强平、4:交割|
|created_at|long|订单创建时间|
|trade_volume|decimal|成交数量|
|trade_turnover|decimal|成交总金额|
|fee|decimal|手续费|
|trade_avg_price|decimal|成交均价|
|margin_frozen|decimal|冻结保证金|
|profit|decimal|收益|
|liquidation_type|string|强平类型 0:非强平类型，1：多空轧差， 2:部分接管，3：全部接管|



#### 订阅订单撮合数据（sub） </a>
成功建立和 WebSocket API 的连接之后，向 Server发送如下格式的数据来订阅数据：

    {
      "op": "sub",
      "cid": "id generated by client",
      "topic": "matchOrders.btc"
    }

|**参数名称**   |**是否必须**   |**类型**   |**描述**   |**默认值**   |**取值范围**|
|-------------- |-------------- |---------- |---------- |------------ |--------------------------------------------------------------------------------|
|op         |true           |string     |必填；操作名称，订阅固定值为sub    |            |     &nbsp;  |
|cid         |false           |string     |选填;Client 请求唯一 ID    |            |     &nbsp;  |
|topic         |true           |string     |订阅主题名称，matchOrders.$symbol ,$symbol可以为*，即订阅所有品种，symbol有：币本位："BTC","ETH"...； USDT本位："BTCUSDT","ETHUSDT"..    |            |     &nbsp;  |

正确订阅请求参数的例子：

    {
     "op": "sub",
     "cid": "1",
     "topic": "matchOrders.btc"
    }

订阅成功返回数据的例子：

    {
      "op": "notify",           // 操作名称
      "topic": "matchOrders.btc",     // 主题
      "ts": 1489474082831,    
      "symbol": "BTC",         //品种
      "contract_type": "",     //合约类型
      "contract_code": "BTCPERP",     //合约代码
      "status": 1,   //订单状态(3未成交 4部分成交 5部分成交已撤单 6全部成交 7已撤单)
      "order_id": 106837,     //订单ID       
      "order_id_str": "106837",     //订单ID ,字符串类型
      "order_type": "1",    //订单类型  1:报单 、 2:撤单 、 3:强平、4:交割
      "trade":[{
        "id": "1232-213123-1231", //成交唯一ID
        "trade_id":112,     //撮合结果id
        "trade_volume":1,    //成交量
        "trade_price":123.4555,     //撮合价格
        "trade_turnover":34.123,     //成交金额
        "created_at": 1490759594752,    //成交时间
        "role": "maker"
      }]
    }

  订阅成功返回数据的说明：

|**字段**   |**类型**   |**说明**   |
|-------------- |-------------- |---------- |
|op|string|必填;操作名称，推送固定值为 notify;|
|topic|string|必填;推送的主题|
|ts|long|服务端应答时间戳|
|symbol|string|品种ID|
|contract_type|string|合约类型|
|contract_code|string|合约代码|
|status|int|订单状态(1准备提交 2准备提交 3已提交 4部分成交 5部分成交已撤单 6全部成交 7已撤单)|
|order_id|bigint|订单ID|
|order_id_str|string|订单ID|
|order_type|int|订单类型 1:报单 、 2:撤单 、 3:强平、4:交割|
|&lt;list&gt;(属性名称: trade)|||
|id|string|全局唯一交易标识|
|trade_id|long|撮合结果id，与api/v1/contract_matchresults返回结果中的match_id一样, 非唯一，可重复，注意：一个撮合结果代表一个taker单和N个maker单的成交记录的集合，如果一个taker单吃了N个maker单，那这N笔trade都是一样的撮合结果id|
|trade_volume|decimal|成交量|
|trade_price|decimal|撮合价格|
|trade_turnover|decimal|成交金额|
|created_at|long|创建时间|
|role|string|taker或maker|
|&lt;/list&gt;||&nbsp;|



#### 订阅资产变动数据（sub） </a>
成功建立和 WebSocket API 的连接之后，向 Server发送如下格式的数据来订阅数据：

    {
      "op": "sub",
      "cid": "id generated by client",
      "topic": "accounts.btc"
    }

|**参数名称**   |**是否必须**   |**类型**   |**描述**   |**默认值**   |**取值范围**|
|-------------- |-------------- |---------- |---------- |------------ |--------------------------------------------------------------------------------|
|op         |true           |string     |必填；操作名称，订阅固定值为sub    |            |     &nbsp;  |
|cid         |false           |string     |选填;Client 请求唯一 ID    |            |     &nbsp;  |
|topic         |true           |string     |订阅主题名称，accounts.$symbol ,$symbol可以为*，即订阅所有品种，symbol有：币本位："BTC","ETH"...； USDT本位："BTCUSDT","ETHUSDT"..    |            |     &nbsp;  |

正确订阅请求参数的例子：

    {
     "op": "sub",
     "cid": "1",
     "topic": "accounts.btc"
    }

订阅成功返回数据的例子：

    {
      "op": "notify",
      "topic": "accounts",
      "ts": 1489474082831,
      "data": [{
          "symbol": "BTC",
          "margin_balance": 1,
          "margin_static": 1,
          "margin_position": 0,
          "margin_frozen": 3.33,
          "margin_available": 0.34,
          "profit_real": 3.45,
          "profit_unreal": 7.45,
          "withdraw_available": 4.0989898,
          "risk_rate": 100,
          "lever_rate": 10,
          "adjust_factor": 0.1
      }]
    }

  订阅成功返回数据的说明：

|**字段**   |**类型**   |**说明**   |
|-------------- |-------------- |---------- |
|op|string|必填;操作名称，推送固定值为 notify;|
|topic|string|必填;推送的主题|
|ts|long|响应生成时间点，单位：毫秒|
|&lt;data&gt;||&nbsp;|
|symbol|string|品种代码 ,，symbol有：币本位："BTC","ETH"...； USDT本位："BTCUSDT","ETHUSDT".. ，当 $symbol值为 * 时代表订阅所有品种|
|margin_balance|decimal|账户权益|
|margin_static|decimal|静态权益|
|margin_position|decimal|持仓保证金（当前持有仓位所占用的保证金）|
|margin_frozen|decimal|冻结保证金|
|margin_available|decimal|可用保证金|
|profit_real|decimal|已实现盈亏|
|profit_unreal|decimal|未实现盈亏|
|risk_rate|decimal|保证金率|
|withdraw_available|decimal|可划转数量|
|lever_rate|decimal|杠杆倍数|
|adjust_factor|decimal|调整系数|
|&lt;/data&gt;||&nbsp;|



#### 订阅持仓变动更新数据（sub） </a>
成功建立和 WebSocket API 的连接之后，向 Server发送如下格式的数据来订阅数据：

    {
      "op": "sub",
      "cid": "id generated by client",
      "topic": "positions.btc"
    }

|**参数名称**   |**是否必须**   |**类型**   |**描述**   |**默认值**   |**取值范围**|
|-------------- |-------------- |---------- |---------- |------------ |--------------------------------------------------------------------------------|
|op         |true           |string     |必填；操作名称，订阅固定值为sub    |            |     &nbsp;  |
|cid         |false           |string     |选填;Client 请求唯一 ID    |            |     &nbsp;  |
|topic         |true           |string     |订阅主题名称，positions.$symbol ,$symbol可以为*，即订阅所有品种，，symbol有：币本位："BTC","ETH"...； USDT本位："BTCUSDT","ETHUSDT"..    |            |     &nbsp;  |

正确订阅请求参数的例子：

    {
     "op": "sub",
     "cid": "1",
     "topic": "positions.btc"
    }

订阅成功返回数据的例子：

    {
      "op": "notify",
      "topic": "positions",
      "ts": 1489474082831,
      "data": [{
          "symbol": "BTC",
          "contract_code": "BTCPERP",
          "contract_type": "",
          "volume": 1,
          "available": 0,
          "frozen": 1,
          "cost_open": 422.78,
          "cost_hold": 422.78,
          "profit_unreal": 0.00007096,
          "profit_rate": 0.07,
          "profit": 0.97,
          "position_margin": 3.4,
          "lever_rate": 10,
          "direction": "sell",
          "last_price": 9584.41
      }]
    }

  订阅成功返回数据的说明：

|**字段**   |**类型**   |**说明**   |
|-------------- |-------------- |---------- |
|op|string|必填;操作名称，推送固定值为 notify;|
|topic|string|必填;推送的主题|
|ts|long|响应生成时间点，单位：毫秒|
|&lt;data&gt;||&nbsp;|
|symbol|string|品种代码 ,symbol有：币本位："BTC","ETH"...； USDT本位："BTCUSDT","ETHUSDT".. ，当 $symbol值为 * 时代表订阅所有品种|
|contract_code|string|合约代码|
|contract_type|string|合约类型:无|
|volume|decimal|持仓量|
|available|decimal|可平仓数量|
|frozen|decimal|冻结数量|
|cost_open|decimal|开仓均价|
|cost_hold|decimal|持仓均价|
|profit_unreal|decimal|未实现盈亏|
|profit_rate|decimal|收益率|
|profit|decimal|收益|
|position_margin|decimal|持仓保证金|
|lever_rate|decimal|杠杆倍数|
|direction|string|仓位方向 "buy":买 "sell":卖|
|last_price|decimal|最新成交价|
|&lt;/data&gt;||&nbsp;|



#### 订阅标记价格数据（sub） </a>
成功建立和 WebSocket API 的连接之后，向 Server发送如下格式的数据来订阅数据：

    {
      "op": "sub",
      "cid": "id generated by client",
      "topic": "indexs.btc"
    }

|**参数名称**   |**是否必须**   |**类型**   |**描述**   |**默认值**   |**取值范围**|
|-------------- |-------------- |---------- |---------- |------------ |--------------------------------------------------------------------------------|
|op         |true           |string     |必填；操作名称，订阅固定值为sub    |            |     &nbsp;  |
|cid         |false           |string     |选填;Client 请求唯一 ID    |            |     &nbsp;  |
|topic         |true           |string     |订阅主题名称，indexs.$symbol ,$symbol可以为*，即订阅所有品种，symbol有：币本位："BTC","ETH"...； USDT本位："BTCUSDT","ETHUSDT"..    |            |     &nbsp;  |

正确订阅请求参数的例子：

    {
     "op": "sub",
     "cid": "1",
     "topic": "positions.btc"
    }

订阅成功返回数据的例子：

    {
      "op": "notify",
      "topic": "indexs.btc",
      "ts": 1489474082831,
      "data": {
          "symbol": "BTC",
          "currentFundRate": 2.2,
          "fairPrice": 3.3,
          "lastFundsRate": 4.4
      }
    }

  订阅成功返回数据的说明：

|**字段**   |**类型**   |**说明**   |
|-------------- |-------------- |---------- |
|op|string|必填;操作名称，推送固定值为 notify;|
|topic|string|必填;推送的主题|
|ts|long|响应生成时间点，单位：毫秒|
|&lt;data&gt;||&nbsp;|
|symbol|string|品种代码 ,，symbol有：币本位："BTC","ETH"...； USDT本位："BTCUSDT","ETHUSDT".. ，当 $symbol值为 * 时代表订阅所有品种|
|currentFundRate|decimal|当前资金费率|
|fairPrice|decimal|标记价格|
|lastFundsRate|decimal|预测资金费率|
|&lt;/data&gt;||&nbsp;|



#### 订阅预估爆仓价格数据（sub） </a>
成功建立和 WebSocket API 的连接之后，向 Server发送如下格式的数据来订阅数据：

    {
      "op": "sub",
      "cid": "id generated by client",
      "topic": "liquidationprice.btc"
    }

|**参数名称**   |**是否必须**   |**类型**   |**描述**   |**默认值**   |**取值范围**|
|-------------- |-------------- |---------- |---------- |------------ |--------------------------------------------------------------------------------|
|op         |true           |string     |必填；操作名称，订阅固定值为sub    |            |     &nbsp;  |
|cid         |false           |string     |选填;Client 请求唯一 ID    |            |     &nbsp;  |
|topic         |true           |string     |订阅主题名称，liquidationprice.$symbol ,$symbol可以为*，即订阅所有品种，symbol有：币本位："BTC","ETH"...； USDT本位："BTCUSDT","ETHUSDT"..    |            |     &nbsp;  |

正确订阅请求参数的例子：

    {
     "op": "sub",
     "cid": "1",
     "topic": "liquidationprice.btc"
    }

订阅成功返回数据的例子：

    {
      "op": "notify",
      "topic": "liquidationprice.btc",
      "ts": 1489474082831,
      "data": {
          "liquidation_price": 333.2
      }
    }

  订阅成功返回数据的说明：

|**字段**   |**类型**   |**说明**   |
|-------------- |-------------- |---------- |
|op|string|必填;操作名称，推送固定值为 notify;|
|topic|string|必填;推送的主题|
|ts|long|响应生成时间点，单位：毫秒|
|&lt;data&gt;||&nbsp;|
|liquidationprice|decimal|预估爆仓价|
|&lt;/data&gt;||&nbsp;|


# websocket 请求与订阅说明<br>

### 1. 数据压缩
WebSocket API 返回的所有数据都进行了 GZIP 压缩，需要 client 在收到数据之后解压，推荐使用pako。（[【pako】](https://github.com/nodeca/pako) 是一个支持压缩和解压 GZIP 的库）

### 2. WebSocket库
[【ws】](https://github.com/websockets/ws) 是 Node.js 下的 WebSocket 库。

### 3. 心跳
WebSocket API 支持双向心跳，无论是 Server 还是 Client 都可以发起 `ping` message，对方返回 `pong` message。

WebSocket Server 发送心跳：
```
{"ping": 18212558000}
```
 WebSocket Client 应该返回：
```
 {"pong": 18212558000}
```
注：返回的数据里面的 `"pong"` 的值为收到的 `"ping"` 的值
注：WebSocket Client 和 WebSocket Server 建立连接之后，WebSocket Server 每隔 `5s`（这个频率可能会变化） 会向 WebSocket Client 发起一次心跳，WebSocket Client 忽略心跳2次后，WebSocket Server 将会主动断开连接；WebSocket Client发送最近2次心跳message中的其中一个`ping`的值，WebSocket Server都会保持WebSocket连接。
```
┌────────┐                         ┌────────┐
│ Client │                         │ Server │
└───┬────┘                         └───┬────┘
    │         {"ping": 18212558000}  │
    │<─────────────────────────────────┤
    │                                  │ wait 5s
    │                                  ├───┐
    │                                  │<──┘
    │         {"ping": 18212558000}  │
    │<─────────────────────────────────┤
    │                                  │
    │ {"pong": 18212558000}          │
    ├┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄>│
    │                                  │
```
注：WebSocket Client 发送最近2次心跳 message 中的其中一个 `"ping"` 的值，WebSocket Server 都会保持 WebSocket 连接
```
┌────────┐                         ┌────────┐
│ Client │                         │ Server │
└───┬────┘                         └───┬────┘
    │         {"ping": 1523778470416}  │
    │<─────────────────────────────────┤
    │                                  │ wait 5s
    │                                  ├───┐
    │                                  │<──┘
    │         {"ping": 1523778475416}  │
    │<─────────────────────────────────┤
    │                                  │ wait 5s
    │                                  ├───┐
    │                                  │<──┘
    │                                  │
    │                                  │ close WebSocket connection
    │                                  ├───┐
    │                                  │<──┘
    │                                  │

```
