## 数值运算自动类型转换
- 如果两个操作数中有一个是double类型, 另一个操作数就会转换为double类型.
- 否则, 如果其中一个操作数是float类型, 另一个操作数会转换为flloat类型
- 否则, 如果其中一个操作数是long类型, 另一个操作数将会转为long类型
- 否则, 两个操作数都转为int类型

## 方法相关

#### 方法参数

java采用的是按值调用, 方法得到的是所有参数值的拷贝.

方法的参数共有两种类型, 方法无法修改基本数据类型的值, 但是能够修改对象引用中的信息

- 基本数据类型
- 对象引用

#### 方法签名和重载

如果有多个方法, 有相同的名字, 不同的参数, 便产生了重载. 编译器会通过给出的参数类型来匹配执行相应的方法

```java
public class Main {

    public static void main(String[] args) {
        System.out.println("Hello World!");

        Test.print();
        Test.print(1);
        Test.print("哈哈");
    }
}


class Test {

    public static void print() {
        System.out.println("没有传值");
    }
    

    public static void print(int a) {
        System.out.println("传了数字: " + a);
    }

    public static void print(String a) {
        System.out.println("传了字符串: " + a);
    }
}
```

这个方法名和参数就叫做方法的签名, **注意返回值并不属于函数的签名**.当有两个方法名字与参数相同, 而返回值不同时, 就会在编译时出现异常, 说方法已经被定义.

#### 更改器与访问器

```java
public class Main {

    public static void main(String[] args) {
        System.out.println("Hello World!");
        Test user = new Test("刚刚",18);
        System.out.println(user.getName());
        user.setName("魏文刚");
        System.out.println(user.getName());

    }
}


class Test {

    private String name;
    private int age;

    public Test(String name, int age){
        this.name = name;
        this.age = age;
    }

    public String getName(){
        return this.name;
    }

    public void setName(String name){
        this.name = name;
    }
}

```

如上, 通过访问器和更改器来获得和设置一个私有属性的值, 但是需要注意一个问题, **访问器不应该返回一个可以修改的对象,** 如下所示:

```java
public class Main {

    public static void main(String[] args) {
        System.out.println("Hello World!");
        Test user = new Test("刚刚",18,new Test("李白",19));
        Test otherUser = user.getMate();
        otherUser.setName("杜甫");
        System.out.println(user.getMate().getName());

    }
}


class Test {

    private String name;
    private int age;
    private Test mate;

    public Test(String name, int age){
        this.name = name;
        this.age = age;
    }

    public Test(String name, int age,Test mate){
        this.name = name;
        this.age = age;
        this.mate = mate;
    }


    public Test getMate(){
        return this.mate;
    }

    public String getName(){
        return this.name;
    }
    public void setName(String name){
        this.name = name;
    }
}

```

otherUser的名称改变后, user的mate也发生了变化. **此时的访问器应该返回一个对象的拷贝**.

## 变长参数

类型后面接一个省略号, 代表接收任意数量的对象. 这些对象会在一个数组中

```java
public class Main {


    public static void main(String[] args) {
        printS("我","是","你","爸","爸");
    }

    public static void printS(Object... args){
        for (Object a : args){
            System.out.print(a);
        }
    }
}
```

## 构造器

如上所示,

- 构造器与类同名
- 构造器可以重载
- 构造器没有返回值
- 构造器方法总是伴随着new操作符一起调用
- 如果在编写类时没有编写构造器, 系统会提供一个无参数的构造器, 这个构造器将所有的实例域设置为默认值, 如果类中实现了至少一个构造器, 但没有提供无参数的构造器, 在创建对象时没有提供参数就会被视为不合法

#### 调用另一个构造器

如果构造器的第一个语句形式如this(...), 这样的构造器将调用同一个类的另一个构造器. 

这样这个构造器非常有用,如下所示

```java
class Test {

    private String name;
    private int age;
    private Test mate;

    public Test(String name, int age){
        this.name = name;
        this.age = age;
    }

    public Test(String name, int age,Test mate){
        this(name,age);
        this.mate = mate;
    }


    public Test getMate(){
        return this.mate;
    }

    public String getName(){
        return this.name;
    }
    public void setName(String name){
        this.name = name;
    }
}
```

#### 初始化块和静态初始化块

在一个类的声明中可以包含多个代码块, **只要构造类的对象**这些块就会被执行.

```java
public class Main {

    public static void main(String[] args) {
        System.out.println("Hello World!");
    	Test2 a = new Test2();
    	Test2 b = new Test2();
    }
}

class Test2{
    {
        System.out.println("我被实例化了");
    }
}
```

在这个代码块中还可以完成初始化操作

```java
public class Main {

    public static void main(String[] args) {
        System.out.println("Hello World!");
        Test a = new Test();
        System.out.println(a.getId()); // 1
        Test b = new Test();
        System.out.println(b.getId()); // 2

    }
}


class Test {
    private static int nextId = 1;
    private int id;


    private String name;
    private int age;
    private Test mate;

    {
        id = nextId;
        nextId++;
    }

    public int getId() {
        return this.id;
    }
}

```

