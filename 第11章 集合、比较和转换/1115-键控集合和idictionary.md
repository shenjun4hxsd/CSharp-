##键控集合和 IDictionary

&emsp;&emsp;除 `IList` 接口外，集合还可以实现类似的 `IDictionary` 接口，允许项通过键值（如字符串）进行索引，而不是通过一个索引。这也可以使用索引符来完成，但这次的索引符参数是与存储的项相关联的键，而不是 `int` 索引，这样集合就更便于用户使用了。

&emsp;&emsp;与所以你的集合一样，可使用一个基类简化 `IDictionary` 接口的实现，这个基类就是 `DictionaryBase`，它也实现 `IEnumerable` 和 `ICollection`，提供了对任何集合都相同的基本集合处理功能。

&emsp;&emsp;`DictionaryBase` 与 `CollectionBase` 一样，实现通过其支持的接口获得的一些成员（但不是全部成员）。`DictionaryBase` 也实现 `Clear` 和 `Count` 成员，但不实现 `RemoveAt()`。这是因为 `RemoveAt()` 是 `IList` 接口中的一个方法，不是 `IDictionary` 接口中的一个方法。但是，`IDictionary` 有一个 `Remove()` 方法，这是一个应在基于 `DictionaryBase` 的定制集合类上实现的方法。


&emsp;&emsp;下面的代码是 `Animals` 类的另一个版本，这次该类派生于 `DictionaryBase`。下面代码包括  `Add()`、`Remove()` 和一个通过键访问的索引符的实现代码：

```javascript
	public class Animals : DictionaryBase
	{
		public void Add(string newID, Animal newAnimal)
		{
			Dictionary.Add(newID, newAnimal);
		}

		public void Remove(string animalID)
		{
			Dictionary.Remove(animalID);
		}

		public Animals() { }

		public Animal this[string animalID]
		{
			get {
				return (Animal)Dictionary[animalID];
			}
			set {
				Dictionary[animalID] = value;
			}
		}
	}
```


&emsp;&emsp;这些成员的区别如下：

&emsp;&emsp;● Add() -- 带有两个参数：一个键和一个值，存储在一起。字典集合有一个继承于 `DictionaryBase` 的成员 `Dictionary`，这个成员是一个 `IDictionary` 接口，有自己的 `Add()` 方法，该方法带有两个 `object` 参数。我们的实现代码使用一个 `string` 值作为键，使用一个 `Animal` 对象作为与该键存储在一起的数据。

&emsp;&emsp;● Remove() -- 以一个键而不是对象引用作为参数。与指定键值对应的项被删除。

&emsp;&emsp;● Indexer -- 使用一个字符串键值，而不是一个索引，用于通过 `Dictionary` 的继承成员来访问存储的项，这里仍需进行数据类型转换。


&emsp;&emsp;基于 `DictionaryBase` 的集合和基于 `CollectionBase` 的集合之间的另一个区别是 `foreach` 的工作方式稍有区别。上一节的集合可以直接从集合中提取 `Animal` 对象。使用 `foreach` 和 `DictionaryBase` 派生类可以提供 `DictionaryEntry` 结构，这是另一个在 `System.Collections` 名称空间中定义的类型。要得到 `Animal` 对象本身，就必须使用这个结构的 `Value` 成员，也可以使用结构的 `Key` 成员得到相关的键。要使代码等价于前面的代码：

```javascript
	foreach (Animal myAnimal in animalCollection)
	{
		Console.WriteLine("New {0} object added to custom collection, " + 
				  "Name = {1}", myAnimal.ToString(), myAnimal.Name);
	}
```

&emsp;&emsp;需要使用以下代码：

```javascript
	foreach (DictionaryEntry myEntry in animalCollection)
	{
		Console.WriteLine("New {0} object added to custom collection, " + 
				  "Name = {1}", myEntry.Value.ToString(),
				  ((Animal)myEntry.Value).Name);
	}
```


&emsp;&emsp;有许多方式可以重写这段代码，以便直接通过 `foreach` 访问 `Animal` 对象，最简单的方式是实现一个迭代器。




























🔚