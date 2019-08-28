# 马克API
马克公司对外提供商品识别服务，并开放如下API.该SDK默认编码为UTF8.

## 签名方法说明
1. 所有参数以ascii从小到大排序；
2. 用“key1=value1&key2=value2”的方式连接成字符串；
3. 在结尾拼接密钥，如“key1=value1&key2=value2&key=xxxxxxxxxxx”；
4. 将字符串进行MD5操作，生成签名，并转为大写。

python 签名demo
```python
def generate_sign(params, key):
    s = ''
    for k in sorted(params.keys()):
        s += '%s=%s&' % (k, params[k])
    s += 'key=%s' % key
    m = hashlib.md5()
    m.update(s.encode('utf8'))
    sign = m.hexdigest().upper()
    return sign
```

## 识别API
- 简介：用户通过该api下发识别任务
- 请求方式：POST JSON
- URL：/recognition/api/create/task/
- HEADER: {'Content-type': 'application/json', 'Accept': 'text/plain'}

|  参数名称 | 类型 | 长度限制  | 简介  | 备注  |
| ------------ | ------------ |------------ | ------------ | ------------ |
| app_id  | int | 11 |  app_id和密钥请在对接前申请 | 必填  |
|  event_id | string |64  | 事件ID  |  选填 |
|  unique_id | string |64  | 识别任务唯一标识，不能重复提交   |  必填 |
|  device_id | string |64  | 设备ID  |  必填 |
|  device_code | string |64  | 设备编码  |  必填 |
|  extra | string |1024 | 长字符串，回调时原样带回 |  必填 |
|  door_open_timestamp | int |  20  | 开门时间戳 精确到毫秒  | 必填  |
|  door_close_timestamp | int|  20  | 关门时间戳 精确到毫秒  | 必填  |
|  top_video | string | 256  | 主摄像头视频链接地址   | 必填  |
|  ass_video | string | 256  | 辅助摄像头视频链接地址   | 可选  |
|  remark | string | 256  | 任务备注   | 可选  |
|  customer_id | string | 64  | 消费者ID   | 可选  |
|  goods_ids | string | 4000  | 格式见商品信息json, 一层最多不超过九种商品|  必填 |
|  weight_info | string | 512  | 重力信息，包含层以及重量变化   | 可选  |
|  request_time | int | 20  | 请求时间戳 精确到毫秒   |  必填 |
|  notify_url | string | 128  | 识别结果回调地址  | 必填 | 
|  sign | string | 128  | 签名  | 必填  |

正常返回值，此时status为0：

