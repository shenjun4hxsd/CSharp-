##集合初始化器

&emsp;&emsp;第5章描述了如何使用如下语法，用值来初始化数组：

```csharp
    int[] myIntArray = new int[5]{ 5, 7, 11, 2, 98 };
```

&emsp;&emsp;这是一种合并实例化和初始化数组的简捷方式。集合初始化器只是把这个语法扩展到集合上：

```csharp
    List<int> myIntCollection = new List<int> { 5, 7, 11, 2, 98 };
```

&emsp;&emsp;通过合并对象和集合初始化器，就可以用简洁的代码配置集合了。下面的代码：

```csharp
    List<Curry> curries = new List<Curry>();
    curries.Add(new Curry("Chicken", "Pathia", 6));
    curries.Add(new Curry("Vegetable", "Korma", 3));
    curries.Add(new Curry("Prawn", "Vindaloo", 9));
```
&emsp;&emsp;可以用如下代码替换：

```csharp
    List<Curry> curries = new List<Curry>
    {
        new Curry
        {
            MainIngredient = "Chicken",
            Style = "Pathia",
            Spiciness = 6
        },
        new Curry
        {
            MainIngredient = "Vegetable",
            Style = "Korma",
            Spiciness = 3
        },
        new Curry
        {
            MainIngredient = "Prawn",
            Style = "Vindaloo",
            Spiciness = 9
        }
    };
```

&emsp;&emsp;这非常适合于主要用于数据表示的类型，因此，集合初始化器和本书后面介绍的LINQ技术一起使用时效果极佳。

<br>

&emsp;&emsp;下面的示例说明了如何使用对象和集合初始化器。

```csharp
    static void Main(string[] args)
    {
        Farm<Animal> farm = new Farm<Animal>
        {
            new Cow { Name = "Norris" },
            new Chicken { Name = "Rita" },
            new Chicken(),
            new SuperCow { Name = "Chesney" }
        };
        farm.MakeNoises();
        Console.ReadKey();
    }
    
    public class Farm<T> : IEnumerable<T> where T : Animal
    {
        public void Add(T animal)
        {
            animals.Add(animal);
        }
        ...
    }
```

>&emsp;&emsp;**示例的说明**

>&emsp;&emsp;这个示例合并了对象和集合初始化器，用一个步骤创建并填充了一个对象集合。它使用前面章节介绍的农场对象集合，但需要对用于这些类的初始化器做两处修改。

>&emsp;&emsp;首先，给派生于Animal基类的类删除构造函数。可以删除这些构造函数，是因为它们设置了动物的Name属性，而这里使用对象初始化器来完成。另外，还可以添加默认的构造函数。无论采用哪种方式，在使用默认的构造函数时，会根据基类中的默认构造函数来初始化Name属性，代码如下：

```csharp
    public Animal()
    {
        name = "The animal with no name";
    }
```

&emsp;&emsp;但是，对象初始化器与派生于Animal类的类一起使用时，初始化器设置的任何属性都在对象实例化后设置，因此在执行这个基类的构造函数之后设置。如果Name属性的值作为对象初始化器的一部分提供，这个值就会覆盖默认值。在示例代码中，为添加到集合中的所有项设置了Name属性，只有一项除外。

&emsp;&emsp;其次，必须给Farm类添加Add()方法，否则会得到如下形式的一些列编译错误：

```csharp
    'Ch14Ex01.Farm<Ch14Ex01.Animal>' does not contain a definition for 'Add'
```

&emsp;&emsp;这个错误显示出了集合初始化器的部分底层功能。**在后台，编译器为在集合初始化器中提供的每一项调用集合的Add()方法**。Farm类通过Animals属性提供了一个Animal对象的集合。编译器猜不出这就是要通过Animals.Add()填充的属性，所以代码会失败。为更正这个问题，可以把Add()方法添加到类中，类通过对象初始化器进行初始化。

&emsp;&emsp;还可以修改示例中的代码，为Animals属性提供一个嵌套的初始化器，如下所示：

```csharp
    static void Main(string[] args)
    {
        Farm<Animal> farm = new Farm<Animal>
        {
            Animals = 
            {
                new Cow { Name = "Norris" },
                new Chicken { Name = "Rita" },
                new Chicken(),
                new SuperCow { Name = "Chesney" }
            }
        };
        farm.MakeNoises();
        Console.ReadKey();
    }
```

&emsp;&emsp;有了此代码，就不需要为Farm类提供Add()方法了。这个技巧适用于包含多个集合的类。这种情况下，用包含类的Add()方法添加的集合没有其他方式。


🔚