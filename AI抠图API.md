# AI抠图API
域名: https://aipic.lufff.com

## 验证码
- 请求方式：POST JSON
- URL：/make_pic/api/send_sms_vcode/
- HEADER: {'Content-type': 'application/json', 'Accept': 'text/plain'}

|  参数名称 | 类型 | 长度限制  | 简介  | 备注  |
| ------------ | ------------ |------------ | ------------ | ------------ |
| phone  | string | 32 |  手机号 | 必填  |

正常返回值，此时status为0：

```json
{
	"phone": "12345678901", 
}
```
异常返回值，status不为0:
```json
{"status": 0, "msg": "", "data": {"result": "ok", "desc": "success"}}
```


## 登陆接口
- 请求方式：POST JSON
- URL：/make_pic/api/login_with_phone_and_vcode/
- HEADER: {'Content-type': 'application/json', 'Accept': 'text/plain'}

|  参数名称 | 类型 | 长度限制  | 简介  | 备注  |
| ------------ | ------------ |------------ | ------------ | ------------ |
| phone  | string | 32 |  手机号 | 必填  |
|  vcode | string|  4  | 验证码 |  必填 |
| share_id | string|  4  | 分享码 |  选填 |


返回示例，记录ID,以及token

```json
{
	"status": 0,
	"msg": "",
	"data": {
		"id": 100001,
		"unikey": null,
		"nickname": null,
		"phone": "13006321436",
		"password": null,
		"credit": 5,
		"invite_code": null,
		"created_at": "2020-08-25 17:41:12",
		"updated_at": "2020-08-25 17:41:12",
		"token": "ed892151-a286-493b-b517-ba72668e5fac"
	}
}
```

## unikey登陆接口
- 请求方式：POST JSON
- URL：/make_pic/api/login_with_unikey/
- HEADER: {'Content-type': 'application/json', 'Accept': 'text/plain'}

|  参数名称 | 类型 | 长度限制  | 简介  | 备注  |
| ------------ | ------------ |------------ | ------------ | ------------ |
| unikey  | string | 128 |  唯一ID | 必填  |
| share_id | string|  4  | 分享码 |  选填 |



返回示例，记录ID,以及token

```json
{
	"status": 0,
	"msg": "",
	"data": {
		"id": 100013,
		"unikey": "6931231232132177",
		"nickname": null,
		"phone": null,
		"password": null,
		"credit": 20,
		"invite_code": "hfjl",
		"created_at": null,
		"updated_at": null,
		"token": "7ee3d6c5-e34b-48ab-a6c1-6921f8e1ac28"
	}
}
```

## 抠图接口
- 请求方式：POST JSON
- URL：/make_pic/api/rm_pic_bg/
- HEADER: {'Content-type': 'application/json', 'Accept': 'text/plain'}

|  参数名称 | 类型 | 长度限制  | 简介  | 备注  |
| ------------ | ------------ |------------ | ------------ | ------------ |
| customer_id  | string | 11 |  登陆返回用户ID | 必填  |
|  token | string|  64  | 登陆返回token|  必填 |
|  img_url | string |128  | 图片url  |  必填 |
|  pic_type | string |128  | 图像处理类型  |  选填 |
|  rectangles | string |256  | 修复区域  |  pic_type为img_fix时必填 |

rectangles json示例

```json
[ //矩形区域, 支持多个数组
    {
      "height": 100,
      "width": 100,
      "x": 160,
      "y": 280
    },
    {
      "height": 100,
      "width": 100,
      "x": 560,
      "y": 680
    },
  ]
```

返回示例

```json
{
	"status": 0,
	"msg": "",
	"data": {
		"rm_bg_pic_url": "http://make-cv.oss-cn-hangzhou.aliyuncs.com/MKCV1598349370338.png"
	}
}
```

### 图像处理类型 pic_type 介绍:
- general 通用抠图，默认值
- human 人像抠图
- object 物体抠图
- head 头像抠图
- beautify 图片美化
- img_fix 图片修复，此时rectangles为必填



