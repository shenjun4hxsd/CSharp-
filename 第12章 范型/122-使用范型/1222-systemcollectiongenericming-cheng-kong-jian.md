## System.Collections.Generic 名称空间

&emsp;&emsp;实际上，本书前面的每个应用程序都包含如下名称空间：

```csharp
    using System;
    using System.Collentions.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
```

&emsp;&emsp;System名称空间包含.NET应用程序使用的大多数基本类型。System.Text名称空间包含与字符串和编码相关的类型，System.Linq名称空间将从第23章开始介绍。System.Threading.Tasks名称空间包含帮助编写异步代码的类型，本书中不予讨论。但System.Collections.Generic名称空间包含什么类型？为什么要在默认情况下把它包含在控制台应用程序中？

&emsp;&emsp;这个名称空间包含用于处理集合的泛型类型，使用得非常频繁，所以用`using`语句配置它，这样使用时就不必添加限定符了。

&emsp;&emsp;本章前面提到过这些泛型类型，下面将予以介绍，它们可以使工作更容易完成，可以毫不费力地创建强类型化的集合类。表12-2描述了本节要介绍的System.Collections.Generics名称空间中的两个类型，本章后面还会详细阐述这个名称空间中的更多类型。

<br>

表12-2泛型集合类型

|类型|说明|
|:--|:--|
|`List<T>`|T类型对象的集合|
|`Dictionary<K, V>`|与K类型的键值相关的V类型的项的集合|

&emsp;&emsp;后面还会介绍和这些类一起使用的各种接口和委托。

####&emsp;&emsp;1. List<T>

&emsp;&emsp;`List<T>`泛型集合类型更加快捷、更易于使用；这样，就不必像上一章那样，从CollectionsBase中派生一个类，然后实现需要的方法。它的另一个好处是正常情况下需要实现的许多方法（例如，`Add()`）已经自动实现了。

&emsp;&emsp;创建T类型对象的集合需要如下代码：

```csharp
    List<T> myCollection = new List<T>();
```

&emsp;&emsp;这就足够了。未必要定义类、实现方法或执行其他操作。还可以把`List<T>`对象传送给构造函数，在集合中设置项的起始列表。使用这个语法实例化的对象将支持表12-3中的方法和属性（其中，提供给`List<T>`泛型的类型是T）。

表12-3 `List<T>`支持的方法和属性

|成员|说明|
|:--|:--|
|`int Count`|该属性给出集合中项的个数|
|`void Add(T item)`|把一个项添加到集合中|
|`void AddRange(IEnumerable<T>)`|把多个项添加到集合中|
|`IList<T> AsReadOnly()`|给集合返回一个只读接口|
|`int Capacity`|获取或设置集合可以包含的项数|
|`void Clear()`|删除集合中的所有项|
|`bool Contains(T item)`|确定item是否包含在集合中|
|`void CopyTo(T[] array, int index)`|把集合中的项复制到数组array中，从数组的索引index开始|
|`IEnumerator<T> GetEnumerator()`|获取一个`IEnumerator<T>`实例，用于迭代集合。注意，返回的接口强类型化为T，所以在foreach循环中不需要类型转换|
|`int IndexOf(T item)`|获取item的索引，如果集合中并未包含该项，就返回-1|
|`void Insert(int index, T item)`|把item插入到集合的指定索引位置上|
|`bool Remove(T item)`|从集合中删除第一个item，并返回true；如果item不包含在集合中，就返回false|
|`void RemoveAt(int index)`|从集合中删除索引index处的项|

&emsp;&emsp;`List<T>`还有一个`Item`属性，允许进行类似于数组的访问，如下所示：

```csharp
    T itemAtIndex2 = myCollectionOfT[2];
```

&emsp;&emsp;这个类还支持其他几个方法，但只要掌握了上述知识，就完全可以开始使用该类了。下面的示例将介绍如何实际使用`List<T>`。

