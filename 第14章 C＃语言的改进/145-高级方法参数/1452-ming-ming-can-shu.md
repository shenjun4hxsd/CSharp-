##命名参数

&emsp;&emsp;使用可选参数时，可能发现某个方法有几个可选参数，但你可能只想给第三个可选参数传送值。从上一节介绍的语法来看，如果不提供前两个可选参数的值，就无法给第三个可选参数传送值。

&emsp;&emsp;C# 4引入了命令参数（named parameters），它允许指定要使用哪个参数。这不需要在方法定义中进行任何特殊处理，它是一个在调用方法时使用的技术。其语法如下：

```csharp
    MyMethod(
        <param1Name> : <param1Value>,
        ...
        <paramNName> : <paramNValue>);
```

&emsp;&emsp;参数的名称是在方法定义中使用的变量名。

&emsp;&emsp;只要命名参数存在，就可以采用这种方式指定需要的任意多个参数，而且参数的顺序是任意的。命名参数也可以是可选的。

&emsp;&emsp;可以仅给方法调用中的某些参数使用命名参数。当方法签名中有多个可选参数和一些必选参数时，这是非常有用的。可以首先指定必选参数，再指定命名的可选参数。例如：

```csharp
    MyMethod(
        requiredParameter1Value,
        optionalParameter5 : optionalParameter5Value);
```

&emsp;&emsp;**但注意，如果混合使用命名参数和位置参数，就必须先包含所有的位置参数，其后是命名参数。但是，只要全部使用命名参数，参数的顺序也可以不同**。例如：

```csharp
    MyMethod(
        optionalParameter5 : optionalParameter5Value，
        requiredParameter1 : requiredParameter1Value);
```

&emsp;&emsp;此时，必须包含所有必选参数的值。

&emsp;&emsp;下面的示例介绍了如何使用命名参数和可选参数。

```csharp
    public static class WordProcessor
    {
        public static List<string> GetWords (
            string sentence,
            bool capitalizeWords = false,
            bool reverseOrder = false,
            bool reverseWords = false)
        {
            List<string> words = new List<string>(sentence.Split(' '));
            if(capitalizeWords)
                words = CapitalizeWords(words);
            if(reverseOrder)
                words = ReverseOrder(words);
            if(reverseWords)
                words = ReverseWords(words);
            return words;
        }
        
        private static List<string> CapitalizeWords(
            List<string> words)
        {
            List<string> capitalizedWords = new List<string>();
            foreach(string word in words)
            {
                if(word.Length == 0)
                    continue;
                if(word.Length == 1)
                    capitalizedWords.Add(
                        word[0].ToString().ToUpper());
                else
                    capitalizedWords.Add(
                        word[0].ToString().ToUpper()
                        + word.Substring(1));
            }
        }
        
        private static List<string> ReverseOrder(List<string words>)
        {
            List<string> reversedWords = new List<string>();
            for(int wordIndex = words.Count - 1;
                wordInde >= 0; wordIndex--)
                reversedWords.Add(words[wordIndex]);
                
            return reversedWords;
        }
        
        private static List<string> ReverseWords(List<string> words)
        {
            List<string> reversedWords = new List<string>();
            foreach(string word in words)
                reversedWords.Add(ReverseWord(word));
                
            return reversedWords;
        }
        
        private static string ReverseWord(string work)
        {
            StringBuilder sb = new StringBuilder();
            for(int characterIndex = word.Length - 1;
                characterIndex >= 0; characterIndex--)
                sb.Append(word[characterIndex]);
                
            return sb.ToString();
        }
        
        static void Main(string[] args)
        {
            string sentence = "'twas brillig, and the slithy toves did gyre "
                + "and gimble in the wabe:";
            List<string> words;
            
            words = WordProcessor.GetWords(sentence:);
            foreach(string word in words)
            {
                Console.Write(word);
                Console.Write(' ');
            }
            
            Console.WriteLine('\n');
            
            words = WordProcessor.GetWords(
                sentence,
                reverseWords : true,
                capitalizeWords : true);
            Console.WriteLine("Capitalized sentence with reversed words:");
            foreach(string word in words)
            {
                Console.Write(word);
                Console.Write(' ');
            }
            
            Console.ReadKey();
        }
    }
```

>&emsp;&emsp;**示例的说明**

>&emsp;&emsp;这个示例创建了一个执行一些简单的字符串处理的实用类，再使用这个类修改一个字符串。类中的单个公共方法包含一个必选参数和3个可选参数：

>```csharp
    public static List<string> GetWords (
        string sentence,
        bool capitalizeWords = false,
        bool reverseOrder = false,
        bool reverseWords = false)
    {
        ...
    }
```

>&emsp;&emsp;这个方法返回string值的一个集合，每个string值都是初始输入的一个单词。根据指定的可选参数，可能会进行额外的转换： 对字符串集合进行整体转换，或者仅转换某个单词。

>&emsp;&emsp;**这里并未深入探讨WordProcessor类的功能，读者可以自己研究它的代码，考虑一下如何改进这些代码，例如\'twas应该为**\'Twas吗？如何进行这个修改？

>&emsp;&emsp;调用这个方法时，只使用了两个可选参数，第三个参数（reverseOrder）使用其默认值false：

>```csharp
    words = WordProcessor.GetWords(
        sentence,
        reverseWords : true,
        capitalizeWords  true);
```

>&emsp;&emsp;还要注意，所指定的两个参数的顺序与定义它们的顺序不同。

>&emsp;&emsp;最后要注意的是，处理带有可选参数的方法时，使用IntelliSense会非常方便。输入这个示例的代码时，注意GetWords()方法的工具提示，如图14-10所示（把鼠标指针停放在方法调用上，也会看到这个工具提示）。

![](/assets/屏幕快照 2017-03-27 下午4.03.21.png)

>&emsp;&emsp;这是一个非常有用的工具提示，它不仅显示了可用参数的名称，还显示了可选参数的默认值，非常便于确定是否需要重写某个默认值。


🔚
