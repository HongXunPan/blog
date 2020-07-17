[//]: # (PHP http_build_query 转换数组参数中含特殊符号的异常)

### 背景

工作中经常需要对接第三方系统，经常遇到 curl 参数报错异常的问题。  


### curl post 传递参数


```php
$url = 'xxx.kangxuanpeng.com/xx/server';
$data = ['param' => 'test'];

$ch = curl_init($url);
$head_array = array('application/x-www-form-urlencoded');
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_POST, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query($req_array));
curl_setopt($ch, CURLOPT_HTTPHEADER, $head_array);
$response = curl_exec($ch);

```

参数经过 `http_build_query()` 转换成 key 对应 value 值的绑定发送到第三方，问题就在于 `http_build_query()` 进行的符号加密。

### 问题定位

如果参数无特殊符号， `http_build_query()`正常加密:

```php
$param = ['param' => 'test', 'blog' => 'HongXunPan'];

echo http_build_query($param);
//param=test&blog=HongXunPan
```

但是如果参数中有特殊参数的话，就不一样了:

```php
$param = ['param' => 'test','blog' => 'HongXunPan', 'url' => 'https://blog.kangxuanpeng.com/'，'time' => '2020-06-18 22:00:00'];

echo http_build_query($param);
//param=test&blog=HongXunPan&url=https%3A%2F%2Fblog.kangxuanpeng.com%2F&time=2020-06-18+22%3A00%3A00
```

然而恰好是对 `/` 符号的转换，第三方系统无法识别。

### 解决方法

问题找到了就很好定位了，只需要自己实现一个方法手动进行拼接即可。

```php
function transferArrayToSignString($req_array)
{
    $str = '';
    foreach ($req_array as $key => $value) {
        if ($str != '') {
            $str .= '&';
        }
        $str .= $key.'='.$value;
    }
    return $str;
}
```

大功告成:

```php
$param = ['param' => 'test','blog' => 'HongXunPan', 'url' => 'https://blog.kangxuanpeng.com/'，'time' => '2020-06-18 22:00:00'];

echo http_build_query($param);
//param=test&blog=HongXunPan&url=https%3A%2F%2Fblog.kangxuanpeng.com%2F&time=2020-06-18+22%3A00%3A00
echo(transferArrayToSignString($param));
//param=test&blog=HongXunPan&url=https://blog.kangxuanpeng.com/&time=2020-06-18 22:00:00
```