```json
{
	"msg": "", 
	"status": 0,
	"data": {
		"task_id": 1678428583713833984
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

商品信息:
```json
[{
	"colIndex": 3,  # 货道编号 
	"floorIndex": 1,  # 层
	"goodsId": 110520  # 商品ID
}, {
	"colIndex": 1,
	"floorIndex": 2,
	"goodsId": 10910
}, {
	"colIndex": 5,
	"floorIndex": 2,
	"goodsId": 110498
}, {
	"colIndex": 1,
	"floorIndex": 4,
	"goodsId": 10936
}]
```

重力信息:
```json
[{
	"endtimestamp": 1566955354439,  # 重力变化结束毫秒时间戳
	"floor": 1,    # 重力变化层
	"starttimestamp": 1566955354020, # 重力变化开始毫秒时间戳
	"weight": -344   # 重力变化 克
}]
```

## 回调接口
- 简介：对接方提供该接口，用于接收服务端任务识别结果通知
- 请求方式：POST JSON
- URL：对接方提供
- HEADER: {'Content-type': 'application/json', 'Accept': 'text/plain'}

|  参数名称 | 类型 | 长度限制  | 简介  | 备注  |
| ------------ | ------------ |------------ | ------------ | ------------ |
| app_id  | int | 11 |  app_id和密钥请在对接前申请 | 必填  |
|  unique_id | string|  64  | 识别任务唯一标识，下单时提交 |  必填 |
|  status | int | 11  | 识别结果：0 未识别， 1 识别失败， 2识别成功 |  必填 |
|  reco_result | string |  512  | JSON格式，识别结果 |  必填 |
|  task_id | int | 20  | 识别系统任务ID |  必填 |
|  extra | string | 1024 | 下单时传入，原样带回 |  必填 |
|  sign | string | 64 | 签名，生成方法和下单一致, 必须校验|  必填 |

收到通知后，需返回纯文本**success**，未收到**success**，会持续不定期重复推送，最长持续15天

返回示例

```json
{
	"status": 1,
	"reco_result": {
		"17": 4,
		"16": 3
	},
	"task_id": 1679277852554428416,
	"extra": "\u54c8\u54c8\u54c8",
	"app_id": 666666,
	"sign": "F9FAAA2592DADD29468A3E99F0F7A813",
	"unique_id": "1563949371285"
}
```

## 获取辅助视频
- 简介：对接方提供该接口，当创建识别任务没有传入辅助视频，而该任务需要时调用。
- 请求方式：POST JSON
- URL：对接方提供
- HEADER: {'Content-type': 'application/json', 'Accept': 'text/plain'}

|  参数名称 | 类型 | 长度限制  | 简介  | 备注  |
| ------------ | ------------ |------------ | ------------ | ------------ |
| app_id  | int | 11 |  app_id和密钥请在对接前申请 | 必填  |
|  unique_id | string|  64  | 创建任务时传入|  必填 |
|  device_id | string |64  | 设备ID  |  必填 |
|  request_time | int | 20  | 请求时间戳 精确到毫秒   |  必填 |
|  door_open_timestamp | int |  20  | 开门时间戳 精确到毫秒  | 必填  |
|  door_close_timestamp | int|  20  | 关门时间戳 精确到毫秒  | 必填  |
|  sign | string | 64 | 签名，生成方法和下单一致, 必须校验|  必填 |


返回示例

```json
{
	"status": 1, # 0 正在上传； 1 上传成功；2 上传失败
	"msg": "xxxxx",
	"ass_video": "http://...."
}
```

## 查询接口
- 简介：主动查询识别任务状态
- 请求方式：同时支持GET/POST 为POST时使用下面的header
- URL：/recognition/api/query/task/
- HEADER: {'Content-type': 'application/json', 'Accept': 'text/plain'}

|  参数名称 | 类型 | 长度限制 | 简介  | 备注  |
| ------------ | ------------ | ------------ | ------------ | ------------ |
| app_id  | int | 11 |  app_id和密钥请在对接前申请 | 必填  |
|  unique_id | string | 64  | 识别任务唯一标识，下单时提交 |  必填 |
|  request_time | int | 20  | 请求时间戳 精确到毫秒   |  必填 |
|  sign | string | 64 | 签名，生成方法和下单一致|  必填 |

返回示例

```json
{
	"msg": "",
	"status": 0,
	"data": {
		"task_info": {
			"status": 1,
			"result": {
				"17": 4,
				"16": 3
			},
			"task_id": 1679277852554428416,
			"extra": "哈哈哈",
			"customer_id": "312dasdas",
			"app_id": 666666,
			"unique_id": "1563949371285"
		}
	}
}
```


## 云库商品查询接口
- 简介：主动查询云库商品
- 请求方式：同时支持GET/POST 为POST时使用下面的header
- URL：/recognition/api/query/goods/
- HEADER: {'Content-type': 'application/json', 'Accept': 'text/plain'}

|  参数名称 | 类型  | 长度限制 | 简介  | 备注  |
| ------------ | ------------ | ------------ | ------------ | ------------ |
| companyId  | int | 11 |  同app_id 请在对接前申请 | 必填  |
|  nonceStr | string | 64  | 随机字符串 |  必填 |
|  requestSerial | int | 20  | 请求ID  |  必填 |
|  timeStamp | int | 20  | 请求时间戳 |  必填 |
|  sign | string | 64 | 签名，生成方法和下单一致|  必填 |
|  goods_name | string | 64 | 商品名称|  选填 |
|  pinyin | string | 64 | 拼音|  选填 |
|  barcode | string | 255 | 条码|  选填 |
|  specifications | string | 255 | 规格|  选填 |

请求示例：
http://47.92.245.190/recognition/api/query/goods/?companyId=666666&goods_name=%E5%93%88%E5%93%88%E5%93%88&requestSerial=request_time&nonceStr=dasdasdas&timeStamp=1564395348957&sign=E47C05D36F088B1CBAE491157D6E6556

返回示例

```json
{
	"msg": "",
	"status": 0,
	"data": {
		"total_count": 4,
		"list": [{
			"is_show_to_consumer": null,
			"brand_name": null,
			"id": 17115,
			"type": 2,
			"status": 0,
			"description": null,
			"goods_name": "哈哈哈",
			"specifications": null,
			"price": 12.0,
			"barcode": null,
			"avg_weight": 0,
			"main_image": "http://thinkboxs.oss-cn-shanghai.aliyuncs.com/goods/1562057373030_72554",
			"created_at": "2019-07-18 14:25:06"
		}, {
			"is_show_to_consumer": null,
			"brand_name": null,
			"id": 17116,
			"type": 2,
			"status": 2,
			"description": null,
			"goods_name": "哈哈哈",
			"specifications": "100g",
			"price": 12.0,
			"barcode": null,
			"avg_weight": 2,
			"main_image": "https://hibox.oss-cn-shanghai.aliyuncs.com/goods/tdrsslltgm.jpg",
			"created_at": "2019-07-18 19:01:48"
		}, {
			"is_show_to_consumer": null,
			"brand_name": null,
			"id": 17117,
			"type": 2,
			"status": 2,
			"description": null,
			"goods_name": "哈哈哈",
			"specifications": "100g",
			"price": 12.0,
			"barcode": null,
			"avg_weight": 2,
			"main_image": "https://hibox.oss-cn-shanghai.aliyuncs.com/goods/tdrsslltgm.jpg",
			"created_at": "2019-07-19 20:22:52"
		}, {
			"is_show_to_consumer": null,
			"brand_name": null,
			"id": 110005,
			"type": 2,
			"status": 2,
			"description": null,
			"goods_name": "哈哈哈",
			"specifications": "100g",
			"price": 12.0,
			"barcode": null,
			"avg_weight": 2,
			"main_image": "http://make-goods.oss-cn-hangzhou.aliyuncs.com/MKG1563791267802.jpg",
			"created_at": "2019-07-22 18:27:47"
		}],
		"page": 1,
		"size": 4
	}
}
```

## 云库商品创建接口
- 请求方式：POST
- URL：/recognition/api/create/goods/
- URL：对接方提供

|  参数名称 | 类型  | 长度限制 | 简介  | 备注  |
| ------------ | ------------ | ------------ | ------------ | ------------ |
| companyId  | int | 11 |  同app_id 请在对接前申请 | 必填  |
|  recognize_type | int | 11  | 识别类型 0：未知，1RFID，2大图少样本，3小图大样本 |  必填 |
|  goods_name | string | 64  | 商品名称  |  必填 |
|  price | FLOAT |   | 商品价格 |  必填 |
|  sign | string | 64 | 签名，生成方法和下单一致|  必填 |
|  main_image | string | 256 | 主图地址|  选填 |
|  images | string | 2048 | 附图地址列表，用英文逗号隔开|  选填 |
|  pinyin | string | 64 | 拼音|  选填 |
|  barcode | string | 255 | 条码|  选填 |
|  specifications | string | 255 | 规格|  选填 |
|  length | int | 11 | 长|  选填 |
|  width | int | 11 | 宽|  选填 |
|  height | int | 11 | 高|  选填 |
|  avg_weight | int | 11 | 平均重量|  选填 |
|  weight_error | int | 11 | 重量误差|  选填 |
|  min_weight | int | 11 | 最小重量|  选填 |
|  max_weight | int | 11 | 最大重量|  选填 |

正确返回示例：
```json
{
	"msg": "",
	"status": 0,
	"data": {
		"id": 110116 # 创建成功后，云库商品ID
	}
}
```


## 错误码介绍
| 错误信息  | 含义  |
| ------------ | ------------ |
| sign is invaild  | 签名错误  |
| ip not in  white_list  | 请求服务器IP不在白名单  |


## 创建及查询识别任务DEMO

```python
# coding=utf-8
import requests
import time
import hashlib
import json

