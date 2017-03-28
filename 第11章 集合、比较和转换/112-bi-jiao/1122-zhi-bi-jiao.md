## 值比较

  考虑两个表示人的Person对象，它们都有一个Age整型属性。下面要比较它们，看看哪个人年龄较大。为此可以使用以下代码：

```csharp
    if(person1.Age > person2.Age)
    {
        ...
    }
```

  这是可以的，但还有其他方法，例如，使用下面的语法：

```csharp
    if(person1 > person2)
    {
        ...
    }
```

  可以使用运算符重载，如本节后面所述。这是一项强大的技术，但应谨慎使用。在上面的代码中，年龄的比较不是非常明显，该段代码还可以比较身高、体重、IQ等。

  另一个方法是使用IComparable和IComparer接口，它们可以采用标准的方式定义比较对象的过程。.NET Framework中的各种集合类支持这种方式，这使得它们成为对集合中的对象进行排序的一种极佳方式。

  **1. 运算符重载**

  通过运算符重载（operator overloading），可以对我们设计的类使用标准的运算符，例如`+`、`>`等。这称为重载，因为在使用特定的参数类型时，我们为这些运算符提供了自己的实现代码，其方式与重载方法相同，也是为同名方法提供不同的参数。

  运算符重载非常有用，因为我们可以在运算符重载的实现中执行需要的任何操作，这并不一定像用“+”表示“把这两个操作数相加”这么简单。稍后介绍一个进一步升级CardLib库的示例。我们将提供比较运算符的实现代码，比较两张牌，看看在一圈（扑克牌游戏中的一局）中哪张牌会赢。

  因为在许多扑克牌游戏中，一圈取决于牌的花色，这并不像比较牌上的数字那样直接。如果第二张牌与第一张牌的花色不同，则无论其点数是什么，第一张牌都会赢。考虑两个操作数的顺序，就可以实现这种比较。也可以考虑“王牌”的花色，而王牌可以胜过其他花色，即使该王牌的花色与第一张牌不同，也是如此。也就是说，card1 &gt; card2 是true（这表示如果card1是第一个出牌，则card1胜过了card2），并不意味着card2 &gt; card1是false。如果card1和card2都不是王牌，且属于不同的花色，则这两个比较都是true。

  但我们先看一下运算符重载的基本语法。要重载运算符，可给类添加运算符类型成员（它们必须是static）。一些运算符有多种用途（如 `-` 运算符就有一元和二元两种功能），因此我们还指定了要处理多少个操作数，以及这些操作数的类型。一般情况下，操作数的类型与定义运算符的类相同，但也可以定义处理混合类型的运算符，详见稍后的内容。

  例如，考虑一个简单类型AddClass1，如下所示：

```csharp
    public class AddClass1
    {
        public int val;
    }
```

  这仅是int值的一个包装器（wrapper），但可以用于说明原理。对于这个类，下面的代码不能编译：

```csharp
    AddClass1 op1 = new AddClass1();
    op1.val = 5;
    AddClass1 op2 = new AddClass1();
    op2.val = 5;
    AddClass1 op3 = op1 + op2;   ❌
```

  其错误是`+`运算符不能应用于AddClass1类型的操作数，因为我们尚未定义要执行的操作。下面的代码则可执行，但无法得到预期的结果：

```csharp
    AddClass1 op1 = new AddClass1();
    op1.val = 5;
    AddClass1 op2 = new AddClass1();
    op2.val = 5;
    bool op3 = op1 == op2;
```

  其中，使用`==`二元运算符来比较op1和op2，看它们是否引用同一个对象，而不是验证它们的值是否相等。在上述代码中，即使op1.val和op2.val相等，op3也是false。

  要重载`+`运算符，可使用下述代码：

```csharp
    public class AddClass1
    {
        public int val;

        public static AddClass1 operator + (AddClass1 op1, AddClass1 op2)
        {
            AddClass1 returnVal = new AddClass1();
            returnVal.val = op1.val + op2.val;
            return returnVal;
        }
    }
```

  可以看出，运算符重载看起来与标准静态方法声明类似，但它们使用关键字`operator`和运算符本身，而不是一个方法名。现在可以成功地使用`+`运算符和这个类，如上面的示例所示：

