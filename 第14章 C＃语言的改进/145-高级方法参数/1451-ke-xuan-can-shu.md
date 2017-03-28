##可选参数

&emsp;&emsp;调用方法时，常常给某个参数传送相同的值。例如，这可能是一个布尔值，以控制方法操作中的不重要部分。具体而言，考虑下面的方法定义：

```csharp
    public List<string> GetWords(string sentence, bool capitalizeWords)
    {
        ...
    }
```

&emsp;&emsp;无论给capitalizeWords参数传送什么值，这个方法都会返回一系列string值，每个string值都是输入句子中的一个单词。根据这个方法的使用方式，可能需要把返回的单词列表转换为大写（也许要格式化一个标题）。但大多数情况下并不需要这么做，所以大多数调用如下所示：

```csharp
    List<strng> words = GetWords(sentence, false);
```

&emsp;&emsp;为将这种方式变成“默认”方式，可以声明第二个方法，如下所示：

```csharp
    public List<string> GetWords(string sentence)
    {
        return GetWords(sentence, false);
    }
```

&emsp;&emsp;这个方法调用第二个方法，并给capitalizeWords传送值false。

&emsp;&emsp;这么做没有任何错误，但可以想象在使用更多的参数时，这种方式会非常复杂。

&emsp;&emsp;另一种方式是把capitalizeWords参数变成可选参数。这需要在方法定义中为参数提供一个默认值，使其成为可选参数，如果调用此方法时没有为该参数提供值，就使用默认值，如下所示：

```csharp
    public List<string> GetWords(string sentence, bool capitalizeWords = false)
    {
        ...
    }
```

&emsp;&emsp;如果以这种方式定义方法，就可以提供一个或两个参数，只有希望capitalizeWords是true时，才需要第二个参数。

####&emsp;&emsp;1. 可选参数的值

&emsp;&emsp;如上一节所述，为方法定义可选参数的语法如下所示：

```csharp
    <parameterType> <parameterName> = <defaultValue>
```

&emsp;&emsp;对于`<defaultValue>` 的值，存在一些限制。默认值必须是字面值、常量值或者默认值类型值。因此不会编译下面的代码：

```csharp
    public bool CapitalizationDefault;
    
    public List<string> GetWords(string sentence, bool capitalizeWords = CapitalizationDefault)   ❌
    {
        ...
    }
```

&emsp;&emsp;为了使上述代码可以工作，CapitalizationDefault值必须定义为常量：

```csharp
    public const bool CapitalizationDefault = false;
```

&emsp;&emsp;这是否有意义取决于具体情形，大多数情况下，最好提供一个字面值，就像上一节那样。


####&emsp;&emsp;2. Optional特性

&emsp;&emsp;除了前面小节中描述的语法，还可以使用Optional特性定义可选参数，如下所示：

```csharp
    [Optional] <parameterType> <parameterName>
```

&emsp;&emsp;此特性包含在System.Runtime.InteropServices名称空间中。注意，如果使用这种语法，就无法为参数提供默认值。


####&emsp;&emsp;3. 可选参数的顺序

&emsp;&emsp;使用可选值时，它们必须位于方法的参数列表末尾。没有默认值的参数不能放在有默认值的参数后面。

&emsp;&emsp;因此下面的代码是非法的：

```csharp
    public List<string> GetWords(bool capitalizeWords = false, string sentence)  ❌
    {
        ...
    }
```

&emsp;&emsp;其中，sentence是必选参数，因此必须放在可选参数capitalizedWords的前面。


🔚
