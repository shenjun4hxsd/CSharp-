##定义泛型委托

&emsp;&emsp;最后一个要介绍的泛型类型时泛型委托。本章前面在介绍如何排序和搜索泛型列表时曾介绍过它们，即分别为次使用了`Comparison<T>`和`Predicate<T>`委托。

&emsp;&emsp;第6章介绍了如何使用方法的参数和返回类型、delegate关键字和委托名来定义委托，例如：

```csharp
    public delegate int MyDelegate(int op1, int op2);
```

&emsp;&emsp;要定义泛型委托，只需声明和使用一个或多个泛型类型参数，例如：

```csharp
    public delegate T1 MyDelegate<T1, T2>(T2 op1, T2 op2) where T1 : T2;
```

&emsp;&emsp;可以看出，也可以在这里使用约束。第13章将更详细地介绍委托，了解在常见的C#编程技术（即“事件”）中如何使用它们。


🔚