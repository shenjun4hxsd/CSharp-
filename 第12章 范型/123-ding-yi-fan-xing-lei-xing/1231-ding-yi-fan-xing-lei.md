##定义泛型类

&emsp;&emsp;要创建泛型类，只需在类定义中包含尖括号语法：

```csharp
    class MyGenericClass<T>
    {
        ...
    }
```

&emsp;&emsp;其中T可以是任意标识符，只要遵循通常的C#命名规则即可，例如，不以数字开头等。但一般只使用T。泛型类可以在其定义中包含任意多个类型参数，它们用逗号分隔开，例如：

```csharp
    class MyGenericClass<T1, T2, T3>
    {
        ...
    }
```

&emsp;&emsp;定义了这些类型后，就可以在类定义中像使用其他类型那样使用它们。可以把它们用作成员变量的类型、属性或方法等成员的返回类型以及方法的参数类型等。例如：

```csharp
    class MyGenericClass<T1, T2, T3>
    {
        private T1 innerT1Object;
        
        public MyGenericClass(T1 item)
        {
            innerT1Object = item;
        }
        
        public T1 InnerT1Object
        {
            get
            {
                return innerT1Object;
            }
        }
    }
```


&emsp;&emsp;其中，类型T1的对象可以传递给构造函数，只能通过InnerT1Object属性对这个对象进行只读访问。注意，不能假定为类提供了什么类型。例如，下面的代码就不会编译：

```csharp
    class MyGenericClass<T1, T2, T3>
    {
        private T1 innerT1Object;
        
        public MyGenericClass()
        {
            innerT1Object = new T1();
        }
        
        public T1 InnerT1Object
        {
            get
            {
                return innerT1Object;
            }
        }
    }
```

&emsp;&emsp;我们不知道T1是什么，也就不能使用它的构造函数，它甚至可能没有构造函数，或者没有可公共访问的默认构造函数。如果不使用涉及本节后面介绍的高级技术的复杂代码，则只能对T1进行如下假设：可以把它看成继承自System.Object的类型或可以封箱到System.Object中的类型。

&emsp;&emsp;显然，这意味着不能对这个类型的实例进行非常有趣的操作，或者对为MyGenericClass泛型类提供的其他类型进行有趣的操作。不使用反射（这是用于在运行期间检查类型的高级技术，本章不介绍它），就只能使用下面的代码：

```csharp
    public string GetAllTypesAsString()
    {
        return "T1 = " + typeof(T1).ToString()
            + ", T2 = " + typeof(T2).ToString()
            + ", T3 = " + typeof(T3).ToString();
    }
```

&emsp;&emsp;可以做一些其他工作，尤其是对集合进行操作，因为处理对象组是非常简单的，不需要对对象类型进行任何假设，这是为什么存在本章前面介绍的泛型集合类的一个原因。

&emsp;&emsp;另一个需要注意的限制是，**在比较为泛型类型提供的类型值和`null`时**，只能使用运算符`==`和`!=`。例如，下面的代码会正常工作：

```csharp
    public bool Compare(T1 op1, T1 op2)
    {
        if(op1 != null && op2 != null)
        {
            return true;
        }
        else
        {
            return false;
        }
    }
```
&emsp;&emsp;其中，如果T1是一个值类型，则总是假定它是非空的，于是在上面的代码中，Compare总是返回true。但是，下面试图比较两个实参op1和op2的代码将不能编译：

```csharp
    public bool Compare(T1 op1, T1 op2)
    {
        if(op1 == op2)   // ❌
        {
            return true;
        }
        else
        {
            return false;
        }
    }
```

&emsp;&emsp;其原因是这段代码假定T1支持`==`运算符。这说明，要对泛型进行实际的操作，需要更多地了解类中使用的类型。


####&emsp;&emsp;1. default关键字

&emsp;&emsp;要确定用于创建泛型类实例的类型，需要了解一个最基本的情况：它们是引用类型还是值类型。若不知道这个情况，就不能用下面的代码赋予`null`值：

```csharp
    public MyGenericClass()
    {
        innerT1Object = null;
    }
```

&emsp;&emsp;如果T1是值类型，则innerT1Object不能取`null`值，所以这段代码不会编译。幸好，开发人员考虑到了这个问题，使用default关键字（本书前面在switch结构中使用过它）的新用法解决了它。该新用法如下：

```csharp
    public MyGenericClass()
    {
        innerT1Object = default(T1);
    }
```

&emsp;&emsp;其结果是，如果innerT1Object是引用类型，就给它赋予`null`值；如果它是值类型，就给它赋予默认值。对于数字类型，这个默认值是0；而结构根据其各个成员的类型，以相同的方式初始化为0或`null`。default关键字允许对必须使用的类型执行更多操作，但为了更进一步，还需要限制所提供的类型。


