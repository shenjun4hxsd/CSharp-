##深度复制

&emsp;&emsp;第9章通过下面的GetCopy()方法，介绍了如何使用受保护的方法`System.Object.MemberwiseClone()`进行浅度复制。

```csharp
    public class Cloner
    {
        public int Val;
        
        public Cloner(int newVal)
        {
            Val = newVal;
        }
        
        public object GetCopy()
        {
            return MemberwishClone();
        }
    }
```

&emsp;&emsp;假定有引用类型的字段，而不是值类型的字段（例如，对象）：

```csharp
    public class Content
    {
        public int Val;
    }
    
    public class Cloner
    {
        public Content MyContent = new Content();
        
        public Cloner(int newVal)
        {
            MyContent.Val = newVal;
        }
        
        public object GetCopy()
        {
            return MemberwishClone();
        }
    }
```

&emsp;&emsp;此时，通过`GetCopy()`得到的浅度复制包括一个字段，它引用的对象与源对象相同。以下代码使用这个`Cloner`类来说明浅度复制引用类型的结果：

```csharp
    Cloner mySource = new Cloner(5);
    Cloner myTarget = (Cloner)mySource.GetCopy();
    Console.WriteLine("myTarget.MyContent.Val = {0}", myTarget.MyContent.Val);
    mySource.MyContent.Val = 2;
    Console.WriteLine("myTarget.MyContent.Val = {0}", myTarget.MyContent.Val);
```

&emsp;&emsp;第4行把一个值赋给`mySource.MyContent.Val`，它是源对象中公共字段`MyContent`的公共字段`Val`。这也改变了`myTarget.MyContent.Val`的值。这是因为`mySource.MyContent`引用了与`myTarget.MyContent`相同的对象实例。上述代码的输出结果如下：

```csharp
    myTarget.MyContent.Val = 5
    myTarget.MyContent.Val = 2
```
