##扩展和使用CardLib

&emsp;&emsp;前面介绍了事件的定义和使用，现在就可以在Ch13CardLib中使用它们了。在库中需要添加一个LastCardDrawn事件，当使用GetCard获得Deck对象中的最后一个Card对象时，就将引发该事件。这个事件允许订阅者（subscriber）自动重新洗牌，减少需要在客户端完成的处理。这个事件将使用EventHandler委托类型，并传递一个Deck对象的应用作为事件源，这样无论处理程序在什么地方，都可以访问Shuffle()方法。在Deck.cs中添加以下代码以定义并引发事件（这段代码包含在Ch13CardLib\Deck.cs文件中）：

```csharp
    namespace Ch13CardLib
    {
        public event EventHandler LastCardDrawn;
        
        ...
        
        public Card GetCard(int cardNum)
        {
            if(cardNum >= 0 && cardNum <= 51)
            {
                if((cardNum == 51) && (LastCardDrawn != null))
                    LastCardDrawn(this, EventArgs.Empty);
                return cards[cardNum];
            }
            else
                throw new CardOutOfRangeException((Cards)cards.Clone());
        }
    }
```

&emsp;&emsp;这是把事件添加到Deck类定义需要的所有代码。


####CardLib的扑克牌游戏客户程序

&emsp;&emsp;在开发了CardLib库后，就可以使用它了。在结束讲述C#和.NET Framework中OOP技术的这个部分前，我们将编写扑克牌应用程序的基本代码，其中将使用我们熟悉的扑克牌类。

&emsp;&emsp;与前面的章节一样，我们将在Ch13CardLib解决方案中添加一个客户控制台应用程序，添加一个Ch13CardLib项目的引用，使其成为启动项目。这个应用程序称为Ch13CardClient。

&emsp;&emsp;首先在Ch13CardClient的一个新文件Player.cs中创建一个新类Player，相应代码可在本章下载代码的Ch13CardClient\Player.cs文件中找到。这个类包含两个自动属性：Name（字符串）和PlayerHand（Cards类型）。这些属性有私有的set访问器。但是PlayHand属性仍可以对其内容进行写入访问，这样就可以修改玩家手中的扑克牌。

&emsp;&emsp;我们还把默认的构造函数设置为私有，以隐藏它，并提供了一个公共的非默认构造函数，该函数接受Player实例中属性Name的初始值。

&emsp;&emsp;最后提供一个bool类型的方法HasWon()。如果玩家手中的扑克牌花色都相同（一个简单的取胜条件，但并没有什么意义），该方法就返回true。

&emsp;&emsp;Player.cs的代码如下所示：

```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using Ch13CardLib;
    
    namespace Ch13CardClient
    {
        public class Player
        {
            public string Name{ get; private set; }
            public Cards PlayHand { get; private set; }
            
            private Player()
            {
            }
            
            public Player(string name)
            {
                Name = name;
                PlayHand = new Cards();
            }
            
            public bool HasWon()
            {
                bool won = true;
                Suit match = PlayHand[0].suit;
                for(int i = 1; i < PlayHand.Count; i++)
                {
                    won &= PlayHand[i].suit == match;
                }
                return won;
            }
        }
    }
```

&emsp;&emsp;接着定义一个处理扑克牌游戏的类Game，这个类在Ch13CardClient项目的Game.cs文件中。

&emsp;&emsp;这个类有4个私有成员字段：

```
    • playDeck --- Deck类型的变量，包含要使用的一副扑克牌
    • currentCard --- 一个int值，用作下一张要翻开的扑克牌的指针
    • players --- 一个Player对象数组，表示游戏玩家
    • discardedCards --- Cards集合，表示玩家扔掉的扑克牌，但还没有放回整副牌中。
```
&emsp;&emsp;这个类的默认构造函数初始化了存储在playDeck中的Deck，并洗牌，把currentCard指针变量设置为0（playDeck中的第一张牌），并关联了playDeck.LastCardDrawn事件的处理程序Reshuffle()。这个处理程序将洗牌，初始化discardedCards集合，并将currentCard重置为0，准备从新的一副牌中读取扑克牌。

&emsp;&emsp;Game类还包含两个实用方法：SetPlayers()可以设置游戏的玩家（Player对象数组），DealHands()给玩家发牌（每个玩家有7张牌）。玩家的数量限制为2～7人，确保每个玩家有足够多的牌。

