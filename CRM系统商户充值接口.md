# 马克CRM系统商户充值接口
马克公司CRM系统，支付宝自助收款接口


## 充值API
- 简介：用户通过该api获取充值链接, app是有手机浏览器打开该链接，可以唤起支付宝支付。
- 请求方式：GET
- URL：http://crm.xiaomai24h.com:8899/make_crm/api/create_mch_recharge/?mch_id=9&amount=0.01

|  参数名称 | 类型 | 长度限制  | 简介  | 备注  |
| ------------ | ------------ |------------ | ------------ | ------------ |
| mch_id  | int | 11 |  商户ID | 必填  |
| amount | float |  | 充值金额(元)  |  必填 |

正常返回值，此时status为0：

```json
{
	"msg": "",
	"status": 0,
	"data": {
		"pay_url": "https://openapi.alipay.com/gateway.do?app_id=2019120769766164&biz_content=%7B%22out_trade_no%22%3A60008%2C%22product_code%22%3A%22QUICK_WAP_PAY%22%2C%22total_amount%22%3A%220.01%22%2C%22subject%22%3A%22recharge%22%7D&charset=utf-8&method=alipay.trade.wap.pay&notify_url=https%3A%2F%2Fapi.lufff.com%2Fnotify%2F&sign_type=RSA2&timestamp=2019-12-11+03%3A30%3A00&version=1.0&sign=odbmLQ1dZGMsA9PClkkS%2FBSKwv14bLlWYkrPbIAP3nCdvYGlo0PYbl8cogB6S9s6QYZzpFVbTYKwSjg0KTUNqOiqksb4fxhh8VDM2m7KKAaUviuS8%2B8kN%2B3npGIxgSE5fKo7Sq1DWCCX21sPi6DPapYOC5sd%2Bhu0NKHctN8%2FgOZjLIHR5Nt4bBcKJVfhuWlGJtekDm%2B8o8ZqZa5JLSzcGVFD1Il%2FoeTP%2Bgkwpa38X360zHwFi5nZXLJ%2FFmBLaFRcefhRmdmfs1uB3A7ahg%2FKBScDGR4mnTZ3IVcDuMDO0z2WO%2FLuRDme%2BhNUdhBSJDADnHuyX0zcoee%2F4XwdAjOnaw%3D%3D"
	}
}
```


## 查询商户账单详情
- 简介：查询商户账单详情
- 请求方式：GET
- URL：http://crm.xiaomai24h.com:8899//make_crm/api/get_mch_detail_bill/?mch_id=9

|  参数名称 | 类型 | 长度限制  | 简介  | 备注  |
| ------------ | ------------ |------------ | ------------ | ------------ |
| mch_id  | int | 11 |  商户ID | 必填  |

正常返回值，此时status为0：

返回参数介绍

|  参数名称 | 类型 | 长度限制  | 简介  | 备注  |
| ------------ | ------------ |------------ | ------------ | ------------ |
| total_left_money  | float |  |  剩余应收款 | 必填  |
| balance  | float |  |  商户余额 | 必填  |
| this_month_left_money  | float |  |  本月剩余应收款 | 必填  |
|   |  |  |   |   |
| receivable  | float |  |  月应收款 | 必填  |
| recv_money  | float |  |  月已收款| 必填  |
| total_amount  | float |  |  月销售额| 必填  |
| order_count  | float |  |  月订单数| 必填  |
| bill_month  | float |  |  时间| 必填  |
| bill_time_stamp  | int |  |  账单毫秒时间戳| 必填  |
| left_money  | float |  |  月剩余应收款| 必填  |


