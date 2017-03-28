##创建特性

&emsp;&emsp;只要通过System.Attribute类进行派生，我们也可以创建出自己的特性。一般来说，如果除了包含和不包含特定的特性外，我们的代码不需要获得更多信息就可以完成需要的工作，那么我们就不必完成这些额外的工作。但有时，如果我们希望某些特性可以被自定义，则可以提供非默认的构造函数和／或可写属性。

&emsp;&emsp;另外，还需要为自己的特性做两个选择：要将其应用到什么类型的目标（类、属性或者是其他），以及是否可以对同一目标进行多次应用。要指定上述信息，我们需要通过对特性应用一个特性来实现（这句话实在是拗口！），这个特性就是`AttributeUsageAttribute`。这个特性带有一个类型为`AttributeTargets`的构造函数参数值，通过`|`运算符即可通过相应的枚举值组合出我们需要的值。另外，该特性还有一个布尔值类型的属性`AllowMultiple`，用于指定是否可以多次应用特性。

&emsp;&emsp;例如，下面的代码指定了一个特性可以应用到类或属性中（一次）：

```csharp
    [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, AllowMultiple = false)]
    class DoesInterestingThingsAttribute : Attribute
    {
        public DoesInterestingThingsAttribute(int howManyTimes)
        {
            HowManyTimes = howManyTimes;
        }
        
        public string WhatDoesItDo { get; set; }
        
        public int HowManyTimes { get; private set; }
    }
```

&emsp;&emsp;这样，就可以像前面的片段中看到的那样来使用DoesInterestingThings特性了：

```csharp
    [DoesInterestingThings(1000, WhatDoesItDo = "voodoo")]
    public class DocoratedClass
    {
    }
```

&emsp;&emsp;只要像下面这样修改前面的代码，就可以访问这一特性的属性：

```csharp
    Type classType = typeof(DecoratedClass);
    object[] customAttributes = classType.GetCustomAttributes(true);
    foreach(object customAttribute in customAttributes)
    {
        Console.WriteLine("Attribute of type {0} found.", customAttribute);
        DoesInterestingThingsAttribute interestingAttribute = customAttribute as DoesInterestingThingsAttribute;
        if(interestingAttribute != null)
        {
            Console.WriteLine("This class does {0} x {1}!",
                interestingAttribute.WhatDoesItDo,
                interestingAttribute.HowManyTimes);
        }
    }
```

&emsp;&emsp;“特性”这一技术在所有.NET应用程序都可以变得非常有用，特别是WPF和Windows Store应用程序。本书其余的部分会多次涉及这一技术。

🔚
