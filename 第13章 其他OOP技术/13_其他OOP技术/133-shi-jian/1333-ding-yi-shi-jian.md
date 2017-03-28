##定义事件

&emsp;&emsp;接着论述如何定义和使用自己的事件。我们将使用本节前面介绍的即时消息传送应用程序示例，并创建一个Connection对象，该对象引发由Display对象处理的事件。

```csharp
    using system;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Timers;
    
    namespace Ch13Ex02
    {
        public delegate void MessageHandle(string messageText);
        
        public class Connection
        {
            public event MessageHandle MessageArrived;
            private Timer pollTimer;
            
            public Connection()
            {
                pollTimer = new Timer(100);
                pollTimer.Elapsed += new ElapsedEventHandler(CheckForMessage);
            }
            
            public void Connect()
            {
                pollTimer.Start();
            }
            
            public void Disconnect()
            {
                pollTimer.Stop();
            }
            
            private void CheckForMessage(object source, ElapsedEventArgs e)
            {
                Console.WriteLine("Checking for new messages.");
                if((random.Next(9) == 0) && (MessageArrived != null))
                {
                    MessageArrived("Hello Mum!");
                }
            }
        }
        
        class Display
        {
            public void DisplayMesage(string message)
            {
                Console.WriteLine("Message arrived: {0}", message);
            }
        }
    }
```

&emsp;&emsp;修改Program.cs中的代码，如下所示：

```csharp
    static void Main(string[] args)
    {
        Connection myConnection = new Connection();
        Display myDisplay = new Display();
        myConnection.MessageArrived +=
            new MessageHandler(myDisplay.DisplayMessage);
        myConnection.Connect();
        Console.ReadKey();
    }
```

>&emsp;&emsp;**示例的说明**

>&emsp;&emsp;这个应用程序中的大部分工作是由Connection类完成的。这个类的实例使用如本章第一个示例中所示的Timer对象，在类的构造函数中初始化它，并通过Connect()和Disconnect()访问它的状态（可访问和禁止访问）：

```csharp
    public class Connection
    {
        private Timer pollTimer;
            
            public Connection()
            {
                pollTimer = new Timer(100);
                pollTimer.Elapsed += new ElapsedEventHandler(CheckForMessage);
            }
            
            public void Connect()
            {
                pollTimer.Start();
            }
            
            public void Disconnect()
            {
                pollTimer.Stop();
            }

            ...
    }
```
>&emsp;&emsp;在构造函数中，我们还以与第一个示例相同的方式注册了Elapsed事件的一个事件处理程序。每当调用这个处理程序方法CheckForMessage()的次数达到10次后，就会引发一个事件。在分析它的代码前，首先来分析事件的定义。

>&emsp;&emsp;在定义事件前，必须首先定义一个委托类型，以用于该事件，这个委托类型指定了事件处理方法必须拥有的返回类型和参数。为此，我们使用标准的委托语法，在Ch13Ex02名称空间中将该委托定义为公共类型，使该类型可供外部代码使用：

```csharp
    namespace Ch13Ex02
    {
        public delegate void MessageHandler(string messageText);
    }
```
>&emsp;&emsp;这个委托类型称为Messagehandler，是void方法的签名，它有一个string参数。使用这个参数可以把Connection对象收到的即时消息发送给Display对象。定义了委托（或者找到合适的现有委托）后，就可以把事件本身定义为Connection类的一个成员：

>```csharp
    public class Connection
    {
        public event MessageHandler MessageArrived;
```

>&emsp;&emsp;给事件命名（这里使用名称MessageArrived），在声明时，使用event关键字，并指向要使用的委托类型（前面定义的MessageHandler委托类型）。以这种方式声明了事件后，就可以引发它，方法是按名称来调用它，就像它是一个其返回类型和参数是由委托指定的方法一样。例如，使用下面的代码引发这个事件：

>```csharp
    MessageArrived("This is a message.");
```
>&emsp;&emsp;如果定义该委托时不包含任何参数，就可以使用下面的代码：

>```csharp
    MessageArrived();
```

>&emsp;&emsp;如果定义了较多参数，就需要用比较多的代码来引发事件。CheckForMessage()方法如下所示：

>```csharp
    private static Random random = new Random();
    
    private void CheckForMessage(object source, ElapsedEventArgs e)
    {
        Console.WriteLine("Checking for new messages.");
        if((random.Next(9) == 0) && (MessageArrived != null))
        {
            MessageArrived("Hello Mum!");
        }
    }
```


