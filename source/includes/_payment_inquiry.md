## استعلام وضعیت تراکنش

با استفاده از آدرس زیر می‌توانید آخرین وضعیت یک تراکنش را دریافت نمایید.

```shell
curl -X POST https://api.idpay.ir/v1/payment/inquiry \
  -H 'Content-Type: application/json' \
  -H 'X-API-KEY: 6a7f99eb-7c20-4412-a972-6dfb7cd253a4' \
  -H 'X-SANDBOX: true' \
  -d '{
  "id": "d2e353189823079e1e4181772cff5292",
  "order_id": "101"
}'
```

```php
<?php
$params = array(
  'id' => 'd2e353189823079e1e4181772cff5292',
  'order_id' => '101',
);

$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, 'https://api.idpay.ir/v1/payment/inquiry');
curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($params));
curl_setopt($ch, CURLOPT_RETURNTRANSFER, TRUE);
curl_setopt($ch, CURLOPT_HTTPHEADER, array(
  'Content-Type: application/json',
  'X-API-KEY: 6a7f99eb-7c20-4412-a972-6dfb7cd253a4',
  'X-SANDBOX: true',
));

$result = curl_exec($ch);
$httpcode = curl_getinfo($ch, CURLINFO_HTTP_CODE);
curl_close($ch);

var_dump($httpcode);
var_dump($result);
```

```javascript
var request = require('request');

var options = {
  method: 'POST',
  url: 'https://api.idpay.ir/v1/payment/inquiry',
  headers: {
    'Content-Type': 'application/json',
    'X-API-KEY': '6a7f99eb-7c20-4412-a972-6dfb7cd253a4',
    'X-SANDBOX': true,
  },
  body: {
    'id': 'd2e353189823079e1e4181772cff5292',
    'order_id': '101',
  },
  json: true,
};

request(options, function (error, response, body) {
  if (error) throw new Error(error);

  console.log(body);
});
```

```go
url := "https://api.idpay.ir/v1/payment/inquiry"

data := map[string]string{
  "id":       "d2e353189823079e1e4181772cff5292",
  "order_id": "101",
}

payload, _ := json.Marshal(data)

req, _ := http.NewRequest("POST", url, bytes.NewBuffer(payload))

req.Header.Set("Content-Type", "application/json")
req.Header.Set("X-API-KEY", "6a7f99eb-7c20-4412-a972-6dfb7cd253a4")
req.Header.Set("X-SANDBOX", "true")

res, _ := http.DefaultClient.Do(req)

defer res.Body.Close()
body, _ := ioutil.ReadAll(res.Body)

fmt.Println(string(body))
```

### آدرس درخواست

`POST https://api.idpay.ir/v1/payment/inquiry`

### پارامترهای مورد نیاز

پارامتر | نوع | ضروری | توضیحات
------- | --- | ----- | -------
id | string | بله | کلید منحصر بفرد تراکنش که در مرحله [ایجاد تراکنش](#2c82b7acb2) دریافت شده است
order_id | string | بله | شماره سفارش پذیرنده که در مرحله [ایجاد تراکنش](#2c82b7acb2) ارسال شده است

### وضعیت پاسخ

کد وضعیت | توضیحات
-------- | -------
200 | استعلام با موفقیت پاسخ داده شد
400 | [لیست خطاها](#d7b83cfb9c)
403 | [لیست خطاها](#d7b83cfb9c)
406 | [لیست خطاها](#d7b83cfb9c)

> با اجرای دستور بالا پاسخی مشابه متن زیر با فرمت JSON دریافت می‌شود:

```json
{
  "status": "1",
  "track_id": "10012",
  "id": "d2e353189823079e1e4181772cff5292",
  "order_id": "101",
  "amount": "10000",
  "card_no": "610433******1064",
  "hashed_card_no": "D6933ABC18DE6DAB26A401738D0F4269EC2836B69F87E51933B012F9A8C6D9DB",
  "date": "1533559373"
}
```

> وضعیت 406: در صورت بروز خطا پاسخی مشابه متن زیر با فرمت JSON دریافت می‌شود:

```json
{
  "error_code": 32,
  "error_message": "شماره سفارش `order_id` نباید خالی باشد."
}
```

### پاسخ

پارامتر | نوع | توضیحات
------- | --- | -------
status | integer | [وضعیت تراکنش](#ad39f18522)
track_id | integer | کد رهگیری آیدی پی
id | string | کلید منحصر بفرد تراکنش که در مرحله [ایجاد تراکنش](#2c82b7acb2) دریافت شده است
order_id | string | شماره سفارش پذیرنده که در مرحله [ایجاد تراکنش](#2c82b7acb2) ارسال شده است
amount | integer | مبلغ ثبت شده هنگام [ایجاد تراکنش](#2c82b7acb2)
card_no | string | شماره کارت پرداخت کننده با فرمت `123456******1234`
hashed_card_no | string | هش شماره کارت پرداخت کننده با الگوریتم SHA256
date | timestamp | زمان پرداخت تراکنش

<aside class="warning"> جهت جلوگیری از دوبار مصرف شدن یک پرداخت (Double Spending)،
پذیرنده موظف است کلیدهای منحصر بفردی که از طریق API آیدی پی دریافت می‌کند را (مثل <code>id</code> و <code>track_id</code>)
در دیتابیس خود ذخیره کند و از یکتا بودن آنها اطمینان حاصل فرماید.
<br/>
توجه داشته باشید که ممکن است یک مشتری رسید پرداخت آیدی پی را ذخیره کند و برای یک خرید دیگر از آن استفاده کند.
<br/>
مسئولیت بررسی و شناسایی Double Spending کاملا به عهده پذیرنده می‌باشد.</aside>
