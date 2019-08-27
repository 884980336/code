Go语言对json的解析函数在encoding/json包里面，主要是编码和解码两个函数。

## 编码

**Marshal函数**

`func Marshal(v interface{}) ([]byte, error)`

```go
package main

import (
	"encoding/json"
	"fmt"
)

type User struct {
	Name string
	Age  int
}

type Father struct {
	Boy User
}

type Husband struct {
	Wife User
}

type Man struct {
	User
	Husband
	Father
	salary float64
}

func main() {
	// map转json
	mp := map[string]int{"1": 1, "2": 2}
	js, err := json.Marshal(mp)
	if err == nil {
		ru := string(js)
		fmt.Println(ru) // {"1":1,"2":2}
	}
	// 结构体转json
	us := User{"gang", 18}
	ujs, uerr := json.Marshal(us)
	if uerr == nil {
		ru := string(ujs)
		fmt.Println(ru) // {"Name":"gang","Age":18}
	}

	// 嵌套结构体转json
	man := Man{User: User{Name: "gang", Age: 18},
		Father:  Father{User{"pipi", 18}},
		Husband: Husband{User{"xia", 18}},
		salary:  998}
	manjs, manerr := json.Marshal(man)
	if manerr == nil {
		ru := string(manjs)
		// 小写开头的不会被序列化
		fmt.Println(ru) // {"Name":"gang","Age":18,"Wife":{"Name":"xia","Age":18},"Boy":{"Name":"pipi","Age":18}}

	}
}
```

通道、复数、函数类型的值不能编码进json。尝试编码它们会导致Marshal函数返回UnsupportedTypeError。

## 解码

`func Unmarshal(data []byte, v interface{}) error`

Unmarshal函数解析json编码的数据并将结果存入v指向的值。

```go
package main

import (
	"encoding/json"
	"fmt"
)

type User struct {
	Name string
	Age  int
}

type Father struct {
	Boy User
}

type Husband struct {
	Wife User
}

type Man struct {
	User
	Husband
	Father
	salary float64
}

func main() {
	// map转json
	mp := map[string]int{"1": 1, "2": 2}
	js, _ := json.Marshal(mp)

	// json转map
	var remp map[string]int // 不需要初始化, 只需要声明一个同类型的map
	json.Unmarshal(js,&remp)
	fmt.Println(remp) // map[1:1 2:2]


	// 结构体转json
	us := User{"gang", 18}
	ujs, _ := json.Marshal(us)
	var user User
	json.Unmarshal(ujs,&user)
	fmt.Println(user) // {gang 18}
	//
	// 嵌套结构体转json
	man := Man{User: User{Name: "gang", Age: 18},
		Father:  Father{User{"pipi", 18}},
		Husband: Husband{User{"xia", 18}},
		salary:  998}
	manjs, _ := json.Marshal(man)
	var newman Man
	json.Unmarshal(manjs,&newman)
	fmt.Println(newman) // {{gang 18} {{xia 18}} {{pipi 18}} 0}

}
```

## Tag

使用结构体时, 可以为字段指定Tag

```
// 字段被本包忽略
Field int `json:"-"`
// 字段在json里的键为"myName"
Field int `json:"myName"`
// 字段在json里的键为"myName"且如果字段为空值将在对象中省略掉
Field int `json:"myName,omitempty"`
// 字段在json里的键为"Field"（默认值），但如果字段为空值会跳过；注意前导的逗号
Field int `json:",omitempty"`
```

示例

```go
package main

import (
	"encoding/json"
	"fmt"
)

type User struct {
	Name string `json:"name"`
	Age  int `json:"age,omitempty"`
	selery int
}



func main() {
	use := User{Name:"wei",selery:998}
	js,_ := json.Marshal(use)
	fmt.Println(string(js)) // {"name":"wei"}

	var usr User
	json.Unmarshal(js,&usr)
	fmt.Println(usr) //{wei 0 0}

}
```