除了初始化块之外还存在着静态初始化块, 就是static修饰的初始化块. 静态初始化块只会在类第一次加载的时候执行

```java
public class Main {

    static
    {
        System.out.println("Main加载完成了"); // 第一个输出
    }

    public static void main(String[] args) {
        System.out.println("Hello World!"); // 第二个输出
        Test a = new Test();
        Test b = new Test();
    }
}


class Test {
    private static int nextId = 1;
    private int id;


    private String name;
    private int age;
    private Test mate;
    static
    {
        System.out.println("Test加载完成了"); // 第三个输出, 并且只输出一次
    }

    public int getId() {
        return this.id;
    }
}

```

## 继承

java只支持单继承,使用关键字**extends**表示继承

#### 在重写的方法中调用父类的方法

使用super关键字, 可以在方法中调用父类的方法

```java
public class Main {


    public static void main(String[] args) {
        Student s = new Student();
        s.print();
    }
}

class Person{
    private String name;
    private String sex;

    public void print(){
        System.out.println("我是爸爸方法");
    }
    public String getName(){
        return  this.name;
    }

    public String getSex(){
        return this.sex;
    }
}


class Student extends Person{
    private  String school;
    public void print(){
        super.print();
        System.out.println("我是儿子方法");
    }
}
```

#### 调用父类的构造器

调用父类的构造器方法也是使用super关键字, 子类的构造器不能访问父类的私有域, 需要借用父类的构造器. 如果子类没有显示的调用父类的构造器, 则会调用父类的无参构造器, 如果父类中没有无参构造器就会报告错误.

```java
class Person{
    private String name;
    private String sex;
    
    public Person(String name, String sex){
        this.name = name;
        this.sex = sex;
    }
    public String getName(){
        return  this.name;
    }

    public String getSex(){
        return this.sex;
    }
}


class Student extends Person{
    private  String school;
    
    public Student(String name,String sex,String school){
        super(name,sex);
        this.school = school;
    }
}
```

**在覆盖一个方法的时候, 子类方法不能低于超类方法的可见性**

## 多态

一个超类类型的对象可以接收子类的实例, 一个接口变量可以接收所有实现了该接口的类的实例

```java
public class Main {


    public static void main(String[] args) {
        Person[] personList = new Person[3];
        Student student = new Student("刚刚", "男", "沙河");
        personList[0] = student;
        student.print();
        // personList[0].print(); //错误写法personList[0]是Person类型变量, Person类没有print方法
        // Student student1 = personList[0]; // 超类的引用不能赋值给子类变量

    }
}

class Person {
    private String name;
    private String sex;

    public Person(String name, String sex) {
        this.name = name;
        this.sex = sex;
    }

    public String getName() {
        return this.name;
    }

    public String getSex() {
        return this.sex;
    }
}


class Student extends Person {
    private String school;

    public Student(String name, String sex, String school) {
        super(name, sex);
        this.school = school;
    }

    public String getSchool() {
        return this.school;
    }

    public void print() {
        System.out.printf("%s, %s, %s\n", this.getName(), this.getSex(), this.getSchool());
    }
}
```

## 修饰符

#### 访问控制修饰符

- default: (什么都不写)同一包内的可以访问.
- private: 私有的, 只能在本类中访问, 子类也不可以. **不能修饰类**
- public: 公开的. 所有类可见.
- protected: 受保护的, 对同一包内的类和所有子类可见**.不能修饰外部类**

在实际应用中, 要谨慎使用protected属性. 不能只为了子类去访问超类的属性和方法,可以使用访问器代替直接获取属性.

#### 非访问修饰符

static修饰符

- 静态变量, 静态方法. 可以直接通过类来访问. 
- 静态变量在类的空间中. 无论一个类实例化多少对象，它的静态变量只有一份拷贝。 静态变量也被称为类变量。**实例可以修改静态变量的值, 但是这是不对的.**
- 静态方法不能使用类的非静态变量。**因为可能是不通过实例来调用的, 不存在实例域**

final修饰符

- final修饰符表示最终的最后的, 一旦被赋值后不能被重新赋值.
- 被 final 修饰的实例变量必须显式指定初始值。final 修饰符通常和 static 修饰符一起使用来创建类常量。
- 被final修饰的方法可以被子类继承, 但是不能被子类修改. final就是为了防止子类重写该方法
- final修饰的类不能被继承.

> 在下面两种下使用静态方法
>
> - 一个方法不需要访问对象的状态, 其所需的参数都是通过显示参数提供
> - 一个方法只需要访问类的静态域
>
> 静态方法还有另一种常见的用途: **工厂方法**. 之所以利用工厂方法来完成实例化可能会有两个原因:
>
> - 无法命名构造器, 构造器的名字必须与类名相同
> - 使用构造器时无法改变构造对象的类型

