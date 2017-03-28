##变体

&emsp;&emsp;变体（variance）是协变（covariance）和抗变（contravariance）的统称，这两个概念在.NET 4中引入。实际上，它们已经存在了较长的时间了（在.NET 2.0中就可以使用），但在.NET 4之前很难实现它们，因为它们需要定制的编译过程。

&emsp;&emsp;要掌握这些术语的含义，最简单的方式是把它们与多态性进行比较。多态性允许把派生类型的对象放在基类型的变量中，例如：

```csharp
    Cow myCow = new Cow("Geronimo");
    Animal myAnimal = myCow;
```

&emsp;&emsp;其中把Cow类型的对象放在Animal类型的变量中，这是可行的，因为Cow派生自Animal。

&emsp;&emsp;但这不适用于接口，也就是说，下面的代码不能工作：

```csharp
    IMethaneProducer<Cow> cowMethaneProducer = myCow;
    IMethaneProdecer<Animal> animalMethaneProducer = cowMethaneProducer;   // ❌
```

&emsp;&emsp;假定Cow支持`IMethaneProducer<Cow>`接口，第一行代码就没有问题。但是，第二行代码预先假定两个接口类型有某种关系，但实际上这种关系不存在，所以无法把一种类型转换为另一种类型。是这样吗？使用本章前面介绍的技术肯定不行，因为泛型类型的所有类型参数都是不变的。但可以在泛型接口和泛型委托上定义变体类型参数，以适合上述代码演示的情形。

&emsp;&emsp;为使上述代码工作，`IMethaneProducer<T>`接口的类型参数T必须是协变的。有了协变的类型参数，就可以在`IMethaneProducer<Cow>`和`IMethaneProducer<Animal>`之间建立继承关系，这样一种类型的变量就可以包含另一种类型的值，这与多态性类似（但稍复杂些）。

&emsp;&emsp;为了完成对变体的介绍，需要看看变体的另一面：抗变。抗变和协变是类似的，但方向相反。**抗变不能像协变那样，把泛型接口值放在使用基类型的变量中，但可以把该接口放在使用派生类型的变量中**，例如：

```csharp
    IGrassMuncher<Cow> cowGrassMuncher = myCow;
    IGrassMuncher<SuperCow> superCowGrassMuncher = cowGrassMuncher;
```

&emsp;&emsp;初看起来似乎有点古怪，因为不能通过多态性完成相同的功能。但是这在一些情况下是一项有效的技术，如“抗变”一节所述。

&emsp;&emsp;接下来的两节将介绍如何在泛型类型中实现变体，以及.NET Framework如何使用变体简化编程。


🔚

