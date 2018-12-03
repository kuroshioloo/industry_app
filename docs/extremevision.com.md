#极视角对接-草稿

本章节中，需要应用系统(算法平台)提供的接口，下文只描述需求和接口要求，具体以应用系统接口文档为准。

##需求背景

经过双方沟通，算法平台作为能力提供方，聚焦算法能力本身，不需要关注用户身份、服务实例等环节。

##接口概览

###算法产品清单同步

算法产品清单的同步支持查询和推送两种方式。

接口提供方-算法平台

| 接口名称 | 接口功能 |
| :- | :- |
| DescribeProducts | 查询算法产品列表 |

接口提供方-工业云平台

| 接口名称 | 接口功能 |
| :- | :- |
| UpdateProducts | 更新算法产品列表 |

###账单信息

付款逻辑已经明确是预付费+算法平台扣费的策略，所以账单信息由算法平台提供更加合理。

接口提供方-算法平台

| 接口名称 | 接口功能 |
| :- | :- |
| DescribeBalance | 查询预付费金额 |
| DescribeBillDetail | 查询账单明细数据 |
| DescribeBillResourceSummary | 查询账单资源汇总数据 |

##接口说明

以下所有接口都不涉及用户身份，基础参数参考**应用系统对接说明-[平台场景]()**中的基础参数描述

**[查询算法产品列表]()接口说明**

https请求方式GET

```
INTERFACE_URL?<公共请求参数>&..
```

请求参数说明：

| 参数名称 | 必选 | 描述 |
| :- | :- | :- |
| client_appid | 是 | 公共参数，平台应用(如算法平台)的appid(又称login_appid) |
| time_stamp | 是 | 公共参数，时间戳，格式参考ISO8601标准，使用UTC时间。格式为：YYYY-MM-DDThh:mm:ssZ |
| signature | 是 | 公共参数，请求消息签名 |

返回内容为全量的算法产品列表，其中单个产品节点的内容有(请算法平台再做补充)：

 - 唯一ID(算法平台内部唯一)
 - 产品名称
 - 产品简介
 - 产品描述图片/视频列表
 - 产品分类(平台提供枚举)
 - 产品版本
 - 产品接口
 - 请求参数和说明
 - 返回参数和说明
 - 平均参考时延(ms)
 - 最大并发量
 - 算法性能说明：包括标准参照系、准确率、精确率和召回率指标
 - 费用说明：计费方式暂时只支持按照调用次数收费(元/千次)
 
返回结果示例：

```
{
    "code": 0,
    "msg": "",
    "product_list": [{
        "product_id": "JSUHEDGJAHAUK",
        "name": "人流密度",
        "profile": "基于动态视频实时统计大型活动现场人流密度、变化趋势等，进行数据采集，实现突发事件预警，现场安全把控等。",
        "profile_media": ["http://static3.extremevision.com.cn/group1/M00/00/54/CqHxLlsjNTOEKl01AAAAAMYFb7M633.mp4",
            "http://static.extremevision.com.cn/donkey_13806c8c-6a03-4d16-81fc-194cd8008b37.jpg"
        ],
        "category": "Personnel testing class",
        "version": "1.1.0",
        "interface": "https://api.cvmart.net/api/algo/face",
        "request": "",
        "response": "",
        "average_delay": 280,
        "max_qps": 120,
        "performance": {
            "frame_of_reference": ["http://static.extremevision.com.cn/donkey_0df8ddf6-5c0c-4ce7-9543-9922a21d7f32.jpg", "http://static.extremevision.com.cn/donkey_0df8ddf6-5c0c-4ce7-9543-9922a21d7f32.jpg"],
            "accuracy": 0.825,
            "precision": 0.764,
            "recall": 0.312
        },
        "rate": 0.02
    }]
}
```

**[更新算法产品列表]()接口说明**

https请求方式POST

```
https://cloud.industry.com/app/v1/:appname/products?<公共请求参数>&..
```

请求参数说明：

请求JSON包体

```
{
    "action": "add",
    "product_list": [{
        "product_id": "JSUHEDGJAHAUK",
        "name": "人流密度",
        "profile": "基于动态视频实时统计大型活动现场人流密度、变化趋势等，进行数据采集，实现突发事件预警，现场安全把控等。",
        "profile_media": ["http://static3.extremevision.com.cn/group1/M00/00/54/CqHxLlsjNTOEKl01AAAAAMYFb7M633.mp4",
            "http://static.extremevision.com.cn/donkey_13806c8c-6a03-4d16-81fc-194cd8008b37.jpg"
        ],
        "category": "Personnel testing class",
        "version": "1.1.0",
        "interface": "https://api.cvmart.net/api/algo/face",
        "request": "",
        "response": "",
        "average_delay": 280,
        "max_qps": 120,
        "performance": {
            "frame_of_reference": ["http://static.extremevision.com.cn/donkey_0df8ddf6-5c0c-4ce7-9543-9922a21d7f32.jpg", "http://static.extremevision.com.cn/donkey_0df8ddf6-5c0c-4ce7-9543-9922a21d7f32.jpg"],
            "accuracy": 0.825,
            "precision": 0.764,
            "recall": 0.312
        },
        "rate": 0.02
    }]
}
```

