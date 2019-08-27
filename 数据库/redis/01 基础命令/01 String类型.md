字符串可以分为 **字符串, 整数,浮点数** 三种类型.整数的取值范围和系统的长整数的取值范围相同, 浮点数的取值范围和精度与double相同.

## 常用操作

`set key value` 设置一个字符串

`get key` 获取一个字符串

```shell
127.0.0.1:6379> set str a
OK
127.0.0.1:6379> get str
"a"
```

`del key` 删除指定键对应的内容, 不仅仅可以用于字符串

```shell
127.0.0.1:6379> del str
(integer) 1
```

## 自增操作

当一个值存到字符串里面的时候, 如果这个值可以被解释为十进制整数或浮点数, redis会察觉到这一点, 并允许用户在这个字符串上进行自增操作.

如果用户对一个不存在的键或者保存了一个空串的字符串进行自增操作, redis会将这个键对应的值当做0来处理

| incr key-name               | 将键对应的值加1              |
| --------------------------- | ---------------------------- |
| decr key-name               | 将键对应的值减去1            |
| incrby key-name amount      | 将键对应的值加上指定整数值   |
| decrby key-name amount      | 将键对应的值减去指定整数值   |
| incrbyfloat key-name amount | 将键对应的值加上指定浮点数值 |

**incrbyfloat **在redis 2.6以上的版本才可以使用, 而且只有加没有减

## 字符串操作

`append key value` 给key对应的值后面添加value

```shell
127.0.0.1:6379> set str w
OK
127.0.0.1:6379> append str w
(integer) 2
127.0.0.1:6379> get str
"ww"
```

`getrange key start end` 或取指定位置的子串, 包括start,end

```shell
127.0.0.1:6379> set str woshini88
OK
127.0.0.1:6379> getrange str 0 2
"wos"
```

`setrange key offset value` 将子串设置为指定值

```shell
127.0.0.1:6379> set str woshini88
OK
127.0.0.1:6379> setrange str 0 ha
(integer) 9
127.0.0.1:6379> get str
"hashini88"
```

`getbit key offset` 将字符串看作是二进制位串, 返回offset位的二进制位的值

`setbit key offset value` 设置二进制位的值

```shell
127.0.0.1:6379> set str a
OK
127.0.0.1:6379> setbit str 6 1
(integer) 0
127.0.0.1:6379> get str
"c"
```

> a的ascii码为97, 二进制为1100001, 这里把倒数第二位改为了1, 变成了1100011, 也就成了c

`bitcount key [start end]` 统计二进制里值为1的数量

```shell
127.0.0.1:6379> set str a
OK
127.0.0.1:6379> bitcount str
(integer) 3
```



 