####&emsp;&emsp;2. 约束类型

&emsp;&emsp;前面用于泛型类的类型称为无绑定（unbounded）类型，因为没有对它们进行任何约束。而通过约束（constraining）类型，可以限制可用于实例化泛型类的类型，这有许多方式。例如，可以把类型限制为继承自某个类型。回顾前面使用的Animal、Cow和Chicken类，你可以把一个类型限制为Animal或继承自Animal，则下面的代码是正确的：

```csharp
    MyGenericClass<Cow> = new MyGenericClass<Cow>();
```

&emsp;&emsp;但下面的代码不能编译：

```csharp
    MyGenericClass<string> = new MyGenericClass<string>();
```

&emsp;&emsp;在类定义中，这可以使用`where`关键字来实现：

```csharp
    class MyGenericClass<T> where T: constraint
    {
        ...
    }
```

&emsp;&emsp;其中constraint定义了约束。可以用这种方式提供许多约束，各个约束之间用逗号分开：

```csharp
    class MyGenericClass<T> where T: constraint1, constraint2
    {
        ...
    }
```

&emsp;&emsp;还可以使用多个where语句，定义泛型类需要的任意类型或所有类型上的约束：

```csharp
    class MyGenericClass<T1, T2> where T1: constraint1 where T2 : constraint2
    {
        ...
    }
```

&emsp;&emsp;约束必须出现在继承说明符的后面：

```csharp
    class MyGenericClass<T1, T2> : MyBaseClass, IMyInterface
    where T1: constraint1 where T2: constraint2
    {
        ...
    }
```

&emsp;&emsp;表12-5中列出了一些可用的约束。

表12-5 泛型类型约束

|约束|定义|用法示例|
|:--|:--|:--|
|struct|类型必须是值类型|在类中，需要值类型才能起作用，例如，T类型的成员变量是0，表示某种含义|
|class|类型必须是引用类型|在类中，需要引用类型才能起作用，例如，T类型的成员变量是null，表示某种含义|
|base-class|类型必须是基类或继承自基类。可以给这个约束提供任意类名|在类中，需要接口公开的某种基本功能，才能起作用|
|interface|类型必须是接口或实现了接口|在类中，需要接口公开的某种基本功能，才能起作用|
|new()|类型必须有一个公共的无参构造函数|在类中，需要能实例化T类型的变量，例如在构造函数中实例化|

>&emsp;&emsp;**如果new()用作约束，它就必须是为类型指定的最后一个约束。**

&emsp;&emsp;可以通过base-class约束，把一个类型参数用作另一个类型参数的约束，如下所示：

```csharp
    class MyGenericClass<T1, T2> where T2 : T1
    {
        ...
    }
```

&emsp;&emsp;其中，T2必须与T1的类型相同，或者继承自T1。这称为裸类型约束（naked type constraint），表示一个泛型类型参数用作另一个类型参数的约束。

&emsp;&emsp;类型约束不能循环，例如：

```csharp
    class MyGenericClass<T1, T2> where T2 : T1 where T1 : T2
    {
        ...
    }
```

&emsp;&emsp;这段代码不能编译。下面的示例将定义和使用一个泛型类，该类使用前面几章介绍的Animal类系列。

```csharp
    public abstract class Animal
    {
        ...
        public abstract void MakeANoise();
    }

    public class Chicken : Animal
    {
        ...
        public override void MakeANoise()
        {
            Console.WriteLine("{0} says says 'cluck!';", name);
        }
    }

    public class Cow : Animal
    {
        ...
        public override void MakeANoise()
        {
            Console.WriteLinie("{0} says 'moo!'", name);
        }
    }

    public class SuperCow : Cow
    {
        public void Fly()
        {
            Console.WriteLine("{0} is flying!", name);
        }

        public SuperCow(string newName) : base(newName)
        {
        }

        public override void MakeANoise()
        {
            Console.WriteLine(
                "{0} says 'here I come to save the day!'", name);
        }
    }
```

&emsp;&emsp;新添加一个新类Farm，并修改Farm.cs中的代码，如下所示：

```csharp
    using System;
    using System.Collections;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;

    namespace Ch12Ex04
    {
        public class Farm<T> : IEnumerable<T>
            where T : Animal
        {
            private List<T> animals = new List<T>();

            public List<T> Animals
            {
                get
                {
                    return animals;
                }
            }

            public IEnumerator<T> GetEnumerator()
            {
                return animals.GetEnumerator();
            }

            IEnumerator IEnumerable.GetEnumerator()
            {
                return animals.GetEnumerator();
            }

            public void MakeNoises()
            {
                foreach(T animal in animals)
                {
                    animal.MakeANoise();
                }
            }

            public void FeedTheAnimals()
            {
                foreach(T animal in animals)
                {
                    animal.Feed();
                }
            }

            public Farm<Cow> GetCows()
            {
                Farm<Cow> cowFarm = new Farm<Cow>();
                foreach(T animal in animals)
                {
                    if(animal is Cow)
                    {
                        cowFarm.Animals.Add(animal as Cow)
                    }
                }
                return cowFarm;
            }
        }
    }
```

