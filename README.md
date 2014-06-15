swift-diff (持续学习中...)
==========

记录在学习Swift时，容易跟其他语言混淆的语法特点


##可以用下标读取key和value
```
var arr = [
	"k1":"v1", 
	"k2":"v2", 
	"k3":"v3"
]
	
//常规
for (k,v) in arr {
	println("key:\(k), value:\(v)")
}

//特殊，貌似可以把v直接看成数组
for v in arr {
   	println("key:\(v.0), value:\(v.1)")
}
```
	
##switch 不能没有default，删了会报错

```
var v = "test"

switch v {
    case "test":
        println("test")
    default:
        println("default")
}
```

##匿名函数

```
func fn(callback:(i2:Int, s2:String)->String) -> String {
    return callback(i2:123, s2:"hello")
}

fn({
    (i:Int, s:String) -> String in
    return "int:\(i), s:\(s)"
})
```

- 想当然把fn参数匿名函数写成：

```
func fn(callback:func(i2:Int, s2:String)->String) -> String {
    //传递参数必须注明参数名，这个不太习惯
    //除非上面(i2:Int, s2:String)改为(Int, String)
    //下面调用就不用写参数名了
    //return callback(123, "hello")
    return callback(i2:123, s2:"hello")
}
```
注意callback后面多了func，原来是不需要的。

- 想当然把匿名函数参数写成

```
fn(func(i:Int, s:String)->String{
	return "int:\(i), s:\(s)"
})
```

正确应该是 { `参数列表和返回类型` in `作用域` }，也不需要func


##返回多个值

```
func fn() -> (String, String, String) {
    return ("t", "t2", "t3")
}

//继续想当然会如此，却是不对的
//var v1, v2, v3 = fn()
//应该是下面方式，用数组接收
var v = fn()
println(v.0)
println(v.1)
println(v.2)

```

## class的属性必须得赋值

不管是直接赋值还是通过init赋值，都必须的赋值，不然直接报错

```
class Person {
    var name: String
    var age: Int = 27
    
    init(n:String) {
        self.name = n
    }
}
```

## 子类调父类属性前必须先调父类init
```
class Person {
    var name: String
    var age: Int = 27
    
    init(n:String) {
        self.name = n
    }
}

class Student: Person{
    var score: Int
    init(age:Int, n:String) {
        
        self.score = 100

        //调用父类属性前，必须先调super.init进行初始化
        //例如这里把self.age = age写到super.init(n:n)前面又得报错
        //但对于子类本身的属性，就没有这个限制了，所以score可以写哪里都可以
        super.init(n:n)
        self.age = age
        self.name = n
        
    }
}

Student(age:28, n:"elson")
```

## class方法支持多态
```
class Person {
    var name: String
    init(n:String) {
        self.name = n
    }
    
    func Say() -> String {
        return "My name is:\(self.name)"
    }
}

class Student: Person{
    func Say(str:String)->String {
        return str
    }
    
    //覆写父类方法，不写override会报错
    override
    func Say()->String {
        return super.Say() + " !"
    }
}

Student(n:"elson").Say("How about you?")
Student(n:"elson").Say()
```