**abstract修饰符**

- 抽象方法, 相当于一个约束, 约束子类必须实现方法. 抽象方法不能同时与static和final存在
- 包含抽象方法的类要被声明为抽象类, 抽象类不能用来实例化对象, 也不能被final修饰.
- 如果一个类继承了抽象类, 有没有实现抽象方法, name他也要被声明为抽象类
- **类即使不能存在抽象方法也可以被声明为抽象类**

## 类型检查, 类型转换

**instanceof**关键字用来检查类型, instanceof不仅能检查继承的实例关系, 还能检查接口实现的关系.

```java
public class Main {


    public static void main(String[] args) {

        Student student = new Student("刚刚", "男", "沙河");
        System.out.println(student instanceof Student); // true
        System.out.println(student instanceof Person); // true
    }
}
```

将一个子类的引用赋值给一个超类变量是允许的, 但是将一个超类的引用赋值给一个子类变量必须进行类型转换, **注意该超类的引用必须是子类实例化出来的**

```java
public class Main {
    public static void main(String[] args) {
        Student student = new Student("刚刚", "男", "沙河");
        Person a = student;
        Person b = new Person("文文", "男");
        // 转换开始
        Student student1 = (Student)a; // 转换成功
        Student student2 = (Student)b; // 转换失败:Person cannot be cast to Student
    }
}
```

## Object类

Object类是java中所有类的始祖, 在java中只有基本类型不是对象(数值,布尔值, 字符).

#### equals方法

Object方法中的equals方法检查一个对象是否等于另一个对象, 在Object中这个方法将会判断两个对象是否具有相同的引用. 如果我们要自定义判断方法, 可以重写这个方法

> == 对于对象来说比较的是内存地址, 对基本数据类型来说比较的是值###

**必须覆盖**

在子类中写的equals方法如果接受的参数与object类中接受的类型不同, 就不会覆盖Object类的equals方法, 而是重载了一个无关的方法

```java
public class Main {


    public static void main(String[] args) {
        Person[] personList = new Person[3];
        personList[0] = new Person("刚刚", "男");
        personList[1] = new Person("文文", "男");
        personList[2] = new Person("微微", "男");
        System.out.println(personList[0].equals(personList[1]));

    }
}

class Person {
    private String name;
    private String sex;

    public boolean equals(Person other){
        return this.name == other.name;
    }

    

    public Person(String name, String sex) {
        this.name = name;
        this.sex = sex;
    }

    public String getName() {
        return this.name;
    }

    public String getSex() {
        return this.sex;
    }
}
```

为此可以使用@Override来对要覆盖超类的方法做个标记, 如果标记的这个方式没有覆盖超类中的方法, 而是一个新方法,编译器就会给出错误报告.

```java
public class Main {


    public static void main(String[] args) {
        Person[] personList = new Person[3];
        personList[0] = new Person("刚刚", "男");
        personList[1] = new Person("文文", "男");
        personList[2] = new Person("微微", "男");
        System.out.println(personList[0].equals(personList[1]));

    }
}

class Person {
    private String name;
    private String sex;

    @Override
    public boolean equals(Person other) {
        return this.name == other.name;
    }


    public Person(String name, String sex) {
        this.name = name;
        this.sex = sex;
    }

    public String getName() {
        return this.name;
    }

    public String getSex() {
        return this.sex;
    }
}
```

#### hashCode方法

散列码是由对象导出的一个整型值

如果我们自定义这个方法的话可以借助java.util.Objects类中的静态方法hash和hashCode方法

hash方法可以接收多个对象, 返回这些对象的散列码组合得到的

hashCode方法接收一个对象, 返回这个对象的hashCode方法的返回值

#### toString方法

返回对象的字符串形式

#### getClass方法

返回对象的实际类型的类, 类的类型是Class, 如果需要变量接收的话, 需要一个Class类型的变量.

```java
import java.util.Objects;
public class Main {
    public static void main(String[] args) {
        Person person = new Person("刚刚", "男");
        System.out.println(person.getClass()); // class Person

    }
}

class Person {
    private String name;
    private String sex;

    public boolean equals(Person other) {
        return this.name == other.name;
    }

    public Person(String name, String sex) {
        this.name = name;
        this.sex = sex;
    }

    public String getName() {
        return this.name;
    }

    public String getSex() {
        return this.sex;
    }
}
```

## Class类 

**Class类是所有类的类型, Class的实例中存储着该类的信息.**

#### getName方法

返回类的名字

```java
public class Main {
    public static void main(String[] args) {
        Person person = new Person("刚刚", "男");
        System.out.println(person.getClass().getName()); // Person
    }
}
```

如果类在一个包里, 包的名字也作为类名的一部分

```java
import java.util.Random;

public class Main {
    public static void main(String[] args) {
        Random g = new Random();
        Class c = g.getClass();
        System.out.println(c.getName()); // java.util.Random
    }
}
```

