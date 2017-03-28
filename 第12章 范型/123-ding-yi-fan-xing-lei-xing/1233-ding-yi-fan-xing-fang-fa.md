##定义泛型方法

&emsp;&emsp;上个示例中使用了方法GetCows()。在讨论这个示例时也提到，可以使用泛型方法得到这个方法的更一般形式。本节将说明如何达到这一目标。在泛型方法中，返回类型和／或参数类型由泛型类型参数来确定。例如：

```csharp
    public T GetDefault<T>()
    {
        return default(T);
    }
```

&emsp;&emsp;这个小示例使用本章前面介绍的default关键字，为类型T返回默认值。这个方法的调用如下所示：

```csharp
    int myDefault = GetDefault<int>();
```

&emsp;&emsp;在调用该方法时提供了类型参数T。

&emsp;&emsp;这个T与用于给类提供泛型类型参数的类型差异极大。实际上，可以通过非泛型类来实现泛型方法：

```csharp
    public class Defaulter
    {
        public T GetDefault<T>()
        {
            return default(T);
        }
    }
```

&emsp;&emsp;但如果类是泛型的，就必须为泛型方法类型使用不同的标识符。下面的代码不会编译：

```csharp
    public class Defaulter<T>
    {
        public T GetDefault<T>()   // ❌
        {
            return default(T);
        }
    }
```

&emsp;&emsp;必须重命名方法或类使用的类型T。

&emsp;&emsp;泛型方法参数可以采用与类相同的方式使用约束，在此可以使用任意的类类型参数，例如：

```csharp
    public class Defaulter<T1>
    {
        public T2 GetDefault<T2>()
        where T2 : T1
        {
            return default(T2);
        }
    }
```

&emsp;&emsp;其中，为方法提供的类型T2必须与给类提供的T1相同，或者继承自T1。这是约束泛型方法的常用方式。

&emsp;&emsp;在前面的`Farm<T>`类中，可以包含下面的方法：

```csharp
    public Farm<U> GetSpecies<U>() where U : T
    {
        Farm<U> speciesFarm = new Farm<U>();
        foreach(T animal in animals)
        {
            if(animal is U)
            {
                speciesFarm.Animals.Add(animal as U);
            }
        }
        return speciesFarm;
    }
```

&emsp;&emsp;这可以替代GetCows()和相同类型的其他方法。这里使用的泛型类型参数U由T约束，T又由`Farm<T>`类约束为Animal。因此，如果愿意，可以把T的实例视为Animal的实例。

&emsp;&emsp;在Program.cs中，使用这个新方法需要进行一处修改：

```csharp
    Farm<Cow> dairyFarm = farm.GetSpecies<Cow>();
```

&emsp;&emsp;也可以编写如下代码：

```csharp
    Farm<Chicken> poultryFarm = farm.GetSpecies<Chicken>();
```

&emsp;&emsp;对于继承了Animal的其他类，都可以使用这种方法。

&emsp;&emsp;这里要注意，如果某个方法有泛型类型参数，会改变该方法的签名。也就是说，该方法有几个重载版本，它们仅在泛型类型参数上有区别。例如：

```csharp
    public void ProcessT<T>(T op1)
    {
        ...
    }
    
    public vid ProcessT<T, U>(T op1)
    {
        ...
    }
```

&emsp;&emsp;使用哪个方法取决于调用方法时指定的泛型类型参数的个数。



🔚