```json
{
	"msg": "",
	"status": 0,
	"data": {
		"this_month_left_money": -0.11,
		"detail_bill": [{
			"pay_remark": "暂无",
			"order_count": 3653.0,
			"recv_money": "1236.77",
			"receivable": "1236.77",
			"total_amount": 20912.81,
			"group_index": "2019-08_9",
			"mch_name": "张楠运营团队",
			"bill_time_stamp": 1564617600000,
			"sales_name": "张楠",
			"bill_month": "2019-08-01",
			"left_money": "0.00"
		}, {
			"pay_remark": "暂无",
			"order_count": 4548.0,
			"recv_money": "1823.25",
			"receivable": "1823.25",
			"total_amount": 30488.68,
			"group_index": "2019-09_9",
			"mch_name": "张楠运营团队",
			"bill_time_stamp": 1567296000000,
			"sales_name": "张楠",
			"bill_month": "2019-09-01",
			"left_money": "0.00"
		}, {
			"pay_remark": "暂无",
			"order_count": 4183.0,
			"recv_money": "1838.45",
			"receivable": "1838.45",
			"total_amount": 30701.88,
			"group_index": "2019-10_9",
			"mch_name": "张楠运营团队",
			"bill_time_stamp": 1569888000000,
			"sales_name": "张楠",
			"bill_month": "2019-10-01",
			"left_money": "0.00"
		}, {
			"pay_remark": "暂无",
			"order_count": 4377.0,
			"recv_money": "1722.94",
			"receivable": "1722.94",
			"total_amount": 28715.7,
			"group_index": "2019-11_9",
			"mch_name": "张楠运营团队",
			"bill_time_stamp": 1572566400000,
			"sales_name": "张楠",
			"bill_month": "2019-11-01",
			"left_money": "0.00"
		}, {
			"pay_remark": "暂无",
			"order_count": 1415.0,
			"recv_money": "496.28",
			"receivable": "496.17",
			"total_amount": 8269.58,
			"group_index": "2019-12_9",
			"mch_name": "张楠运营团队",
			"bill_time_stamp": 1575158400000,
			"sales_name": "张楠",
			"bill_month": "2019-12-01",
			"left_money": "-0.11"
		}],
		"total_left_money": 0.0,
		"balance": 0.11
	}
}
```

## 查询商户充值详情
- 简介：查询商户账单详情
- 请求方式：GET
- URL：http://crm.xiaomai24h.com:8899//make_crm/api/get_mch_receivable_detail/?mch_id=9

|  参数名称 | 类型 | 长度限制  | 简介  | 备注  |
| ------------ | ------------ |------------ | ------------ | ------------ |
| mch_id  | int | 11 |  商户ID | 必填  |

正常返回值，此时status为0：

返回参数介绍

|  参数名称 | 类型 | 长度限制  | 简介  | 备注  |
| ------------ | ------------ |------------ | ------------ | ------------ |
| amount  | float |  |  入账金额 | 必填  |
| recv_time  | string |  |  入账时间 | 必填  |
| recv_time_stamp  | int |  |  入账毫秒时间戳 | 必填  |
| pay_method  | string |  |  付款方式 | 必填  |

```json
{
	"msg": "",
	"status": 0,
	"data": {
		"resp_items": [{
			"amount": 1236.77,
			"pay_method": "自动收款账号",
			"recv_time": "2019-08-01",
			"recv_time_stamp": 1564617600000
		}, {
			"amount": 1823.25,
			"pay_method": "自动收款账号",
			"recv_time": "2019-09-01",
			"recv_time_stamp": 1567296000000
		}, {
			"amount": 1838.45,
			"pay_method": "自动收款账号",
			"recv_time": "2019-10-01",
			"recv_time_stamp": 1569888000000
		}, {
			"amount": 1722.94,
			"pay_method": "自动收款账号",
			"recv_time": "2019-11-01",
			"recv_time_stamp": 1572566400000
		}, {
			"amount": 510.55,
			"pay_method": "自动收款账号",
			"recv_time": "2019-12-01",
			"recv_time_stamp": 1575158400000
		}, {
			"amount": 0.1,
			"pay_method": "支付宝自助充值",
			"recv_time": "2019-12-10",
			"recv_time_stamp": 1575995922000
		}, {
			"amount": 0.01,
			"pay_method": "支付宝自助充值",
			"recv_time": "2019-12-10",
			"recv_time_stamp": 1576000370000
		}]
	}
}
```

## 商户API充值接口
- 简介：商户API充值接口，只有白名单IP可以调用
- 请求方式：GET/POST表单
- URL：http://crm.xiaomai24h.com:8899//make_crm/api/mch_api_recharge/?mch_id=9&amount=1

|  参数名称 | 类型 | 长度限制  | 简介  | 备注  |
| ------------ | ------------ |------------ | ------------ | ------------ |
| mch_id  | int | 11 |  商户ID | 必填  |
| amount  | float | 11 |  充值金额元，范围-1000到10000 | 必填  |

正常返回值，此时status为0：

返回参数介绍

```json
{
	"msg": "",
	"status": 0,
	"data": {
		"id": 6015
	}
}
```
