#无用户身份的应用平台接入

**无用户身份**是指应用平台只为工业云提供应用能力，不关心工业云平台的用户账户体系。

这种接入通常由应用平台提供一个对接账户，通过接口输出对接能力，供工业云平台对接使用。

本章节中，需要应用系统提供的接口，下文只简单描述流程，具体以应用系统接口文档为准。

##参考流程

```sequence
participant 用户
participant 工业云平台
participant 应用平台

工业云平台->应用平台: 获取应用列表(说明、接口和文档)
应用平台->工业云平台: 更新应用列表(说明、接口和文档)
工业云平台->工业云平台: 应用列表更新
用户->工业云平台: 购买应用
工业云平台->工业云平台: 服务状态更新
工业云平台->用户: 提供应用接口和文档
用户->工业云平台: 调用应用接口
工业云平台->应用平台: 调用应用接口
应用平台->应用平台: 记录账单
应用平台->工业云平台: 返回调用结果
工业云平台->工业云平台: 记录账单
工业云平台->用户: 返回调用结果
工业云平台->应用平台: 查询账单，对账
```

##接口概览

参考上一章节的流程

需要工业云平台向应用平台提供的接口有：

| 接口名称 | 接口功能 |
| :- | :- |
| UpdateProducts | 更新应用产品列表 |

需要应用平台向工业云平台提供的接口有：

| 接口名称 | 接口功能 |
| :- | :- |
| DescribeProducts | 查询应用产品列表 |
| DescribeBalance | 查询付费余额 |
| DescribeBillDetail | 查询账单明细数据 |
| DescribeBillResourceSummary | 查询账单汇总数据 |

##工业云平台接口说明

基础参数参考**应用系统对接说明-[平台场景]()**中的基础参数描述

**[更新应用产品列表]()接口说明**

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
        "category": "Personnel testing class",
        "profile": "基于动态视频实时统计大型活动现场人流密度、变化趋势等，进行数据采集，实现突发事件预警，现场安全把控等。",
        "profile_media": ["http://static3.extremevision.com.cn/group1/M00/00/54/CqHxLlsjNTOEKl01AAAAAMYFb7M633.mp4",
            "http://static.extremevision.com.cn/donkey_13806c8c-6a03-4d16-81fc-194cd8008b37.jpg"
        ],
        "version": "1.1.0",
        "rate": 0.02,
        "extended_info": {
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
            }
        }
    }]
}
```

| 参数名称 | 必选 | 描述 |
| :- | :- | :- |
| client_appid | 是 | 公共参数，平台应用(如算法平台)的appid(又称login_appid) |
| time_stamp | 是 | 公共参数，时间戳，格式参考ISO8601标准，使用UTC时间。格式为：YYYY-MM-DDThh:mm:ssZ |
| signature | 是 | 公共参数，请求消息签名 |
| action | 是 | 更新操作类型，包括：全量更新-"clear_and_update"，增加-"add"，删除-"delete"，修改-"modify"|
| product_list | 是 | 要更新的产品列表，进行删除操作时，product节点内只需要携带"product_id"字段 |

product_list的字段说明：

| 参数名称 | 必选 | 描述 |
| :- | :- | :- |
| product_id | 是 | 唯一ID(应用平台内部唯一) |
| name | 是 | 产品名称 |
| category | 是 | 产品分类(平台提供枚举) |
| profile | 是 | 产品简介 |
| profile_media | 是 | 产品描述图片/视频列表 |
| version | 是 | 产品版本 |
| rate | 是 | 计费说明(暂时只支持计次，单位: 元/千次) |
| extended_info | 是 | 产品专有信息(json格式的扩展信息，对接时做额外说明) |

extended_info示例字段说明：

| 参数名称 | 必选 | 描述 |
| :- | :- | :- |
| interface | 否 | 产品接口 |
| request | 否 | 请求参数和说明 |
| response | 否 | 返回参数和说明 |
| average_delay | 否 | 平均参考时延(ms) |
| max_qps | 否 | 最大并发量 |
| performance | 否 | 算法性能说明：包括标准参照系、准确率、精确率和召回率指标 |

返回内容：
 
返回结果示例：

```json
{
    "code": 0,
    "msg": ""
}
```