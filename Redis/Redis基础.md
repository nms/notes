# Redis基础 Rdb
## Redis使用场景

### 字符串缓存
```
//举例
$redis->set();
$redis->get();
$redis->hset();
$redis->hget();
```

### 队列
```
//举例
$redis->rpush();
$redis->lpop();
$redis->lrange();
```

### 发布订阅
```
//举例
$redis->publish();
$redis->subscribe();
```

### 计数器
```
//举例
$redis->set();
$redis->incr();
```

### 排行榜
```
//举例
$redis->zadd();
$redis->zrevrange();
$redis->zrange();
```
### 集合间操作
```
//举例
$redis->sadd();
$redis->spop();
$redis->sinter();
$redis->sunion();
$redis->sdiff();
```


### 悲观锁

#### 解释：
悲观锁(Pessimistic Lock), 顾名思义，就是很悲观。
每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁。

#### 场景：
如果项目中使用了缓存且对缓存设置了超时时间。
当并发量比较大的时候，如果没有锁机制，那么缓存过期的瞬间，
大量并发请求会穿透缓存直接查询数据库，造成雪崩效应。

```
/**
 * 获取锁
 * @param  String  $key    锁标识
 * @param  Int     $expire 锁过期时间
 * @return Boolean
 */

public function lock ($key='', $expire=5) {
    $is_lock = $this->_redis->setnx($key, time() + $expire);
    //不能获取锁

    if (!$is_lock) {
        //判断锁是否过期
        $lock_time = $this->_redis->get($key);

        //锁已过期，删除锁，重新获取    
        if (time() > $lock_time) {
            unlock($key);
            $is_lock = $this->_redis->setnx($key, time() + $expire);
        }
    }

    return $is_lock ? true : false;
}

/**
 * 释放锁
 * @param  String  $key 锁标识
 * @return Boolean
 */

public function unlock($key = '') {    
    return $this->_redis->del($key);
}

// 定义锁标识
$key = 'test_lock';

// 获取锁
$is_lock = lock($key, 10);
if ($is_lock) {
    echo 'get lock success<br>';
    echo 'do sth..<br>';
    sleep(5);
    echo 'success<br>';
    unlock($key);
} else { 
    //获取锁失败
    echo 'request too frequently<br>';
}
```


### 乐观锁
#### 解释：
乐观锁(Optimistic Lock), 顾名思义，就是很乐观。
每次去拿数据的时候都认为别人不会修改，所以不会上锁。
watch命令会监视给定的key，当exec时候如果监视的key从调用watch后发生过变化，则整个事务会失败。
也可以调用watch多次监视多个key。这样就可以对指定的key加乐观锁了。
注意watch的key是对整个连接有效的，事务也一样。
如果连接断开，监视和事务都会被自动清除。
当然了exec，discard，unwatch命令都会清除连接中的所有监视。

```
$strKey = 'test_age';
$redis->set($strKey, 10);
$age = $redis->get($strKey);

echo "---- Current Age:{$age} ---- <br/><br/>";
$redis->watch($strKey);
// 开启事务
$redis->multi();
//在这个时候新开了一个新会话执行
$redis->set($strKey, 30);
//新会话
echo "---- Current Age:{$age} ---- <br/><br/>";//30
$redis->set($strKey, 20);
$redis->exec();
$age = $redis->get($strKey);
echo "---- Current Age:{$age} ---- <br/><br/>"; //30
//当exec时候如果监视的key从调用watch后发生过变化，则整个事务会失败
```