&emsp;&emsp;最后，PlayGame()方法包含游戏逻辑。在分析了Program.cs中的代码后介绍这个方法，Game.cs的剩余代码如下所示（这段代码包含在Ch13CardClient\Game.cs文件中）：

```csharp
    using System;
    using System.Collentions.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using Ch13CardLib;
    
    namespace Ch13CardClient
    {
        public class Game
        {
            private int currentCard;
            private Deck playDeck;
            private Player[] players;
            private Cards discardedCards;
            
            public Game()
            {
                currentCard = 0;
                playDeck = new Deck(true);
                playDeck.LastCardDrawn += Reshuffle;
                playDeck.Shuffle();
                discardedCards = new Cards();
            }
            
            private void Reshuffle(object source, EventArgs args)
            {
                Console.WriteLine("Discarded cards reshuffled into deck.");
                ((Deck)source).Shuffle();
                discardedCards.Clear();
                currentCard = 0;
            }
            
            public void SetPlayers(Player[] newPlayers)
            {
                if(newPlayers.Length > 7)
                    throw new ArgumentException(
                        "A maximum of 7 players may play this game.");
                        
                if(newPlayers.Length < 2)
                    throw new ArgumentException(
                        "A minimum of 2 players may play this game.");
                        
                players = newPlayers;
            }
            
            private void DealHands()
            {
                for(int p = 0; p < players.Length; p++)
                {
                    for(int c = 0; c < 7; c++)
                    {
                        players[p].PlayHand.Add(playDeck.GetCard(currentCard++));
                    }
                }
            }
            
            public int PlayGame()
            {
                // Code to follow.
            }
        }
    }
```


&emsp;&emsp;Program.cs中包含Main()方法，它初始化并运行游戏。这个方法执行以下步骤：

```
    （1）显示引导画面。
    （2）提示用户输入玩家数（2～7）。
    （3）根据玩家数建立一个Player对象数组。
    （4）给每个玩家取名，用于初始化数组中的一个PLayer对象。
    （5）创建一个Game对象，使用SetPlayers()方法指定玩家。
    （6）使用PlayGame()方法启动游戏。
    （7）PlayGame()的int返回值用于显示一条获胜消息（返回的值是Player对象数组中获胜的玩家的索引）。
```

&emsp;&emsp;这个方法的代码（为清晰起见，加了一些注释）如下所示（这段代码包含在Ch13CardClient\Program.cs文件中）：

```csharp
    static void Main(string[] args)
    {
        // Display introduction.
        Console.WriteLine("KarliCards: a new and exciting card game.");
        Console.WriteLine("To win you must have 7 cards of the same suit in" +
            " your hand.");
        Console.WriteLine();

        // Prompt for number of players.
        bool inputOK = false;
        int choice = -1;

        do
        {
            Console.WriteLine("How many players(2-7)?");
            string input = Console.ReadLine();

            try
            {
                // Attempt to convert input into a valid number of players.
                choice = Convert.ToInt32(input);
                if((choice >= 2) && (choice <= 7))
                    inputOK = true;
            }
            catch
            {
                // Ignore failed conversions, just continue prompting.
            }

        }while(inputOK == false);

        // Initialize array of Player objects.
        Player[] players = new Player[choice];

        // Get player names.
        for(int p = 0; p < players.Length; p++)
        {
            Console.WriteLine("Player {0}, enter your name:", p + 1);
            string palyerName = Console.ReadLine();
            players[p] = new Player(playerName);
        }

        // Start game.
        Game newGame = new Game();
        newGame.SetPlayers(players);
        int whoWon = newGame.PlayGame();

        // Display winning player.
        Console.WriteLine("{0} has won the game!", players[whoWon].Name);
        Console.ReadKey();
    }
```

&emsp;&emsp;接着分析一下应用程序的主体PlayGame()。由于篇幅所限，这里不准备详细讲解这个方法，而只是加注了一些注释，使其更容易理解。实际上，这些代码都不复杂，仅是较多而已。

&emsp;&emsp;每个玩家都可以查看手中的牌和桌面上的那张牌，或者翻开一张新牌。在拾取一张牌后，玩家必须扔掉一张牌，如果他们拾取了桌面上的那张牌，就必须用另一张牌替换桌面上的那张牌，或者把扔掉的那张牌放在桌面上那张牌的上面（把扔掉的那张牌添加到discardedCards集合中）。