#### getSuperclass方法

返回超类

```java
public class Main {
    public static void main(String[] args) {
        Person person = new Person("刚刚", "男");
        System.out.println(person.getClass().getSuperclass()); // class java.lang.Object
    }
}
```

#### forName静态方法

静态方法forName可以获得类名对应的Class对象

```java
public class Main {
    public static void main(String[] args) throws ClassNotFoundException {
        String s = "java.util.Random";
        Class c = Class.forName(s);
        System.out.println(c.getName()); // java.util.Random
    }
}
```

**无论何时使用这个方法都应该提供一个异常处理器**

#### 第三种获取Class的方法

- Object.getClass()
- Class.forName(String s)

第三种是class.class, **注意这里前面的class代表是一个类, 后面的class是一个属性**

```java
import java.util.Random;

public class Main {
    public static void main(String[] args) throws ClassNotFoundException {
        Class c = Random.class;
        System.out.println(c.getName()); // java.util.Random
    }
}
```

#### newInstance方法

newInstance方法可以动态的创建一个类的实例, 他会调用默认的构造器, 如果没有默认构造器便会抛出一个异常

> Object newInstance()

```java
public class Main {
    public static void main(String[] args) throws InstantiationException, IllegalAccessException,ClassNotFoundException {
        Class c = Class.forName("Person");
        Object s = c.newInstance(); // java.util.Random
        System.out.println(s.getClass());
    }
}
```

但是newInstance不能够调用带参数的构造器, 如果要想使用带参数的构造器, 就要使用Constructor类中的newInstance方法. Constructor类会在下面介绍

```java
public class Main {
    public static void main(String[] args) throws  InstantiationException,
            InvocationTargetException, NoSuchMethodException, IllegalArgumentException, IllegalAccessException, SecurityException, ClassNotFoundException {
        Class c = Class.forName("Person");
        Constructor C = c.getConstructor(new Class[]{String.class,String.class});
        Person P = (Person)C.newInstance(new Object[]{"刚刚","19"}); // 该方法返回的是一个Object对象, 需要进行类型转换
        System.out.println(P.getName()); // 刚刚
    }
}
```

## 反射

在java.lang.reflect包中有三个类 **Field, Method, Constructor**. 分别描述类的域,方法, 和构造器.这个三个类都有一个getName方法用来返回项目的名称, Field类有一个getType方法, 用来返回与所属类型的Class对象. Method和Constructor类有能够报告参数类型的方法, Method类还有一个报告返回值类型的方法getReturnType. 

另外这三个类还有一个getModifiers方法, 他返回一个整型值, 用来描述访问控制修饰符. 可以利用java.long.reflect包中的Modifier类的静态方法getModifiers返回整型数值,还有isPublic, isPrivate或者isFinal判断方法或构造器是否是public, private或者final. 还可以利用toString方法将修饰符打印出来

那么如何获得 **Field, Method, Constructor**呢? 

Class类中的getFields,getMethods, getConstructors方法将分别返回类提供的public域, 方法和构造器数组, 其中包括超类的公有成员.还提供了getField, getMethod, getConstructor方法, 可以返回指定名称的域(有的需要指定参数名)

getDeclareFields, getDeclareMethods,getDeclareConstructors方法将返回类中声明的全部域, 方法,构造器. 其中包含私有的和受保护的成员, 但是不包括超类成员.

#### 获取与操作对象域

查看对象域的关键方法是Field类中的get方法

```java
public class Main {
    public static void main(String[] args) throws NoSuchFieldException, IllegalArgumentException, IllegalAccessException, SecurityException, SecurityException,ClassNotFoundException {
        Class c = Class.forName("Person");
        Field F = c.getField("name");

        Person P = new Person("刚刚","19");
        System.out.println(F.get(P));

    }
}
```

但是这里这个name属性是私有的, get方法抛出了**IllegalAccessException**, 除非拥有访问权限, 否则Java的安全管理机制只允许查看任意对象有那些域, 而不允许读取他们的数据.

使用**Field, Method, Constructor**对象的setAccessible方法可以解除java安全管理器的控制, 可以完成覆盖访问控制.

> 还有一个isAccessible方法返回可访问标志的值

```java
public class Main {
    public static void main(String[] args) throws NoSuchFieldException, IllegalArgumentException, IllegalAccessException, SecurityException, SecurityException,ClassNotFoundException {
        Class c = Class.forName("Person");
        Field F = c.getDeclaredField("name");
        F.setAccessible(true);
        Person P = new Person("刚刚","19");
        System.out.println(F.get(P)); // 刚刚

    }
}
```

除此之外还有一个问题, get方法返回的是一个Object类型, 对于基础数据类型来说会将他打包到相应的包装器中, 也可以使用getInter等方法

**设置对象的域使用的是Field对象的set方法,**