```csharp
    static void Main(string[] args)
    {
        List<Animal> animalCollection = new List<Animal>();
        animalCollection.Add(new Cow("Jack"));
        animalCollection.Add(new Chicken("Vera"));
        
        foreach(Animal myAnimal in animalCollection)
        {
            myAnimal.Feed();
        }
        Console.ReadKey();
    }
```

>&emsp;&emsp;**示例的说明**

>&emsp;&emsp;这个示例与Ch11Ex02只有两个区别。第一个区别是下面的代码行：

>```csharp
    Animals animalCollection = new Animals();
```

>&emsp;&emsp;被替换为：

>```csharp
    List<Animal> animalCollection = new List<Animal>();
```
>&emsp;&emsp;第二个区别比较重要：项目中不再有Animals集合类。通过使用泛型的集合类，前面为创建这个类所做的工作现在用一行代码即可完成。

>&emsp;&emsp;获得相同效果的另一个方法是不修改Program.cs中的代码，而是使用Animals的如下定义：

>```csharp
    public class Animals : List<Animal>
    {
    }
```

>&emsp;&emsp;这么做的优点是，能较容易地看懂Program.cs中的代码，还可以在合适时给Animals类添加额外的成员。

&emsp;&emsp;为什么不从CollectionBase中派生类？这是一个很好的问题。实际上，在许多情况下，我们都不会从CollectionBase中派生类。知道内部工作原理肯定是件好事，因为`List<T>`以相同的方式工作，但CollectionBase主要用于向后兼容。使用CollectionBase的唯一场合是要更多地控制向类的用户展示的成员。例如，如果希望集合类的Add()方法使用内部访问修饰符，则使用CollectionBase是最合理的选择。

>&emsp;&emsp;**也可以把要使用的初始容量（作为int）传递给List<T>的构造函数，或者传递使用IEnumerable<T>接口的初始项列表。支持这个接口的类包括List<T>。**


---

####&emsp;&emsp;2. 对泛型列表进行排序和搜索

&emsp;&emsp;对泛型列表进行排序与对其他列表进行排序是一样的。第11章介绍了如何使用IComparer和IComparable接口比较两个对象，然后对该类型的对象列表排序。这里唯一的区别在于，可以使用泛型接口`IComparer<T>`和`IComparable<T>`，它们提供了略有区别的、针对特定类型的方法。表12-4列出了它们的区别。

表12-4 对泛型列表进行排序

|泛型方法|非泛型方法|区别|
|:--|:--|:--|
|`int IComparable<T>.CompareTo(T otherObj)`|int IComparable.CompareTo(object otherObj)|在泛型版本中是强类型化的|
|`bool IComparable<T>.Equals(T otherObj)`|N/A|在非泛型接口中不存在，可以改用继承的object.Equals()|
|`int IComparer<T>.Comparer(T objectA, T objectB)`|int IComparer.Compare(object objectA, object objectB)|在泛型版本中是强类型化的|
|`bool IComparer<T>.Equals(T objectA, T objectB)`|N/A|在非泛型接口中不存在，可以改用继承的object.Equals()|
|`int IComparer<T>.GetHashCode(T objectA)`|N/A|在非泛型接口中不存在，可以该用继承的object.GetHashCode()|

&emsp;&emsp;要对`List<T>`排序，可以在要排序的类型上提供`IComparable<T>`接口，或者提供`IComparer<T>`接口。另外，还可以提供泛型委托，作为排序方法。从了解工作原理的角度看，这非常有趣，因为实现上述接口并不比实现非泛型版本更麻烦。



&emsp;&emsp;一般情况下，给列表排序需要有一个方法来比较两个T类型的对象。要在列表中搜索，只需要一个方法来检查T类型的对象，看看它是否满足某个条件。定义这样的方法很简单，这里给出两个可以使用的泛型委托类型：

• Comparison<T>：这个委托类型用于排序方法，其返回类型和参数如下：