```csharp
    AddClass1 op3 = op1 + op2;
```

  重载所有的二元运算符都是一样的，一元运算符看起来也是类似的，但只有一个参数：

```csharp
    public class AddClass1
    {
        public int val;

        public static AddClass1 operator +(AddClass1 op1, AddClass1 op2)
        {
            AddClass1 returnVal = new AddClass1();
            returnVal.val = op1.val + op2.val;
            return returnVal;
        }

        public static AddClass1 operator -(AddClass1 op1)
        {
            AddClass1 returnVal = new AddClass1();
            returnVal.val = -op1.val;
            return returnVal;
        }
    }
```

  这两个运算符处理的操作数的类型与类相同，返回值也是该类型，但考虑下面的类定义：

```csharp
    public class AddClass1
    {
        public int val;

        public static AddClass3 operator + (AddClass1 op1, AddClass2 op2)
        {
            AddClass3 returnVal = new AddClass3();
            returnVal.val = op1.val + op2.val;
            return returnVal;
        }

        public class AddClass2
        {
            public int val;
        }

        public class AddClass3
        {
            public int val;
        }
    }
```

  下面的代码就可以执行：

```csharp
    AddClass1 op1 = new AddClass1();
    op1.val = 5;
    AddClass2 op2 = new AddClass2();
    op2.val = 5;
    AddClass3 op3 = op1 + op2;
```

  可以酌情采用这种方式混合类型。但要注意，如果把相同的运算符添加到AddClass2中，上面的代码就会失败，因为它弄不清要使用哪个运算符。因此，应注意不要把签名相同的运算符添加到多个类中。

  还要注意，如果混合了类型，操作数的顺序必须与运算符重载的参数顺序相同。如果使用了重载的运算符和顺序错误的操作数，操作就会失败。所以不能像下面这样使用运算符：

```csharp
    AddClass3 op3 = op2 + op1;   ❌
```

  当然，除非提供了另一个重载运算符和倒序的参数：

```csharp
    public static AddClass3 operator + (AddClass2 op1, AddClass1 op2)
    {
        AddClass3 returnVal = new AddClass3();
        returnVal.val = op1.val + op2.val;
        return returnVal;
    }
```

  可以重载下述运算符：

• 一元运算符：`+`，`-`，`!`，`~`，`++`，`--`，`true`，`false`  
• 二元运算符：`+`，`-`，`*`，`/`，`%`，`&`，`|`，`^`，`<<`，`>>`  
• 比较运算符：`==`，`!=`，`<`，`>`，`<=`，`>=`

> **如果重载true和false运算符，就可以在布尔表达式中使用类，例如，if\(op1\){}。**

  **不能重载赋值运算符**，例如`+=`，但这些运算符使用与它们对应的简单运算符，例如`+`，所以不必担心它们。重载`+`意味着`+=`如期执行。`=`运算符不能重载，因为它有一个基本的用途。但这个运算符与用户定义的转换运算符相关，详见下一节。

  也不能重载`&&`和`||`，但它们使用对应的运算符`&`和`|`执行计算，所以重载`&`和`|`就足够了。

  一些运算符（如`<`和`>`）必须成对重载。这就是说，如重载`>`，就不能重载`<`。许多情况下，可以在这些运算符中调用其他运算符，以减少需要的代码数量（和可能发生的错误），例如：

```csharp
    public class AddClass1
    {
        public int val;

        public static bool operator >= (AddClass1 op1, AddClass1 op2)
        {
            return (op1.val >= op2.val);
        }

        public static bool operator < (AddClass1 op1, AddClass1 op2)
        {
            return !(op1 >= op2);
        }

        // Also need implementations for <= and > operators.
    }
```

  在较复杂的运算符定义中，这可以减少代码行数。这页意味着，如果后来决定修改这些运算符的实现，需要改动的代码将较少。

  这同样适用于`==`和`!=`，但对于这些运算符，通常需要重写Object.Equals\(\)和Object.GetHashCode\(\)，因为这两个函数也可以用于比较对象。重写这些方法，可以确保无论类的用户使用什么技术，都能得到相同的结果。这不太重要，但应增加进来，以保证其完整性。它需要下述非静态重写方法：