```java
public class Main {
    public static void main(String[] args) throws NoSuchFieldException, IllegalArgumentException, IllegalAccessException, SecurityException, SecurityException,ClassNotFoundException {
        Class c = Class.forName("Person");
        Field F = c.getDeclaredField("name");
        F.setAccessible(true);
        Person P = new Person("刚刚","19");
        F.set(P,"小哥哥"); 
        System.out.println(P.getName()); // 小哥哥
    }
}
```

#### 方法执行

Methed类中有一个invoke方法, 他允许调用包装在当前Methed对象中的方法

> Object invoke(Object obj, Object... args)

第一个参数是隐式参数, 其余对象提供了显示参数,对于静态方法,第一个参数可以被忽略, 即可将他设置为null

```java
public class Main {
    public static void main(String[] args) throws
            InvocationTargetException, NoSuchMethodException, IllegalArgumentException, IllegalAccessException, SecurityException, ClassNotFoundException {
        Class c = Class.forName("Person");
        Person P = new Person("刚刚", "19");
        Method M = c.getMethod("getName");
        System.out.println(M.invoke(P)); // 刚刚
    }
}
```



## 对象包装器与自动装箱

所有的基本类型都有一个与之对应的类. **泛型的尖括号内是不允许存在基本类型的**, 所以这里要用到包装器类.

**基本类型转换为包装器类型叫做自动装箱, 包装器对象装换为基本类型是叫做自动拆箱**

一个包装器可以进行运算操作, 在这个过程中会自动转换为基本类型, 完成运算后再装箱为包装器类型

```java
public class Main {
    public static void main(String[] args) {
        Integer a = 4;
        a++;
        System.out.println(a); // 5
    }
}
```

 == 运算符也可以用于包装器类型, 只不过检测的是对象的地址是否相同

```java
public class Main {
    public static void main(String[] args) {
        Integer a = 1000;
        Integer b = 1000;
        System.out.println(a == b); // false
    }
}
```

在一个表达式中混合使用Integet和Double类型, Integet就会拆箱, 提成为double, 再装箱为Double

```java
public class Main {
    public static void main(String[] args) {
        Integer a = 10;
        Double b = 1000.0;
        System.out.println(true ? a : b); // 10.0
    }
}
```

## 枚举类

```java
public class Main {
    public static void main(String[] args) {
        Size a = Size.LARGE;
    }
}
enum Size { SMALL, MEDIUM, LARGE}
```

实际上这个声名定义的类型是一个类, 他刚好有四个实例.**所以在比较枚举值是可以直接使用==来判断**.

如果需要的话, 可以在枚举类型中添加一些构造器,方法和域

```java
public class Main {
    public static void main(String[] args) {
        Size a = Size.LARGE;
        System.out.println(a.getSrc()); // L
    }
}

enum Size {
    SMALL("S"), MEDIUM("M"), LARGE("L");
    private String src;
    private Size(String src){this.src=src;}

    public String getSrc() {
        return this.src;
    }
}
```

toString方法返回枚举常量名, 与之对用的是valueOf方法

```java
public class Main { 
    public static void main(String[] args) {
        Size a = Size.LARGE;
        System.out.println(a.toString()); // LARGE
        Size b = Size.valueOf("SMALL");
        System.out.println(b.toString()); // SMALL
        Size c = Size.valueOf(Size.class,"MEDIUM");
        System.out.println(c.toString()); // MEDIUM
    }
}

enum Size {
    SMALL("S"), MEDIUM("M"), LARGE("L");
    private String src;
    private Size(String src){this.src=src;}

    public String getSrc() {
        return this.src;
    }
}
```

每个枚举类型都有一个静态的values方法, 他将返回一个包含全部枚举值的数组

```java
public class Main {
    public static void main(String[] args) {
        Size[] a = Size.values();
        for (Size b : a){
            System.out.println(b.toString()); // SMALL,MEDIUM,LARGE
        }    
    }
}

enum Size {
    SMALL("S"), MEDIUM("M"), LARGE("L");
    private String src;
    private Size(String src){this.src=src;}

    public String getSrc() {
        return this.src;
    }
}
```

## 接口

一个类可以实现一个或多个接口,并在需要接口的地方,随时使用实现了相应接口的对象. 抽象类可以认为是对子类的约束, 而接口相当于对类的拓展, **实现了某个接口就必须实现该接口规定的方法**

```java
interface SayHello{
    void hello(); // 注意千万不要带{}, 带{}就相当于实现了一个没有方法体的方法
}

interface SayBye{
    void bye(); // 注意千万不要带{}, 带{}就相当于实现了一个没有方法体的方法
}

class Hello implements SayHello,SayBye{
    @Override
    public void hello() {
        System.out.println("哈哈哈");
    }
    
    public void bye(){
        System.out.println("拜拜");
    }
}

public class Main {
    public static void main(String[] args)  {
        Hello obj = new Hello();
        sayhaha(obj);
    }

    static void sayhaha(SayHello obj){
        obj.hello();
    }
}
```

