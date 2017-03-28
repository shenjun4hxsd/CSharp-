##练习

（1）下面哪些元素可以是泛型？

```
    a. 类
    b. 方法
    c. 属性
    d. 运算符重载
    e. 结构
    f. 枚举
```

>答案：a、b和e是；c和d否，但它们可以使用由包含它们的类提供的泛型类型参数。f否。

（2）扩展Ch12Ex01中的Vector类，使*运算符返回两个矢量的点积。

>答案：

>```csharp
    public static double? operator * (Vector op1, Vector op2)
    {
        try
        {
            double angleDiff = (double)(op2.ThetaRadians.Value - 
                op1.ThetaRadians.Value);
            return op1.R.Value * op2.R.Value * Math.Cos(angleDiff);
        }
        catch
        {
            return null;
        }
    }
```

>&emsp;&emsp;两个矢量的点积定义为两个矢量的大小与两个矢量之间夹角余弦的乘积。

（3）下面的代码存在什么错误？请加以修改。

```csharp
    public class Instantiator<T>
    {
        public T instance;
        
        public Instantiator()
        {
            instance = new T();
        }
    }
```

>答案：不在T上强制new()约束，就不能实例化T。在T上强制new()约束可以确保有一个公共的默认构造函数是可用的。

>```csharp
    public class Instantiator<T>
        where T : new()
    {
        public T instance;
        
>         public Instantiator()
        {
            instance = new T();
        }
    }
```



（4）下面的代码存在什么错误？请加以修改。

```csharp
    public class StringGetter<T>
    {
        public string GetString<T>(T item)
        {
            return item.ToString();
        }
    }
```

>答案：同一个泛型类型参数T既用于泛型类，又用于泛型方法。需要重命名其中的一个或两个。例如：

>```csharp
    public class StringGetter<U>
    {
        public string GetString<T>(T item)
        {
            return item.ToString();
        }
    }
```



（5）创建一个泛型类`ShortList<T>`，它实现了`IList<T>`，包含一个项集合及集合的最大容量。这个最大容量应是一个整数，并可以提供给`ShortList<T>`的构造函数，或者默认为10。构造函数还应通过`IEnumerable<T>`参数获取项的最初列表。该类与`List<T>`的功能相同，但如果试图给集合添加太多的项，或者传递给构造函数的`IEnumerable<T>`包含太多的项，就会抛出IndexOutOfRangeException类型的异常。

>答案：

```csharp
    pbulic class ShortList<T> : IList<T>
    {
        protected IList<T> innerCollection;
        protected int maxSize = 10;
        
        public ShortList() : this(10)
        {
        }
        
        public ShortList(IEnumerable<T> list) : this(10, list)
        {
        }
        
        public ShortList(int size, IEnumerable<T> list)
        {
            maxSize = size;
            innerCollection = new List<T>(list);
            if(Count > maxSize)
            {
                ThrowTooManyItemsException();
            }
        }
        
        protected void ThrowTooManyItemsException()
        {
            throw new IndexOutOfRangeException(
                "Unable to add any more items, maximum size is " + maxSize.ToString()
                + " items.");
        }
        
        #region IList<T> Members
        
        public int IndexOf(T item)
        {
            return innerCollection.IndexOf(item);
        }
        
        public void Insert(int index, T item)
        {
            if(Count < maxSize)
            {
                innerCollection.Insert(index, item);
            }
            else
            {
                ThrowTooManyItemsException();
            }
        }
        
        public void RemoveAt(int index)
        {
            innerCollection.RemoveAt(index);
        }
        
        public T this[int index]
        {
            get
            {
                return innerCollection[index];
            }
            set
            {
                innerCollection[index] = value;
            }
        }
        #endregion
        
        #region ICollection<T> Members
        
        public void Add(T item)
        {
            if(Count < maxSize)
            {
                innerCollection.Add(item);
            }
            else
            {
                ThrowTooManyItemsException();
            }
        }
        
        public void Clear()
        {
            innerCollection.Clear();
        }
        
        public bool ConTains(T item)
        {
            return innerCollection.Contains(item);
        }
        
        public void CopyTo(T[] array, int arrayIndex)
        {
            innerCollection.CopyTo(array, arrayIndex);
        }
        
        public int Count
        {
            get
            {
                return innerCollection.Count;
            }
        }
        
        public bool IsReadOnly
        {
            get
            {
                return innerCollection.IsReadOnly;
            }
        }
        
        public bool Remove(T item)
        {
            return innerCollection.Remove(item);
        }
        #endregion
        
        #region IEnumerable<T> Members
        
        public IEnumerator<T> GetEnumerator()
        {
            return innerCollection.GetEnumerator();
        } 
        #endregion
        
        #region IEnumerable Members
        
        IEnumerator IEnumerable.GetEnumerator()
        {
            return GetEnumerator();
        }
        
        #endregion
    }
```

（6）下面的代码可以进行编译吗？试说明原因。

```csharp
    public interface IMethaneProducer<out T>
    {
        void BelchAt(T target);
    }
```

>答案：不，类型参数T定义为协变。但协变参数类型只能用作方法的返回值，不能用作方法实参。否则就会得到如下编译错误（假定使用名称空间VarianceDemo）：

>```csharp
    Invalid variance: The type parameter 'T' must be contravariantly valid on
    'VarianceDemo.IMethaneProcucer<T>.BelchAt(T)'. 'T' is covariant.
```

🔚