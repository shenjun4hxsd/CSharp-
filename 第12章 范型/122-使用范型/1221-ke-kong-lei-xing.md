##可空类型

&emsp;&emsp;前面的章节介绍了值类型（大多数基本类型，例如，int、double和所有结构）区别于引用类型（string和任意类）的一种方式：值类型必须包含一个值，它们可以声明之后、赋值之前，在未赋值的状态下存在，但不能使用未赋值的变量。而引用类型可以是`null`。

&emsp;&emsp;有时让值类型为空是很有用的（尤其是处理数据库时），泛型使用`System.Nullable<T>`类型提供了使值类型为空的一种方式。例如：

```csharp
    System.Nullable<int> nullableInt;
```

&emsp;&emsp;这行代码声明了一个变量`nullableInt`，它可以拥有`int`变量能包含的任意值，还可以拥有值`null`。所以可以编写如下的代码：

```csharp
    nullableInt = new System.Nullable<int>(); 
``` 

&emsp;&emsp;与其他任意变量一样，无论是初始化为`null`（使用上面的语法），还是通过给它赋值来初始化，都不能在初始化之前使用它。

&emsp;&emsp;可以像测试引用类型一样测试可空类型，看看它们是否为`null`：
```csharp
    if(nullableInt == null)
    {
        ...
    }
```

&emsp;&emsp;另外，可以使用`HasValue`属性：

```csharp
    if(nullableInt.HasValue)
    {
        ...
    }
```

&emsp;&emsp;这不适用于引用类型，即使引用类型有一个`HasValue`属性，也不能使用这种方法，因为引用类型的变量值为`null`，就表示不存在对象，当然就不能通过对象来访问这个属性，否则会抛出一个异常。

&emsp;&emsp;可以来使用`Value`属性来查看可空类型的值。如果`HasValue`是`true`，就说明`Value`属性有一个非空值。但如果`HasValue`是`false`，就说明变量被赋予了`null`，访问`Value`属性会抛出`System.InvalidOperationException`类型的异常。

<br>

&emsp;&emsp;可空类型非常有用，以至于修改了C#语法。声明可空类型的变量不使用上述语法，而是使用下面的语法：

```csharp
    int? nullableInt;
```

&emsp;&emsp;`int?`是`System.Nullable<int>`的缩写，但更便于读取。在后面的章节中就使用了这个语法。

####&emsp;&emsp;1. 运算符和可空类型

&emsp;&emsp;对于简单类型（如`int`），可以使用`+`、`-`等运算符来处理值。而对于对应的可空类型，这是没有区别的：包含在可空类型中的值会隐式转换为需要的类型，使用适当的运算符。这也适用于结构和自己提供的运算符。例如：

```csharp
    int? op1 = 5;
    int? result = op1 * 2;
```

&emsp;&emsp;注意，其中`result`变量的类型也是`int?`。下面的代码不会被编译：

```csharp
    int? op1 = 5;
    int result = op1 * 2;   ❌
```

&emsp;&emsp;为了使上面的代码正常工作，必须进行显式转换：

```csharp
    int? op1 = 5;
    int result = (int)op1 * 2;   ✅
```

&emsp;&emsp;或通过`Value`属性访问值：

```csharp
    int? op1 = 5;
    int result = op1.Value * 2;
```

&emsp;&emsp;只要`op1`有一个值，上面的代码就可以正常运行。如果`op1`是`null`，就会生成`System.InvalidOperationException`类型的异常。

&emsp;&emsp;这就引出了下一个问题：当运算表达式中的一个或两个值是`null`时，例如，下面代码中`op1`，会发生什么情况？

```csharp
    int? op1 = null;
    int? op2 = 5;
    int? result = op1 * op2;
```

&emsp;&emsp;答案是：对于除了`bool?`外的所有简单可空类型，该操作的结果是`null`，可以把它解释为“不能计算”。对于结构，可以定义自己的运算符来处理这种情况（详见本章后面的内容）。对于`bool?`，为`&`和`|`定义的运算符会得到非空返回值，如表12-1所示。

|op1|op2|op1 & op2|op1 &#124; op2|
|:--|:--|:--|:--|
|true|true|true|true|
|true|false|false|true|
|true|null|null|true|
|false|true|false|true|
|false|false|false|false|
|false|null|false|null|
|null|true|null|true|
|null|false|false|null|
|null|null|null|null|

&emsp;&emsp;这些运算符的结果十分符合逻辑，如果不需要知道其中一个操作数的值，就可以计算出结果，则该操作数是否为`null`就不重要。



####&emsp;&emsp;2. ??运算符

&emsp;&emsp;为进一步减少处理可空类型所需的代码量，使可空变量的处理变得更简单，可以使用??运算符。这个运算符称为空接合运算符（null coalescing operator），是一个二元运算符，允许给可能等于`null`的表达式提供另一个值。如果第一个操作数不是`null`，该运算符就等于第一个操作数，否则，该运算符就等于第二个操作数。下面的两个表达式的作用是相同的：

```csharp
    op1 ?? op2
    op1 == null ? op2 : op1
```

&emsp;&emsp;在这两行代码中，`op1`可以是任意可空表达式，包括引用类型和更重要的可空类型。因此，如果可空类型是`null`，就可以使用??运算符提供要使用的默认值，如下所示：

```csharp
    int? op1 = null;
    int result = op1 * 2 ?? 5;
```

&emsp;&emsp;在这个示例中，`op1`是`null`，所以 op1 * 2 也是`null`。但是，??运算符检测到这个情况，并把值5赋予result。这里要特别注意，在结果中放入`int`类型的变量result不需要显式转换。??运算符会自动处理这个转换。还可以把??表达式的结果放在`int?`中：

