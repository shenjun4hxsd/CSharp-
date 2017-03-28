##重载转换运算符

&emsp;&emsp;除了重载上述数学运算符外，还可以定义类型之间的隐式和显式转换。如果要在不相关的类型之间转换，例如类型之间没有继承关系，也没有共享接口，就必须这么做。

&emsp;&emsp;下面定义ConvClass1和ConvClass2之间的隐式转换，即编写下列代码：

```csharp
    ConvClass1 op1 = new ConvClass1();
    ConvClass2 op2 = op1;
```

&emsp;&emsp;另外，可以定义一个显式转换：

```csharp
    ConvClass1 op1 = new ConvClass1();
    ConvClass2 op2 = (ConvClass2)op1;
```

&emsp;&emsp;例如，考虑下面的代码：

```csharp
    public class ConvClass1
    {
        public int val;
        
        public static implicit operator ConvClass2(ConvClass1 op1)
        {
            ConvClass2 returnVal = new ConvClass2();
            returnVal.val = op1.val;
            return returnVal;
        }
    }
    
    public class ConvClass2
    {
        public double val;
        
        public static explicit operator ConvClass1(ConvClass2 op1)
        {
            ConvClass1 returnVal = new ConvClass1();
            checked
            {
                returnVal.val = (int)op1.val;
            };
            return returnVal;
        }
    }
```

&emsp;&emsp;其中，ConvClass1包含一个`int`值，ConvClass2包含一个`double`值。`int`值可以隐式转换为`double`值，所以可以在ConvClass1和ConvClass2之间定义一个隐式转换。但反过来就不行了，应把ConvClass2和ConvClass1之间的转换定义为显式转换。

&emsp;&emsp;在代码中，用关键字`implicit`和`explicit`来指定这些转换，如上所示。对于这些类，下面的代码就很好：

```csharp
    ConvClass1 op1 = new ConvClass1();
    op1.val = 3;
    ConvClass2 op2 = op1;
```

&emsp;&emsp;但反向转换需要进行下述显式数据类型转换：

```csharp
    ConvClass2 op1 = new ConvClass2();
    op1.val = 3e15;
    ConvClass1 op2 = (ConvClass1)op1;
```

&emsp;&emsp;如果在显式转换中使用了`checked`关键字，则上述代码将产生一个异常，因为`op1`的`val`属性值太大，不能放在`op2`的`val`属性中。

🔚