接口中的所有方法自动的属于public, 所以在接口的方法声明中,不必提供关键字public.但是在实现接口时, 必须把方法声明为public. 否则会认为这个方法的访问属性时包可见性.

#### 接口中可以可以定义常量

接口中可以定义常量, 但是绝对不能含有实例域. 在java8之后, 接口中已经可以实现简单的方法了, 但是这些方法不能引用实例域.

```java
interface SayHello{
    double SPEED_LIMIT = 99;
    void hello(); // 注意千万不要带{}, 带{}就相当于实现了一个没有方法体的方法
}

class Hello implements SayHello{
    @Override
    public void hello() {
        System.out.println("哈哈哈");
        System.out.println(this.SPEED_LIMIT); //99.0
    }
}
```

java8中允许接口中有静态方法. 但是通常的做法是将静态方法放在伴随类中

另外, 还可以为接口方法提供一个默认方法, 必须用default修饰符标记

```java
interface SayHello{
    double SPEED_LIMIT = 99;
    default void hello(){
        System.out.println("你怎么不自己写这个方法");
    }; // 注意千万不要带{}, 带{}就相当于实现了一个没有方法体的方法
}


class Hello implements SayHello{

}

public class Main {
    public static void main(String[] args)  {
        Hello obj = new Hello();
        sayhaha(obj);
    }

    static void sayhaha(SayHello obj){
        obj.hello();
    }
}
```

这样一来, 在超类, 超接口, 和默认方法就有冲突了, 那么那个会先执行呢???

首先, 自己实现了这个方法, 毫无疑问, 调用的是自己的那个方法

```java
interface Say{
    default void hello(){
        System.out.println("Say接口的hello");
    };
}

interface SayHello extends Say{
    default void hello(){
        System.out.println("SayHello接口的hello");
    };
}

class PrentHello{
    public void hello(){
        System.out.println("PrentHello类的hello");
    }
}

class Hello extends PrentHello implements SayHello{
    public void hello(){
        System.out.println("Hello类的hello");
    }
}

public class Main {
    public static void main(String[] args)  {
        Hello obj = new Hello();
        obj.hello();
    }
```

我们去掉自己的实现, 这次调用的父类的方法, 我们后面去掉父类的方法

```java
interface Say{
    default void hello(){
        System.out.println("Say接口的hello");
    };
}

interface SayHello extends Say{
    default void hello(){
        System.out.println("SayHello接口的hello");
    };
}

class PrentHello{
    public void hello(){
        System.out.println("PrentHello类的hello");
    }
}

class Hello extends PrentHello implements SayHello{
    
}

public class Main {
    public static void main(String[] args)  {
        Hello obj = new Hello();
        obj.hello();
    }
```

去掉了本类,和父类的方法, 这次执行的是直接继承的接口的方法

```java
interface Say{
    default void hello(){
        System.out.println("Say接口的hello");
    };
}

interface SayHello extends Say{
    default void hello(){
        System.out.println("SayHello接口的hello");
    };
}

class PrentHello{

}

class Hello extends PrentHello implements SayHello{

}

public class Main {
    public static void main(String[] args)  {
        Hello obj = new Hello();
        obj.hello();
    }
    
}
```

接口冲突, 如果两个接口中都实现了一个方法(相同签名)

```java
interface Say{
    default void hello(){
        System.out.println("Say接口的hello");
    };
}

interface SayHello{
    default void hello(){
        System.out.println("SayHello接口的hello");
    };
}

class Hello implements SayHello,Say{

}
```

向上面这种java并不知道要选择那个接口, 所以在编译时会提示一个异常, 这时我们就需要在Hello类中在这两个冲突的方法中选择一个了

```java
class Hello implements SayHello,Say{
    public void hello(){
        SayHello.super.hello();
    };
}
```

**接口的继承** -- extends

## lambda表达式

在介绍lambda表达式之前要先介绍两个接口, 一个是**Comparable**接口, 另一个是**Comparator**接口.

实现了Comparable接口就可以通过Arrays接口进行排序

Comparable接口中的compareTo将两个对象进行比较, 如果这个对象小于other返回负值.相等返回0, 否则返回正值. 这样在sort排序时会得到一个**升序**的结果

```java
import java.util.Arrays;


class Employee implements Comparable {
    private String name;
    private double salary;

    @Override
    public int compareTo(Object otherObject) {
        Employee other = (Employee) otherObject;
        return (int) (this.salary - other.salary);
    }

    public Employee(String name,double salary){
        this.salary = salary;
        this.name = name;
    }
    @Override
    public String toString(){
        return this.name + this.salary;
    }
}

public class Main {
    public static void main(String[] args) {
        Employee[] employees  = new Employee[3];
        Employee e1 = new Employee("刚刚",1);
        Employee e2 = new Employee("微微",500);
        Employee e3 = new Employee("雯雯",1000);
        employees[0] = e3;
        employees[1] = e1;
        employees[2] = e2;
        Arrays.sort(employees);
        for (Employee a : employees){
            System.out.println(a);
        }
    }
}
```

