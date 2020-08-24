[//]: # (Laravel 创建帮助类自定义函数)


在 laravel 里封装自定义函数其实很简单。


### 创建 helper 类文件

在 `bootstrap\\` 目录下新建 `helpers.php` 文件。

### 配置 composer 自动加载

`composer.json` 的 `"autoload.files"` 填写 `helper.php的路径`

```
    "autoload": {
        ...
        "files": [
            "bootstrap/helpers.php"
        ],
        ...
    },
```

### 执行 composer 加载

```bash
composer dump-autoload
```

### 自定义函数示例

`helper.php` 里自定义的函数可以在任意地方使用，使用方法跟 php 内置函数完全一样。

```php
<?php
/**
 * Created by PhpStorm.
 * User: HongXunPan
 * Date: 2020/4/12
 * Time: 15:55
 */

/**
 * 金额转换
 * 数据库存的是分,转换为元
 */
if (!function_exists('money_format_from_db')) {
    function money_format_from_db($number , $decimals = 2 , $dec_point = '.' , $thousands_sep = ',')
    {
        return number_format(($number / 100 . ''), $decimals, $dec_point, $thousands_sep);
    }
}

/**
 * 金额转换
 * 元的金额转换为入库单位
 */
if (!function_exists('money_format_to_db')) {
    function money_format_to_db($number, $decimals = 2, $dec_point = '.' , $thousands_sep = ',')
    {
        return number_format($number * 100 . '', $decimals, $dec_point, $thousands_sep);
    }
}

```

调用 

```php
     $value = money_format_from_db($job->price, 2, '.', '') . '元/' . $job->unit;
```