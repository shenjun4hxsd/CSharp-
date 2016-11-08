##键控集合和 IDictionary

&emsp;&emsp;除 `IList` 接口外，集合还可以实现类似的 `IDictionary` 接口，允许项通过键值（如字符串）进行索引，而不是通过一个索引。这也可以使用索引符来完成，但这次的索引符参数是与存储的项相关联的键，而不是 `int` 索引，这样集合就更便于用户使用了。

&emsp;&emsp;与所以你的集合一样，可使用一个基类简化 `IDictionary` 接口的实现，这个基类就是 `DictionaryBase`，它也实现 `IEnumerable` 和 `ICollection`，提供了对任何集合都相同的基本集合处理功能。

&emsp;&emsp;