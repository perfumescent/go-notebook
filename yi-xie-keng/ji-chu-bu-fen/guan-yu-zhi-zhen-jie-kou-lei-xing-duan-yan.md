# 关于指针、接口类型断言

### 问题

```go
func show(a interface{}) {
	a = a.(string)
	b := a.(string)
	fmt.Printf("a:%T  &a:%T  b:%T\n", a, &a, b)
}
func main() {
	a := "Go"
	show(a)
}

>>>
a:string  &a:*interface {}  b:string

```

`a`在断言之后到底有没有变？按理说`a.(string)`不应该返回一个string类型的变量吗？那为什么`&a`却变成了接口指针？

### 解答

```go
func show(a interface{}) {
	fmt.Printf("a:%T  &a:%T\n", a, &a)
	fmt.Println(a==a.(string), a, &a)
	a = a.(string)
	b := a.(string)
	fmt.Println(a==b, &a, &b,a,b)
	fmt.Printf("a:%T  &a:%T  b:%T\n", a, &a, b)
}

>>>
a:string  &a:*interface {}
true Go 0xc000088230
true 0xc000088230 0xc000088250 Go Go
a:string  &a:*interface {}  b:string
```

在断言赋值之前，printf打印a就会是string类型，println打印a会显示Go，同时<mark style="color:purple;">`a==a.(string)`</mark>为真，这也就说明了断言前后a并未发生改变，都是string类型的Go。（为什么会觉得奇怪，可能是与空接口有关，当一个具体类型传给空接口，这个值会变成接口类型吗？）

接下来来验证部分类型的变量：

```go
func show(a interface{}) {
	fmt.Printf("a:%T  &a:%T\n", a, &a)
	fmt.Println(a, &a)
}

type diy struct {
	int
	string
}

func main() {
	a := "Go"
	show(a)
	b := 123
	show(b)
	c := []int{1, 2, 3}
	show(c)
	d := diy{123, "Go"}
	show(d)
}

>>>
a:string  &a:*interface {}
Go 0xc000088230
a:int  &a:*interface {}
123 0xc000088240
a:[]int  &a:*interface {}
[1 2 3] 0xc000088250
a:main.diy  &a:*interface {}
{123 Go} 0xc000088260
```

可见，具体类型变量传给空接口后，仍被识别为本来类型，但其地址指向的类型却都为<mark style="color:blue;">空接口指针</mark>。同时需要注意，在show函数内的变量是副本，其地址与main中变量地址是不同的。

那为何其地址指向的类型却都为空接口指针呢?

### 补充阅读

{% embed url="https://www.jianshu.com/p/ae04262d16b5" %}
