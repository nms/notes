# MySQL事务总结

## 使用场景
电商订单生成及支付处理

## 使用逻辑
由PHP开启事务
```
try {
    业务逻辑
    if (false) {
        回滚事务
    }
    提交事务
} catch () {
    回滚事务
}
```

## 存储引擎支持

innodb 支持
myisam 不支持

## 事务的四个特性

特性|释义
--:|--:
原子性|对于数据的操作，要么一起执行，要么一起失败
隔离性|在操作的事务没有完成，其它事务无法操作被操作数据
一致性|根据MySQL规则，保证数据执行数量一致
持久性|事务保存在磁盘中

## 事务与行级锁的关系

```
BEGIN [START TRANSATION]
...
COMMIT
...
ROLLBACK
```

## 行锁

行锁分为排他锁和共享锁
排他锁 不允许其它程序读写

`START TRANSATION`<br>
`SELECT * FROM USER WHERE 1` **for update**`;`<br>
`COMMIT`<br>
MySQL中 `update insert delete alter` 操作默认都会加上排他锁<br>
排他锁不能与其它锁一起使用

共享锁 其它程序可以读不可以写
`START TRANSATION`<br>
`SELECT * FROM USER WHERE 1` **lock in share mode**`;`<br>
`COMMIT`<br>
共享锁可以与共享锁一起使用