&emsp;&emsp;修改Program.cs，如下所示：

```csharp
    static void Main(string[] args)
    {
        Farm<Animal> farm = new Farm<Animal>();
        farm.Animals.Add(new Cow("Jack"));
        farm.Animals.Add(new Chicken("Vera"));
        farm.Animals.Add(new Chicken("Sally"));
        farm.Animals.Add(new SuperCow("Kevin"));
        farm.MakeNoises();

        Farm<Cow> dairyFarm = farm.GetCows();
        dairyFarm.FeedTheAnimals();

        foreach(Cow cow in dairyFarm)
        {
            if(cow is SuperCow)
            {
                (cow as SuperCow).Fly();
            }
        }
        Console.ReadKey();
    }
```

>&emsp;&emsp;**示例的说明**

>&emsp;&emsp;在这个示例中，创建了一个泛型类`Farm<T>`，它没有继承泛型List类，而将泛型List类作为公共属性公开，该List的类型由传送给`Farm<T>`的类型参数T确定，且被约束为Animals，或者继承自Animal。

>```csharp
        public class Farm<T> : IEnumerable<T>
            where T : Animal
        {
            private List<T> animals = new List<T>();

            public List<T> Animals
            {
                get
                {
                    return animals;
                }
            }
```


>&emsp;&emsp;`Farm<T>`还实现了`IEnumerable<T>`，其中，T传递给这个泛型接口，因此也以相同的方式进行了约束。实现这个接口，就可以迭代包含在`Farm<T>`中的项，而不必显式迭代`Farm<T>.Animals`。很容易就能做到这一点，只需返回Animals公开的枚举器即可，该枚举器是一个`List<T>`类，也实现了`IEnumerable<T>`。

>```csharp
            public IEnumerator<T> GetEnumerator()
            {
                return animals.GetEnumerator();
            }
```

>&emsp;&emsp;因为`IEnumerable<T>`继承自IEnumerable，所以还需要实现`IEnuerable.GetEnumerator()`：

>```csharp
            IEnumerator IEnumerable.GetEnumerator()
            {
                return animals.GetEnumerator();
            }
```

>&emsp;&emsp;之后，`Farm<T>`包含的两个方法利用了抽象类Animal的方法：

>```csharp
            public void MakeNoises()
            {
                foreach(T animal in animals)
                {
                    animal.MakeANoise();
                }
            }

            public void FeedTheAnimals()
            {
                foreach(T animal in animals)
                {
                    animal.Feed();
                }
            }
```

>&emsp;&emsp;T被约束为Animal，所以这段代码会正确编译---无论T是什么，都可以访问MakeNoise()和Feed()方法。

>&emsp;&emsp;下一个方法GetCows()更加有趣。这个方法提取了集合类型为Cow（或继承自Cow，例如，新的SuperCow类）的所有项：

>```csharp
            public Farm<Cow> GetCows()
            {
                Farm<Cow> cowFarm = new Farm<Cow>();
                foreach(T animal in animals)
                {
                    if(animal is Cow)
                    {
                        cowFarm.Animals.Add(animal as Cow)
                    }
                }
                return cowFarm;
            }
```

>&emsp;&emsp;有趣的是，这个方法似乎有点浪费。如果以后希望有同一系列的其他方法，如GetChickens()，也需要显式实现它们。在使用许多类型的系统中，需要更多方法。一个较好的解决方案是使用泛型方法，详见本章后面的内容。

>&emsp;&emsp;Program.cs中的客户代码测试了Form的各个方法，它包含的代码大多已在前面列出，所以不需要深入探讨这些代码。


---


####&emsp;&emsp;3. 从泛型类中继承

&emsp;&emsp;上例中的`Farm<T>`类以及本章节前面介绍的其他几个类都继承自一个泛型类型。在`Farm<T>`中，这个类型是一个接口`IEnumerable<T>`。这里`Farm<T>`在T上提供的约束也会在`IEnumerable<T>`中使用的T上添加一个额外的约束。这可以用于限制未约束的类型，但需要遵循一些规则。

&emsp;&emsp;首先，如果某个类型所继承的基类型中受到了约束，该类型就不能“解除约束”。也就是说，类型T在所继承的基类型中使用时，该类型必须受到至少与基类型相同的约束。例如，下面的代码是正确的：