```csharp
    int? result = op1 * 2 ?? 5;
```

&emsp;&emsp;在处理可空变量时，??运算符有许多用途，它也是一种提供默认值的便捷方式，不需要使用`if`结构中的代码块或容易引起混淆的三元运算符。

&emsp;&emsp;在下面的示例中，将介绍可空类型Vector。

```csharp
    public class Vector
    {
        public double? R = null;
        public double? Theta = null;
        
        public double? ThetaRadians
        {
            get
            {
                // Convert degrees to radians.
                return (Theta * Math.PI / 180.0);
            }
        }
        
        public Vector(double? r, double? theta)
        {
            // Normalize.
            if(r < 0)
            {
                r = -r;
                theta += 180;
            }
            theta = theta % 360;
            
            // Assign fields.
            R = r;
            Theta = theta;
        }
        
        public static Vector operator + (Vector op1, Vector op2)
        {
            try
            {
                // Get (x, y) coordinates for new vector.
                double newX = op1.R.Value * Math.Sin(op1.ThetaDadians.Value)
                    + op2.R.Value * Math.Sin(op2.ThetaRadians.Value);
                double newY = op1.R.Value * Math.Cos(op1.ThetaRadians.Value)
                    + op2.R.Value * Math.Cos(op2.ThetaRadians.Value);
                
                // Convert to (r, theta).
                double newR = Math.Sqrt(newX * newX + newY * newY);
                double newTheta = Math.Atan2(newX, newY) * 180.0 / Math.PI;
                
                // Return result.
                return new Vector(newR, newTheta);
            }
            catch
            {
                // Return "null" vector.
                return new Vector(null, null);
            }
        }
        
        public static Vector operator - (Vector op1)
        {
            return new Vector(-op1.R, op1.Theta);
        }
        
        public static Vector operator - (Vector op1, Vector op2)
        {
            return op1 + (-op2);
        }
        
        public override string ToString()
        {
            // Get string representation of coordinates.
            string rString = R.HasValue ? R.ToString() : "null";
            string thetaString = Theta.HasValue ? Theta.ToString() : "null";
            
            // Return (r, theta) string.
            return string.Format("({0}, {1})", rString, thetaString);
        }
    }
```



&emsp;&emsp;修改 Program.cs 中的代码，如下所示：

```csharp
    class Program
    {
        Vector v1 = GetVector("vector1");
        Vector v2 = GetVector("vector1");
        Console.WriteLine("{0} + {1} = {2}", v1, v2, v1 + v2);
        Console.WriteLine("{0} - {1} = {2}", v1, v2, v1 - v2);
        Console.ReadKey();
    }

    static Vector GetVector(string name)
    {
        Console.WriteLine("Input {0} magnitude:", name);
        double? r = GetNullableDouble();
        Console.WriteLine("Input {0} angle (in degrees):", name);
        douoble? theta = GetNullableDouble();
        return new Vector(r, theta);
    }

    static double? GetNullableDouble()
    {
        double? result;
        string userInput = Console.ReadLine();
        try
        {
            result = double.Parse(userInput);
        }
        catch
        {
            result = null;
        }
        return result;
    }
```

>&emsp;&emsp;**示例的说明**

>&emsp;&emsp;在这个示例中，创建了一个类Vector，它表示带极坐标（有一个幅值和一个角度）的矢量，如图12-3所示。

![](/assets/屏幕快照 2017-03-20 下午8.41.54.png)



&emsp;&emsp;坐标r和Ɵ在代码中用公共字段R和Theta表示，其中Theta的单位是度（°）。ThetaRadians用于获取Theta的弧度值，这是必需的，因为Math类在其静态方法中使用弧度。R和Theta的类型都是double?，所以它们可以为空。

```csharp
    public class Vector
    {
        public double? R = null;
        public double? Theta = null;

        public double? ThetaRadians
        {
            get
            {
                // Convert degrees to radians.
                return (Theta * Math.PI / 180.0);
            }
        }
    }
```

&emsp;&emsp;Vector的构造函数标准化R和Theta的初始值，然后赋予公共字段。

```csharp
    public class Vector
    {
        public double? R = null;
        public double? Theta = null;

        public double? ThetaRadians
        {
            get
            {
                // Convert degrees to radians.
                return (Theta * Math.PI / 180.0);
            }
        }
```

&emsp;&emsp;Vector的构造函数标准化R和Theta的初始值，然后赋予公共字段。

```csharp
    public Vector(double? r, double? theta)
    {
        // Normalize.
        if(r < 0)
        {
            r = -r;
            theta += 180;
        }
        theta = theta % 360;
        // Assign fields.
        R = r;
        Theta = theta;
    }
```

&emsp;&emsp;Vector类的主要功能是使用运算符重载对矢量进行相加和相减运算，这需要一些非常基本的三角函数知识，这里不解释它们。在代码中，重要的是，如果在获取R或ThetaRadians的Value属性时抛出了异常，即其中一个是`null`，就返回“空”矢量。

```csharp
    public static Vector operator + (Vector op1, Vector op2)
    {
        try
        {
            // Get (x, y) coordinates for new vector.
           ...
        }
        catch
        {
            // Return "null" vector.
            return new Vector(null, null);
        }
    }
```

&emsp;&emsp;如果组成矢量的一个坐标是`null`，该矢量就是无效的，这里用R和Theta都可为`null`的Vector类来表示。Vector类的其他代码重写了其他运算符，以便扩展相加的功能，使其包含相减操作，再重写ToString()，获取Vector对象的字符串表示。

&emsp;&emsp;Program.cs中的代码测试Vector类，让用户初始化两个矢量，再对它们进行相加和相减。如果用户省略了某个值，该值就解释为`null`，应用前面提及的规则。




🔚
