##把Lambda表达式用于匿名方法

&emsp;&emsp;下面看一下Lambda表达式。Lambda表达式是简化匿名方法的语法的一种方式。实际上，Lambda表达式还有其他用处，但为了简单起见，本节只介绍Lambda表达式的这个方面。使用Lambda表达式可以重写上一节最后的一段代码，如下所示：

```csharp
    myTimer.Elapsed += (source, e) => Console.WriteLine(
        "Event handler called after {0} milliseconds.",
        (source as Timer).Interval);
```

&emsp;&emsp;这段代码初看上去有点让人摸不着头脑（除非很熟悉所谓的函数化编程语言，如Lisp或Haskell）。但如果仔细观察，就会看出或至少推断出代码是如何工作的，它与所替代的匿名方法有什么关系。

Lambda表达式由3个部分组成：

```
    • 放在括号中的参数列表（未类型化）
    • => 运算符
    • C#语句
```

&emsp;&emsp;使用本章前面“匿名类型”一节中介绍的逻辑，从上下文推断出参数的类型。`=>`运算符只是把参数列表与表达式体分开。在调用Lambda表达式时，执行表达式体。

&emsp;&emsp;编辑器会提取这个Lambda表达式，创建一个匿名方法，其工作方式与上一节中的匿名方法相同。其实，它会被编译为相同或相似的CIL代码。

&emsp;&emsp;为了说明Lambda表达式中的内容，下面列举一个例子。

```csharp
    namespace Ch14Ex06
    {
        delegate int TwoIntegerOperationDelegate(int paramA, int paramB);
        
        class Program
        {
            static void PerformOperations(TwoIntegerOperationDelegate del)
            {
                for(int paramAVal = 1; paramAVal <= 5; paramAVal++)
                {
                    for(int paramBVal = 1; paramBVal <= 5; paramBVal++)
                    {
                        int delegateCallResult = del(paramAVal, paramBVal);
                        Console.Write("f({0},{1})={2}",
                            paramAVal, paramBVal, delegateCallResult);
                        if(paramBVal != 5)
                        {
                            Console.Write(", ");
                        }
                    }
                    Console.WriteLine();
                }
            }
            
            static void Main(string[] args)
            {
                Console.WriteLine("f(a, b) = a + b:");
                PerformOperations((paramA, paramB) => paramA + paramB);
                Console.WriteLine();
                Console.WriteLine("f(a, b) = a * b:");
                PerformOperations((paramA, paramB) => paramA * paramB);
                Console.WriteLine();
                Console.WriteLine("f(a, b) = (a - b) % b:");
                PerformOperations((paramA, paramB) => (paramA - paramB) % paramB);
                Console.ReadKey();
            }
        }
    }
```

>&emsp;&emsp;**示例的说明**

>&emsp;&emsp;这个示例使用Lambda表达式生成函数，用来在两个输入参数上执行指定的处理，并返回结果。接着这些函数操作25对值，把结果输出到控制台上。

>&emsp;&emsp;首先定义一个委托类型TwoIntegerOperationDelegate，表示一个方法，该方法有两个int参数，返回一个int结果：

>```csharp
    delegate int TwoIntegerOperationDelegate(int paramA, int paramB);
```

>&emsp;&emsp;在以后定义Lambda表达式时使用这个委托类型。这些Lambda表达式编译为方法，其返回类型和参数匹配这个委托类型，如稍后所述。

&emsp;&emsp;接着添加方法PerformOperations()，它带有一个TwoIntegerOperationDelegate类型的参数：

>```csharp
    static void PerformOperations(TwoIntegerOperationDelegate del)
    {
```

&emsp;&emsp;这个方法的含义是，可以给它传送一个委托实例（或者匿名方法，或者Lambda表达式，因为这些结构都会编译为委托实例），该方法会用一组值调用委托实例所表示的方法：

>```csharp
                for(int paramAVal = 1; paramAVal <= 5; paramAVal++)
                {
                    for(int paramBVal = 1; paramBVal <= 5; paramBVal++)
                    {
                        int delegateCallResult = del(paramAVal, paramBVal);
```

>&emsp;&emsp;接着把参数和结果输出到控制台上：

>```csharp
                        Console.Write("f({0},{1})={2}",
                            paramAVal, paramBVal, delegateCallResult);
                        if(paramBVal != 5)
                        {
                            Console.Write(", ");
                        }
                    }
                    Console.WriteLine();
                }
            }
```

>&emsp;&emsp;在Main()方法中，创建了3个Lambda表达式，使用它们依次调用PerformOperations()。第一个调用如下所示：

>```csharp
                Console.WriteLine("f(a, b) = a + b:");
                PerformOperations((paramA, paramB) => paramA + paramB);
```

>&emsp;&emsp;这里使用的Lambda表达式如下：

>```csharp
    (paramA, paramB) => paramA + paramB
```

>&emsp;&emsp;这个Lambda表达式分为3部分：

>```
（1）参数定义部分。这里有两个参数paramA和paramB。这些参数都是未类型化的，因此编译器可以根据上下文推断出它们的类型。在这个例子中，编译器可以确定，PerformOperations()方法调用需要一个TwoIntegerOperationDelegate类型的委托。这个委托类型有两个int参数，所以根据推断，paramA和paramB都是int类型的变量。
（2）=> 运算符。它把Lambda表达式的参数与表达式体分开。
（3）表达式体。它指定了一个简单操作：把paramA和paramB加起来。注意，不需要指定这是返回值。编译器知道，要创建可以使用TwoIntegerOperationDelegate的方法，这个方法就必须有int返回类型。根据指定的操作，paramA + paramB等于一个int类型的值，且没有提供额外的信息，所以编译器推断，这个表达式的结果就是方法的返回类型。
```


>&emsp;&emsp;接着，就可以把使用这个Lambda表达式的代码扩展到下面使用匿名方法的代码中：

>```csharp
    Console.WriteLine("f(a, b) = a + b:");
    PerformOperations(delegate(int paramA, int paramB)
        {
            return paramA + paramB;
        });
```

>&emsp;&emsp;其余代码以相同方式使用两个不同的Lambda表达式来执行操作：

>```csharp
    Console.WriteLine();
    Console.WriteLine("f(a, b) = a * b:");
    PerformOperations((paramA, paramB) => paramA * paramB);
    Console.WriteLine();
    Console.WriteLine("f(a, b) = (a - b) % b:");
    PerformOperations((paramA, paramB) => (paramA - paramB) % paramB);
    Console.ReadKey();
```

>&emsp;&emsp;最后一个Lambda表达式涉及较多计算，但并不比其他Lambda表达式更复杂。Lambda表达式的语法允许执行更复杂的操作，如稍后所述。


🔚```
