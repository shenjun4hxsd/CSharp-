##动态类型

&emsp;&emsp;C# 4引入了dynamic关键字，以用于定义变量。例如：

```csharp
    dynamic myDynamicVar;
```

&emsp;&emsp;与前面介绍的var关键字不同，的确存在动态类型，所以在声明myDynamicVar时，不必初始化它的值。

>&emsp;&emsp;**动态类型不同寻常之处是，它仅在编译期间存在，在运行期间它会被System.Object类型替代。这是较细微的实现细节，但必须记住这一点，因为这可能澄清了后面的一些讨论。**

&emsp;&emsp;一旦有了动态变量，就可以继续访问其成员（这里没有列出实际获取变量值的代码）。

```csharp
    myDynamicVar.DoSomething("With this!");
```

&emsp;&emsp;无论myDynamicVar实际包含什么值，这行代码都会编译。但是，如果所请求的成员不存在，在执行这行代码时会生成一个RuntimeBinderException类型的异常。

&emsp;&emsp;实际上，像这样的代码提供了一个应在运行期间应用的“处方”。检查myDynamicVar的值，找到带一个字符串参数的DoSomething()方法，并在需要时调用它。

&emsp;&emsp;这最好举例说明。

>&emsp;&emsp;**警告：下面的示例仅用于演示！一般情况下，应仅在动态类型是唯一的选项时使用它们，例如处理非.NET对象。**

```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using Microsoft.CSharp.RuntimeBinder;
    
    namespace Ch14Ex03
    {
        class MyClass1
        {
            public int Add(int var1, int var2)
            {
                return var1 + var2;
            }
        }
        
        class MyClass2
        {
        }
        
        class Program
        {
            static int callCount = 0;
            
            static dynamic GetValue()
            {
                if(callCount++ == 0)
                {
                    return new MyClass1();
                }
                return new MyClass2();
            }
            
            static void Main(string[] args)
            {
                try
                {
                    dynamic firstResult = GetValue();
                    dynamic secondResult = GetValue();
                    Console.WriteLine("firstResult is: {0}",
                        firstResult.ToString());
                    Console.WriteLine("secondResult is: {0}",
                        secondResult.ToString());
                    Console.WriteLine("firstResult call: {0}",
                        firstResult.Add(2, 3));
                    Console.WriteLine("secondResult call: {0}",
                        secondResult.Add(2, 3));
                }
                catch(RuntimeBinderException ex)
                {
                    Console.WriteLine(ex.Message);
                }
                Console.ReadKey();
            }
        }
    }
```

>&emsp;&emsp;**示例的说明**

>&emsp;&emsp;这个示例使用一个方法返回两个类型的对象中的一个，以获取动态值，再尝试使用所获取的对象。代码在编译时没有遇到任何问题，但尝试访问不存在的方法时，抛出（并处理）了一个异常。

>&emsp;&emsp;首先，为包含RuntimeBindingException异常的名称空间添加一条using语句：

>```csharp
    using Microsoft.CSharp.RuntimeBinder;
```

>&emsp;&emsp;接着定义两个类MyClass1和MyClass2，其中MyClass1包含Add()方法，而MyClass2不包含成员：

>```csharp
        class MyClass1
        {
            public int Add(int var1, int var2)
            {
                return var1 + var2;
            }
        }
        
>         class MyClass2
        {
        }
```

>&emsp;&emsp;还要给Program类添加一个字段（callCount）和一个方法（GetValue()），以获取其中一个类的实例：

>```csharp
            static int callCount = 0;
            
>             static dynamic GetValue()
            {
                if(callCount++ == 0)
                {
                    return new MyClass1();
                }
                return new MyClass2();
            }
```

>&emsp;&emsp;使用一个简单的调用计数器，这样，第一次调用这个方法时，返回MyClass1的一个实例，之后返回MyClass2的实例。注意dynamic关键字可以用作方法的返回类型。

>&emsp;&emsp;接着，Main()中的代码调用GetValue()方法两次，再尝试在返回的两个值上依次调用GetString()和Add()。这些代码放在try...catch块中，以捕获可能发生的RuntimeBinderException类型的异常。

>```csharp
            static void Main(string[] args)
            {
                try
                {
                    dynamic firstResult = GetValue();
                    dynamic secondResult = GetValue();
                    Console.WriteLine("firstResult is: {0}",
                        firstResult.ToString());
                    Console.WriteLine("secondResult is: {0}",
                        secondResult.ToString());
                    Console.WriteLine("firstResult call: {0}",
                        firstResult.Add(2, 3));
                    Console.WriteLine("secondResult call: {0}",
                        secondResult.Add(2, 3));
                }
                catch(RuntimeBinderException ex)
                {
                    Console.WriteLine(ex.Message);
                }
                Console.ReadKey();
            }
```



>&emsp;&emsp;可以肯定，调用secondResult.Add()时会抛出一个异常，因为在MyClass2上不存在这个方法。异常消息说明了这一点。

>&emsp;&emsp;dynamic关键字也可以用于其他需要类型名的地方，例如方法参数。Add()方法可以重写为：

>```csharp
    public int Add(dynamic var1, dynamic var2)
    {
        return var1 + var2;
    }
```

>&emsp;&emsp;这对结果没有任何影响。在这个例子中，传送给var1和var2的值在运行期间检查，以确定加号`+`是否存在一个兼容的运算符定义。如果传送了两个int值，就存在这样的运算符。如果使用了不兼容的值，就抛出RuntimeBinderException异常。例如，如果尝试：

>```csharp
    Console.WriteLine("firstResult call: {0}", firstResult.Add("2", 3));
```

>&emsp;&emsp;异常消息就如下所示：

>```csharp
    Cannot implicitly convert type 'string' to 'int'
```

>&emsp;&emsp;从这里获得的教训是动态类型是非常强大的，但有一个警告。如果用强类型代替动态类型，就完全可以避免抛出这些异常。对于大多数自己编写的C#代码，应避免使用dynamic关键字。但是，如果需要使用它，就应使用它，并会喜欢上它---而不像过去那样可怜的程序员那样没有这个强大的工具可用。


🔚