```csharp
    public class AddClass1
    {
        public int val;

        public static bool operator == (AddClass1 op1, AddClass1 op2)
        {
            return (op1.val == op2.val);
        }

        public static bool operator != (AddClass1 op1, AddClass1 op2)
        {
            return !(op1 == op2);
        }

        public override bool Equals(object op1)
        {
            return val == ((AddClass1)op1).val;
        }

        public override int GetHashCode()
        {
            return val;
        }
    }
```

  GetHashCode\(\)可根据其状态，获取对象实例的一个唯一`int`值。这里使用`val`就可以了，因为它也是一个`int`值。

  注意，Equals\(\)使用`object`类型参数。我们需要使用这个签名，否则就将重载这个方法，而不是重写它。类的用户仍可以访问默认的实现代码。这样就必须使用数据类型转换得到所需的结果。这常常需要使用本章前面讨论的`is`运算符检查对象类型，代码如下所示：

```csharp
    public override bool Equals(object op1)
    {
        if(op1 is AddClass1)
        {
            return val == ((AddClass1)op1).val;
        }
        else
        {
            throw new ArgumentException("Cannot compare AddClass1 objects with object of type" + op1.GetType().ToString());
        }
    }
```

  在这段代码中，如果传送给Equals的操作数的类型有误，或者不能转换为正确类型，就会抛出一个异常。当然，这可能并不是我们希望的操作。我们要比较一个类型的对象和另一个类型的对象，此时需要更多的分支结构。另外，可能只允许对类型完全相同的两个对象进行比较，这需要对第一个`if`语句做如下修改：

```csharp
        if(op1.GetType() == typeof(AddClass1))
```

<br>

&emsp;&emsp;**2. 给CardLib添加运算符重载**

&emsp;&emsp;现在再次升级Ch11CardLib项目，给Card类添加运算符重载。在本章下载代码的Ch11CardLib文件夹中可以找到以下的类的代码。首先给Card类添加额外字段，指定某花色比其他花色大，使A有更高的级别。把这些字段指定为静态，因为设置它们后，它们就可以应用到所有Card对象上：

```csharp
    public class Card
    {
        /// <summary>
        /// Flag for trump usage. If true, trumps are valued higher
        /// than cards of other suits.
        /// </summary>
        public static bool useTrumps = false;

        /// <summary>
        /// Trump suit to use if useTrumps is true.
        /// </summary>
        public static Suit trump = Suit.Club;

        /// <summary>
        /// Flag that determines whether aces are higher than kings or lower
        /// than deuces.
        /// </summary>
        public static bool isAceHigh = true;
    }
```

&emsp;&emsp;这些规则应用于应用程序中每个Deck的所有Card对象上。因此，两个Deck中的Card不可能遵守不同规则。这适用于这个类库，但是确实可以做出这样的假设：如果一个应用程序要使用不同的规则，可以自行维护这些规则；例如，在切换牌时，设置Card的静态成员。

&emsp;&emsp;完成后，就要给Deck类再添加几个构造函数，以便用不同的特性来初始化扑克牌：

```csharp
    /// <summary>
    /// Nondefault constructor. Allows aces to be set high.
    /// </summary>
    public Deck(bool isAceHigh) : this()
    {
        Card.isAceHigh = isAceHigh;
    }

    /// <summary>
    /// Nondefault constructor. Allows a trump suit to be used.
    /// </summary>
    public Deck(bool useTrumps, Suit trump) : this()
    {
        Card.useTrumps = useTrumps;
        Card.trump = trump;
    }

    /// <summary>
    /// Nondefault constructor. Allows aces to be set high and a trump suit
    /// to be used.
    /// </summary>
    public Deck(bool isAceHigh, bool useTrumps, Suit trump) : this()
    {
        Card.isAceHigh = isAceHigh;
        Card.useTrumps = useTrumps;
        Card.trump = trump;
    }
```

