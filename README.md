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