## UNIKEY抠图接口
- 请求方式：POST JSON
- URL：/make_pic/api/rm_pic_bg_with_unikey/
- HEADER: {'Content-type': 'application/json', 'Accept': 'text/plain'}

|  参数名称 | 类型 | 长度限制  | 简介  | 备注  |
| ------------ | ------------ |------------ | ------------ | ------------ |
|  unikey | string|  128  | 唯一ID|  必填 |
|  img_url | string |128  | 图片url  |  必填 |
|  pic_type | string |128  | 图像处理类型  |  选填 |
|  rectangles | string |256  | 修复区域  |  pic_type为img_fix时必填 |


返回示例

```json
{
	"status": 0,
	"msg": "",
	"data": {
		"rm_bg_pic_url": "http://make-cv.oss-cn-hangzhou.aliyuncs.com/MKCV1598349370338.png"
	}
}
```


## 抠图历史记录
- 请求方式：POST JSON
- URL：/make_pic/api/get_rm_bg_list/
- HEADER: {'Content-type': 'application/json', 'Accept': 'text/plain'}

|  参数名称 | 类型 | 长度限制  | 简介  | 备注  |
| ------------ | ------------ |------------ | ------------ | ------------ |
| customer_id  | string | 11 |  登陆返回用户ID | 必填  |
|  token | string|  64  | 登陆返回token|  必填 |
|  page | int |11  | 分页ID， 从1开始  |  必填 |
|  size | int |11  | 分页大小  |  必填 |


返回示例

```json
{
	"status": 0,
	"msg": "",
	"data": {
		"list": [{
			"id": 1,
			"customer_id": 100001,
			"remark": null,
			"img_url": "https://xiaomaibox.oss-cn-shanghai.aliyuncs.com/1597976210945_32039_tmp_3235fa8891892248c7db909c4b3bea23e1bb22b6d2281015.jpg",
			"rmbg_img_url": "http://make-cv.oss-cn-hangzhou.aliyuncs.com/MKCV1598349370338.png",
			"created_at": "2020-08-25 17:56:10",
			"updated_at": "2020-08-25 17:56:10"
		}],
		"page": 1,
		"size": 1,
		"total_count": 1
	}
}
```


## 通过手机号查询信用额度
- 请求方式：POST JSON
- URL：/make_pic/api/get_left_credit_by_phone/
- HEADER: {'Content-type': 'application/json', 'Accept': 'text/plain'}

|  参数名称 | 类型 | 长度限制  | 简介  | 备注  |
| ------------ | ------------ |------------ | ------------ | ------------ |
| phone  | string | 32 |  用户手机号 | 必填  |


返回示例

```json
{
	"status": 0,
	"msg": "",
	"data": {
		"credit": 4
	}
}
```


## 通过唯一ID查询信用额度
- 请求方式：POST JSON
- URL：/make_pic/api/get_left_credit_by_unikey/
- HEADER: {'Content-type': 'application/json', 'Accept': 'text/plain'}

|  参数名称 | 类型 | 长度限制  | 简介  | 备注  |
| ------------ | ------------ |------------ | ------------ | ------------ |
| unikey  | string | 128 |  用户唯一ID | 必填  |


返回示例

```json
{
	"status": 0,
	"msg": "",
	"data": {
		"credit": 4
	}
}
```


## 获取广告
- 请求方式：POST JSON
- URL：/make_pic/api/get_one_ad/
- HEADER: {'Content-type': 'application/json', 'Accept': 'text/plain'}


返回示例

```json
{
	"status": 0,
	"msg": "",
	"data": {
		"ad": {
			"id": 6,
			"pic_url": "42",
			"link_url": "rwerwerwe",
			"created_at": "2020-08-27 14:17:14",
			"updated_at": "2020-08-27 14:17:14"
		}
	}
}
```

## 获取广告列表
- 请求方式：POST JSON
- URL：/make_pic/api/get_ad_list/
- HEADER: {'Content-type': 'application/json', 'Accept': 'text/plain'}


