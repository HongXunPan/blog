[//]:  # (一种 PHP 版并发独占锁设计)

### 背景

前段时间有反馈过来说线上有用户一天会多次签到，签到时间都是同一秒。经排查推测是并发请求一起过来，每个请求都判断今天未签到所以都给了放行条件。  

所以很有必要加上并发独占锁。

### 常规流程

1. redis 缓存值，key存在则返回报错
2. 文件独占锁 `flock($file_handle, LOCK_EX)`， 用完释放

方法有多个结束出口，需要多处写释放代码。

### lock类

把各地方的加锁、解锁抽离出来，新建lock类。

#### 原理

调用传入用户id、锁名称，加锁方法组装 redis 缓存 key，  

判断缓存是否存在，不存在则写入缓存；  

存在则进行计数器增加 `incr`，并且返回false。

#### 调用加独占锁

```php
//activity.receiveDailyTaskGift
Lock($mid, 'receiveDailyTask')->addUserLock();

```

#### 释放独占锁

```php
//领取签到任务礼金
public function receiveDailyTaskGift($param)
{
    $data = $this->receiveDailyTaskGift($param);
    if ($data['code'] != -1) {
        Lock($param['mid'], 'receiveDailyTask')->clearUserLock();
    }
    return $data;
}
```

#### 防刷用户标记（可拓展）

`incrLockTimes()` 进行计数器累加，超过了配置中的最大请求数记录用户，可列为刷子用户进行拉黑封号等操作。



### 实现源码

```php
/**
 * 用户独占锁
 * created by HongXunPan
 */
class Lock
{
    //一定时间内最大请求次数 超过则列为刷子
    private $maxTimes = 5;

    private $mid;
    private $lockName;
    private $redisKey;

    public function __construct($mid, $lockName = 'lock')
    {
        $this->mid = $mid;
        $this->lockName = $lockName;
        $this->redisKey = $this->lockName .'_'. $this->mid;
    }

    /**
     * 添加用户独占锁
     * @param int $time
     * @return bool|false|int
     */
    public function addUserLock($time = 10)
    {
        if (empty($this->mid)) {
            return false;
        }

        $result = $this->incrLockTimes();
        if ($time != 0 && $result == 1) {
            ocache::redisMember()->expire($this->redisKey, $time);
        }
        return $result;
    }

    /**
     * 清除用户独占锁
     * @return bool|int
     */
    public function clearUserLock()
    {
        if (empty($this->mid)) {
            return false;
        }
        return ocache::redisMember()->delete($this->redisKey);
    }

    /**
     * 一定时间内频繁请求的用户
     * 超过设定次数则特别标记重点观察
     */
    private function incrLockTimes()
    {
        $times = ocache::redisMember()->incr($this->redisKey);
        if ($times >= $this->maxTimes) {
            //一定时间内发多个请求 超过最大限制
            oo::logs()->dayLog('user lock max time, userId=' . $this->mid . ', key = ' .$this->redisKey . ',times=' . $times, 'lock' . $this->lockName.'txt');
            //todo 封号xxx
        }
        return $times;
    }

    //文件锁的效率耗时比redis的少,如果不考虑计数器防刷的情况下优先考虑文件锁
    //-------文件锁-------

    //文件锁的弊端是不能设置过期时间，不适合做一定时间内限制多少次请求以及超出部分怎么处理
    public function addFileLock()
    {
        $lock_path = PATH_DAT . 'lock/';
        $fileName = $lock_path . $this->redisKey;
        $file_handle = fopen($fileName, 'wb+');
        if (flock($file_handle, LOCK_EX)) {
            return true;
        } else {
            return false;
        }
    }

    //todo 需要考虑文件锁 用完的文件是否需要删除
    public function clearFileLock()
    {
        $lock_path = PATH_DAT . 'lock/';
        $fileName = $lock_path . $this->redisKey;
        $file_handle = fopen($fileName, 'wb+');
        flock($file_handle, LOCK_UN);
        unlink($fileName);
    }

    //-------文件锁--------
}
```

