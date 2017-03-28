##对象初始化器

&emsp;&emsp;考虑下面的简单类定义：

```csharp
    public class Curry
    {
        public string MainIngredient { get; set; }
        public string Style { get; set; }
        
        public int Spiciness { get; set; }
    }
```

&emsp;&emsp;这个类有3个属性，用第10章介绍的自动属性语法来定义。如果希望实例化和初始化这个类的一个对象实例，就必须执行如下几个语句：

```csharp
    Curry tastyCurry = new Curry();
    tastyCurry.MainIngredient = "panir tikka";
    tastyCurry.Style = "jalfrezi";
    tastyCurry.Spiciness = 8;
```

&emsp;&emsp;如果类定义中未包含构造函数，这段代码就使用C#编译器提供的默认无参构造函数。为了简化这个初始化过程，可提供一个合适的非默认构造函数：

```csharp
    public class Curry
    {
        public Curry(sring mainIngredient, string style, int spciness)
        {
            MainIngredient = mainIngredient;
            Style = style;
            Spiciness = spciness;
        }
        
        ...
    }
```

&emsp;&emsp;这样就可以编写代码，把实例化和初始化合并起来：

```csharp
    Curry tastyCurry = new Curry("panir tikka", "jalfrezi", 8);
```

&emsp;&emsp;这段代码工作得很好，但它会强制使用Curry类的代码使用这个构造函数，这将阻止前面使用无参构造函数的代码运行。常需要提供无参构造函数，在必须序列化类时尤其如此：

```csharp
    public class Curry
    {
        public Curry()
        {
        }
        
        ...
    }
```

&emsp;&emsp;现在可以用任意方式来实例化和初始化Curry类，但已在最初的类定义中添加几行代码，为这种灵活性提供基本结构。

&emsp;&emsp;进入对象初始化器（object initializer），这是不必在类中添加额外的代码（如此处详细说明的构造函数）就可以实例化和初始化对象的方式。实例化对象时，要为每个需要初始化的、可公开访问的属性或字段使用名称-值对，来提供其值。其语法如下：

```csharp
    <ClassName> <variableName> = new <ClassName>
    {
        <propertyOrField1> = <value1>,
        <propertyOrField2> = <value2>,
        ...
        <propertyOrFieldN> = <valueN>,
    };
```

&emsp;&emsp;例如，重写前面的代码，实例化和初始化一个Curry类型的对象，如下所示：

```csharp
    Curry tastyCurry = new Curry
    {
        MainIngredient = "panir tikka",
        Style = "jalfrezi",
        Spiciness = 8
    };
```

&emsp;&emsp;我们常常可以把这样的代码放在一行上，而不会严重影响可读性。

&emsp;&emsp;使用对象初始化器时，不必显式调用类的构造函数。如果像上述代码那样省略构造函数的括号，就会自动调用默认的无参构造函数。这是在初始化器设置参数值之前调用的，以便在需要时为默认构造函数中的参数提供默认值。另外，可以调用特定的构造函数。同样，先调用这个构造函数，所以在构造函数中对公共属性进行的初始化可能会被初始化器中提供的值覆盖。只有能够访问所使用的构造函数（如果没有显式指出，就是默认的构造函数），对象初始化器才能正常工作。

&emsp;&emsp;如果要用对象初始化器进行初始化的属性比本例中使用的简单类型复杂，可以使用嵌套的对象初始化器，即使用与前面相同的语法：

```csharp
    Curry tastyCurry = new Curry
    {
        MainIngredient = "panir tikka",
        Style = "jalfrezi",
        Spiciness = 8,
        Origin = new Restaurant
        {
            Name = "King's Balti",
            Location = "York Road",
            Ratiing = 5
        }
    };
```

&emsp;&emsp;这里初始化了一个Restaurant类型（这里没有列出）的Origin属性。代码初始化了Origin属性的3个特性：Name、Location和Rating，其值的类型分别是string、string和int。这个初始化操作使用了嵌套的对象初始化器。

&emsp;&emsp;注意，对象初始化器没有替代非默认的构造函数。在实例化对象时，可以使用对象初始化器来设置属性和字段值，但这并不意味着总是知道需要初始化什么状态。通过构造函数，可以准确地指定对象需要什么值才能起作用，再执行代码，以便立即响应这些值。

&emsp;&emsp;另外，在上面的示例中，使用嵌套的对象初始化器和使用构造函数还有一个不太容易注意到的区别：对象的创建顺序。使用嵌套的对象初始化器时，首先创建顶级对象（Curry），然后创建嵌套对象（Restaurant），并把它赋值给属性Origin。如果使用构造函数，对象的创建顺序就反了过来，而且要把Restaurant实例传递给Curry的构造函数。在这个简单例子中，使用这两种方法的实际效果没什么区别，但在某些情况下它们的区别可能十分重要。


🔚
