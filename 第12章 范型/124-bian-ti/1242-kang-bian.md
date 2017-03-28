##抗变

&emsp;&emsp;要把泛型类型参数定义为抗变，可在类型定义中使用in关键字：

```csharp
    public interface IGrassMuncher<in T>
    {
        ...
    }
```

&emsp;&emsp;**对于接口定义，抗变类型参数只能用作方法参数，不能用作返回类型。**

&emsp;&emsp;理解这一点的最佳方式是列举一个在.NET Framework中使用抗变的例子。带有抗变类型参数的一个接口是前面用过的`IComparer<T>`。可以给Animal实现这个接口，如下所示：

```csharp
    public class AnimalNameLengthComparer : IComparer<Animal>
    {
        public int Compare(Animal x, Animal y)
        {
            return x.Name.Length.CompareTo(y.Name.Length);
        }
    }
```

&emsp;&emsp;这个比较器按名称的长度比较动物，所以可以使用它对`List<Animal>`的实例排序。通过抗变，还可以使用它对`List<Cow>`的实例排序，尽管`List<Cow>.Sort()`方法需要`IComparer<Cow>`的实例。

&emsp;&emsp;这个比较器按名称的长度比较动物，所以可以使用它对`List<Animal>`的实例排序。通过抗变，还可以使用它对`List<Cow>`的实例排序，尽管`List<Cow>.Sort()`方法需要`IComparer<Cow>`的实例。

```csharp
    List<Cow> cows = new List<Cow>();
    cows.Add(new Cow("Geronimo"));
    cows.Add(new SuperCow("Tonto"));
    cows.Add(new Cow("Gerald"));
    cows.Add(new Cow("Phil"));
    cows.Sort(new AnimalNameLengthComparer());
```

&emsp;&emsp;大多数情况下，抗变都会发生---它被添加到.NET Framework中就是为了帮助执行这种操作。.NET 4及更高版本中这两种变体的优点是，你可以在需要时使用本节介绍的技术实现它。


🔚