**Comparator**是一个比较器, 对于一些内置的类型, 没有实现Comparable接口, 不能进行比较. 这是就可以通过比较器来排序了, 比如要来一个按字符串长度进行排序

```java
class ByName implements Comparator{
    @Override
    public int compare(Object first,Object other){
        String f = ((Employee)first).getName();
        String o = ((Employee)other).getName();
        return f.length() - o.length();
    }
}

class Employee {
    private String name;
    private double salary;

    public Employee(String name,double salary){
        this.salary = salary;
        this.name = name;
    }
    @Override
    public String toString(){
        return this.name + this.salary;
    }

    public String getName(){
        return this.name;
    }
}

public class Main {
    public static void main(String[] args) {
        Employee[] employees  = new Employee[3];
        Employee e1 = new Employee("飞翔的企鹅",1);
        Employee e2 = new Employee("周树人",500);
        Employee e3 = new Employee("雷雷",1000);
        employees[0] = e3;
        employees[1] = e1;
        employees[2] = e2;
        Comparator byname = new ByName();
        Arrays.sort(employees,byname);
        for (Employee a : employees){
            System.out.println(a);
        }
    }
}
```

这时,我们再来说lambda表达式

```java
// 形式
() -> {} 
```

在()中放置的是参数, {}中是包含return的方法体. 即使没有参数也要提供空的()

无需指定lambda表达式的返回类型, 返回类型会由上下文推导得出.

#### 函数式接口

对于**只有一个抽象方法**的接口, 需要这种**接口对象**时, 就可以提供一个lambda表达式, 这种接口称之为函数式接口, 比如**Comparator**, 我们来修改前面的那个代码

```java
Arrays.sort(employees,(Employee a,Employee b) ->{return a.getName().length()-b.getName().length();});
```

**在java中lambda表达式所能做的也只是转换为函数式接口**

 #### 方法引用

有时, 可能已经有**现成的方法**可以完成你想要传递到其他某个代码的动作.

```java
class Employee {
    private String name;
    private double salary;

    public int comparaTo(Employee other){
        return (int)(this.salary - other.salary);
    }

    public Employee(String name,double salary){
        this.salary = salary;
        this.name = name;
    }
    @Override
    public String toString(){
        return this.name + this.salary;
    }

    public String getName(){
        return this.name;
    }
}

public class Main {
    public static void main(String[] args) {
        Employee[] employees  = new Employee[3];
        Employee e1 = new Employee("飞翔的企鹅",1);
        Employee e2 = new Employee("周树人",500);
        Employee e3 = new Employee("雷雷",1000);
        employees[0] = e3;
        employees[1] = e1;
        employees[2] = e2;
        Arrays.sort(employees,Employee::comparaTo); // 注意这里
        for (Employee a : employees){
            System.out.println(a);
        }
    }

}
```

从上面可以看出要用::分割方法名与对象或类名.主要有三种情况

- obj::instanceMethod
- class:: staticMethod
- class:: instanceMethod

前两种相当于提供了方法参数的lambda表达式, 比如Math::pow等价于(x,y)->Math.pow(x,y)

第三种情况, 就是上面演示的那个, 第一个参数会变成方法的调用者Employee::comparaTo等价于(x,y)->x.comparaTo(y)

**方法引用中可以使用this和super, super会去调用超类的方法**

#### 构造器引用

构造器引用和方法引用很类似, 只不过方法名为new

例如int[]::new是一个构造器引用,他有一个参数:即数组的长度.这相当于x->new int[x]

>如果设计你自己的接口,其中只有一个抽象方法, 可以用@FunctionalInterface注解来标记这个接口.这样做有两个优点. 如果你无意增加了另一个非抽象方法, 编译器会产生一个错误信息

#### 其他

lambda表达式可以捕获外围作用域中变量的值, 但是这里有一个限制, 只能引用值不会改变的量, 不能在lambda内部改变,也不能在外部改变. lambda引用的外部变量必须是最终变量

在一个lambda表达式中使用this关键字, 是指创建这个lambda表达式的方法的this

## 内部类

**内部类**是定义在另一个类中的类. 他有以下几种特性

- 内部类的方法可以访问该类定义所在的作用域中的数据, 包括私有数据
- 内部类可以对同一个包中的其他类隐藏起来
- 当想要定义一个回调函数且不想编写大量代码时, 使用匿名内部类比较便捷 

#### 内部类引用外部作用域的例子

```java
class Outer {
    private int salary;
    private String name;

    public Outer(String name, int salary){
        this.name = name;
        this.salary = salary;
    }

    public void start() {
        Inner a = new Inner();
        a.conlse();
    }

    public class Inner {
        public void conlse() {
            System.out.println(name);
        }
    }
}


public class Main {
    public static void main(String[] args) {
        Outer o = new Outer("刚刚",666);
        o.start();
    }

}
```