```csharp
    int method(T objectA, T objectB)
```

• Predicate<T>：这个委托类型用于搜索方法，其返回类型和参数如下：

```csharp
    bool method(T targetObject)
```

&emsp;&emsp;可以定义任意多个这样的方法，使用它们实现 `List<T>`的搜索和排序方法。下面的示例进行了演示。

```csharp
    public class Vectors : List<Vector>
    {
        public Vectors()
        {
        }

        public Vectors(IEnumerable<Vector> initialItems)
        {
            foreach(Vector vector in initialitems)
            {
                Add(vector);
            }
        }

        public string Sum()
        {
            StringBuilder sb = new StringBuilder();
            Vector currentPoint = new Vector(0.0, 0.0);
            sb.Append("origin");
            foreach(Vector vector in this)
            {
                sb.AppendFormat(" + {0}", vector);
                currentPoint += vector;
            }
            sb.AppendFormat(" = {0}", currentPoint);
            return sb.ToString();
        }
    }
```

&emsp;&emsp;添加一个新类VectorDelegates。
&emsp;&emsp;修改VectorDelegates.cs中的代码，如下所示：

```csharp
    public static class VectorDelegates
    {
        public static int Compare(Vector x, Vector y)
        {
            if(x.R > y.R)
            {
                return 1;
            }
            else if(x.R < y.R)
            {
                return -1;
            }
            return 0;
        }

        public static bool TopRightQuadrant(Vector target)
        {
            if(target.Theta >= 0.0 && target.Theta <= 90.0)
            {
                return true;
            }
            else
            {
                return false;
            }
        }
    }
```

&emsp;&emsp;修改Program.cs中的代码，如下所示：

```csharp
    static void Main(string[] args)
    {
        Vectors route = new Vectors();
        route.Add(new Vector(2.0, 90.0));
        route.Add(new Vector(1.0, 180.0));
        route.Add(new Vector(0.5, 45.0));
        route.Add(new Vector(2.5, 315.0));

        Console.WriteLine(route.Sum());

        Comparison<Vector> sorter = new Comparison<Vector>(
            VectorDelegates.Compare);
        route.Sort(sorter);
        Console.WriteLine(route.Sum());

        Predicate<Vector> searcher = 
            new Predicate<Vector>(VectorDelegates.TopRightQuadrant);
        Vectors topRightQuadrantRoute = new Vectors(route.FindAll(searcher));
        Console.WriteLine(topRightQuadrantRoute.Sum());

        Console.ReadKey();
    }
```

>&emsp;&emsp;**示例的说明**

>&emsp;&emsp;在这个示例中，为Ch12Ex01中的Vector类创建了一个集合类Vectors。可以只使用`List<Vector>`类型的变量，但因为需要其他功能，所以使用了一个新类Vectors，它派生自`List<Vector>`，允许添加需要的其他成员。

>&emsp;&emsp;该类有一个返回字符串的成员Sum()，该字符串依次列出每个矢量，以及把它们加在一起（使用源类Vector的重载`+`运算符）的结果。每个矢量都可以看成“方向+距离”，所以这个矢量列表构成了一条有端点的路径。

>```csharp
        public string Sum()
        {
            StringBuilder sb = new StringBuilder();
            Vector currentPoint = new Vector(0.0, 0.0);
            sb.Append("origin");
            foreach(Vector vector in this)
            {
                sb.AppendFormat(" + {0}", vector);
                currentPoint += vector;
            }
            sb.AppendFormat(" = {0}", currentPoint);
            return sb.ToString();
        }
```

>&emsp;&emsp;该方法使用System.Text名称空间中简便的StringBuilder类来构建响应字符串。这个类包含Append()和AppendFormat()等成员（这里使用），所以很容易组合字符串，其性能也比串联各个字符串要高。使用这个类的ToString()方法即可获得最终的字符串。

