##定制异常

&emsp;&emsp;第7章讨论了异常，以及如何使用`try...catch...finally`块处理它们。我们还论述了几个标准的.NET异常，包括异常的基类System.Exception。在应用程序中，有时也可以从这个基类中派生自己的异常类，并使用它们，而不是使用标准的异常。这样就可以把更具体的信息发送给捕获该异常的代码，让处理异常的捕获代码更有针对性。例如，可以给异常类添加一个新属性，以便访问某些底层信息，这样异常的接收代码就可以做出必要的改变，或者仅给出异常起因的更多信息。

&emsp;&emsp;定义了异常类后，就可以使用Debug|Exception对话框中的Add按钮，把它添加到VS可以识别的异常列表中，然后定义与异常相关的操作，如第7章所述。

>&emsp;&emsp;**在System名称空间中有两个基本的异常类ApplicationException和SystemException，它们派生于Exception。SystemException用作.NET Framework预定义的异常的基类，ApplicationException由开发人员用于派生自己的异常类。但最近的最佳做法是不从这个类中派生异常，而应使用Exception。ApplicationException类在未来可能会被废弃。**

**给CardLib添加定制异常**

&emsp;&emsp;为了演示定制异常的用法，最好通过升级CardLib项目来说明。目前，如果试图访问索引小于0或大于51的扑克牌，Deck.GetCard()方法目前就会抛出一个标准的.NET异常，但下面改为使用一个定制异常。

&emsp;&emsp;首先需要在BegVCSharp\Chapter13目录中创建一个新的类库项目Ch13CardLib，像以前一样把类从Ch12CardLib中复制过来，并把名称空间改为Ch13CardLib。接着定义该异常。方法是使用在新类文件CardOutOfRange Exception.cs中定义一个新类，这个新类是使用Project|Add Class添加到Ch13CardLib项目中的（这段代码包含在Ch13CardLib\CardOutOfRangeException.cs文件中）：

```csharp
    public class CardOutOfRangeException : Exception
    {
        private Cards deckContents;
        
        public Cards DeckContents
        {
            get
            {
                return deckContents;
            }
        }
        
        public CardOutOfRangeException(Cards sourceDeckContents) : base("There are only 52 cards in the deck.")
        {
            deckContents = sourceDeckContents;
        }
    }
```

&emsp;&emsp;这个类的构造函数需要使用Cards类的一个实例，它允许通过DeckContents属性来访问这个Cards对象，为Exception基类构造函数提供合适的错误信息，使该错误信息可以通过类的Message属性得到。

&emsp;&emsp;接着Deck.cs中添加抛出该异常的代码，替换原来的标准异常（这段代码包含在Ch13CardLib\Deck.cs文件中）：

```csharp
    public Card GetCard(int cardNum)
    {
        if(cardNum >= 0 && cardNum <= 51)
            return cards[cardNum];
        else
            throw new CardOutOfRangeException(cards.Clone() as Cards);
    }
```

&emsp;&emsp;DeckContents属性是通过对Deck对象的当前内容（其形式是一个Cards对象）进行深度复制来初始化的。这表示，此时的内容是异常抛出时的内容，所以随后对Deck内容的修改不会丢失这些信息。

&emsp;&emsp;要进行测试，使用下面的客户代码（这段代码包含在Ch13CardClient\Program.cs文件中）：

```csharp
    Deck deck1 = new Deck();
    try
    {
        Card myCard = deck1.GetCard(60);
    }
    catch(CardOutOfRangeException e)
    {
        Console.WriteLine(e.Message);
        Console.WriteLine(e.DeckContents[0]);
    }
    Console.ReadKey();
```

&emsp;&emsp;其中捕获代码把异常的Message属性写到屏幕上。我们还通过DeckContents显示了Cards对象中的第一张牌，以证明可以通过定制的异常对象来访问Cards集合。


🔚