其实上面console方法里就相当于`System.out.println(Outer.this.name);`, `OuterClass.this`就是外部类的实例

#### 构造内部类

在外部类内部实例化时可以直接用new, 也可以使用this.new

```java
public void start() {
    Inner a = this.new Inner();
    a.conlse();
}
```

this不是必须的不过可以通过显示的命名将外围类引用设置为其他的对象.

```java
Outer o = new Outer("刚刚",666);
Outer.Inner  i = o.new Inner();
i.conlse();
```

首先内部类是共有的, 其次该类的类型是Outer.Inner

> 内部类中声明的所有**静态域**都必须是final, 我们希望一个静态域只有一个实例, 不过对于每个外部对象, 会分别有一个单独的内部类实例,  如果这个域不是final, 他可能就不是唯一的.
>
> 内部类不能有static方法

#### 局部内部类

当一个内部类只在创建这个类型对象的时候使用了一次, 可以在这个方法中定义局部类,

```java
class Outer {
    private int salary;
    private String name;

    public Outer(String name, int salary){
        this.name = name;
        this.salary = salary;
    }

    public void start() {
        class Inner {
            public void conlse() {
                System.out.println(Outer.this.name);
            }
        }

        Inner a = new Inner();
        a.conlse();
    }
}


public class Main {
    public static void main(String[] args) {
        Outer o = new Outer("刚刚",666);
        o.start();
    }

}
```

局部类不能用public或private访问修饰符进行修饰, 他的作用域被限定在声明这个类的局部块中.  局部类有一个优势, 他对外部世界可以完全的隐藏起来, 除了start方法, 没有任何方法知道Inner类的存在

局部类还有一个优点, 他除了能够访问包含他的外部类还可以访问局部变量,  不过那些局部变量必须声明为final

```java
class Outer {
    private int salary;
    private String name;

    public Outer(String name, int salary){
        this.name = name;
        this.salary = salary;
    }

    public void start(String str) {

        class Inner {
            public void conlse() {
                System.out.println(str); // 看这里
            }
        }


        Inner a = new Inner();
        a.conlse();
    }
}


public class Main {
    public static void main(String[] args) {
        Outer o = new Outer("刚刚",666);
        o.start("哈哈哈");
    }

}
```

#### 匿名内部类

将局部内部类在深入一步. 假如之创建这个类的一个对象, 就不必命名了.这种类称为匿名内部类

```java
Inner a = new Inner() {
    public void conlse() {
        System.out.println(str);
    }
};
```

Inner可以是一个接口, 内部类要实现这个接口. 也可以是一个类, 内部类来拓展他

匿名内部类没有名字,所以不能够有构造方法, 这里的参数将传给父类

#### 静态内部类

有时候使用内部类只是为了把一个类隐藏在另一个类的内部, 并不需要内部类引用外围类的对象. 这是可以把内部类声明为static

## 异常

在java中异常对象都是派生于Throwable类的一个实例. 在下一层分解为两个分支Error和Exception

我们只需要关注Exception层次结构,  

在Exception又分为RuntimeException和IOException, RuntimeException和Error称为非受查异常.

一个方法必须声明所有可能抛出的受查异常, 而非受查异常要么不可控制, 要么就应该避免发生

#### 声明受查异常

方法应该在首部声明其所有可能抛出的异常. 前面说到的**throws**就是这个意思. 他可以在首部反映出这个方法可能抛出哪类受查异常.

一个方法调用另外一个方法, 如果另一个方法声明了异常,而在这个方法中没有处理, 那么在这个方法中也应该声明这个异常, 以下的这些异常都是其他方法中的

```java
public class Main {
    public static void main(String[] args) throws
            InvocationTargetException, NoSuchMethodException, IllegalArgumentException, IllegalAccessException, SecurityException, ClassNotFoundException {
        Class c = Class.forName("Person");
        Person P = new Person("刚刚", "19");
        Method M = c.getMethod("getName");
        System.out.println(M.invoke(P)); // 刚刚
    }
}
```

#### 抛出异常

以IOException为例, throw关键字用来抛出异常对象

```java
class Outer {
    private int salary;
    private String name;

    public Outer(String name, int salary){
        this.name = name;
        this.salary = salary;
    }

    public void start(String str) throws IOException {
        throw new IOException();
    }


}


public class Main {
    public static void main(String[] args) throws Exception {
        Outer o = new Outer("刚刚",666);
        o.start("哈哈哈");
    }

}
```

o.start("哈哈哈")这里代码便停止运行了, 这里有几个需要注意的点

- 抛出异常的方法应该声明受查异常
- 如果在main方法里没有处理这个异常, 也应该声明这个异常, 不顾上面用了Exception
- IOException类有一个含有一个字符串类型参数的构造器, 用来更加细致的描述异常出现的情况

```java
throw new IOException("我自己抛个异常咋啦");
// Exception in thread "main" java.io.IOException: 我自己抛个异常咋啦
```

#### 创建异常类

#### 捕获异常





