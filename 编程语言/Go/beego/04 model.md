安装

```shell
go get github.com/astaxie/beego/orm
```

配置

在models中创建结构体, 并且通过init来注册

```go
package models

import (
	"github.com/astaxie/beego/orm"
	_ "github.com/go-sql-driver/mysql" // go get github.com/go-sql-driver/mysql 这里必须需要驱动
)

type User struct {
	Id int
	Name string
	Pwd string
	// Nickname string
}

func init(){
	// 设置数据库连接
	orm.RegisterDataBase("default","mysql","root:123456@tcp(127.0.0.1)/test?charset=utf8")
	// 注册模型, 这里还有前缀注册, 具体的看文档去吧
	orm.RegisterModel(new(User))

	// 生成表, 第二个参数是重新生成表(表里数据也会清掉), 第三个参数是展示创建过程; 生成表的时候有一个比较好玩的事情, 就是添加字段的时候会添加
	// 但是减少字段的时候数据库里不会真的减少
	orm.RunSyncdb("default",true,false)
}
```

这里既然用到了init,那肯定需要导入他了, 注意这里的orm是可以单独使用的.通常在项目中, 我们会在main.go中像导入路由一样来导入orm 

### 数据操作

#### 插入数据

```go
	o:= orm.NewOrm() // 创建一个orm对象
	user := models.User{Name:"刚刚", Pwd:"123"} // 创建一个结构体实例
	_,err := o.Insert(&user) // 
	if err != nil{
		fmt.Println(err)
	}
```

#### 批量插入

批量插入于插入类似, 不过它使用了一个结构体数组

第一个参数 bulk 为并列插入的数量, 值为1时会按顺序插入,返回值为插入的行数

```go
users := []User{
    {Name: "slene"},
    {Name: "astaxie"},
    {Name: "unknown"},
    ...
}
successNums, err := o.InsertMulti(100, users)
```

#### 简单查询

简单查询也是创建一个结构体, 里面就是他的条件

```go
o:= orm.NewOrm()
user := models.User{Name:"刚刚"}
err := o.Read(&user,"name") // 这里第二个参数数字段名, 如果不写的话默认是用主键查的
if err == orm.ErrNoRows{
  fmt.Println("查询不到")
}else if err==orm.ErrMissPK{
  fmt.Println("找不到主键")
}else{
  // 查询结束后,数据会存在上面那个结构体中
  fmt.Println(user.Id,user.Pwd)
}
```

当查询出来多个结果的时候, 他只会选择第一个

#### 更新

更新时需要先把数据查询出来

```go
o:= orm.NewOrm()

user := models.User{Name:"刚刚"}
err := o.Read(&user,"name") // 这里第二个参数数字段名, 如果不写的话默认是用主键查的
if err == nil { // 正确的查询是没有返回值的
  user.Name = "顿顿"
  if num,err1 := o.Update(&user);err1 == nil{
    fmt.Println(num)
  }
}
```

另外这个更新是将所有的字段全部更新的, 当然也可以选择字段更新

```go
o.Update(&user, "name")
```

#### 删除

删除也是需要先把对象查出来

```go
o := orm.NewOrm()
if num, err := o.Delete(&User{Name:"刚刚","name"}); err == nil {
    fmt.Println(num)
}
```



#### 读取或创建

默认必须传入一个参数作为条件字段，同时也支持多个参数多个条件字段

```go
o := orm.NewOrm()
user := User{Name: "slene"}
// 三个返回参数依次为：是否新创建的，对象 Id 值，错误
if created, id, err := o.ReadOrCreate(&user, "Name"); err == nil {
    if created {
        fmt.Println("New Insert an object. Id:", id)
    } else {
        fmt.Println("Get an object. Id:", id)
    }
}
```



#### 直接使用SQL语句

```go
o := orm.NewOrm()
var r orm.RawSeter
r = o.Raw("UPDATE user SET name = ? WHERE name = ?", "testing", "slene")
```

### 复杂查询

高级查询就不能够使用上面那个o来解决了, 需要用到QuerySeter, 有点像django的queryset, 每个返回queryset的方法都会返回一个新的queryset

```go
o := orm.NewOrm()

// 获取 QuerySeter 对象，user 为表名
qs := o.QueryTable("user")

// 也可以直接使用对象作为表名
user := new(User)
qs = o.QueryTable(user) // 返回 QuerySeter
```

#### 查询全部

All方法可以将查询结果放进结构体切片中

```go
	o:= orm.NewOrm()
	qs := o.QueryTable("user")

	var users []*models.User
	qs.All(&users)

	for _,v:= range users{
		fmt.Println(v)
	}
```

#### 条件查询

条件查询是通过Filter方法来实现的, 他与django的filter类似, 同样他也支持外键, 语法也与django类似

