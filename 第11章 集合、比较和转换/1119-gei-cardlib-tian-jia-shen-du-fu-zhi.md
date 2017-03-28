##给CardLib添加深度复制

&emsp;&emsp;下面把上述内容付诸于实践：使用ICloneable接口，复制Card、Cards和Deck对象，这在某些扑克牌游戏中是有用的，因为在这些游戏中不需要让两副扑克牌引用同一组Card对象，但肯定会使一副扑克牌中的牌序与另一副牌的牌序相同。

&emsp;&emsp;在Ch11CardLib中，对Card类执行复制操作是很简单的，因为只需进行浅度复制（Card只包含值类型的数据，其形式为字段）。我们只需对类定义进行如下修改：

```csharp
    public class Card : ICloneable
    {
        public object Clone()
        {
            return MemberwiseClone();
        }
    }
```

&emsp;&emsp;ICloneable接口的这段实现代码只是一个浅度复制，无法确定在Clone()方法中执行了什么操作，所以这已经足以满足我们的需求。

&emsp;&emsp;接着，需要对Cards集合类实现ICloneable接口。这个过程稍复杂些，因为涉及到复制源集合中的每个Card对象，所以需要进行深度复制：

```csharp
    public class Cards : CollectionBase, ICloneable
    {
        public object Clone()
        {
            Cards newCards = new Cards();
            foreach(Card sourceCard in List)
            {
                newCards.Add((Card)sourceCard.Clone());
            }
            return newCards;
        }
    }
```

&emsp;&emsp;最后，需要在Deck类上实现ICloneable接口。这里存在一个小问题：因为Ch11CardLib中的Deck类无法修改它包含的扑克牌，所以没有洗牌。例如，无法修改有给定牌序的Deck实例。为解决这个问题，为Deck类定义一个新的私有构造函数，在实例化Deck对象时，可以给该函数传送指定的Cards集合。所以，在这个类中执行复制的代码如下所示：

```csharp
    public class Deck : ICloneable
    {
        public object Clone()
        {
            Deck newDeck = new Deck(cards.Clone() as Cards);
            return newDeck;
        }
        
        private Deck(Cards newCards)
        {
            cards = newCards;
        }
    }
```

&emsp;&emsp;再次用一些简单的客户代码进行测试。与以前一样，这应放在客户项目的Main()方法中，以便进行测试（包含在本章下载代码的Ch11CardClient\Program.cs文件中）：

```csharp
    Deck deck1 = new Deck();
    Deck deck2 = (Deck)deck1.Clone();
    Console.WriteLine("The first card in the original deck is : {0}",
                      deck1.GetCard(0));
    Console.WriteLine("The first card in the cloned deck is : {0}",
                      deck2.GetCard(0));
    deck1.Shuffle();
    Console.WriteLine("Original deck shuffled.");
    Console.WriteLine("The first card in the original deck is : {0}",
                      deck1.GetCard(0));
    Console.WriteLine("The first card in the cloned deck is : {0}",
                      deck2.GetCard(0));
    Console.ReadKey();
```


🔚