&emsp;&emsp;在分析这段代码时，一个关键问题在于Card对象的处理方式。必须清楚，这些对象定义为引用类型，而不是值类型（使用结构）。给定的Card对象似乎同时存在于多个地方，因为引用可以存在于Deck对象、Player对象的hand字段、discardedCards集合和playCard对象（桌面上的当前牌）中。这样便于跟踪扑克牌，特别是可以用于从一副牌中拾取一张新牌。如果牌不在任何玩家的手中，也不在discardedCards集合中，才能接受该牌。

&emsp;&emsp;代码如下所示：

```csharp
    public int PlayGame()
    {
        // Only play if players exist.
        if(players == null)
            return -1;

        // Deal initial hands.
        DealHands();

               // Initialize game vars, including an initial card to place on the
               // table: playCard.
               bool GameWon = false;
               int currentPlayer;
               Card playCard = playDeck.GetCard(currentCard++);
               discardedCards.Add(playCard);

               // Main game loop, continues until GameWon == true.
               do
               {
                   Console.WriteLine("Press T to take card in play or D to " +
                       "draw:");
                   string input = Console.ReadLine();
                   if(input.ToLower() == "t")
                   {
                       // Add card from table to player hand.
                       Console.WriteLine("Drawn: {0}", playCard);

                       // Remove from discarded cards if possible(if deck
                       // is reshuffled it won't be there any more)
                       if(discardedCards.Contains(playCard))
                       {
                           discardedCards.Remove(playCard);
                       }
                       players[currentPlayer].PlayHand.Add(playCard);
                       inputOK = true;
                   }
                   if(input.ToLower() == "d")
                   {
                       // Add new card from deck to player hand.
                       Card newCard;

                       // Only add card if it isn't already in a player hand
                       // or in the discard pile
                       bool cardIsAvailable;
                       do
                       {
                           newCard = playDeck.GetCard(currentCard++);
                           // Check if card is in discard pile
                           cardIsAvailable = !discardedCards.Contains(newCard);
                           if(cardIsAvailable)
                           {
                               // Loop through all player hands to see if newCard
                               // is already in a hand.
                               foreach(Player testPlayer in players)
                               {
                                   if(testPlayer.PlayHand.Contains(newCard))
                                   {
                                       cardIsAvailable = false;
                                       break;
                                   }
                               }
                           }
                       }while(!cardIsAvailable);
                       // Add the card found to player hand.
                       Console.WriteLine("Drawn: {0}", newCard);
                       players[currentPlayer].PlayHand.Add(newCard);
                       inputOK = true;
                   }
               }while(inputOK == false);

               // Display new hand with cards numbered.
               Console.WriteLine("New hand:");
               for(int i = 0; i < players[currentPlayer].PlayHand.Count; i++)
               {
                  Console.WriteLine("{0}: {1}", i+1, 
                      players[currentPlayer].PlayHand[i]);
               }

               // Prompt player for a card to discard.
               inputOK = false;
               int choice = -1;
               do
               {
                   Console.WriteLine("Choose card to discard:");
                   string input = Console.ReadLine();
                   try
                   {
                        // Attempt to convert input into a valid card number.
                        choice = Convert.ToInt32(input);
                        if((choice > 0) && (choice <= 8))
                            inputOK = true;
                    }
                    catch
                    {
                        // Ignore failed conversions, just continue prompting.
                    }
                }while(inputOK == false);

                // Place reference to removed card in playCard (place the card
                // on the table), then remove card from player hand and add
                // to discarded card pile.
                playCard = players[currentPlayer].PlayHand[choice - 1];
                players[currentPlayer].PlayHand.RemoveAt(choice - 1);
                discardedCards.Add(playCard);
                Console.WriteLine("Discarding: {0}", playCard);

                // Space out text for players
                Console.WriteLine();

                // Check to see if player has won the game, and exit the player
                // loop if so.
                GameWon = players[currentPlayer].HasWon();
                if(GameWon == true)
                    break;

            }
        } while(GameWon == false);

        // End game, noting the winning palyer.
        return currentPlayer;
    }
```

&emsp;&emsp;玩这个游戏，确保花一些时间去仔细研究它。应尝试在Reshuffle()方法中设置一个断点，由7个玩家来玩这个游戏。如果在拾取了扑克牌后，马上扔掉它，不需要太长的时间就要重新洗牌了，因为7个玩家玩这个游戏时，就只富余3张牌。这样就可以注意3张牌何时重新翻开，验证程序是否正常执行。


🔚