_APP_ID = 666666
_APP_KEY = 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx'

_URL = 'http://47.92.245.190/recognition/api/create/task/'
_QUERY_URL = 'http://47.92.245.190/recognition/api/query/task/'


def generate_sign(params, key):
    s = ''
    for k in sorted(params.keys()):
        s += '%s=%s&' % (k, params[k])
    s += 'key=%s' % key
    m = hashlib.md5()
    m.update(s.encode('utf8'))
    sign = m.hexdigest().upper()
    return sign


def create_task():
    request_time = int(time.time() * 1000)
    d = \
    {
        "app_id": "666666",
        "ass_video": "",
        "customer_id": "450560",
        "device_code": "12695",
        "device_id": "16840",
        "door_close_timestamp": "1566955357484",
        "door_event_id": "12184431",
        "door_open_timestamp": "1566955351846",
        "event_id": "10098462",
        "extra": "",
        "goods_info": "[{\"colIndex\":3,\"floorIndex\":1,\"goodsId\":110520},{\"colIndex\":1,\"floorIndex\":2,\"goodsId\":10910},{\"colIndex\":5,\"floorIndex\":2,\"goodsId\":110498},{\"colIndex\":1,\"floorIndex\":4,\"goodsId\":10936},{\"floorIndex\":-1,\"goodsId\":10910},{\"floorIndex\":-1,\"goodsId\":10936},{\"floorIndex\":-1,\"goodsId\":110498},{\"floorIndex\":-1,\"goodsId\":110520}]",
        "has_ass_video": "1",
        "notify_url": "https://v2.lufff.com/orders/insert",
        "request_time": "1566955366247",
        "sign": "507EFF049E91FE274F77A9B6A9BF951A",
        "top_video": "https://xiaomaibox.oss-cn-shanghai.aliyuncs.com/box/video/10098462_top_1566955351846.mp4",
        "unique_id": "12184431",
        "weight_info": "[{\"endtimestamp\":1566955354439,\"floor\":1,\"starttimestamp\":1566955354020,\"weight\":-344}]"
    }
    d['sign'] = generate_sign(d, _APP_KEY)
    headers = {'Content-type': 'application/json', 'Accept': 'text/plain'}
    return requests.post(_URL, data=json.dumps(d, separators=(',', ':')), headers=headers, timeout=5)


