##练习

（1）创建一个集合类People，它是下述Person类的集合，该集合中的项可以通过一个字符串索引符来访问，该字符串索引符是人名，与Person.Name属性相同：

```csharp
    public class Person
    {
        private string name;
        private int age;
        
        public string Name
        {
            get
            {
                return name;
            }
            set
            {
                name = value;
            }
        }
        
        public int Age
        {
            get
            {
                return age;
            }
            set
            {
                age = value;
            }
        }
    }
```

>答案：

>```csharp
    using System;
    using System.Collections;
    
>     namespace Exercise_Answers
    {
        public class People : DictionaryBase
        {
            public void Add(Person newPerson)
            {
                Dictionary.Add(newPerson.Name, newPerson);
            }
            
>             public void Remove(string name)
            {
                Dictionary.Remove(name);
            }
            public Person this[string name]
            {
                get
                {
                    return (Person)Dictionary[name];
                }
                set
                {
                    Dictionary[name] = value;
                }
            }
        }
    }
```

（2）扩展上一题中的Person类，重载`>`、`<`、`>=`、`<=`运算符，比较Person实例的Age属性。

>答案：

>```csharp
    public class Person
    {
        private string name;
        private int age;
        
>         public string Name
        {
            get
            {
                return name;
            }
            set
            {
                name = value;
            }
        }
        
>         public int Age
        {
            get
            {
                return age;
            }
            set
            {
                age = value;
            }
        }
        
>         public static bool operator > (Person p1, Person p2)
        {
            return p1.Age > p2.Age;
        }
        
>         public static bool operator < (Person p1, Person p2)
        {
            return p1.Age < p2.Age;
        }
        
>         public static bool operator >= (Person p1, Person p2)
        {
            return !(p1 < p2);
        }
        
>         public static bool operator <= (Person p1, Person p2)
        {
            return !(p1 > p2);
        }
    }
```

（3）给People类添加GetOldest()方法，使用练习（2）中定义的重载运算符，返回其Age属性值为最大的Person对象数组（1个或多个对象，因为对于这个属性而言，多个项可以有相同的值。）

>答案：

>```csharp
    public Person[] GetOldest()
    {
        Person oldestPerson = null;
        People oldestPeople = new People();
        Person currentPerson;
        foreach(DictionaryEntry p in Dictionary)
        {
            currentPerson = p.Value as Person;
            if(oldestPerson == null)
            {
                oldestPerson = currentPerson;
                oldestPeople.Add(oldestPerson);
            }
            else
            {
                if(currentPerson > oldestPerson)
                {
                    oldestPeople.Clear();
                    oldestPeople.Add(currentPerson);
                    oldestPerson = currentPerson;
                }
                else
                {
                    if(currentPerson >= oldestPerson)
                    {
                        oldestPeople.Add(currentPerson);
                    }
                }
            }
        }// end foreach
        Person[] oldestPeopleArray = new Person[oldestPeople.Count];
        int copyIndex = 0;
        foreach(DictionaryEntry p in oldestPeople)
        {
            oldestPeopleArray[copyIndex] = p.Value as Person;
            copyIndex ++;
        }
        return oldestPeopleArray;
    }
```
>&emsp;&emsp;这个函数比较复杂，因为没有为Person定义==运算符，但仍可以构建逻辑。另外，返回People实例更加简单，因为在处理过程中比较容易操作这个类。作为一个折中方法，在整个函数中都使用了People实例，再在最后转换为一个Person实例数组。

（4）在People类上实现ICloneable接口，提供深度复制功能。

>答案：

>```csharp
    public class People : DictionaryBase, ICloneable
    {
        public object Clone()
        {
            People clonedPeople = new People();
            Person currentPerson, newPerson;
            foreach(DictionaryEntry p in Dictionary)
            {
                currentPerson = p.Value as Person;
                newPerson = new Person();
                newPerson.Name = currentPerson.Name;
                newPerson.Age = currentPerson.Age;
                clonePeople.Add(newPerson);
            }
            return clonedPeople;
        }
        ...
    }
```

>&emsp;&emsp;在Person类上实现ICloneable接口，可以简化这段代码。

（5）给People类添加一个迭代器，在下面的`foreach`循环中获取所有成员的年龄：

```csharp
    foreach(int age in myPeople.Ages)
    {
        // Display ages.
    }
```


>答案：

>```csharp
    public IEnumerable Ages
    {
        get
        {
            foreach(object person in Dictionary.Values)
            {
                yield return (person as Person).Age;
            }
        }
    }
```


🔚