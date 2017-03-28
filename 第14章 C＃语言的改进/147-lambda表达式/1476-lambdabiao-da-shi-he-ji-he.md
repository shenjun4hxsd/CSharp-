##Lambda表达式和集合

&emsp;&emsp;学习了`Func<>`泛型委托后，就可以理解System.Linq名称空间为数组类型提供的一些扩展方法了（在编码的不同地方，可在弹出IntelliSense时看到它们）。例如，有一个扩展方法Aggregate()定义了3个重载版本，如下所示：

```csharp
    public static TSource Aggregate<TSource>(
        this IEnumerable<TSource> source,
        Func<TSource, TSource, TSource> func);
        
    public static TAccumulate Aggregate<Tsource, TAccumulate>(
        this IEnumerable<TSource> source,
        TAccumulate seed,
        Func<TAccumulate, TSource, TAccumulate> func);
        
    public static TResult Aggregate<TSource, TAccumulate, TResult>(
        this IEnumerable<TSource> source,
        TAccumulate seed,
        Func<TAccumulate, TSource, TAccumulate> func,
        Func<TAccumulate, TResult> resultSelector);
```

&emsp;&emsp;与前面的扩展方法一样，这段代码初看上去非常深奥，但如果分解它们，就很容易理解其工作过程。这个函数的IntelliSense告诉用户它会执行如下工作：

```csharp
    Applies an accumulator function over a sequence.
```

&emsp;&emsp;这表示要把一个累加器函数（可以采用Lambda表达式的形式提供）应用于集合中从开始到结束的每对元素上。这个累加器函数必须有两个参数和一个返回值。其中一个参数是当前元素，另一个参数是一个种子值，集合中的第一个值，或者前一次计算的结果。

&emsp;&emsp;在3个重载版本中，最简单的版本只有一个泛型类型，这可从实例参数的类型推理出来。例如，在下面的代码中，泛型类型是int（累加器函数现在是空的）：

```csharp
    int[] myIntArray = { 2, 6, 3};
    int result = myIntArray.Aggregate(...);
```

&emsp;&emsp;这等价于：

```csharp
    int[] myIntArray = { 2, 6, 3 };
    int result = myIntArray.Aggregate<int>(...);
```

&emsp;&emsp;这里需要的Lambda表达式可以从扩展方法中推断出来。在这段代码中，类型TSource是int，所以必须为委托`Func<int, int , int>`提供一个Lambda表达式。例如，可以使用前面的Lambda表达式：

```csharp
    int[] myIntArray = { 2, 6, 3 };
    int result = myIntArray.Aggregate((paramA, paramB) => paramA + paramB);
```

&emsp;&emsp;这个调用会使Lambda表达式调用两次，一次使用的参数是paramA = 2，paramB = 6，另一次使用的参数是paramA = 8（第一次计算的结果），paramB = 3。最后赋予变量result的结果是int值11，即数组中所有元素的总和。

&emsp;&emsp;扩展方法Aggregate()的其他两个重载版本是类似的，但可以执行略微复杂的计算，如下面的简短示例所示。

```csharp
    static void Main(string[] args)
    {
        string[] curries = { "pathis", "jalfrezi", "korma" };
        Console.WriteLine(curries.Aggregate((a, b) => {a + " " + b}));
        Console.WriteLine(curries.Aggregate<string, int>(
            0,
            (a, b) => a + b.Length));        //Func<int, string, int>
        Console.WriteLine(curries.Aggregate<string, string, string>(
            "Some curries:",
            (a, b) => a + " " + b,
            a => a));
        Console.WriteLine(curries.Aggregate<string, string, int>(
            "Some curries:",
            (a, b) => a + " " + b,
            a => a.Length));
        Console.ReadKey();
    }
```

&emsp;&emsp;运行结果：

```csharp
    pathia jalfrezi korma
    19
    Some curries: pathia jalfrezi korma
    35
```

>&emsp;&emsp;**示例的说明**

>&emsp;&emsp;这个示例把包含3个元素的字符串数组作为源数据，试验了扩展方法Aggregate()的每个重载版本。

>&emsp;&emsp;首先执行一个简单的串联操作：

>```csharp
    Console.WriteLine(curries.Aggregate(
        (a, b) => a + " " + b));
```

>&emsp;&emsp;第一对元素用简单的语法串联成一个字符串。这远非连接字符串的最佳方式，理想情况是使用string.Concat()或string.Format()以优化性能，但这里使用它提供了一种非常简单的方式来说明发生了什么。第一个串联操作后，结果和数组中的第3个元素传送回Lambda表达式，其方式与前面要计算总和的int值相同。结果是串联整个数组，并用空格分隔各个项。使用string.Join()方法能够更方便地实现同样的效果，但是本示例中演示的其他重载版本提供了string.Join()所没有的额外功能。

>&emsp;&emsp;接着使用Aggregate()函数的第二个重载版本，它有两个泛型类型的参数TSource和TAccumulate。在整个示例中，Lambda表达式的形式必须是`Func<TAccumulate, TSource, TAccumulate>`。另外，必须指定TAccumulate类型的种子值，这个种子值和第一个数组元素在Lambda表达式的第一次调用中使用。后续调用从前面的调用中把累加器的结果提取到表达式中。代码如下：

>```csharp
    Console.WriteLine(curries.Aggregate<string, int>(
        0,
        (a, b) => a + b.Length));
```


>&emsp;&emsp;累加器（以及返回值）的类型是int。累加器的值最初设置为种子值0，在对Lambda表达式的每次调用中，都把该值累加到数组元素的长度上。最后的结果是数组中所有元素的总长度。

>&emsp;&emsp;之后使用Aggregate()函数的最后一个重载版本，它带有3个泛型类型的参数，与前一个重载版本的唯一区别是，其返回类型可以与数组元素和累加值的类型都不同。首先，这个重载版本把字符串元素与种子字符串联在一起：

>```csharp
    Console.WriteLine(curries.Aggregate<string, string, string>(
        "Some curries:",
        (a, b) => a + " " + b,
        a => a));
```

>&emsp;&emsp;即使累加值只是复制到结果中（如本例所示），也必须指定这个方法的最后一个参数resultSelector。这个参数是一个`Func<TAccumulate, TResult>`类型的Lambda表达式。

>&emsp;&emsp;在最后一段代码中，再次使用了Aggregate()的这个版本，但这次使用int类型的返回值。其中，给resultSelector提供一个Lambda表达式，返回累加字符串的长度：

>```csharp
    Console.WriteLine(curries.Aggregate<string, string, int>(
        "Some curries:",
        (a, b) => a + " " + b,
        a => a.Length));
```

>&emsp;&emsp;这个示例没有什么花哨的地方，但演示了如何使用更复杂的扩展方法，其中涉及泛型类型的参数、集合和看似复杂的语法。本书后面还将予以讨论。


🔚