def query_task_status():
    request_time = int(time.time() * 1000)
    d = \
        {
            'app_id': _APP_ID,
            'unique_id': 1563949371285,
            'request_time': request_time,
        }
    d['sign'] = generate_sign(d, _APP_KEY)
    return requests.get(_QUERY_URL, params=d, timeout=5)


def post_query_task_status():
    request_time = int(time.time() * 1000)
    d = \
        {
            'app_id': _APP_ID,
            'unique_id': 1563949371285,
            'request_time': request_time,
        }
    d['sign'] = generate_sign(d, _APP_KEY)
    headers = {'Content-type': 'application/json', 'Accept': 'text/plain'}
    return requests.post(_QUERY_URL, data=json.dumps(d), headers=headers, timeout=5)


if __name__ == '__main__':
    print(post_query_task_status().text)

```

## 云库商品查询创建DEMO
```python
# coding=utf-8
import requests
import time
import hashlib
import json

_APP_ID = 666666
_APP_KEY = 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx'

_URL = 'http://47.92.245.190/recognition/api/create/goods/'
_QUERY_URL = 'http://47.92.245.190/recognition/api/query/goods/'


def generate_sign(params, key):
    s = ''
    for k in sorted(params.keys()):
        s += '%s=%s&' % (k, params[k])
    s += 'key=%s' % key
    print(s)
    m = hashlib.md5()
    m.update(s.encode('utf8'))
    sign = m.hexdigest().upper()
    return sign


def create_goods():
    request_time = int(time.time() * 1000)
    d = \
        {
            'companyId': _APP_ID,
            'recognize_type': 2,
            'goods_name': u'哈哈哈1',
            'price': 12,
            'main_image': 'http://rm-bg-image.oss-cn-hangzhou.aliyuncs.com/rm_bg_img_1563591282307.jpg',
            'pinyin': '4dsdasda',
            'nonceStr': 'dasdasdas',
            'specifications': '100g',
            'type': 2,
            'avg_weight': 2,
            'requestSerial': request_time,
            'timeStamp': request_time,
        }
    d['sign'] = generate_sign(d, _APP_KEY)
    headers = {'Content-type': 'application/json', 'Accept': 'text/plain'}
    return requests.post(_URL, data=json.dumps(d, separators=(',', ':')), headers=headers, timeout=5)


def query_goods():
    request_time = int(time.time() * 1000)
    params = \
        {
            'companyId': _APP_ID,
            'goods_name': u'哈哈哈',  # 可选
            # 'pinyin': '4dsdasd', # 可选
            'requestSerial': 'request_time',
            'nonceStr': 'dasdasdas',
            'timeStamp': request_time,
        }
    params['sign'] = generate_sign(params, _APP_KEY)
    return requests.get(_QUERY_URL, params=params, timeout=5)


def post_query_goods():
    request_time = int(time.time() * 1000)
    params = \
        {
            'companyId': _APP_ID,
            'goods_name': u'哈哈哈',  # 可选
            # 'pinyin': '4dsdasd', # 可选
            'requestSerial': 'request_time',
            'nonceStr': 'dasdasdas',
            'timeStamp': request_time,
        }
    params['sign'] = generate_sign(params, _APP_KEY)
    headers = {'Content-type': 'application/json', 'Accept': 'text/plain'}
    return requests.post(_QUERY_URL, data=json.dumps(params), headers=headers, timeout=5)


if __name__ == '__main__':
    response = post_query_goods()
    print(response.text)
    print(response.url)
    print(response.status_code)

```