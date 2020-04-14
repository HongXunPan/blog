[//]: # (PHP curl 获取当前请求 header 信息)

> 本文首发于 https://blog.kangxuanpeng.com/post/php-curl_getinfo

### 背景

工作中经常需要对接第三方 api ，排查问题的时候需要提供请求详细参数信息。  
有时候可能是认证头 Authorization 有误，如果不一并记录获取提供检查，可能会走很多弯路都不能发现问题所在。


### 正常 curl 获取响应结果


```php
$url = 'xxx.kangxuanpeng.com/xx/server';
$data = ['param' => 'test'];

$ch = curl_init($url);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, array("content-type:application/json"));
curl_setopt($ch, CURLOPT_POST, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($data));

$response = curl_exec($ch);  // 获取响应内容
curl_close($ch);
```

这样只能获取得到 api 响应内容 body，如果出错了或者是返回非期望内容。需要联系提供方排查问题，我们可以用 php 的 curl_getinfo 函数获取更详细的信息。

### curl_getinfo() 获取响应详情

```php

...
$response = curl_exec($ch);  // 获取响应内容
$info = curl_getinfo($ch);
$error = curl_error($ch);

curl_close($ch);
```

利用 curl_getinfo() 获取响应详情，可以获得以下内容结构：
```
 $info = Array
(
    [url] => xxx.kangxuanpeng.com/xx/server
    [content_type] => application/json;charset=UTF-8
    [http_code] => 200
    [header_size] => 196
    [request_size] => 144
    [filetime] => -1
    [ssl_verify_result] => 0
    [redirect_count] => 0
    [total_time] => 0.266
    [namelookup_time] => 0.063
    [connect_time] => 0.188
    [pretransfer_time] => 0.188
    [size_upload] => 8
    [size_download] => 33
    [speed_download] => 124
    [speed_upload] => 30
    [download_content_length] => -1
    [upload_content_length] => 8
    [starttransfer_time] => 0.266
    [redirect_time] => 0
    [redirect_url] => 
    [primary_ip] => 161.117.85.239
    [certinfo] => Array
        (
        )

    [primary_port] => 80
    [local_ip] => 192.168.15.86
    [local_port] => 61451
)
```
curl_error() 可以获取 http 错误信息


### 获取发送的请求 header 信息

需要在 curl_exec() 之前设置
```php
//至关重要，CURLINFO_HEADER_OUT选项可以拿到请求头信息
curl_setopt($ch, CURLINFO_HEADER_OUT, TRUE);

$response = curl_exec($ch);
$info = curl_getinfo($ch);
$error = curl_error($ch);

curl_close($ch);
```

这样执行后得到的 info 如下，其中 request_header 为请求的 header 信息：
```
 $info = Array
(
    [url] => xxx.kangxuanpeng.com/xx/server
    [content_type] => application/json;charset=UTF-8
    [http_code] => 200
    [header_size] => 196
    [request_size] => 144
    [filetime] => -1
    [ssl_verify_result] => 0
    [redirect_count] => 0
    [total_time] => 0.218
    [namelookup_time] => 0
    [connect_time] => 0.125
    [pretransfer_time] => 0.125
    [size_upload] => 8
    [size_download] => 33
    [speed_download] => 151
    [speed_upload] => 36
    [download_content_length] => -1
    [upload_content_length] => 8
    [starttransfer_time] => 0.218
    [redirect_time] => 0
    [redirect_url] => 
    [primary_ip] => 161.117.85.239
    [certinfo] => Array
        (
        )

    [primary_port] => 80
    [local_ip] => 192.168.15.86
    [local_port] => 61756
    [request_header] => POST /xx/server HTTP/1.1
Host: xxx.kangxuanpeng.com
Accept: */*
content-type:application/json
Content-Length: 8


)
```

PS: 如果 api 是根据 http 状态来区分执行结果的话，可以选择不接收响应体，可以节省带宽以及时间。
```
// 是否不需要响应的正文,为了节省带宽及时间,在只需要响应头的情况下可以不要正文
curl_setopt($oCurl, CURLOPT_NOBODY, true);
```
