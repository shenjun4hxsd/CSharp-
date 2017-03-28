##处理事件

&emsp;&emsp;如前所述，要处理事件，需要提供一个事件处理方法来订阅事件，该方法的返回类型和参数应该匹配事件指定的委托。下面的示例使用一个简单的计时器对象引发事件，调用一个处理方法。

```csharp
    using System;
    using Sytem.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Timers;
    
    namespace Ch13Ex01
    {
        class Program
        {
            static int counter = 0;
            
            static string displayStrng = 
                "This string will appear one letter at a time.";
                
            static void Main(string[] args)
            {
                Timer myTimer = new Timer(100);
                myTimer.Elapsed += new ElapsedEventHandler(WriteChar);
                myTimer.Start();
                System.Threading.Thread.Sleep(200);
                Console.ReadKey();
            }
            
            static void WriteChar(object source, ElapsedEventArgs e)
            {
                Console.Write(displayString[counter++ % displayString.Length]);
            }
        }
    }
```

>&emsp;&emsp;**示例的说明**

&emsp;&emsp;用于引发事件的对象是System.Timers.Timer类的一个实例。使用一个时间段（以毫秒为单位）来初始化该对象。当使用Start()方法启动Timer对象时，就引发一系列事件，根据指定的时间段来引发事件。Main()用100毫秒初始化Timer对象，所以在启动该对象后，1秒钟内将引发10次事件：

```csharp
    static void Main(string[] args)
    {
        Timer myTimer = new Timer(100);
```

&emsp;&emsp;Timer对象有一个Elapsed事件，这个事件要求事件处理程序必须匹配System.Timers.ElapsedEventHandler委托类型的返回类型和参数，该委托是.NET Framework中定义的标准委托之一，指定了如下所示的返回类型和参数：

```csharp
    void <MethodName>(object source, ElapsedVenetArgs e);
```

&emsp;&emsp;Timer对象的第一个参数是它本身的引用，第二个参数则是ElapsedEventArgs对象的一个实例。现在可以不考虑这些参数，后面将论述它们。

&emsp;&emsp;在代码中，有一个匹配该返回类型和参数的方法：

```csharp
    static void WriteChar(object source, ElapsedEventArgs e)
    {
        Console.Write(displayString[counter++ % displayString.Length]);
    }
```

&emsp;&emsp;这个方法使用Program的两个静态字段counter和displayString来显示一个字符。每次调用该方法时，显示的字符都不相同。

&emsp;&emsp;下一个任务是把这个处理程序与事件关联起来---即订阅它。为此，可以使用+=运算符，给事件添加一个处理程序，其形式是使用事件处理方法初始化的一个新委托实例：

```csharp
    static void Main(string[] args)
    {
        Timer myTimer = new Timer(100);
        myTimer.Elapsed += new ElapsedEventHandler(WriteChar);
    }
```

&emsp;&emsp;这个命令（使用有点古怪的语法，专用于委托）在列表中添加一个处理程序，当引发Elapsed事件时，就会调用该处理程序。可给这个列表添加任意多个处理程序，只要它们满足指定的条件即可。当引发事件时，会依次调用每个处理程序。

&emsp;&emsp;Main()剩余的任务是启动计时器：

```csharp
    myTimer.Start();
```

&emsp;&emsp;我们不想在处理完任何事件前终止应用程序，所以要让Main()函数一直执行。最简单的方式是请求用户输入，因为这个命令要在用户按下任意键后，才会停止处理。

```csharp
    Console.ReadKey();
```

&emsp;&emsp;这里，Main()中的处理会停止，但Timer对象中的处理将继续。当该对象引发事件时，就调用WriteChar()方法，同时该方法运行Console.ReadLine()语句。使用System.Threading.Thread.Sleep(200)语句是为了让计时器有机会把消息发送给控制台应用程序。

&emsp;&emsp;注意，可使用上一章介绍的方法组概念来稍简化添加事件处理程序的语法：

```csharp
    myTimer.Elapsed += WriteChar;
```

&emsp;&emsp;最终结果是完全相同的，但不必显式指定委托类型，编译器会根据使用事件的上下文来指定它。但是，许多程序员不喜欢这个语法，因为它降低了可读性---不再能一眼看出使用了什么委托类型。如果喜欢，就可以使用这个语法。但为了清晰起见，本章使用的所有委托都显式指定。


🔚