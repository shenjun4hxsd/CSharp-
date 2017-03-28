##复习匿名方法

&emsp;&emsp;第13章学习了匿名方法，这是提供的内联（inline）方法，否则就需要使用委托类型的变量。给事件添加处理程序时，过程如下：

```
（1）定义一个事件处理方法，其返回类型和参数匹配要订阅的事件需要的委托的返回类型和参数。
（2）声明一个委托类型的变量，用于事件。
（3）把委托变量初始化为委托类型的实例，该实例指向事件处理方法。
（4）把委托变量添加到事件的订阅者列表中。
```

&emsp;&emsp;实际上，这个过程会比上述简单一些，因为一般不使用变量来存储委托，只在订阅事件时使用委托的一个实例。

&emsp;&emsp;第13章使用的代码就属于这种情况，如下所示：

```csharp
    Timer myTimer = new Timer(100);
    myTimer.Elapsed += new ElapsedEventHandler(WriteChar);
```

&emsp;&emsp;这段代码订阅了Timer对象的Elapsed事件。这个事件使用委托类型ElapsedEventHandler，使用方法标识符WriteChar实例化该委托类型。结果是Timer对象引发Elapsed事件时，就调用方法WriteChar()。传送给WriteChar()的参数取决于由ElapsedEventHandler委托定义的参数类型和Timer中引发事件的代码传送的值。

&emsp;&emsp;实际上，如第13章所述，C#编译器可以通过方法组语法，用更少的代码获得相同的结果：

```csharp
    myTimer.Elapsed += WriteChar;
```

&emsp;&emsp;C#编译器知道Elapsed事件需要的委托类型，所以可以填充该类型。但在大多数情况下，最好不要这么做，因为这会使代码更难理解，也不清楚会发生什么。使用匿名方法时，该过程会减少为一步：

（1）使用内联的匿名方法，该匿名方法的返回类型和参数匹配所订阅事件需要的委托的返回类型和参数。

&emsp;&emsp;用delegate关键字定义内联的匿名方法：

```csharp
    myTimer.Elapsed +=
        delegate(object source, ElapsedEventArgs e)
        {
            Console.WriteLine(
                "Event handler called after {0} millisenconds.",
                (source as Timer).Interval);
        };
```

&emsp;&emsp;这段代码像单独使用事件处理程序一样正常工作。主要区别是这里使用的匿名方法对于其余代码而言实际上是隐藏的。例如，不能在应用程序的其他地方重用这个事件处理程序。另外，为了更好地加以描述，这里使用的语法有点沉闷。delegate关键字会带来混淆，因为它具有双重含义---匿名方法和定义委托类型都要使用它。

🔚
