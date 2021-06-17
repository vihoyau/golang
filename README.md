![golang-developer-roadmap-zh-CN](https://user-images.githubusercontent.com/30063579/122187495-a9571a80-cec1-11eb-91f4-37f94366cb9b.png)
# 关于golang知识汇总（外部）

**说明** ：对于一个后端来说，无疑让自己能有一席之地，要有扎实的计算机基础和数据结构之外，更重要的是从事的后端开发语言，因为选择比能力有时候更加重要。例如小编目前是做nodejs开发的，使用的是动态开发语言，可谓是全栈，目前这种岗位比较少人参与，多数人都选择做java开发去了，只要您有几年开发经验，找个17-20k问题不大。小编现在是想学习golang作为以后的发展方向，因为golang缺少工程师，只要您进入这个领域，至少20k起步。为了获取更多的薪酬，小编利用平时多余的时间，进行学习golang语言。

## 1.数组与切片

### 1.1 声明与初始化

**数组定义**：需要定义长度，但是也可以使用空接口（使用时需要做类型判断），数组长度最大为2GB
<br/>
` 声明的类型格式 var identifer [len] type`
<br/>
**值类型**：var arr1=new ([5]int)   ｜  var arr2 [5]int ｜  arr1 是*[5]int  arr2是[5]int    所以在使用arr1的之前，需要做内存拷贝。arr11  := *arr1. arr11[2]=10  ，这种赋值不影响原先的arr1数组
<br/>
**数组常量**：var aa=[5]int{18,11} ｜ var bb=[5]string{3:"apple",4:"banana"} ｜ var cc=[...]string{3:"apple",4:"banana"}  从技术上cc这种命名方式，相当于切片
<br/>
**将数组传递给函数**：直接传递数组会消耗相当多的内存，两种方法可以解决：1，传递数组的指针。2，传递数组的切片
<br/>

### 1.2 切片

**定义**：是对数组的连续引用。切片提供了相关数组的动态窗口。切片是可索引是，并且可以通过len()获取长度。切片是一个长度可变的数组。多个切片如果代表同一个数组，它们可以共享数据。可以共享存储。
<br/>
`切片的初始化格式是：var slice1 []type = arr1[start:end]`
<br/>
**用make创建一个切片**：slice := make([]type ,len)   另外：slice := make ([]type,len,cap)
<br/>
**new 和 make 的区别**：都是分配内存，行为不同，适用类型就不同。new(T)适用于数组和结构体。make(T)适用于切片、map和channel。换言之，new函数分配内存，make函数初始化。
<br/>
![0JP6mwnf6J](https://user-images.githubusercontent.com/30063579/122150421-4a2ce200-ce90-11eb-9e71-3741efc0a196.png)
<br/>
**多维切片**：简单来说就是切片的切片，内层的切片需要单独分配（通过make）
<br/>
**bytes包**：和字符串包十分相似，而且还包含了十分有用的buffer类型。
<br/>

```
import "bytes"
	type Buffer struct {
	...
}
这是一个bytes类型的buffer，可以read和write功能。
```

```
1,buffer可以这样定义 var buffer bytes.buffer
2,或者使用new 获得一个指针
3,var r *bytes.Buffer =new （bytes.Buffer）
4,或者通过函数 ： func NewBuffer(buf []byte)  *Buffer
```

**通过buffer串联字符串**：我们创建一个buffer，通过buffer.WriteString(s)将字符串s追加在后面。最后通过buffer.String()转换成字符串。

```
var buffer bytes.Buffer
for {
    if s, ok := getNextString(); ok { //method getNextString() not shown here
        buffer.WriteString(s)
    } else {
        break
    }
}
fmt.Print(buffer.String(), "\n")
```

> 这种方法比+=更节省cpu、内存，尤其是字符串数目比较多的时候。

### 1.3 For-range结构

**定义**：这种构造方法可以应用于切片和数组。

```
for ix, value := range slice1 {
    ...
}
```

> ix是索引，value是该切片索引下的值。value只是一种拷贝，不能用来修改slice1.

```
多维下的切片：

for row := range screen {
    for column := range screen[row] {
        screen[row][column] = 1
    }
}
```

### 1.4 切片重组

```
slice1 := make([]type, start_length, capacity)
```

> start_length:切片初始长度。capacity 相关数组长度。
> 改变切片长度过程叫做重组：`slice1 = slice1[0:end]` `sl = sl[0:len(sl)+1]`

### 1.5 切片的复制与追加

**应用场景**：如果想增加切片的容量，并且把原来的切片复制过来，我们一般使用append。

```
package main 
import 'fmt'
func main(){
        sl_from :=[]int{1,2,3}	
	sl_to :=make([]int,10)
	n := copy(sl_to,sl_from)        
        fmt.print(sl_to)
	fmt.print(n)
	sl3 :=[]int{1,2,3}	
        sl3=append(sl3,4,5,6)
	fmt.print(sl3)
}
```

1. 如果sl3不足以追加元素，append会为sl3分配新的切片以确保存储过程
2. append函数总是返回成功，除非系统内存耗尽
3. 如果想在将切片y追加到切片x后面，只要将第二个参数拓展成一个列表即可。append(x,y,z...)

> func copy(dst, src []T) int copy 方法将类型为 T 的切片从源地址 src 拷贝到目标地址 dst，覆盖 dst 的相关元素，并且返回拷贝的元素个数。源地址和目标地址可能会有重叠。拷贝个数是 src 和 dst 的长度最小值。如果 src 是字符串那么元素类型就是 byte。如果你还想继续使用 src，在拷贝结束后执行 src = dst

### 1.6 切片和垃圾回收

**内存填满**：切片指向底层数组的，当底层数组没有被指向就会释放内存，但是如果一直被指向，就会占用多余的内存了。往往一个函数，直接返回一个底层数组，却得不到释放，就会一直占用内存。<br/>
**垃圾回收**：为了解决上述问题，我们采取创建新的一个切片，把需要返回的切片复制到新的切片里面，即可返回。


## 2.函数

### 2.1 介绍

**go语言有三种类型的函数**：

1. 普通的带有名字的函数
2. 匿名函数或lambda函数
3. 方法<br/>
   **按值传递和引用传递**
   <br/>

* [ ] 按值传递和引用传递：切片、字典、接口、通道 这样的引用默认是引用传递，引用传递是可以修改传递的参数的，也就是将参数的地址传递给函数。
* [ ] 命名返回值:尽量使用命名返回值：会使代码更清晰、更简短，同时更加容易读懂
  
  ```
  命名返回值作为结果形参（result parameters）被初始化为相应类型的零值，当需要返回的时候，我们只需要一条简单的不带参数的 return 语句。需要注意的是，即使只有一个命名返回值，也需要使用 () 括起来
  ```

### 2.2 改变外部变量

1. 定义：传递指针参数不仅可以节省内存（因为没有复制变量里面的值），而且赋予了函数直接修改外部变量的能力。

```
package main

import (
    "fmt"
)

// this function changes reply:
func Multiply(a, b int, reply *int) {
    *reply = a * b
}

func main() {
    n := 0
    reply := &n
    Multiply(10, 5, reply)
    fmt.Println("Multiply:", *reply) // Multiply: 50
}
```

2.  当需要改变函数内一个占用内存比较大的变量时，性能优势就更加明显了。但是，传递一个指针很容易引发不确定性。

### 2.3 defer和追踪

**定义**：关键字defer允许我们推迟到函数返回之前执行某个语句或函数。类似于java中的finally

**defer实现代码追踪**：

1. 记录进入和离开某个函数打印相关信息。
2. 记录函数参数和返回值。

### 2.4 内置函数

**定义**：go语言有一些不需要导入就可以使用的内置函数。它们有时参与不同类型的操作。

| 名称 | 说明|
| --- | --- | 
|  close| 用于管道通信  | 
|  lencap  | len用于返回某个函数的长度和数量（字符串、数组、切片、map和管道）；cap是容量的意思，用于返回某个类型的最大容量（只能用于切片和map） |
|  new,make| new,make均是用于分配内存：new用于值类型和用户定义的类型，如自定义结构。map用于内置引用类型（切片、map、管道）。它们的用法就像函数，但是将类型作为参数：new(type)、make(type). new(T)分配类型T的零值并返回其地址，也就是指向类型的指针，它可以被用于基本类型：v :=new(int) 。 make(T)返回类型T的初始化之后的值，因此它比new进行更多的工作。**new是一个函数，不要忘记（）** | 
|  copy,append| 复制和连接切片| 
|  panic,recover| 两者均用于错误处理机制 | 
|  print,println| 底层打印函数，建议使用fmt包  |
|  complex,real,image| 用于创建和操作复数 |

### 2.5 递归函数

**定义**：调用函数本身称之为递归。<br/>
**应用场景**：快速排序<br/>
**问题及解决方法**：容易栈溢出，原因是大量递归调用导致程序栈内存分配耗尽。在go中，我们使用channel和goroutine来实现。

### 2.6 将函数作为参数

**定义**：函数作为其他函数的参数来传递，在其他函数体内调用执行，一般称为回调。

### 2.7 闭包

**定义**：匿名函数被赋值给一个变量，称之为闭包。它们被允许调用定义在其他环境下的变量。闭包使得某个函数捕抓到一些外部状态（函数创建时状态）。另一种表达方式：一个闭包继承了函数声明的作用域。闭包经常被用作包装函数。

### 2.8 通过内存缓存来提升性能

**定义**：在大量计算时，避免重复计算。通过利用内存的缓存和利用相同的计算结果称之为内存缓存。

## 3.MAP

### 3.1 介绍

**定义**：key-value 的元素对无序集合，称之为关联数组或者字典。是一种快速寻找值的理想结构，在java中称之为hash，hashtable等

### 3.2 声明、初始化、make

**声明**：在声明的时候，不需要知道map的长度，是可以动态增长的。

```
var map1 map[keytype]valuetype
```

> 切片和结构体不能作为key，但是指针和接口类型可以。
> value可以是任意类型的，但是当我们使用value之前，需要做类型判断
> map读取速度比线性查找快得多，但是比数组和切片用索引读取慢100倍。索引您很在乎性能的话，还是用切片解决问题
> **不要使用new，永远用make来构造map**：因为new相当于分配一个空指针，相当于声明了未初始化并且获得它的地址，当被调用时，就会报错。
> <br/>

**map也可以用函数作为自己的值，这样可以用来做分支结构**

```
mf := map[int] func()  int{
        1: func()  int { return 10 },
        2: func()  int { return 20 },
        5: func()  int { return 50 },
}
```

**用切片作为map的值**：当需要管理多个进程的子进程时候，可以优雅地这样解决

```
mp1 := make(map[int][]int)
mp2 := make(map[int]*[]int)
```

### 3.3 测试键值对是否存在及删除元素

```
`val1, isPresent = map1[key1]`
```

* [ ] 上述表明，如果key1存在，val1是key1对应的值，并且isPresent为true。反之val1为空值，isPresent为false
* [ ] 我们也可以不关心value是什么  ```_, ok := map1[key1] // 如果key1存在则ok == true，否则ok为false```
* [ ] 从map1中删除key1 ```delete(map1,key1) ```

### 3.4 for-range的配套使用

```
for key, value := range map1 {
    ...
}
```

### 3.5 map类型的切片使用

**方案**：应该通过索引使用map元素，第二种方法只是对map的值进行拷贝。

```
package main
import "fmt"

func main() {
    // Version A:
    items := make([]map[int]int, 5)
    for i:= range items {
        items[i] = make(map[int]int, 1)
        items[i][1] = 2
    }
    fmt.Printf("Version A: Value of items: %v\n", items)

    // Version B: NOT GOOD!
    items2 := make([]map[int]int, 5)
    for _, item := range items2 {
        item = make(map[int]int, 1) // item is only a copy of the slice element.
        item[1] = 2 // This 'item' will be lost on the next iteration.
    }
    fmt.Printf("Version B: Value of items: %v\n", items2)
}
```

### 3.6 map的排序

**定义**：默认是无序的，如果需要对map进行排序，需要将key或者value拷贝到切片中，然后用切片进行排序

### 3.7 将map的键值对调

**前提条件**：value是唯一的，且value的值类型可以作为key。其实很简单，通过for循环，进行深度拷贝到另外一个map中即可。

```
package main
import (
    "fmt"
)

var (
    barVal = map[string]int{"alpha": 34, "bravo": 56, "charlie": 23,
                            "delta": 87, "echo": 56, "foxtrot": 12,
                            "golf": 34, "hotel": 16, "indio": 87,
                            "juliet": 65, "kili": 43, "lima": 98}
)

func main() {
    invMap := make(map[int]string, len(barVal))
    for k, v := range barVal {
        invMap[v] = k
    }
    fmt.Println("inverted:")
    for k, v := range invMap {
        fmt.Printf("Key: %v, Value: %v / ", k, v)
    }
}
```

## 4. 包（package）

### 4.1 标准库概述

**定义**：向os，fmt 这样具有常用功能的内置包在go语言中有150个以上，它们被称为标准库。大部分内置于go本身。

### 4.2 锁和sync包

**定义**：当线程访问一个变量时，我们为它上锁，直到完成操作并解锁后，其他线程才能访问它。
**应用**：map中不出现锁的机制来实现这种资源竞争的效果，出于性能考虑。所以map是非线程安全的。当并行访问map，map将会出错。
**解决方法**：在go中，我们对于这种锁的机制，是用sync包中的Mutex来实现的。意味着，线程有序地对同一变量访问。

```
import  "sync"

type Info struct {
    mu sync.Mutex
    // ... other fields, e.g.: Str string
}
```

```
共享缓存器：
type SyncedBuffer struct {
    lock    sync.Mutex
    buffer  bytes.Buffer
}
```

> sync.Mutex是一个互斥锁，它的作用是守护在临界区入口，保障在同一时间只能有一个线程进入临界区。

**`RWMutex`**：同时允许多个线程对变量的读操作，但是只能允许一个写操作。

* [ ] 但是以上操作会导致程序变慢，我们需要重新思考goroutines和channels来解决问题。这是go语言提前用来解决并发问题的方法。

### 4.3 精密计算和big包

**应用场景**：如果我们对数据精度十分严格，float是满足不了的，因为它在内存中只是表示近似。

**定义**：对于整数高精度，我们使用big.Int类型。缺点是：当数据足够大，开销大，要比内置类型慢得多。

**解决方法**：为了解决上述问题，go提供了：Add() 和 Mul() 方法，所以没有必要创建Big.int 来存放中间结果，采用内存链式存储即可。

```
// big.go
package main

import (
    "fmt"
    "math"
    "math/big"
)

func main() {
    // Here are some calculations with bigInts:
    im := big.NewInt(math.MaxInt64)
    in := im
    io := big.NewInt(1956)
    ip := big.NewInt(1)
    ip.Mul(im, in).Add(ip, im).Div(ip, io)
    fmt.Printf("Big Int: %v\n", ip)
    // Here are some calculations with bigInts:
    rm := big.NewRat(math.MaxInt64, 1956)
    rn := big.NewRat(-1956, math.MaxInt64)
    ro := big.NewRat(19, 56)
    rp := big.NewRat(1111, 2222)
    rq := big.NewRat(1, 1)
    rq.Mul(rm, rn).Add(rq, ro).Mul(rq, rp)
    fmt.Printf("Big Rat: %v\n", rq)
}

/* Output:
Big Int: 43492122561469640008497075573153004
Big Rat: -37/112
*/
```

### 4.4 自定义包和可见性

**导入外部安装包**：```go install``` 将包安装在 `$GOROOT/src/` 目录下

**包的初始化**：导入的包，在包自身初始化前被初始化，而一个包在程序执行中只能被初始化一次。

### 4.5 安装自定义包

`go install -a`

## 5. 结构与方法（struct&method）

### 5.1 章节说明

**定义**：GO通过类型别名和结构体的形式支持用户自定义类型。在java中，叫做ADT，抽象数据类型。

### 5.2 结构体定义

`type T struct {a, b int}` 是一个合法的定义方式。

**定义**：
**递归结构体**

```
type Tree strcut {
	le      *Tree
	data    float64
	ri      *Tree
}
```

### 5.3 使用工厂方法创建结构体实例

```
type File struct {
    fd      int     // 文件描述符
    name    string  // 文件名
}

func NewFile(fd int, name string) *File {
    if fd < 0 {
        return nil
    }

    return &File{fd, name}
}
f := NewFile(10, "./test.txt")
```

**应用场景**：我们常常使用上述的工厂方法来实现构造函数。

* [ ] 如果File是结构体类型，那么new(File)和&File{}是等价的。相对于java，File f = new File() ，简单。
* [ ] 如果我们想知道一个结构体T实例占用多少内存，则 size :=unsafe.SizeOf(T{})

**如何强制使用工厂方法**：禁止使用new函数，从而使得结构体是私有的。

```
type matrix struct {
    ...
}

func NewMatrix(params) *matrix {
    m := new(matrix) // 初始化 m
    return m
}
```

**在其他包下使用工厂方法**

```
package main
import "matrix"
...
wrong := new(matrix.matrix)     // 编译失败（matrix 是私有的）
right := matrix.NewMatrix(...)  // 实例化 matrix 的唯一方式
```

**map&struct vs new()&make()**

```
package main

type Foo map[string]string
type Bar struct {
    thingOne string
    thingTwo int
}

func main() {
    // OK
    y := new(Bar)
    (*y).thingOne = "hello"
    (*y).thingTwo = 1

    // NOT OK
    z := make(Bar) // 编译错误：cannot make type Bar
    (*z).thingOne = "hello"
    (*z).thingTwo = 1

    // OK
    x := make(Foo)
    x["x"] = "goodbye"
    x["y"] = "world"

    // NOT OK
    u := new(Foo)
    (*u)["x"] = "goodbye" // 运行时错误!! panic: assignment to entry in nil map
    (*u)["y"] = "world"
}
```

### 5.3 匿名字段和内嵌结构体

**定义**：结构体中，可以包含内嵌结构体。
**区别**：在go中，相比较继承，组合更受欢迎。

* [ ] 同样地，结构体也是一种数据类型，所以它可以内作为内嵌字段来使用。

### 5.4 命名冲突

**定义**：很可能通过继承匿名结构体，导致字段命名冲突，必须由程序员自身修改。

### 5.5 方法

**定义**：在go中，结构体就像是类的一个简化形式。GO方法是作用在接受者（receiver）上的函数。接受者是某种类型的变量。因此，方法是一种特殊类型的函数。

* [ ] 接受者不能是一个接口类型。因为方法是一种实现，而接口只是一种抽象定义。
* [ ] 接收者不能是一个指针类型，但是它可以是任何类型的指针。
* [ ] 一个类型加上它的方法，等价于面向对象中的类。
* [ ] 类型T的所有方法的集合，叫做T的方法集
* [ ] **定义方法** `func (recv receiver_type) methodName(parameter_list) (return_value_list) { ... }`

### 5.6 函数和方法的区别

**定义**：函数将变量变为参数，方法在变量上被调用。

**指针或值作为接收者**：`func (b *B) change() { b.thing = 1 }`
**方法和未导出字段**：和java中的getter、setter一样，对于setter使用set作为前缀，get只使用方法名。

> 另外：防止并发set，为了解决当前问题，请使用sync包

### 5.7 内嵌类型的方法和包

**如何在类型中嵌入功能**：聚合、内嵌
**多重继承**：一般在java中不被实现的，因为在编译器中引入额外的复杂度。在go中，通过在类型中嵌入有必要的父类型，可以很简单地实现多重继承。

```
package main

import (
    "fmt"
)

type Camera struct{}

func (c *Camera) TakeAPicture() string {
    return "Click"
}

type Phone struct{}

func (p *Phone) Call() string {
    return "Ring Ring"
}

type CameraPhone struct {
    Camera
    Phone
}

func main() {
    cp := new(CameraPhone)
    fmt.Println("Our new CameraPhone exhibits multiple behaviors...")
    fmt.Println("It exhibits behavior of a Camera: ", cp.TakeAPicture())
    fmt.Println("It works like a Phone too: ", cp.Call())
}
```




