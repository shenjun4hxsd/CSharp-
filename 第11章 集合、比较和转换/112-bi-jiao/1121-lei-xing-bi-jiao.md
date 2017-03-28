##类型比较

&emsp;&emsp;在比较对象时，常需要了解它们的类型，才能确定是否可以进行值的比较。第9章介绍了GetType()方法，所有的类都从System.Object中继承了这个方法，这个方法和typeof()运算符一起使用，就可以确定对象的类型（并据此执行操作）：

```csharp
    if(myObj.GetType() == typeof(MyComplexClass))
    {
        // myObj is an instance of the class MyComplexClass
    }
```

&emsp;&emsp;前面还提到ToString()的默认实现方式，ToString()也是从System.Object继承来的，该方法可以提供对象类型的字符串表示。也可以比较这些字符串，但这是比较杂乱的方式。

&emsp;&emsp;本节将介绍比较值的一种简单方式：is运算符。它可以提供可读性较高的代码，还可以检查基类。在介绍is运算符之前，需要了解处理值类型（与引用类型相反）时后台的一些常见操作：封箱（boxing）和拆箱（unboxing）。

<br>

&emsp;&emsp;**1. 封箱和拆箱**

&emsp;&emsp;第8章讨论了引用类型和值类型之间的区别，第9章通过比较结构（值类型）和类（引用类型）展示了这些区别。封箱（boxing）是把值类型转换为System.Object类型，或者转换为由值类型实现的接口类型。拆箱（unboxing）是相反的转换过程。

&emsp;&emsp;例如，下面的结构类型：

```csharp
    struct MyStruct
    {
        public int Val;
    }
```

&emsp;&emsp;可以把这种类型的结构放在object类型的变量中，以封装它：

```csharp
    MyStruct valType1 = new MyStruct();
    valType1.Val = 5;
    object refType = valType1;
```

&emsp;&emsp;其中创建了一个类型为MyStruct的新变量valType1，并把一个值赋予这个结构的Val成员，然后把它封箱在object类型的变量refType中。

&emsp;&emsp;以这种方式封箱变量而创建的对象，包含值类型变量的一个副本的引用，而不包含源值类型变量的引用。要进行验证，可以修改源结构的内容，把对象中包含的结构拆箱到新变量中，检查其内容：

```csharp
    valType1.Val = 6;
    MyStruct valType2 = (MyStruct)refType;
    Console.WriteLine("valType2.Val = {0}", valType2.Val);
```

&emsp;&emsp;执行这段代码将得到如下输出结果：

```csharp
    valType2.Val = 5
```

&emsp;&emsp;但在把一个引用类型赋予对象时，将执行不同的操作。把MyStruct改为一个类（不考虑这个类名不合适的情况），即可看到这种情形：

```csharp
    class MyStruct
    {
        public int Val;
    }
```
&emsp;&emsp;如果不修改上面的客户代码（再次忽略名称有误的变量），就会得到如下输出结果：

```csharp
    valType2.Val = 6
```
&emsp;&emsp;也可以把值类型封箱到接口类型中，只要它们实现这个接口即可。例如，假定MyStruct类型实现IMyInterface接口，如下所示：

```csharp
    interface IMyInterface
    {
    }
    
    struct MyStruct : IMyInterface
    {
        public int Val;
    }
```

&emsp;&emsp;接着把结构封箱到一个IMyInterface类型中，如下所示：

```csharp
    MyStruct valType1 = new MyStruct();
    IMyInterface refType = valType1;
```

&emsp;&emsp;然后使用一般的数据类型转换语法拆箱它：

```csharp
    MyStruct ValType2 = (MyStruct)refType;
```
&emsp;&emsp;从这些示例中可以看出，**封箱是在没有用户干涉的情况下进行的（即不需要编写任何代码），但拆箱一个值需要进行显式转换，即需要进行数据类型转换（封箱是隐式的，所以不需要进行数据类型转换）**。

&emsp;&emsp;读者可能想知道为什么要这么做。封箱非常有用，有两个非常重要的原因。首先，它允许在项的类型是object的集合（如ArrayList）中使用值类型。其次，有一个内部机制允许在值类型（例如int和结构）上调用object方法。

