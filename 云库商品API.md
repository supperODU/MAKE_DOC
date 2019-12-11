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
- URL：/make_dc/api/create/task/
- HEADER: {'Content-type': 'application/json', 'Accept': 'text/plain'}

|  参数名称 | 类型  | 简介  | 备注  |
| ------------ | ------------ | ------------ | ------------ |
| app_id  | int 11 |  app_id和密钥请在对接前申请 | 必填  |
|  unique_id | string 64  | 识别任务唯一标识，不能重复提交   |  必填 |
|  extra | string 1024 | 长字符串，回调时原样带回 |  必填 |
|  door_open_timestamp | int 20  | 开门时间戳 精确到毫秒  | 必填  |
|  door_close_timestamp | int 20  | 关门时间戳 精确到毫秒  | 必填  |
|  top_video | string 256  | 主摄像头视频链接地址   | 必填  |
|  ass_video | string 256  | 辅助摄像头视频链接地址   | 可选  |
|  remark | string 256  | 任务备注   | 可选  |
|  customer_id | string 64  | 消费者ID   | 可选  |
|  goods_ids | string 512  | 识别商品ID范围列表，以英文逗号分割   |  必填 |
|  weight_info | string 512  | 重力信息，包含层以及重量变化   | 可选  |
|  request_time | int 20  | 请求时间戳 精确到毫秒   |  必填 |
|  notify_url | string 128  | 识别结果回调地址  | 必填  |
|  sign | string 128  | 签名  | 必填  |


demo:

```python
# coding=utf-8
import requests
import time
import hashlib
import json

_APP_ID = 666666
_APP_KEY = 'e8d1af4ebf844bf39481609bb74d868e'
# _APP_KEY = 'e8d1af4ebf844bf39481609bb74d86e1'

_URL = 'http://47.92.245.190/make_dc/api/create/task/'


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
    d = \
        {
            'app_id': _APP_ID,
            'unique_id': int(time.time() * 1000),
            'extra': u'哈哈哈',
            'door_open_timestamp': 213,
            'door_close_timestamp': 312312,
            'top_video': '4dsdasda',
            'remark': 'dasdasdas',
            'customer_id': '312dasdas',
            'goods_ids': '1,4,9,123,3123,1243',
            'request_time': int(time.time() * 1000),
            'notify_url': 'http://123.123.132.312',
        }
    d['sign'] = generate_sign(d, _APP_KEY)
    headers = {'Content-type': 'application/json', 'Accept': 'text/plain'}
    return requests.post(_URL, data=json.dumps(d, separators=(',', ':')), headers=headers, timeout=5)

if __name__ == '__main__':
    print(create_task().text)



```

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

## 回调接口
- 简介：用户提供该接口，用于接收服务端任务识别结果通知
- 请求方式：POST JSON
- URL：对接方提供
- HEADER: {'Content-type': 'application/json', 'Accept': 'text/plain'}

|  参数名称 | 类型  | 简介  | 备注  |
| ------------ | ------------ | ------------ | ------------ |
| app_id  | int 11 |  app_id和密钥请在对接前申请 | 必填  |
|  unique_id | string 64  | 识别任务唯一标识，下单时提交 |  必填 |
|  status | int 11  | 识别结果：0 未识别， 1 识别失败， 2识别成功 |  必填 |
|  fee | float  | 识别金额(元) |  必填 |
|  reco_result | string 512  | JSON格式，识别结果 |  必填 |
|  task_id | int 20  | 识别系统任务ID |  必填 |
|  extra | string 1024 | 下单时传入，原样带回 |  必填 |
|  sign | string 64 | 签名，生成方法和下单一致|  必填 |

收到通知后，需返回纯文本**success**，未收到**success**，会持续不定期重复推送，最长持续15天

返回示例

```json
{
	"msg": "",
	"status": 0,
	"data": {
		"status": 0,
		"fee": null,
		"reco_result": null,
		"task_id": 1678544481511538688,
		"extra": "\u54c8\u54c8\u54c8",
		"app_id": 666666,
		"sign": "0F4D057A06B5B5E4E3FB9EB7490F4D7B",
		"unique_id": "1563266367668"
	}
}
```

## 错误码介绍
| 错误信息  | 含义  |
| ------------ | ------------ |
| sign is invaild  | 签名错误  |
| ip not in  white_list  | 请求服务器IP不在白名单  |
