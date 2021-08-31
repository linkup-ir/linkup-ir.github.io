# سرویس تایید پرداخت مشتری
---
با استفاده از این سرویس قادر خواهید بود وب سایت فروشگاهتون رو به شبکه لینکاپ وصل کنید. برای این منظور می بایست دو مرحله رو انجام بدید:

## ۱. ذخیره شناسه رهگیری
در این مرحله شما بایستی شناسه‌ی رهگیری که به صفحه‌ی محصول شما از طریق متد GET ارسال می‌شود را ذخیره کنید تا در آینده آن را برای ما ارسال کنید.  
اطلاعات ارسال شده به شما بصورت زیر است:  

```http
GET /your-product?track_id={track-id} HTTP/1.1
Host: your-website-url.com
```
> مثال از URL ارسالی: `https://example.com/product-2/track_id=064ee72930404724974761b0b41e9e49`
---
## ۲. ارسال اطلاعات تایید تراکنش به ما
در این مرحله که بعد از پرداخت موفق مشتری صورت ‌می‌گیرد (معمولا در صفحه‌ی callback بانک)، شما شناسه رهگیری ذخیره شده در مرحله قبل را باید برای ما بصورت زیر ارسال کنید:  

?>   توجه کنید که متد درخواست باید بصورت POST و همچنین اطلاعات ارسالی بصورت JSON باشد.  

###### آدرس درخواست:
```
https://api.hamekar.ir/customer/callback
```
```http
POST /customer/callback HTTP/1.1
Host: api.hamekar.ir
x-store-token: {your-token}
Content-Type: application/json

{
    "track_id": {track-id},
    "status": 1
}
```

###### پارامتر‌های مجاز هدر
|فیلد  |نوع  |توضیحات  |اجباری  |
|--:|---|--:|---|
|x-store-token     |String         |توکن دسترسی که از پنل کاربری فروشگاه قابل دریافت است.          |بله         |

###### پارامتر‌های مجاز ورودی
|فیلد  |نوع  |توضیحات  |اجباری  |
|--:|--- |--:|---|
|track_id     |String         |شناسه رهگیری ذخیره شده در مرحله اول         |بله         |
|status         |Integer         |وضعیت تایید تراکنش، درصورتی که پرداخت صورت گرفته باشد باید همیشه 1 ارسال شود. مقادیر مجاز 0 و 1 می‌باشد.         |بله

!> در صورت این‌که پرداخت مشتری موفقیت آمیز نباشد، شما می‌توانید این مرحله را کاملا نادیده بگیرید چرا که اگر پارامتر `status` صفر ارسال شود، پاسخ دریافتی بصورت خطا می‌باشد.
### پاسخ‌های دریافتی
<!-- tabs:start -->
#### **در صورت صحت اطلاعات ارسالی**
```http
HTTP/1.1 202 OK
Content-Type: application/json

{
    "data": {
        "message": "با موفقیت ثبت شد."
    },
    "success": true
}
```

#### **در صورت بروز خطا در اطلاعات ورودی**
```http
HTTP/1.1 422 UNPROCESSABLE ENTITY
Content-Type: application/json

{
    "data": {
        "message": "خطایی در روند تایید رخ داد."
    },
    "success": false
}
```


#### **در صورت اشتباه بودن توکن دسترسی**
```http
HTTP/1.1 401 UNAUTHORIZED
Content-Type: application/json

{
    "data": {
        "error_type": "APPUnauthorizedException",
        "errors": [],
        "message": "اپلیکیشن دسترسی ندارد!"
    },
    "success": false
}
```
<!-- tabs:end -->

## نمونه کد ها:

<!-- tabs:start -->

### **cURL**
```bash
curl --location --request POST 'https://api.hamekar.ir/customer/callback' \
--header 'x-store-token: {your-token}' \
--header 'Content-Type: application/json' \
--data-raw '{
    "track_id": "{track-id}",
    "status": 1
}'
```

### **PHP - cURL**
```php
<?php

$curl = curl_init();

curl_setopt_array($curl, array(
  CURLOPT_URL => 'https://api.hamekar.ir/customer/callback',
  CURLOPT_RETURNTRANSFER => true,
  CURLOPT_ENCODING => '',
  CURLOPT_MAXREDIRS => 10,
  CURLOPT_TIMEOUT => 0,
  CURLOPT_FOLLOWLOCATION => true,
  CURLOPT_HTTP_VERSION => CURL_HTTP_VERSION_1_1,
  CURLOPT_CUSTOMREQUEST => 'POST',
  CURLOPT_POSTFIELDS =>'{
    "track_id": "{track-id}",
    "status": 1
}',
  CURLOPT_HTTPHEADER => array(
    'x-store-token: {your-token}',
    'Content-Type: application/json'
  ),
));

$response = curl_exec($curl);

curl_close($curl);
echo $response;

```


## **Python - Requests**
```python
import requests
import json

url = "https://api.hamekar.ir/customer/callback"

payload = json.dumps({
  "track_id": "{track-id}",
  "status": 1
})
headers = {
  'x-store-token': '{your-token}',
  'Content-Type': 'application/json'
}

response = requests.request("POST", url, headers=headers, data=payload)

print(response.text)
```


## **JavaScript - jQuery**
```js
var settings = {
  "url": "https://api.hamekar.ir/customer/callback",
  "method": "POST",
  "timeout": 0,
  "headers": {
    "x-store-token": "{your-token}",
    "Content-Type": "application/json"
  },
  "data": JSON.stringify({
    "track_id": "{track-id}",
    "status": 1
  }),
};

$.ajax(settings).done(function (response) {
  console.log(response);
});
```


## **Go - Native**
```go
package main

import (
  "fmt"
  "strings"
  "net/http"
  "io/ioutil"
)

func main() {

  url := "https://api.hamekar.ir/customer/callback"
  method := "POST"

  payload := strings.NewReader(`{`+"
"+`
    "track_id": "{track-id}",`+"
"+`
    "status": 1`+"
"+`
}`)

  client := &http.Client {
  }
  req, err := http.NewRequest(method, url, payload)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("x-store-token", "{your-token}")
  req.Header.Add("Content-Type", "application/json")

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(string(body))
}
```

<!-- tabs:end -->