>&emsp;&emsp;使用前面几章中的Random类实例，生成一个介于0～9之间的随机数，如果该随机数为0，就引发一个事件，它的发生几率为10%。这类似于轮询连接，看看是否接收到消息，不可能每次检测时，都没有接收到消息。为将计时器与Connection的实例分隔开，使用了Random类的一个私有静态实例。

>&emsp;&emsp;注意，这里还提供了其他逻辑。只有表达式MessageArrived != null为true，才引发一个事件。这个表达式也使用了委托语法，但语法稍有不同，其含义是“事件是否有订阅者？”。如果没有订阅者，MessageArrived就是null，也就不会引发事件。

>&emsp;&emsp;订阅事件的类是Display，它包含一个方法DisplayMessage()，其定义如下所示：

>```csharp
    public class Display
    {
        public void DisplayMessage(string message)
        {
            Console.WriteLine("Message arrived: {0}", message);
        }
    }
```

>&emsp;&emsp;这个方法匹配委托类型（而且是公共的，如果类不是生成该事件的类，则其事件处理程序必须是公共的），所以可以用它来响应MessageArrived事件。

>&emsp;&emsp;剩下的是Main()中的代码初始化了Connection和Display类的实例，把它们关联起来，开始执行任务。这里需要的代码类似于第一个示例中的代码：

>```csharp
    static void Main(string[] args)
    {
        Connection myConnection = new Connection();
        Display myDisplay = new Display();
        myConnection.MessageArrived +=
            new MessageHandler(myDisplay.DisplayMessage);
        myConnection.Connect();
        Console.ReadKey();
}
```

>&emsp;&emsp;再次调用Console.ReadKey()，当开始执行Connection对象的Connect()方法并增加一段延迟时间后，暂停Main()的处理。

---

####&emsp;&emsp;1. 多用途的事件处理程序

&emsp;&emsp;前面Timer.Elapsed事件的委托包含了事件处理程序中常见的两类参数，如下所示：

```
    • object source --- 引发事件的对象的引用
    • ElapsedEventArgs e --- 由事件传送的参数
```

&emsp;&emsp;在这个事件（以及许多其他的事件）中使用object类型参数的原因是，我们常常要为由不同对象引发的几个相同事件使用同一个事件处理程序，但仍要指定哪个对象生成了事件。

&emsp;&emsp;要说明这一点，下面将扩展上一个示例。

```csharp
    namespace Ch13Ex03
    {
        public class MessageArrivedEventArgs : EventArgs
        {
            private string message;

            public string Message
            {
                get
                {
                    return message;
                }
            }

            public MessageArrivedEventArgs()
            {
                message = "No message sent.";
            }

            public MesageArrivedEventArgs(string newMessage)
            {
                message = newMessage;
            }
        }
    }
```

&emsp;&emsp;修改Connection.cs，所下所示：

```csharp
    namespace Ch13Ex03
    {
        // delegate definition removed

        public class Connection
        {
            public event EventHandle<MessageArrivedEventArgs> MessageArrived;

            public string Name { get; set; }

            ...

            private void CheckForMessage(object source, EventArgs e)
            {
                Console.WriteLine("Checking for new messages.");
                if((random.Next(9) == 0) && (MessageArrived != null))
                {
                    MessageArrived(this, new MessageArrivedEventArgs("Hello Mum!"));
                }
            }

            ...
        }
    }
```

&emsp;&emsp;修改Display.cs，如下所示：

```csharp
    public void DisplayMessage(object source, MessageArrivedEventArgs e)
    {
        Console.WriteLine("Message arrived from: {0}", 
            ((Connection)source).Name);
        Console.WriteLine("Message Text: {0}", e.Message);
    }
```

&emsp;&emsp;修改Program.cs，如下所示：

```csharp
    static void Main(string[] args)
    {
        Connection myConnection1 = new Connection();
        myConnection1.Name = "First connection";
        Connection myConnection2 = new Connection();
        myConnection2.Name = "Second connection";
        Display myDisplay = new Display();
        myConnection1.MessageArrived += myDisplay.DisplayMessage;
        myConnection2.MessageArrived += myDisplay.DisplayMessage;
        myConnection1.Connect();
        myConnection2.Connect();
        System.Threading.Thread.Sleep(200);
        Console.ReadKey();
    }
```

>&emsp;&emsp;**示例的说明**

>&emsp;&emsp;发送一个引发事件的对象引用，将其作为事件处理程序的一个参数，就可以为不同对象定制处理程序的响应。利用该引用可以访问源对象，包括它的属性。

