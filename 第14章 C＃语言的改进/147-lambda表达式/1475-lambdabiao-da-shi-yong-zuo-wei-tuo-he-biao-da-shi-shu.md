##Lambda表达式用作委托和表达式树

&emsp;&emsp;前面提到了Lambda表达式和匿名方法的一些区别：Lambda表达式比较灵活，例如，隐式类型化的参数。现在，应注意另一个重要区别，但在学习本书后面的LINQ之前，这个区别的意义并不是很明显。

&emsp;&emsp;可以采用两种方式来解释Lambda表达式。第一，如本章所述，Lambda表达式是一个委托。即可以把Lambda表达式赋予一个委托类型的变量，如前面的示例所示。

&emsp;&emsp;一般可以把拥有至多8个参数的Lambda表达式表示为如下泛型类型，它们都在System名称名空间中定义：

```
    • Action，表示的Lambda表达式不带参数，返回类型是void
    • Action<>，表示的Lambda表达式有至多8个参数，返回类型是void
    • Func<>，表示的Lambda表达式有至多8个参数，返回类型不是void
```
&emsp;&emsp;`Action<>`最多有8个泛型类型的参数，分别用于Lambda表达式的8个参数，`Func<>`最多有9个泛型类型的参数，分别用于Lambda表达式的8个参数和返回类型。在`Func<>`中，返回类型始终在列表的最后。

&emsp;&emsp;例如，下面的Lambda表达式：

```csharp
    (int paramA, int paramB) => paramA + paramB
```

&emsp;&emsp;可以表示为`Func<int, int, int>`类型的委托，因为它有两个int参数，返回类型是int。注意，在很多情况中，可以使用这些泛型委托类型，而不必定义自己的泛型委托类型。例如，可以使用它们代替前面的示例中定义的TwoIntegerOperationDelegate委托。

&emsp;&emsp;第二，可以把Lambda表达式解释为表达式树。表达式树是Lambda表达式的抽象表示，因此不能直接执行。可使用表达式树以编程方式分析Lambda表达式，执行操作，以响应Lambda表达式。

&emsp;&emsp;显然这是一个复杂主题，但表达式树对本书后面介绍LINQ功能至关重要。下面列举一个具体的例子。LINQ框架包含一个泛型类`Expression<>`，可用于封装Lambda表达式。使用这个类的一种方式是提取用C#编写的Lambda表达式，把它转换为相应的SQL脚本，以便在数据库中直接执行。

&emsp;&emsp;目前并不需要了解太多内容，在本书后面遇到这个功能时，能更好地理解其过程，因为现在我们已经理解了C#语言提供的重要概念。


🔚