| 参数名称 | 必选 | 描述 |
| :- | :- | :- |
| client_appid | 是 | 公共参数，平台应用(如算法平台)的appid(又称login_appid) |
| time_stamp | 是 | 公共参数，时间戳，格式参考ISO8601标准，使用UTC时间。格式为：YYYY-MM-DDThh:mm:ssZ |
| signature | 是 | 公共参数，请求消息签名 |
| action | 是 | 更新操作类型，包括：全量更新-"replace"，增加-"add"，删除-"delete" |
| product_list | 是 | 要更新的产品列表，进行删除操作时，product节点内只需要携带"product_id"字段 |

返回内容：
 
返回结果示例：

```json
{
    "code": 0,
    "msg": "",
}
```

**[查询预付费金额]()接口说明**

https请求方式GET

```
INTERFACE_URL?<公共请求参数>&..
```

请求参数说明：

| 参数名称 | 必选 | 描述 |
| :- | :- | :- |
| client_appid | 是 | 公共参数，平台应用(如算法平台)的appid(又称login_appid) |
| time_stamp | 是 | 公共参数，时间戳，格式参考ISO8601标准，使用UTC时间。格式为：YYYY-MM-DDThh:mm:ssZ |
| signature | 是 | 公共参数，请求消息签名 |

返回内容要求：

 - 账户余额(单位:分)
 
返回结果示例：

```json
{
    "code": 0,
    "msg": "",
    "balance": 42700000
}
```

**[查询账单明细数据]()接口说明**

https请求方式GET

```
INTERFACE_URL?<公共请求参数>&..
```

请求参数说明：

| 参数名称 | 必选 | 描述 |
| :- | :- | :- |
| client_appid | 是 | 公共参数，平台应用(如算法平台)的appid(又称login_appid) |
| time_stamp | 是 | 公共参数，时间戳，格式参考ISO8601标准，使用UTC时间。格式为：YYYY-MM-DDThh:mm:ssZ |
| signature | 是 | 公共参数，请求消息签名 |
| offset | 是 | 偏移量 |
| limit | 是 | 数量，最大值为100 |
| time_begin | 是 | 起始时间，格式参考ISO8601标准，使用UTC时间。格式为：YYYY-MM-DDThh:mm:ssZ |
| time_end | 是 | 结束时间，格式参考ISO8601标准，使用UTC时间。格式为：YYYY-MM-DDThh:mm:ssZ |

返回内容要求：

 - 账单详情数组
 
返回结果示例：

```json
{
    "code": 0,
    "msg": "",
    "detail_set": [
        {
            "user_id": 144115518765462352,
            "product_id": "JSUHEDGJAHAUK",
            "app_id": 1240089,
            "bill_id": 100000074,
            "pay_time": "2018-01-01T00:00:00+08:00",
            "usage_times": 4
        }
    ]
}
```

**[查询账单资源汇总数据]()接口说明**

https请求方式GET

```
INTERFACE_URL?<公共请求参数>&..
```

请求参数说明：

| 参数名称 | 必选 | 描述 |
| :- | :- | :- |
| client_appid | 是 | 公共参数，平台应用(如算法平台)的appid(又称login_appid) |
| time_stamp | 是 | 公共参数，时间戳，格式参考ISO8601标准，使用UTC时间。格式为：YYYY-MM-DDThh:mm:ssZ |
| signature | 是 | 公共参数，请求消息签名 |
| time_begin | 是 | 起始时间，格式参考ISO8601标准，使用UTC时间。格式为：YYYY-MM-DDThh:mm:ssZ |
| time_end | 是 | 结束时间，格式参考ISO8601标准，使用UTC时间。格式为：YYYY-MM-DDThh:mm:ssZ |

返回内容要求：

 - 账单汇总数据
 
返回结果示例：

```
{
    "code": 0,
    "msg": "",
    "detail_set": [
        {
            "product_id": "JSUHEDGJAHAUK",
            "app_id": 1240089,
            "usage_times": 1230
        },
        {
            "product_id": "UWHDUWHDUHWDU",
            "app_id": 1240093,
            "usage_times": 22021
        }
    ]
}
```