```go
qs.Filter("id", 1) // WHERE id = 1
qs.Filter("age", 18) // WHERE age = 18
qs.Filter("Age", 18) // 使用字段名和 Field 名都是允许的
qs.Filter("age__gt", 18) // WHERE age > 18
qs.Filter("age__gte", 18) // WHERE age >= 18
qs.Filter("age__in", 18, 20) // WHERE age IN (18, 20)
```

更多条件, i开头的表示忽略大小写

- exact/ iexact 等于
- contains / icontains 包含
- gt / gte 大于 / 大于等于
- lt / lte 小于 / 小于等于
- startswith/ istartswith 以…起始
- endswith/ iendswith 以…结束
- in
- isnull

既然这里有filter了, name这里也还有一个类似get的方法, 当找不到对象或者找到多条的时候他就会报错

```go
var user User
err := o.QueryTable("user").Filter("name", "slene").One(&user)
if err == orm.ErrMultiRows {
    // 多条的时候报错
    fmt.Printf("Returned Multi Rows Not One")
}
if err == orm.ErrNoRows {
    // 没有找到记录
    fmt.Printf("Not row found")
}
```

One还可以指定返回的字段, 其余的字段都是该类型的默认值

```go
var user User
err := o.QueryTable("user").Filter("name", "slene").One(&user,"id","name")
```



#### 排除

Exclude方法用于排除满足条件的

```go
qs.Exclude("age", 18).Filter("name", "刚刚")
// WHERE NOT age != 18 AND name = '刚刚'
```

#### Limit

limit都不陌生, 主要是用来限制返回条数, 在beego中正好也有一个Limit方法, 它还可以设置偏移量

```go
qs.Limit(10)
// LIMIT 10, 默认情况下的limit是1000

qs.Limit(10, 20)
// LIMIT 10 OFFSET 20 注意跟 SQL 反过来的
```

这里就直接把offset也说了吧

```go
qs.Offset(20)
// LIMIT 1000 OFFSET 20
```

#### 排序

在 expr 前使用减号 `-` 表示 `DESC` 的排列, 支持跨表

```go
qs.OrderBy("id", "-age")
// ORDER BY id ASC, age DESC

qs.OrderBy("-age")
// ORDER BY age DESC
```

#### 去重

对应 sql 的 `distinct` 语句, 返回不重复的值.

```go
qs.Distinct()
// SELECT DISTINCT
```

#### 计数

```go
cnt, err := o.QueryTable("user").Count() // SELECT COUNT(*) FROM USER
fmt.Printf("Count Num: %s, %s", cnt, err)
```

#### 指定查询字段

```go
var list orm.ParamsList
num, err := o.QueryTable("user").ValuesFlat(&list, "name")
if err == nil {
    fmt.Printf("Result Nums: %d\n", num)
    fmt.Printf("All User Names: %s", strings.Join(list, ", "))
}
```

#### Values和ValuesList

这两个方法返回的都是数组, 不过一个里面是映射, 另一个里面是切片里面的每个值都是字符串

```go
o := orm.NewOrm()
	qs := o.QueryTable("user")

	var maps []orm.Params
	_, err := qs.Values(&maps) // 后面可以跟选择的字段
	if err == nil {
		for _, v := range maps {
			fmt.Println(v)
		}
	}
/*
map[Id:1 Name:顿顿 Pwd:123 Age:20]
map[Pwd:123 Age:17 Id:2 Name:顿顿]
*/

// ValueList
	o := orm.NewOrm()
	qs := o.QueryTable("user")

	var lists []orm.ParamsList
	_, err := qs.ValuesList(&lists) // 后面可以跟选择的字段
	if err == nil {
		for _, v := range lists {
			fmt.Println(v)
		}
	}
/*
[1 顿顿 123 20]
[2 顿顿 123 17]
*/
```

### 跨表

model编写

```
type User struct {
	Id int
	Name string
	Pwd string
	Age int
	Profile     *Profile   `orm:"rel(one)"` // OneToOne relation
	Post        []*Post `orm:"reverse(many)"` // 设置一对多的反向关系
	// Nickname string
}

type Profile struct {
	Id          int
	Age         int16
	User        *User   `orm:"reverse(one)"` // 设置一对一反向关系(可选)
}

type Post struct {
	Id    int
	Title string
	User  *User  `orm:"rel(fk)"`    //设置一对多关系
}
```

RelatedSel方法用来进行关系查询, 默认情况下他会进行5层, 也可以指定字段去进行查询

```go
qs.RelatedSel()
qs.RelatedSel("user")
```

现在user和profile是一对一关系

现在我们来进行查询把

```go
	o := orm.NewOrm()
	user := &models.User{}
	o.QueryTable("user").Filter("id",1).RelatedSel().One(user)
	fmt.Println(user) // &{1 顿顿 123 20 0xc0000c68e0 []}
	fmt.Println(user.Profile) // &{1 18 0xc0000c8410}
	fmt.Println(user.Profile.User) // &{1 顿顿 123 20 0xc00000c520 []}
```

