##定义泛型接口

&emsp;&emsp;前面介绍了几个泛型接口，它们都位于System.Collections.Generics名称空间中，例如，上一个示例中使用的`IEnumerable<T>`。定义泛型接口与定义泛型类所用的技术相同，例如：

```csharp
    interface MyFarmingInterface<T>
    where T : Animal
    {
        bool AttemptToBreed(T animal1, T animal2);
        T OldestInHead{ get; }
    }
```

&emsp;&emsp;其中，泛型参数T用作AttemptToBreed()的两个实参的类型和OldestInHead属性的类型。

&emsp;&emsp;其继承规则与类相同。如果继承了一个基泛型接口，就必须遵循这些规则，例如保持基接口泛型类型参数的约束。


🔚