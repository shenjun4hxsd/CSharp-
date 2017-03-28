##as 运算符

&emsp;&emsp;`as`运算符使用下面的语法，把一种类型转换为指定的引用类型：

```csharp
    <operand> as <type>
```

&emsp;&emsp;这只适用于下列情况：

```
    • <operand> 的类型是 <type> 类型
    • <operand> 可以隐式转换为 <type> 类型
    • <operand> 可以封箱到 <type> 类型中
```

&emsp;&emsp;如果不能从`<operand>`转换为`<type>`，则表达式的结果就是`null`。

&emsp;&emsp;基类到派生类的转换可以使用显式转换来进行，但这并不总是有效的。考虑前面示例中的两个类ClassA和ClassD，其中ClassD派生于ClassA：

```csharp
    class ClassA : IMyInterface
    {
    }
    
    class ClassD : ClassA
    {
    }
```

&emsp;&emsp;下面的代码使用 `as` 运算符把 `obj1` 中存储的 ClassA 实例转换为 ClassD 类型：

```csharp
    ClassA obj1 = new ClassA();
    ClassD obj2 = obj1 as ClassD;
```
&emsp;&emsp;则obj2的结果为 `null`。

&emsp;&emsp;还可以使用多态性把 ClassD 实例存储在 ClassA 类型的变量中。下面的代码演示了这一点，ClassA 类型的变量包含 ClassD 类型的实例，使用 `as` 运算符把 ClassA 类型的变量转换为 ClassD 类型。

```csharp
    ClassD obj1 = new ClassD();
    ClassA obj2 = obj1;
    ClassD obj3 = obj2 as ClassD;
```

&emsp;&emsp;这次`obj3`最后包含与`obj1`相同的对象引用，而不是`null`。

&emsp;&emsp;因此，`as`运算符非常有用，因为下面使用简单类型转换的代码会抛出一个异常：

```csharp
    ClassA obj1 = new ClassA();
    ClassD obj2 = (ClassD)obj1;
```
&emsp;&emsp;与此代码等价的`as`代码会把`null`值赋予`obj2`，不会抛出异常。这表示，下面的代码（使用本章前面开发的两个类：Animal和派生于Animal的一个类Cow）在C#应用程序中是很常见的：

```csharp
    public void MilkCow(Animal myAnimal)
    {
        Cow myCow = myAnimal as Cow;
        if(myCow != null)
        {
            myCow.Milk();
        }
        else
        {
            Console.WriteLine("{0} isn't a cow, and so can't be milked.", 
                myAnimal.Name);
        }
    }
```

&emsp;&emsp;这要比检查异常简单得多！

🔚


