##给 `CardLib` 添加 `Cards` 集合

&emsp;&emsp;第 10 章创建了一个类库项目 `Ch10CardLib`，它包含一个表示扑克牌的 `Card` 类和一个表示一副扑克牌的 `Deck` 类，这个 `Deck` 类是 `Card` 类的集合，且实现为一个简单数组。

&emsp;&emsp;本章给这个库添加一个新类，并将类库重命名为 `Ch11CardLib`。这个新类 `Cards` 是 `Card` 对象的一个定制集合，并拥有本章前面介绍的各种功能。在 `C:\BegVCSharp\Chapter11` 目录中创建于给新类库 `Ch11CardLib`。然后删除自动生成的 `Class1.cs` 文件，再使用 `Project | Add Existing Item` 选择 `C:\BegVCSharp\Chapter10\Ch10CardLib\Ch10CardLib` 目录中的 `Card.cs`、`Deck.cs`、`Suit.cs` 和 `Rank.cs` 文件，把它们添加到项目中。与第 10 章介绍的这个项目的上一个版本相同，这里也不使用标准的 “试一试” 格式介绍这些变化。


&emsp;&emsp;如果要自己创建这个项目，就应该添加一个新类 `Cards`，修改 `Cards.cs` 中的代码，如下所示：

```javascript
	using System;
	using System.Collections;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;

	namespace Ch11CardLib
	{
		public class Cards : CollectionBase
		{
			public void Add(Card newCard)
			{
				List.Add(newCard);
			}

			public void Remove(Card oldCard)
			{
				List.Remove(oldCard);
			}

			public Card this[int cardIndex]
			{
				get {
					return (Card)List[cardIndex];
				}
				set {
					List[cardIndex] = value;
				}
			}

			public void CopyTo(Cards targetCards)
			{
				for (int index = 0; index < this.Count; index++)
				{
					targetCards[index] = this[index];
				}
			}

			public bool Contains(Card card)
			{
				return InnerList.Contains(card);
			}
		}
	}
```


&emsp;&emsp;然后需要修改 `Deck.cs`，以利用这个新集合，而不是数组：

```javascript
	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;

	namespace Ch11CardLib
	{
		public class Deck
		{
			private Cards cards = new Cards();

			public Deck()
			{
				for (int suitVal = 0; suitVal < 4; suitVal++)
				{
					for (int rankVal = 1; rankVal < 14; rankVal++)
					{
						cards.Add(new Card((Suit)suitVal, (Rank)rankVal));
					}
				}
			}

			public Card GetCard(int cardNum)
			{
				if (cardNum >=0 && cardnum <= 51)
					return cards[cardNum];
				else
					throw (new System.ArgumentOutOfRangeException("cardNum", cardNum, "Value must be between 0 and 51."));
			}

			public void Shuffle()
			{
				Cards newDeck = new Cards();
				bool[] assigned = new bool[52];
				Random sourceGen = new Random();
				for (int i = 0; i < 52; i++)
				{
					int sourceCard = 0;
					bool foundCard = false;
					while (foundCard == false)
					{
						sourceCard = sourceGen.Next(52);
						if (assigned[sourceCard] == false)
							foundCard = true;
					}
					assigned[sourceCard] = true;
					newDeck.Add(cards[sourceCard]);
				}
				newDeck.CopyTo(cards);
			}
		}

		
	}
```



&emsp;&emsp;在此不需要做很多修改。其中大多数修改都涉及改变洗牌逻辑，才能把 `cards` 中随机的一张牌添加到新 `Cards` 集合 `newDeck` 的开头，而不是把 `cards` 集合中顺序位置的一张牌添加 `newDeck` 集合的随机位置上。

&emsp;&emsp;`Ch10CardLib` 解决方案的客户控制台应用程序 `Ch10CardClient` 可以使用这个新库得到与以前相同的结果，因为 `Deck` 的方法签名没有改变。这个类库的客户程序现在可以使用 `Cards` 集合类，而不是依赖 `Card` 对象数组，例如，在扑克牌游戏应用程序中定义一手牌。


🔚