>&emsp;&emsp;本例还创建了两个用作委托的方法，作为VectorDelegates的静态成员。Compare()用于比较（排序），TopRightQuadrant()用于搜索。稍后在分析Program.cs中的代码时介绍它们。

>&emsp;&emsp;Main()中的代码首先初始化Vectors集合，给它添加几个Vector对象：

>```csharp
        Vectors route = new Vectors();
        route.Add(new Vector(2.0, 90.0));
        route.Add(new Vector(1.0, 180.0));
        route.Add(new Vector(0.5, 45.0));
        route.Add(new Vector(2.5, 315.0));
```

>&emsp;&emsp;如前所述，Vectors.Sum()方法用于输出集合中的项，这次是按照其初始顺序输出：

>```csharp
    Console.WriteLine(route.Sum());
```

>&emsp;&emsp;接着创建第一个委托sorter，这个委托属于`Comparison<Vector>`类型，因此可以赋予带如下返回类型和参数的方法：

>```csharp
    int method(Vector objectA, Vector objectB)
```

>&emsp;&emsp;它匹配VectorDelegates.Compare()，该方法就是赋予委托的方法。

>```csharp
        Comparison<Vector> sorter = new Comparison<Vector>(
            VectorDelegates.Compare);
```

>&emsp;&emsp;Compare()比较两个矢量的大小，如下所示：

>```csharp
        public static int Compare(Vector x, Vector y)
        {
            if(x.R > y.R)
            {
                return 1;
            }
            else if(x.R < y.R)
            {
                return -1;
            }
            return 0;
        }
```

>&emsp;&emsp;这样就可以按大小对矢量排序了：

>```csharp
        route.Sort(sorter);
        Console.WriteLine(route.Sum());
```

>&emsp;&emsp;应用程序的输出结果符合我们的预期---汇总的结果是一样的，因为无论用什么顺序执行各个步骤，“矢量路径”的端点都是相同的。

>&emsp;&emsp;然后进行搜索，获取集合中的一个矢量子集。这需要使用VectorDelegates.TopRightQuadrant()来实现：

>```csharp
        public static bool TopRightQuadrant(Vector target)
        {
            if(target.Theta >= 0.0 && target.Theta <= 90.0)
            {
                return true;
            }
            else
            {
                return false;
            }
        }
```

>&emsp;&emsp;如果方法的Vector参数的Theta的值介于0°~90°之间，该方法就返回true，也就是说，它在前面的排序图中指向上或右。

>&emsp;&emsp;在Main()方法中，通过`Predicate<Vector>`类型的委托使用这个方法，如下所示：

```csharp
        Predicate<Vector> searcher = 
            new Predicate<Vector>(VectorDelegates.TopRightQuadrant);
        Vectors topRightQuadrantRoute = new Vectors(route.FindAll(searcher));
        Console.WriteLine(topRightQuadrantRoute.Sum());
```

>&emsp;&emsp;这需要在Vectors中定义构造函数：

>```csharp
        public Vectors(IEnumerable<Vector> initialItems)
        {
            foreach(Vector vector in initialitems)
            {
                Add(vector);
            }
        }
```

>&emsp;&emsp;其中，使用`IEnumerable<Vector>`的接口初始化了一个新的Vectors集合，这是必需的，因为`List<Vector>.FindAll()`返回一个`List<Vector>`实例，而不是Vectors实例。

>&emsp;&emsp;搜索的结果是，只返回Vector对象的一个子集，所以汇总的结果不同（这正是我们希望的）。使用这些泛型委托类型来排序和搜索泛型集合需要一段时间才能习惯，但代码更加流畅高效了，代码的结构更富逻辑性。最好花点时间研究本介绍的技术。

>&emsp;&emsp;另外，在这个示例中，注意下面的代码：

>```csharp
        Comparison<Vector> sorter = new Comparison<Vector>(
            VectorDelegates.Compare);
        route.Sort(sorter);
```

>&emsp;&emsp;可以简化为：

