##Lambda表达式的参数

&emsp;&emsp;在前面的代码中，Lambda表达式使用类型推理功能来确定所传送的参数类型。实际上这不是必需的，也可以定义类型。例如，可使用下面的Lambda表达式：

```csharp
    (int paramA, int paramB) => paramA + paramB;
```

&emsp;&emsp;其优点是代码更便于理解，缺点是不够简明灵活。在前面委托类型的示例中，可以通过隐式类型化的Lambda表达式来使用其他数字类型，例如，long变量。

&emsp;&emsp;**注意，不能在同一个Lambda表达式中同时使用隐式和显式的参数类型。**下面的Lambda表达式就不会编译，因为paramA是显式类型化的，而paramB是隐式类型化的：

```csharp
    (int paramA, paramB) => paramA + paramB   ❌
```

&emsp;&emsp;Lambda表达式的参数列表始终包含一个用逗号分隔的列表，其中的参数要么都是显式类型化的，要么都是隐式类型化的。**如果只有一个隐式类型化的参数，就可以省略括号；否则就需要在参数列表上加上括号**，如前面所示。例如，下面的Lambda表达式只有一个参数，且是隐式类型化的：

```csharp
    param1 => param1 * param1
```

&emsp;&emsp;**还可以定义没有参数的Lambda表达式，这使用空括号来表示：**

```csharp
    () => Math.PI
```

&emsp;&emsp;当委托不需要参数，但需要返回一个double值时，就可以使用这个Lambda表达式。


🔚