&emsp;&emsp;每个构造函数都使用第9章介绍的：this()语法来定义，这样，无论如何，默认构造函数总会在非默认的构造函数之前调用，初始化扑克牌。

&emsp;&emsp;接着，给Card类添加运算符重载（和推荐的重写代码）：

```csharp
    public static bool operator == (Card card1, Card card2)
    {
        return (card1.suit == card2.suit) && (card1.rank == card2.rank);
    }

    public static bool operator != (Card card1, Card card2)
    {
        return !(card1 == card2);
    }

    public override bool Equals(object card)
    {
        return this == (Card)card;
    }

    public override int GetHashCode()
    {
        return 13 * (int)suit + (int)rank;
    }

    public static bool operator > (Card card1, Card card2)
    {
        if(card1.suit == card2.suit)
        {
            if(isAceHigh)
            {
                if(card1.rank == Rank.Ace)
                {
                    if(card2.rank == Rank.Ace)
                        return false;
                    else
                        return true;
                }
                else
                {
                    if(card2.rank == Rank.Ace)
                        return false;
                    else
                        return (card1.rank > card2.rank);
                }
            }
            else
            {
                return (card1.rank > card2.rank);
            }
        }
        else
        {
            if(useTrumps && (card2.suit == Card.trump))
                return false;
            else
                return true;
        }
    }

    public static bool operator < (Card card1, Card card2)
    {
         return !(card1 >= card2);
    }

    public static bool operator >= (Card car1, Card card2)
    {
        if(card1.suit == card2.suit)
        {
            if(isAceHigh)
            {
                if(card1.rank == Rank.Ace)
                {
                    return true;
                }
                else
                {
                    if(card2.rank == Rank.Ace)
                        return false;
                    else
                        return (card1.rank >= card2.rank);
                }
            }
            else
            {
                return (card1.rank >= card2.rank);
            }
        }
        else
        {
            if(useTrumps && (card2.suit == Card.trump))
                return false;
            else
                return true;
        }
    }

    public static boo operator <= (Card card1, Card card2)
    {
        return !(card1 > card2);
    }
```

&emsp;&emsp;这段代码没什么需要特别关注之处，只是`>`和`>=`重载运算符的代码比较长。如果单步执行`>`运算符的代码，就可以看到它的执行情况，明白为什么需要这些步骤。

&emsp;&emsp;比较两张牌card1和card2，其中card1假定为先出的牌。如前所述，在使用王牌时，这是很重要的，因为王牌胜过其他牌，即使非王牌比较大，也是这样。当然，如果两张牌的花色相同，则王牌是否也是该花色就不重要了，所以这是我们要进行的第一个比较：

```csharp
    public static bool operator > (Card card1, Card card2)
    {
        if(card1.suit == card2.suit)
        {
    }
```

&emsp;&emsp;如果静态的isAceHigh标记为`true`，就不能直接通过Rank枚举中的值比较牌的点数了。因为A的点数在这个枚举中是1，比其他牌都小。此时就需要如下步骤：

• 如果第一张牌是A，就检查第二张牌是否也是A。如果是，则第一张牌就胜不过第二张牌。
  如果第二张牌不是A，则第一张牌胜出：

```csharp
            if(isAceHigh)
            {
                if(card1.rank == Rank.Ace)
                {
                    if(card2.rank == Rank.Ace)
                        return false;
                    else
                        return true;
                }

```

• 如果第一张牌不是A，也需要检查第二张牌是不是A。如果是，则第二张牌胜出：否则，就可以比较牌的点数，因为此时已不比较A了：

```csharp
                else
                {
                    if(card2.rank == Rank.Ace)
                        return false;
                    else
                        return (card1.rank > card2.rank);
                }
            }
```

• 另外，如果A不是最大的，就只需比较牌的点数：

```csharp
            else
            {
                return (card1.rank > card2.rank);
            }
```

&emsp;&emsp;代码的其余部分主要考虑的是card1和card2花色不同的情况。其中静态useTrumps标记是非常重要的。如果这个标记是true，且card2是王牌，则可以肯定，card1不是王牌（因为这两张牌有不同的花色），王牌总是胜出，所以card2比较大：