>```csharp
    route.Sort(VectorDelegates.Compare);
```

>&emsp;&emsp;这样就不需要隐式引用`Comparison<Vector>`类型了。实际上，仍会创建这个类型的一个实例，但它是隐式创建的。显然，Sort()方法需要这个类型的实例才能工作，但编译器会认识到这一点，在我们提供的方法中自动创建该类型的实例。此时，对VectorDelegates.Compare()的引用（没有括号）称为方法组。许多情况下，都可以使用方法组以这种方式隐式地创建委托，使代码变得更便于读取。



---

####&emsp;&emsp;3. Dictionary<K, V>

&emsp;&emsp;这个类型可以定义键／值对的集合。与本章前面介绍的其他泛型集合类型不同，这个类需要实例化两个类型，分别用于键和值，以表示集合中的各个项。

&emsp;&emsp;实例化Dictionary<K, V>对象后，就可以像在继承自DictionaryBase的类上那样，对它执行相同的操作，但要使用已有的类型安全的方法和属性。例如，使用强类型化的Add()方法添加键／值对。

```csharp
    Dictionary<string, int> things = new Dictionary<string, int>();
    things.Add("Green Things", 29);
    things.Add("Blue Things", 94);
    things.Add("Yellow Things", 34);
    things.Add("Red Things", 52);
    things.Add("Brown Things", 27);
```



&emsp;&emsp;可以使用Keys和Values属性迭代集合中的键和值：

```csharp
    foreach(string key in things.Keys)
    {
        Console.WriteLine(key);
    }

    foreach(int value in things.Values)
    {
        Console.WriteLine(value);
    }
```

&emsp;&emsp;还可以迭代集合中的各个项，把每个项作为一个KeyValuePair<K, V>实例来获取，这与第11章介绍的DictionaryEntry对象十分相似：

```csharp
    foreach(KeyValuePair<string, int> thing in things)
    {
        Console.WriteLine("{0} = {1}", thing.Key, thing.Value);
    }
```

&emsp;&emsp;对于Dictionary<K, V>要注意的一点是，每个项的键都必须是唯一的。如果要添加的项的键与已有项的键相同，就会抛出ArgumentException异常。所以，Dictionary<K, V>允许把`IComparer<K>`接口传递给其构造函数。如果要把自己的类用作键，且它们不支持IComparable或`IComparable<K>`接口，或者要使用非默认的过程比较对象，就必须把`IComparer<K>`接口传递给其构造函数。例如，在上面的示例中，可以使用不区分大小写的方法来比较字符串键：

```csharp
    Dictionary<string, int> things =
        new Dictionary<string, int>(StringComparer.CurrentCultureIgnoreCase);
```

如果使用下面的键，就会得到一个异常：

```csharp
    things.Add("Green Things", 29);
    things.Add("Green Things", 94);
```

&emsp;&emsp;也可以给构造函数传递初始容量（使用int）或项的集合（使用`IDictionary<K, V>`接口）。

####&emsp;&emsp;4. 修改CardLib以便使用泛型集合类

&emsp;&emsp;对前几章创建的CardLib项目可以进行简单的修改，即修改Cards集合类，以使用一个泛型集合类，这将减少许多行代码。对Cards的类定义需要做如下修改:

```csharp
    public class Cards : List<Card>, ICloneable
    {
         ...
    }
```

&emsp;&emsp;还可以删除Cards的所有方法，但Clone()和CopyTo()除外，因为Clone()是ICloneable需要的方法，而`List<Card>`提供的CopyTo()版本处理的是Card对象数组，而不是Cards集合。需要对Clone()做一些轻微的修改，因为`List<T>`没有定义List属性：

```csharp
    public object Clone()
    {
        Cards newCards = new Cards();
        foreach(Card sourceCard in this)
        {
            newCards.Add((Card)sourceCard.Clone())
        }
        return newCards;
    }
```




🔚