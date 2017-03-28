##协变

&emsp;&emsp;要把泛型类型参数定义为协变，可以在类型定义中使用`out`关键字，如下面的示例所示：

```csharp
    public interface IMethaneProducer<out T>
    {
        ...
    }
```

&emsp;&emsp;**对于接口定义，协变类型参数只能用作方法的返回值或属性`get`访问器。** ？？

&emsp;&emsp;说明协变用途的一个很好例子在.NET Framework中，即前面使用的`IEnumerable<T>`接口。在这个接口中，项类型T定义为协变，这表示可以把支持`IEnumerable<Cow>`的对象放在`IEnumerable<Animal>`类型的变量中。

&emsp;&emsp;因此下面的代码是有效的：

```csharp
    static void Main(string[] args)
    {
        List<Cow> cows = new List<Cow>();
        cows.Add(new Cow("Gernoimo"));
        cows.Add(new SuperCow("Tonto"));
        ListAnimals(cows);
        Console.ReadKey();
    }
    
    static void ListAnimals(IEnumerable<Animal> animals)
    {
        foreach(Animal animal in animals)
        {
            Console.WriteLine(animal.ToString());
        }
    }
```

&emsp;&emsp;其中cows变量的类型是`List<Cow>`，它支持`IEnumerable<Cow>`接口。通过协变，这个变量可以传送给需要`IEnumerable<Animal>`类型的参数的方法。回顾一下froeach循环的工作方式，就知道GetEnumerator()方法用于获取`IEnumerator<T>`的一个枚举器，该枚举器的Current属性用于访问项。`IEnumerator<T>`还将其类型参数定义为协变，这表示可以把它用作参数的get访问器，而且一切都运转良好。


🔚