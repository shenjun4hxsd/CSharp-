##特性

&emsp;&emsp;本节将简要介绍一种为使用所创建类型的代码提供额外信息的方法：特性（attribute）。特性让我们可以为代码标记一些信息，而这样的信息又可以从外部读取，并通过各种方式来影响我们所定义的类型的使用方式。这种手段通常被称为对代码进行“装饰（decorating）”。本部分所包含的代码可以在本章在线下载页面的CustomAttributes\Program.cs文件中找到。

&emsp;&emsp;举例来说，比如我们要创建的某个类包含了一个极简单的方法。换句话说，这个方法简单到我们都没有必要去理它。但遗憾的是，即使简单，我们在应用程序调试期间还是不得不对这一代码进行检查。这种情况下，我们就可以对该方法添加一个特性，告诉VS在调试时不要进入该方法进行逐语句调试，而是应该跳出该方法，直接调试下一条语句。这样的特性声明如下：

```csharp
    [DEbuggerStepThrough]
    public void DullMethod()
    {
        ...
    }
```

&emsp;&emsp;上述代码中所包含的特性就是[DebuggerStepThrough]。所有特性的添加方式都是如此，也就是只需将特性名称用方括号括起来，并写在要应用的目标代码前面即可。并且可以为一段目标代码添加多个特性，将这些特性用逗号（，）分隔开，或者用多个方括号括起来每一个均可。

&emsp;&emsp;上述代码中所使用的特性实际上是通过DebuggerStepThroughAttribute这个类来实现的，而这个类位于system.Diagnostics名称空间中，因此如果我们要使用上面那个特性，就必须使用using语句来引用这一名称空间。引用该特性既可以直接使用其完整的名称，也可以像在前面的代码中那样，去掉后缀Attribute。

&emsp;&emsp;通过上述方式添加特性后，编译器就会创建该特性类的一个实例，然后将其与类方法关联起来。某些特性可以通过构造函数的参数或属性进行自定义，并在添加特性的时候进行指定，例如：

```csharp
    [DoesInterestingThings(1000, WhatDoesItDo = "woodoo")]
    public class DecorateClass
    {
    }
```

&emsp;&emsp;上述特性就将值1000传递给了DoesInterestingThingsAttribute的构造函数，并将WhatDoesItDo属性的值设置为字符串“voodoo”。

🔚