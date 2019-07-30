# 分析器与服务器交互API
分析器通过MQTT与服务器连接，接收识别任务，并将识别任务结果返回给服务器。
目前识别器只有在服务器注册后，获取到识别器名称以及密钥才能使用。
签名算法同云库商品API.

## 获取MQTT连接信息

- 简介：用户通过该api下发识别任务
- 请求方式：同时支持POST/GET
- URL：/recognition/api/get_connection_info/
- HEADER: {'Content-type': 'application/json', 'Accept': 'text/plain'}

|  参数名称 | 类型 | 长度限制  | 简介  | 备注  |
| ------------ | ------------ |------------ | ------------ | ------------ |
| name  | string | 64 |  识别器名称 | 必填  |
| timestamp | int 20  | 请求时间戳 精确到毫秒   |  必填 |
|  sign | string 128  | 签名  | 必填  |

正常返回值，此时status为0：

```json
{
	"msg": "",
	"status": 0,
	"data": {
		"data": {
			"topic": "Analyst",
			"password": "VqXJuQbNd7ehw14HZvwwrgL81TU=",
			"client_id": "GID_Analyst@@@analyst_3",
			"keepalive": 60,
			"server_client_id": "GID_Analyst@@@monitor",
			"user_name": "Signature|LTAIAcXx0yUAXd9q|post-cn-4590nfec207",
			"port": 1883,
			"brokerUrl": "post-cn-4590nfec207.mqtt.aliyuncs.com"
		}
	}
}
```
异常返回值，status不为0:
```json
{
	"msg": "sign is invaild",
	"status": 4
}
```

## MQTT公共消息介绍
|  参数名称 | 描述 |
| ------------ | ------------ |
| action  | 消息类型 |
| data  | 详细信息，不同的消息数据格式不同 |


```json
{
	"action": "ANALYSIS_START",
	"data": {
	}
}
```

action 介绍
|  action名称 | 描述 |
| ------------ | ------------ |
| ANALYSIS_START  | 分配识别任务，开始识别，服务器发给分析器 |
| ANALYSIS_FINISH  | 分析器完成分析任务，发送结果给服务器 |
| ANALYSIS_FAIL  | 分析任务异常，分析器返回结果给服务器 |
| ANALYSIS_TIMEOUT  | 分析任务超时，服务器结束改次任务转发给其他分析器，分析器返回结果给服务器 |


## 收到识别任务

- 简介：服务器通过该api分配识别任务
- 请求方式：MQTT/P2P

|  参数名称 | 类型 | 长度限制  | 简介  | 备注  |
| ------------ | ------------ |------------ | ------------ | ------------ |
| id  | int | 20 |  识别任务ID,识别结束后回传 | 必填  |
| top_video | string |128  | 主摄像头视频   |  必填 |
|  goods_ids | string |512  | 商品信息  | 必填  |
| ass_video | string |128  | 辅助摄像头视频   |  选填 |
|  weight_info | string |512  | 重力变化信息  | 选填  |
|  door_open_timestamp | int |20  | 开门时间戳  | 选填  |
|  door_close_timestamp | int |20  | 关门时间戳  | 选填  |

```json
{
	"action": "ANALYSIS_START",
	"data": {
		"door_close_timestamp": 1558583538419,
		"goods_ids": {
			"1": [1, 4, 7, 9, 11], # key 1 代表第一层， value [1, 4, 7, 9, 11]代表 该层可能会出现如下商品
			"3": [30, 36, 48, 59],
			"2": [20, 26, 28, 29],
			"5": [50, 56, 58, 59],
			"4": [40, 46, 48, 59],
			"-1": [1, 4, 7, 9, 11]  # key为-1代表value中商品[1, 4, 7, 9, 11]可能出现在任意层
		},
		"top_video": "4dsdasda",
		"door_open_timestamp": 1558583537598,
		"weight_info": [{
			"startTimeStamp": 1558583537598,
			"endTimeStamp": 1558583538419,
			"weight": -536,
			"floor": 4
		}, {
			"startTimeStamp": 1558583541091,
			"endTimeStamp": 1558583545205,
			"weight": -81,
			"floor": 2
		}],
		"ass_video": "assssssss",
		"analyst_id": null,
		"id": 1679846303806259200
	}
}
```

## 完成识别任务
- 简介：分析器完成分析后，向服务器发送分析结果
- 请求方式：MQTT/P2P

|  参数名称 | 类型 | 长度限制  | 简介  | 备注  |
| ------------ | ------------ |------------ | ------------ | ------------ |
| id  | int | 20 |  识别任务ID,识别结束后回传 | 必填  |
|  result | string |512  | 识别结果  | 必填  |


```json
{
        "action": "ANALYSIS_FINISH",
        "data": {
            "id": 1679277852554428416,
            "RESULT": {
                "17": 4, # 商品ID为17的商品，被取出4个
                "16": 3 # 商品ID为16的商品，被取出3个
            },
        }
    }
```

## 识别任务异常
- 简介：识别异常，分析器返回信息到服务器
- 请求方式：MQTT/P2P

|  参数名称 | 类型 | 长度限制  | 简介  | 备注  |
| ------------ | ------------ |------------ | ------------ | ------------ |
| id  | int | 20 |  识别任务ID,识别结束后回传 | 必填  |
|  result | string |512  | 识别结果  | 必填  |


```json
{
        "action": "ANALYSIS_FAIL",
        "data": {
            "id": 1679277852554428416,
            "RESULT": "错误原因",
        }
    }
```

## 识别超时
- 简介：识别超时，服务器发送消息到识别器，取消当前识别任务
- 请求方式：MQTT/P2P

|  参数名称 | 类型 | 长度限制  | 简介  | 备注  |
| ------------ | ------------ |------------ | ------------ | ------------ |
| id  | int | 20 |  识别任务ID,识别结束后回传 | 必填  |


```json
{
        "action": "ANALYSIS_TIMEOUT"
    }
```