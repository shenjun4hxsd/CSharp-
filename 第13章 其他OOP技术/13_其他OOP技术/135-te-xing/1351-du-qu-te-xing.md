##读取特性

&emsp;&emsp;要读取特性的值，我们必须使用一种称为“反射（reflection）”的技术。这种非常高级的技术让我们可以在运行的时候动态检查类型信息，甚至是在创建对象的位置，或者在不必知道具体对象的情况下直接调用某个方法。本书无法详细介绍这一技术，但在使用特性之前，需要了解该技术的一些基本知识。

&emsp;&emsp;简单来说，反射可以取得保存在Type对象（本书中会多次提到该对象）中的使用信息，以及通过System.Reflection名称空间中的各种类型来获取不同的类型信息。在此之前，我们已经了解过通过typeof运算符从类中快速获取类型信息，以及使用GetType()方法从对象实例中获取信息的方法。通过反射技术，我们可以继续从Type对象取得成员信息。基于这个方法，我们就可以从类或类的不同成员中取得特性信息了。

&emsp;&emsp;为此，最简单的方法也就是本书将要为大家介绍的唯一方法，即通过Type对象取得成员信息。基于这个方法，我们就可以从类或类的不同成员中取得特性信息了。

&emsp;&emsp;为此，最简单的方法也就是本书将要为大家介绍的唯一方法。即通过Type.GetCustomAttributes()方法来实现。这个方法最多使用两个参数，然后返回一个包含一系列object实例的数组，每个实例都是一个特性实例。第一个参数是可选的，即传送我们感兴趣的类型或若干特性的类型（其他所有特性均会被忽略）。如果不使用这一参数，将返回所有特性。第二个参数是必需的，即通过一个布尔值来指示，只想了解类本身的信息，还是除了该类之外还希望了解派生自该类的所有类。

&emsp;&emsp;例如，下面的代码可以列出DecoratedClass这个类的特性：

```csharp
    Type classType = typeof(DecoratedClass);
    object[] customAttributes = classType.GetCustomAttributes(true);
    foreach(object customAttribute in customAttributes)
    {
        Console.WriteLine("Attribute of type {0 found.}", customAttribute);
    }
```

&emsp;&emsp;通过这种方法了解到不同的特性后，我们就可以为不同的特性采取不同的操作了。这也正是当VS遇到前面介绍的DebuggerStepThroughAttribute特性时所执行的操作。

🔚