```csharp
        else
        {
            if(useTrumps && (card2.suit == Card.trump))
                return false;
```

&emsp;&emsp;如果card2不是王牌（或者useTrumps是false），则card1胜出，因为它是最先出的牌：

```csharp
            else
                return true;
        }
    }
```

&emsp;&emsp;另有一个运算符（`>=`）使用与此类似的代码，除此之外的其他运算符都非常简单，所以不需要详细地分析它们。

&emsp;&emsp;下面的简单客户代码测试这些运算符。把它放在客户项目的Main()函数中进行测试，就像前面CardLib示例的客户代码那样（这段代码包含在Ch11CardClient\Program.cs文件中）：

```csharp
    Card.isAceHigh = true;
    Console.WriteLine("Aces are high.");
    Card.useTrumps = true;
    Card.trump = Suit.Club;
    Console.WriteLine("Clubs are trumps.");

    Card card1, card2, card3, card4, card5;
    card1 = new Card(Suit.Club, Rank.Five);
    card2 = new Card(Suit.Club, Rank.Five);
    card3 = new Card(Suit.Club, Rank.Ace);
    card4 = new Card(Suit.Heart, Rank.Ten);
    card5 = new Card(Suit.Diamond, Rank.Ace);
    Console.WriteLine("{0} == {1} ? {2}", 
        card1.ToString(), card2.ToString(), card1 == card2);
    Console.WriteLine("{0} == {1} ? {2}", 
        card1.ToString(), card3.ToString(), card1 != card3);
    Console.WriteLine("{0}.Equals({1}) ? {2}", 
        card1.ToString(), card4.ToString(), card1.Equals(card4));
    Console.WriteLine("Card.Equals({0}, {1}) ? {2}", 
        card3.ToString(), card4.ToString(), Card.Equals(card3, card4));
    Console.WriteLine("{0} > {1} ? {2}", 
        card1.ToString(), card2.ToString(), card1 > card2);
    Console.WriteLine("{0} <= {1} ? {2}", 
        card1.ToString(), card3.ToString(), card1 <= card3);
    Console.WriteLine("{0} > {1} ? {2}", 
        card1.ToString(), card4.ToString(), card1 > card4);
    Console.WriteLine("{0} > {1} ? {2}", 
        card1.ToString(), card4.ToString(), card1 > card4);
    Console.WriteLine("{0} > {1} ? {2}", 
        card4.ToString(), card1.ToString(), card4 > card1);
    Console.WriteLine("{0} > {1} ? {2}", 
        card5.ToString(), card4.ToString(), card5 > card4);
    Console.WriteLine("{0} > {1} ? {2}", 
        card4.ToString(), card5.ToString(), card4 > card5);
    Console.ReadKey();
```

&emsp;&emsp;在这两种情况下，在应用运算符时都考虑了指定的规则。这在输出结果的最后4行中尤其明显，说明王牌总是胜过其他牌。


<br>


&emsp;&emsp;**3. IComparable 和 IComparer 接口**

&emsp;&emsp;`IComparable` 和 `IComparer` 接口是 .NET Framework 中比较对象的标准方式。这两个接口之间的差别如下：

```
    • IComparable 在要比较的对象的类中实现，可以比较该对象和另一个对象。

    • IComparer 在一个单独的类中实现，可以比较任意两个对象。
```

&emsp;&emsp;一般使用 `IComparable` 提供了一个方法 `CompareTo()`，这个方法接受一个对象。例如，在实现该方法时，使其可以接受一个 `Person` 对象，以便确定这个人比当前的人更年老还是更年轻。实际上，这个方法返回一个`int`，所以也可以确定第二个人与当前的人的年龄差：

```csharp
    if(person1.CompareTo(person2) == 0)
    {
        Console.WriteLine("Same age");
    }
    else if(person1.CompareTo(person2) > 0)
    {
        Console.WriteLine("person 1 is Older");
    }
    else
    {
        Console.WriteLine("person 1 is Younger");
    }
```

&emsp;&emsp;`IComparer` 也提供了一个方法 `Compare()`。这个方法接受两个对象，返回一个整型结果，这与 `CompareTo()` 相同。对于支持 `IComparer` 的对象，可使用下面的代码：