```csharp
    class SuperFarm<T> : Farm<T>
        where T : SuperCow
    {
    }
```

&emsp;&emsp;因为T在`Farm<T>`中被约束为Animal，把它约束为SuperCow，就是把T约束为这些值的一个子集，所以这是可行的。但是，以下代码不会编译：

```csharp
    class SuperFarm<T> : Farm<T>
        where T : struct            // ❌
    {
    }
```

&emsp;&emsp;可以肯定地讲，提供给`SuperFarm<T>`的类型T不能转换为可由`Farm<T>`使用的T，所以代码不会编译。

&emsp;&emsp;甚至对于约束为超集的情况，也会出现相同的问题：

```csharp
    class SuperFarm<T> : Farm<T>
        where T : class
    {
    }
```

&emsp;&emsp;即使`SuperFarm<T>`允许有像Animal这样的类型，`Farm<T>`中也不允许有满足类约束的其他类型。否则编译就会失败。这个规则适用于本章前面介绍的所有约束类型。



&emsp;&emsp;另外，如果继承了一个泛型类型，就必须提供所有必须的类型信息，这可以使用其他泛型类型参数的形式上提供，如上所述，也可以显式提供。这也适用于继承了泛型类型的非泛型类。例如：

```csharp
    public class Cards : List<Card>, ICloneable
    {
    }
```

&emsp;&emsp;这是可行的，但下面的代码会失败：

```csharp
    public class Cards : List<T>, ICloneable
    {
    }
```

&emsp;&emsp;因为没有提供T的信息，所以无法编译。

>&emsp;&emsp;**如果给泛型类型提供了参数，例如，上面的List<Card>，就可以称该类型是“关闭的”。同样，继承List<T>，就是继承一个“打开”的泛型类型。**


---

####&emsp;&emsp;4. 泛型运算符

&emsp;&emsp;在C#中，可以像其他方法一样进行运算符的重写，这也可以在泛型类中实现此类重写。例如，可在Farm<T>中定义如下隐式的转换运算符：

```csharp
    public static implicit operator List<Animal>(Farm<T> farm)
    {
        List<Animal> result = new List<Animal>();
        foreach(T animal in farm)
        {
            result.Add(animal);
        }
        return result;
    }
```

&emsp;&emsp;这样，如有必要，就可以在`Farm<T>`中把Animal对象直接作为`List<Animal>`来访问。例如，使用下面的运算符添加两个`Farm<T>`实例，这是很方便的：

```csharp
    public static Farm<T> operator + (Farm<T> farm1, List<T> farm2)
    {
        Farm<T> result = new Farm<T>();
        foreach(T animal in farm1)
        {
            result.Animals.Add(animal);
        }
        foreach(T animal in farm2)
        {
            if(!result.Animals.Contains(animal))
            {
                result.Animals.Add(animal);
            }
        }
        return result;
    }

    public static Farm<T> operator + (List<T> farm1, Farm<T> farm2)
    {
        return farm2 + farm1;
    }
```

&emsp;&emsp;接着可以添加Farm<Animal>和Farm<Cow>的实例，如下所示：

```csharp
    Farm<Animal> newFarm = farm + dairyFarm;
```

&emsp;&emsp;在这行代码中，dairyFarm（是`Farm<Cow>`的实例）隐式转换为`List<Animal>`，`List<Animal>`可以在`Farm<T>`中由重载运算符`+`使用。

&emsp;&emsp;读者可能认为，使用下面的代码也可以做到这一点：

```csharp
    public static Farm<T> operator + (Farm<T> farm1, Farm<T> farm2)
    {
        ...
    }
```

&emsp;&emsp;但是，`Farm<Cow>`不能转换为`Farm<Animal>`，所以汇总会失败。为了更进一步，可以使用下面的转换运算符来解决这个问题：

```csharp
    public static implicit operator Farm<Animal>(Farm<T> farm)
    {
        Farm<Animal> result = new Farm<Animal>();
        foreach(T animal in farm)
        {
            result.Animals.Add(animal);
        }
        return result;
    }
```

&emsp;&emsp;使用这个运算符，`Farm<T>`的实例（如`Farm<Cow>`）就可以转换为`Farm<Animal>`的实例，这解决了上面的问题。所以，可以使用上面列出的两种方法中的一种，但是后者更适合，因为它比较简单。

---

####&emsp;&emsp;5. 泛型结构

&emsp;&emsp;前几章说过，结构实际上与类相同，只有一些微小的区别，而且结构是值类型，不是引用类型。所以，可以用与泛型类相同的方式来创建泛型结构。例如：

```csharp
    public struct MyStruct<T1, T2>
    {
        public T1 item1;
        public T2 item2;
    }
```


🔚