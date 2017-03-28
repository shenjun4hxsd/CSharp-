##Lambda表达式的语句体

&emsp;&emsp;在前面的所有代码中，Lambda表达式的语句体都只使用了一个表达式。我们还说明了这个表达式如何解释为Lambda表达式的返回值，例如，如何给返回类型为int的委托使用表达式 `paramA + paramB` 作为Lambda表达式的语句体（假定paramA和paramB隐式或显式类型化为int值，如示例代码所示）。

&emsp;&emsp;前面的一个示例说明了对于语句体中使用的代码而言，返回类型为void的委托的要求并不高：

```csharp
    myTimer.Elapsed += (source, e) => Console.WriteLine(
        "Event handler called after {0} millisenconds.",
        (source as Timer).Interval);
```

&emsp;&emsp;上面的语句不返回任何值，所以它只是执行，其返回值不在任何地方使用。

&emsp;&emsp;可以将Lambda表达式看成匿名方法语法的扩展，所以还可以在Lambda表达式的语句体中包含多个语句。为此，只需把一个代码块放在花括号中，类似于C#中提供多行代码的其他情况：

```csharp
    (paramA, param2) => 
    {
        // Multiple statements ahoy!
    }
```

&emsp;&emsp;如果使用Lambda表达式和返回类型不是void的委托类型，就必须用return关键字返回一个值，这与其他方法一样：

```csharp
    (param1, param2) =>
    {
        // Multiple statements ahoy!
        return returnValue;
    }
```

&emsp;&emsp;例如，可将前面示例中的如下代码：

```csharp
    PerformOperations((paramA, paramB) => paramA + paramB);
```

&emsp;&emsp;改写为：

```csharp
    PerformOperations(delegate(int paramA, int paramB)
        {
            return paramA + paramB;
        });
```

&emsp;&emsp;另外，也可以把代码改写为：

```csharp
    PerformOperations((paramA, paramB) =>
        {
            return paramA + paramB;
        });
```

&emsp;&emsp;这更像是原来的代码，因为它保持了paramA和paramB参数的隐式类型化。

&emsp;&emsp;大多数情况下，在使用单一的表达式时，Lambda表达式最有用，也最简洁。说实话，如果需要多个语句，则定义一个单独的非匿名方法来替代Lambda表达式较好，这也会使代码更便于重用。


🔚