返回示例

```json
{
	"status": 0,
	"msg": "",
	"data": {
		"list": [{
			"id": 6,
			"pic_url": "https://xiaomaibox.oss-cn-shanghai.aliyuncs.com/1/default_01%402x.png",
			"link_url": "rwerwerwe",
			"created_at": "2020-08-27 14:17:14",
			"updated_at": "2020-08-31 09:40:19"
		}, {
			"id": 7,
			"pic_url": "https://xiaomaibox.oss-cn-shanghai.aliyuncs.com/1/default_01%401x.png",
			"link_url": "321",
			"created_at": "2020-08-31 09:40:33",
			"updated_at": "2020-08-31 09:40:35"
		}],
		"total_count": 2
	}
}
```


## UNIKEY证件照生成接口
- 请求方式：POST JSON
- URL：/make_pic/api/certificate_photo_with_unikey/
- HEADER: {'Content-type': 'application/json', 'Accept': 'text/plain'}

|  参数名称 | 类型 | 长度限制  | 简介  | 备注  |
| ------------ | ------------ |------------ | ------------ | ------------ |
|  unikey | string|  128  | 唯一ID|  必填 |
|  img_url | string |128  | 图片url  |  必填 |
|  bgColor | string |128  | 背景色  |  必填 |
|  dpi | int |11  | 一般300  |  必填 |
|  mmHeight | int |11  | 图片高  |  必填 |
|  mmWidth | int |11  | 图片宽  |  必填 |
|  printBgColor | string |128  | 打印背景色  |  必填 |
|  printMmHeight | int |11  | 打印高度 毫米  |  必填 |
|  printMmWidth | int |11  | 打印宽 毫米  |  必填 |


返回示例

```json
{
	"status": 0,
	"msg": "",
	"data": {
		"cer_img_url": "http://make-cv.oss-cn-hangzhou.aliyuncs.com/MKCV1598349370338.png",    
		"layout_img_url": "http://make-cv.oss-cn-hangzhou.aliyuncs.com/MKCV1598349370338.png", # 打印图片
	}
}
```

## 查询 签到增加次数
- 请求方式：POST JSON
- URL：/make_pic/api/get_sign_in_reward/
- HEADER: {'Content-type': 'application/json', 'Accept': 'text/plain'}

|  参数名称 | 类型 | 长度限制  | 简介  | 备注  |
| ------------ | ------------ |------------ | ------------ | ------------ |


返回示例

```json
{
	"status": 0,
	"msg": "",
	"data": {
		"sign_in_reward": 2
	}
}
```

## 签到
- 请求方式：POST JSON
- URL：/make_pic/api/customer_sign_in/
- HEADER: {'Content-type': 'application/json', 'Accept': 'text/plain'}

|  参数名称 | 类型 | 长度限制  | 简介  | 备注  |
| ------------ | ------------ |------------ | ------------ | ------------ |
| customer_id  | string | 11 |  登陆返回用户ID | 必填  |
|  token | string|  64  | 登陆返回token|  必填 |

返回示例

```json
{
	"status": 0,
	"msg": "",
	"data": {
		"credit": 22
	}
}
```

## 签到历史
- 请求方式：POST JSON
- URL：/make_pic/api/get_customer_sign_in_list/
- HEADER: {'Content-type': 'application/json', 'Accept': 'text/plain'}

|  参数名称 | 类型 | 长度限制  | 简介  | 备注  |
| ------------ | ------------ |------------ | ------------ | ------------ |
| customer_id  | string | 11 |  登陆返回用户ID | 必填  |
|  token | string|  64  | 登陆返回token|  必填 |

返回示例

```json
{
	"status": 0,
	"msg": "",
	"data": {
		"list": [{
			"id": 3,
			"customer_id": 100018,
			"credit": 2,
			"remark": null,
			"created_at": "2020-09-17 17:24:03",
			"updated_at": "2020-09-17 17:24:03"
		}],
		"total_count": 1
	}
}
```