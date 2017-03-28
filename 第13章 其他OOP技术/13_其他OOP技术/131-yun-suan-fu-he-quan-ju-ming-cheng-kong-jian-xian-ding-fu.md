## `::`运算符和全局名称空间限定符

&emsp;&emsp;`::`运算符提供了另一种访问名称空间中类型的方式。如果要使用一个名称空间的别名，但该别名与实际名称空间层次结构之间的界限不清晰，就必须使用`::`运算符。在那种情况下，名称空间层次结构优先于名称空间别名。为阐明其含义，考虑下列代码：

```csharp
    using MyNamespaceAlias = MyRootNamespace.MyNestedNamespace;
    
    namespace MyRootNamespace
    {
        namespace MyNamespaceAlias
        {
            public class MyClass
            {
            }
        }
        
        namespace MyNestedNamespace
        {
            public class MyClass
            {
            }
        }
    }
```

&emsp;&emsp;MyRootNamespace中的代码使用以下代码引用一个类：

```csharp
    MyNamespaceAlias.MyClass
```

&emsp;&emsp;这行代码表示的类是MyRootNamespace.MyNamespaceAlias.MyClass类，而不是MyRootNamespace.MyNestedNamespace.MyClass类。也就是说，MyRootNamespace.MyNamespaceAlias名称空间隐藏了由using语句定义的别名，该别名指向MyRootNamespace.MyNestedNamespace名称空间。仍然可以访问这个名称空间以及其中包含的类，但需要使用不同的语法：

```csharp
    MyNestedNamespace.MyClass
```

&emsp;&emsp;另外，还可以使用`::`运算符：

```csharp
    MyNamespaceAlias::MyClass
```

&emsp;&emsp;**使用这个运算符会迫使编译器使用由using语句定义的别名**，因此代码指向MyRootNamespace.MyNestedNamespace.MyClass。

&emsp;&emsp;`::`运算符还可以与global关键字一起使用，它实际上是顶级根名称空间的别名。这有助于更清晰地说明要指向哪个名称空间，如下所示：

```csharp
    global::System.Collections.Generic.List<int>
```
&emsp;&emsp;这是希望使用的类，即`List<T>`泛型集合类。它肯定不是用下列代码定义的类：

```csharp
    namespace MyRootNamespace
    {
        namespace System
        {
            namespace Collections
            {
                namespace Generic
                {
                    class List<T>
                    {
                    }
                }
            }
        }
    }
```
&emsp;&emsp;当然，应避免使名称空间的名称与已有的.NET名称空间相同，但这个问题只在大型项目中才会出现，作为大型开发队伍中的一员进行开发时，此类问题尤其严重。使用`::`运算符和global关键字可能是访问所需类型的唯一方式。


🔚