```csharp
    if(personComparer.Compare(person1, person2) == 0)
    {
        Console.WriteLine("Same age");
    }
    else if(personComparer.Compare(person1, person2) > 0)
    {
        Console.WriteLine("person 1 is Older");
    }
    else
    {
        Console.WriteLine("person 1 is Younger");
    }
```

&emsp;&emsp;这两种情况下，提供给方法的参数是 `System.Object` 类型。这意味着可以比较一个对象与其他任意类型的另一个对象。所以，在返回结果之前，通常需要进行某种类型比较，如果使用了错误类型，还会抛出异常。

&emsp;&emsp;.NET Framework 在类 `Comparer` 上提供了 `IComparer` 接口的默认实现方式，类 `Comparer` 位于 `System.Collections` 名称空间中，可以对简单类型以及支持 `IComparable` 接口的任意类型进行特定文化的比较。例如，可通过下面的代码使用它：

```csharp
    string firstString = "First String";
    string secondString = "Second String";
    Console.WriteLine("Comparing '{0}' and '{1}', result : {2}", 
        firstString, secondString,
        Comparer.Default.Compare(firstString, secondString));

    int firstNumber = 35;
    int secondNumber = 23;
    Console.WriteLine("Comparing '{0}' and '{1}', result : {2}", 
        firstNumber, secondNumber,
        Comparer.Default.Compare(firstNumber, secondNumber));
```

&emsp;&emsp;这里使用 `Comparer.Default` 静态成员获取 `Comparer` 类的一个实例，接着使用 `Compare()`方法比较前两个字符串，之后比较两个整数，结果如下：

```csharp
    Comparing 'First String' and 'Second String', result : -1
    Comparing '35' and '23', result : 1
```

&emsp;&emsp;在字母表中，F 在 S 的前面，所以 F “小于” S，第一个比较的结果就是 -1。同样，35大于23，所以结果是1。注意这里的结果并未给出相差的幅度。

&emsp;&emsp;在使用 `Comparer` 时，必须使用可以比较的类型。例如，试图比较 firstString 和 firstNumber 就会生成一个异常。

&emsp;&emsp;下面列出有关这个类的一些注意事项：

```
    • 检查传送给 Comparer.Compare()的对象，看看它们是否支持IComparable。如果支持，就使用该实现代码。
    • 允许使用null值，它表示“小于”其他任意对象。
    • 字符串根据当前文化来处理。要根据不同的文化（或语言）处理字符串，Comparer类必须使用其构造函数进行实例化，以便传送用于指定所使用的文化的 System.Globalization.CultureInfo 对象。
    • 字符串在处理时要区分大小写。如果要以不区分大小写的方式来处理它们，就需要使用 CaseInsensitiveComparer 类，该类以相同的方式工作。
```


<br>

&emsp;&emsp;**4. 对集合排序**

&emsp;&emsp;许多集合类可以用对象的默认比较方式进行排序，或者用定制方法来排序。`ArrayList` 就是一个示例，它包含方法 `Sort()`，这个方法使用时可以不带参数，此时使用默认的比较方式，也可以给它传送 `IComparer` 接口，以比较对象时。

&emsp;&emsp;在给 `ArrayList` 填充了简单类型时，例如整数或字符串，就会进行默认的比较。对于自己的类，必须在类定义中实现 `IComparable`，或创建一个支持 `IComparer` 的类，来进行比较。

&emsp;&emsp;注意，System.Collentions 名称空间中的一些类，包括 `CollectionBase`，都没有提供排序方法。如果要对派生于这个类的集合排序，就必须多做一些工作，自己给内部的 `List` 集合排序。

&emsp;&emsp;下面的示例说明如何使用默认的和非默认的比较方式给列表排序。

添加一个新类 Person，修改 Person.cs 中的代码，如下所示：

```csharp
    namespace Ch11Ex05
    {
        public class Person : IComprable
        {
            public string Name;
            public int Age;

            public Person(string name, int age)
            {
                Name = name;
                Age = age;
            }

            public int CompareTo(object obj)
            {
                if(obj is Person)
                {
                    Person otherPerson = obj as Person;
                    return this.Age - otherPerson.Age;
                }
                else
                {
                    throw new ArgumentException("Object to compare to is not a Person object.");
                }
            }
        }
    }
``` 

