# 马克CRM系统商户充值接口
马克公司CRM系统，支付宝自助收款接口


## 充值API
- 简介：用户通过该api获取充值链接
- 请求方式：GET
- URL：http://47.103.115.71:8899/make_crm/api/create_mch_recharge/?mch_id=9&amount=0.01

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

