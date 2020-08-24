[//]: # (PHP 快速获取周一0点的时间戳)

### 背景

项目中有遇到一些业务是以周为单位的，比如周一0点进行某些结算排名之类的。这时候就需要获取周一0点的时间戳进行数据的分割拆分统计等操作，然而获取本周一0点的时间戳 PHP 并没有提供原生函数 。  

### 传统方法

判断今天是星期几，然后根据周一应该跟今天相差的时间算出周一0点的时间。

```php
//获取本周一
public static function onMonday($type)
{
    $weekday = date('w');
    if ($weekday == 0) {
        $weekday = 7;
    }
    $weekday = $weekday - 1;
    if ($type == 1) {
        return date("Ymd", strtotime("- {$weekday} days"));
    } else {
        return strtotime(date("Ymd", strtotime("- {$weekday} days")));
    }
}
```

### 捷径

偶然一次发现，`strtotime('next Monday')` 总是会返回下一周的周一的时间，无论当前时间是星期几。这与 `strtotime('this Monday')` 抑或 `strtotime('last Monday')` 都不一样。

```php
$thisMonday = strtotime('next Monday') - 60*60*24*7;
```

