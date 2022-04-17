## Golang学习之error篇

以一个打开系统文件为例，在error文件夹下有个`hello.txt`

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	file, err := os.Open("C:\\Users\\42012\\Desktop\\代码\\error\\hello.txt")
	if err != nil {
		fmt.Println("open file error")
	}
	fmt.Println(file, err)
	fmt.Printf("Error的类型是：%T", err)
}

```

正常运行的结果是

```
&{0xc000072780} <nil>
Error的类型是：<nil>
```

当我们把`hello.txt`改成`hello1.txt`后，结果是

```
open file error
<nil> open C:\Users\42012\Desktop\代码\error\hello1.txt: The system cannot find the file specified.
Error的类型是：*os.PathError
```

控制台打印出了`PathError`路径错误，我们就可以根据error的类型和提示，快速定位代码中有问题的代码片段，并且加以修正。

### 自定义error

虽然golang有很多自带的error类型和提示，但是并不能完全覆盖所有的错误。

例如定义一个除法函数，当我们输入的被除数出现0时，整个程序就崩溃无法执行

```go
package main

import (
	"fmt"
)

func DivisionForInt(dividend, divsor int) int {
	return dividend / divsor
}

func main() {
	fmt.Println(DivisionForInt(10, 5))
	fmt.Println(DivisionForInt(10, 0))
	fmt.Println(DivisionForInt(0, 100))

}

```

这时候运行程序得到的结果是

```
2
panic: runtime error: integer divide by zero

goroutine 1 [running]:
main.DivisionForInt(...)
```

可以看出在执行完第一个除法后，第二个除法的被除数为0，导致程序直接出现了panic崩溃，第三个除法也不会继续执行了。那么为了让程序更加健壮，我们需要对这个错误类型进行兼容处理，可以通过自定义error来解决。

```go
package main

import (
	"errors"
	"fmt"
)

var errDivisionByZero = errors.New("被除数不能为0")

func DivisionForInt(dividend, divsor int) (int, error) {
	if divsor == 0 {
		return 0, errDivisionByZero
	}
	return dividend / divsor, nil
}

func main() {
	fmt.Println(DivisionForInt(10, 5))
	fmt.Println(DivisionForInt(10, 0))
	fmt.Println(DivisionForInt(0, 100))

}

```

通过new得到一个自定义的error，在程序中对除数先进行判断，如果是0的话返回这个error给主程序

```
2 <nil>
0 被除数不能为0
0 <nil>
```

程序就不会发生崩溃，后面的除法也可以正常的运行。

另一种实现方式

```go
package main

import (
	"fmt"
)

type errDiv struct {
	errStr string
}

func (e *errDiv) Error() string {
	return e.errStr
}

func DivisionForInt2(dividend, divsor int) (int, error) {
	if divsor == 0 {
		return 0, &errDiv{
			errStr: "除数不能为0哦",
		}
	}
	return dividend / divsor, nil
}

func main() {
	fmt.Println(DivisionForInt2(10, 5))
	fmt.Println(DivisionForInt2(10, 0))
	fmt.Println(DivisionForInt2(0, 100))
}

```

### panic和recover