>&emsp;&emsp;通过发送包含在派生于System.EventArgs（与ElapsedEventArgs相同）的类中的参数，就可以将其他必要信息提供为参数（例如，MessageArrivedEventArgs类上的Message参数）。

>&emsp;&emsp;另外，这些参数也将得益于多态性。可为MessageArrived事件定义一个处理程序，如下所示：

>```csharp
    public void DisplayMessage(object source, EventArgs e)
    {
        Console.WriteLine("Message arrived from: {0}",
            ((Connection)source).Name);
        Console.WriteLine("Message Test: {0}",
            ((MessageArrivedEventArgs)e).Message);
    }
```

>&emsp;&emsp;这个应用程序将像以前那样执行，但DisplayMessage()方法变得更加通用（至少从理论上讲是这样的---需要使用更多实现代码，才能满足生产环境的要求）。这个处理程序还可以处理其他事件，例如Timer.Elapsed事件（以这种方式把它们转换为Connection和MessageArrivedEventArgs对象，会抛出一个异常，所以这里应使用as运算符，检查null值）。


---

####&emsp;&emsp;2. EventHandler和泛型EventHandler<T>类型

&emsp;&emsp;大多数情况下，都应遵循上一节提出的模式，使用返回类型为void、带两个参数的事件处理程序。第一个参数的类型是object，是事件源。第二个参数的类型派生于System.EventArgs，包含任意事件实参。这非常常见，为此.NEt提供了两个委托类型EventHandler和EventHandler<T>，以便于定义事件。它们都是委托，使用标准的事件处理模式。泛型版本允许指定要使用的事件实参的类型。

&emsp;&emsp;在前面的示例中演示了这一点，使用了泛型委托类型EventHandler<T>，如下所示：

```csharp
    public class Connection
    {
        public event EventHandler<MessageArrivedEventArgs> MessageArrived;
        ...
    }
```

&emsp;&emsp;这显然是件好事，因为它简化了代码。一般来说，在定义事件时，最好使用这些委托类型。注意，如果事件不需要事件实参数据，仍然可以使用EventHandler委托类型，只不过要传递EventArgs.Empty作为实参值。

####&emsp;&emsp;3. 返回值和事件处理程序

&emsp;&emsp;前面的所有事件处理程序都使用void类型的返回值。可以为事件提供返回类型，但这会出问题。这是因为引发给定的事件，可能会调用多个事件处理程序。如果这些处理程序都返回一个值，那么我们该使用哪个返回值？

&emsp;&emsp;系统处理这个问题的方式是，只允许访问由事件处理程序最后返回的那个值，也就是最后一个订阅该事件的处理程序返回的值。这个功能在某些情况下是有用的，但最好使用void类型的事件处理程序，且避免使用out类型的参数（如果使用out参数，参数返回的值的源头就是不清楚的）。


####&emsp;&emsp;4. 匿名方法

&emsp;&emsp;除了定义事件处理方法外，还可以选择使用匿名方法（anonymous method）。匿名方法实际上并非传统意义上的方法，它不是某个类上的方法，而纯粹是为用作委托目的而创建的。

&emsp;&emsp;要创建匿名方法，需要使用下面的代码：

```csharp
    delegate(parameters)
    {
        // Anonymous method code.
    };
```


&emsp;&emsp;其中parameters是一个参数列表，这些参数匹配正在实例化的委托类型，由匿名方法的代码使用，例如：

```csharp
    delegate(Connection source, MessgeArrivedEventArgs e)
    {
        // Anonymous method code matching MessageHandler event in Ch13Ex03.
    };
```

&emsp;&emsp;例如，使用这段代码可以完全绕过Ch13Ex03中的Display.DisplayMessage()方法：

```csharp
    myConnection1.MessageArrived +=
        delegate(Connection source, MessageArrivedEventArgs e)
        {
            Console.WriteLine("Message arrived from: {0}", source.Name);
            Console.WriteLine("Message Text: {0}", e.Message);
        };
```

&emsp;&emsp;对于匿名方法要注意，对于包含它们的代码块来说，它们是局部的，可以访问这个作用域内的局部变量。如果使用这样一个变量，它就成为外部变量（outer variable）。外部变量在超出作用域时，是不会删除的，这与其他局部变量不同，在使用它们的匿名方法被销毁时，才会删除外部变量。这比我们希望的时间晚一些，所以要格外小心。如果外部变量占用了大量内存，或者使用的资源在其他方面是比较昂贵的（例如资源数量有限），就可能导致内存或性能问题。


🔚