&emsp;&emsp;最后需要注意的是，在访问值类型内容前，必须进行拆箱。

<br>

&emsp;&emsp;**2. is 运算符**

&emsp;&emsp;`is`运算符并不是用来说明对象是某种类型，而是用来检查是不是给定类型，或者是否可以转换为给定类型，如果是，这个运算符就返回`true`。

&emsp;&emsp;在前面的示例中，有Cow和Chicken类，它们都继承于Animal。使用is运算符比较Animal类型的对象，如果对象是这3种类型中的一种（不仅是Animal），`is`运算符就返回`true`。使用前面介绍的GetType()方法和typeof()运算符很难做到这一点。

&emsp;&emsp;`is`运算符的语法如下：

```csharp
    <operand> is <type>
```

&emsp;&emsp;这个表达式的结果如下：

```
    • 如果<type>是一个类类型，而<operand>也是该类型，或者它继承了该类型，或者它可以封箱到该类型中，则结果为true。
    • 如果<type>是一个接口类型，而<operand>也是该类型，或者它是实现该接口的类型，则结果为true。
    • 如果<type>是一个值类型，而<operand>也是该类型，或者它可以拆箱到该类型中，则结果为true。
```

&emsp;&emsp;下面用一个示例说明如何使用该运算符。

&emsp;&emsp;修改Program.cs中的代码，如下所示：


```csharp
    namespace Ch11Ex04
    {
        class Checker
        {
            public void Check(object param1)
            {
                if(param1 is ClassA)
                    Console.WriteLine("Variable can be converted to ClassA.");
                else
                    Console.WriteLine("Variable can't be converted to ClassA.");
                    
                if(param1 is IMyInterface)
                    Console.WriteLine("Variable can be converted to IMyInterface.");
                else
                    Console.WriteLine("Variable can't be converted to IMyInterface.");

                if(param1 is MyStruct)
                    Console.WriteLine("Variable can be converted to MyStruct.");
                else
                    Console.WriteLine("Variable can't be converted to MyStruct.");
            }
        }
        
        interface IMyInterface{}
        
        class ClassA : IMyInterface {}
        
        class ClassB : IMyInterface {}
        
        class ClassC {}
        
        class ClassD {}
        
        struct MyStruct : IMyInterface {}
        
        class Program
        {
            static void Main(string[] args)
            {
                Checker check = new Checker();
                ClassA try1 = new ClassA();
                ClassB try2 = new ClassB();
                ClassC try3 = new ClassC();
                ClassD try4 = new ClassD();
                MyStruct try5 = new MyStruct();
                object try6 = try5;
                Console.WriteLine("Analyzing ClassA type variable:");
                check.Check(try1);
                Console.WriteLine("\nAnalyzing ClassB type variable:");
                check.Check(try2);
                Console.WriteLine("\nAnalyzing ClassC type variable:");
                check.Check(try3);
                Console.WriteLine("\nAnalyzing ClassD type variable:");
                check.Check(try4);
                Console.WriteLine("\nAnalyzing MyStruct type variable:");
                check.Check(try5);
                Console.WriteLine("\nAnalyzing boxed MyStruct type variable:");
                check.Check(try6);

                Console.ReadKey();
 
            }
        }
        
    }
```

>&emsp;&emsp;**示例的说明**

>&emsp;&emsp;这个示例说明了使用`is`运算符的各种可能结果。其中定义了3个类、一个接口和一个结构，并把它们用作一个类的方法的参数，这个类使用`is`运算符确定它们是否可以转换为ClassA类型、接口类型和结构类型。

>&emsp;&emsp;只有ClassA和ClassD（继承了ClassA）类型与ClassA兼容。如果一个类型没有继承一个类，该类型不会与该类兼容。

>&emsp;&emsp;ClassA、ClassB和MyStruct类型都实现了IMyInterface类型兼容。ClassD继承了ClassA，所以它们两个也兼容。因此，只有ClassC是不兼容的。

>&emsp;&emsp;最后，只有MyStruct类型本身的变量和该类型的封箱变量与MyStruct兼容，因为不能把引用类型转换为值类型（当然，我们能够拆箱以前封箱的变量）。


🔚
