添加一个新类 PersonComparerName，修改代码，如下所示：

```csharp
    using System;
    using System.Collections;
    using System.Collentions.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;

    namespace Ch11Ex05
    {
        public class PersonComparerName : IComperer
        {
            public static IComparer Default = new PersonComparerName();

            public int Compare(object x, object y)
            {
                if(x is Person && y is Person)
                {
                    return Comparer.Default.Compare(
                        ((Person)x).Name, ((Person)y).Name);
                }
                else
                {
                    throw new ArgumentException("One or both objects to compare are not Person objects.");
                }
            }
        }
    }
```

修改 Program.cs 中的代码，如下所示：

```csharp
    using System;
    using System.Collections;
    using System.Collentions.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;

    namespace Ch11Ex05
    {
        class Program
        {

            static void Main(string[] args)
            {
                ArrayList list = new ArrayList();
                list.Add(new Person("Jim", 30));
                list.Add(new Person("Bob", 25));
                list.Add(new Person("Bert", 27));
                list.Add(new Person("Ernie", 22));

                Console.WriteLine("Unsorted people");
                for(int i = 0; i < list.Count; i++)
                {
                    Console.WriteLine("{0} ({1})",
                        (list[i] as Person).Name, (list[i] as Person).Age);
                }
                Console.WriteLine();

                Console.WriteLine(
                    "People sorted with default comparer (by age):");
                list.Sort();
                for(int i = 0; i < list.Count; i++)
                {
                    Console.WriteLine("{0} ({1})",
                        (list[i] as Person).Name, (list[i] as Person).Age);
                }
                Console.WriteLine();

                Console.WriteLine(
                    "People sorted with nondefault comparer (by name):");
                list.Sort(PersonCompareName.Default);
                for(int i = 0; i < list.Count; i++)
                {
                    Console.WriteLine("{0} ({1})",
                        (list[i] as Person).Name, (list[i] as Person).Age);
                }

                Console.ReadKey();
            }
        }
    }

```


>&emsp;&emsp;**示例的说明**

>&emsp;&emsp;在这个示例中，包含 `Person` 对象的 `ArrayList` 用两种不同的方式排序。调用不带参数的 `ArrayList.Sort()` 方法，将使用默认的比较方式，也就是使用 `Person` 类中的 `CompareTo()` 方法（因为这个类实现了 `IComparable`）：

>```csharp
            public int Compare(object x, object y)
            {
                if(x is Person && y is Person)
                {
                    return Comparer.Default.Compare(
                        ((Person)x).Name, ((Person)y).Name);
                }
                else
                {
                    throw new ArgumentException("One or both objects to compare are not Person objects.");
                }
            }
```

>&emsp;&emsp;这个方式首先检查其参数能否与 `Person` 对象比较，即该对象是否能转换为 `Person` 对象。如果遇到问题，就抛出一个异常。否则，就比较两个 `Person` 对象的 `Age` 属性。

>&emsp;&emsp;接着，使用实现了 `IComparer` 的 `PersonComparerName` 类，执行非默认的比较排序。这个类有一个公共的静态字段，以方便使用：


>```csharp
           public static IComparer Default = new PersonComparerName();
```

>&emsp;&emsp;它可以用 PersonComparerNameDefault 获取一个实例，就像前面的 `Comparer` 类一样。这个类的 `Compareto()` 方法如下：

>```csharp
            public int Compare(object x, object y)
            {
                if(x is Person && y is Person)
                {
                    return Comparer.Default.Compare(
                        ((Person)x).Name, ((Person)y).Name);
                }
                else
                {
                    throw new ArgumentException("One or both objects to compare are not Person objects.");
                }
            }
```

>&emsp;&emsp;这里也是首先检查参数，看看它们是不是 `Person` 对象，如果不是，就抛出一个异常；如果是，就使用默认的 `Comparer` 对象比较 `Person` 对象的两个字符串字段 `Name`。


🔚