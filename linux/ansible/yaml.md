YAML 是一种简洁的非标记语言。YAML以数据为中心，使用空白，缩进，分行组织数据，从而使得表示更加简洁易读.

#### 基本格式要求

1，YAML大小写敏感；
2，使用缩进代表层级关系；
3，缩进只能使用空格，不能使用TAB，不要求空格个数，只需要相同层级左对齐（一般2个或4个空格）

#### 基本数据类型

YAML中提供了多种基本类型，包括：整数，浮点数，字符串，NULL，日期，布尔，时间。下面使用一个例子来快速了解常量的基本使用：

```yaml
boolean: 
    - TRUE  #true,True都可以
    - FALSE  #false，False都可以
float:
    - 3.14
    - 6.8523015e+5  #可以使用科学计数法
int:
    - 123
    - 0b1010_0111_0100_1010_1110    #二进制表示
null:
    nodeName: 'node'
    parent: ~  #使用~表示null
string:
    - 哈哈
    - 'Hello world'  #可以使用双引号或者单引号包裹特殊字符
    - newline
      newline2    #字符串可以拆成多行，每一行会被转化成一个空格
date:
    - 2018-02-17    #日期必须使用ISO 8601格式，即yyyy-MM-dd
datetime: 
    -  2018-02-17T15:02:31+08:00    #时间使用ISO 8601格式，时间和日期之间使用T连接，最后使用+代表时区
```

#### 数组

使用一个短横线加一个空格代表一个数组项：

```yaml
hobby:
    - Java
    - python
```

转换为json为

```json
{"hobby":["java","python"]}
```

#### 对象

使用冒号代表，格式为key: value。冒号后面要加一个空格：

```yaml
key: 
    child-key: value
    child-key2: value2
```

转换为json为

```json
{"key":{"child-key1":"value", "child-key2":"value2"}}
```

