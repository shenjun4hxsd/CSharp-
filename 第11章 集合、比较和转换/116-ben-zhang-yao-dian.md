##本章要点

|主题|要点|
|:--|:--|
|定义集合|集合是可以包含其他类的实例的类。要定义集合，可从CollectionBase中派生，或者自己实现集合接口，例如IEnumerable、ICollection和IList。一般需要为集合定义一个索引器，以使用collection[index]语法来访问集合成员。|
|字典|也可以定义键控集合，即字典，字典中的每一项都有一个关联的键。在字典中，键可以用于标识一项，而不必使用该项的索引。定义字典时，可以实现IDictionary，或者从DictionaryBase中派生类|
|迭代器|可以实现一个迭代器，来控制循环代码如何在其循环过程中获取值。要迭代一个类，需要实现GetEnumerator()方法，其返回类型是IEnumerator。要迭代类的成员，例如方法，可使用IEnumerable返回类型。在迭代器的代码块中，使用yield关键字返回值|
|类型比较|可以使用GetType()方法获得对象的类型，使用typeof()运算符可以获得类的类型。可以比较这些类型值。还可以使用is运算符确定对象是否与某个类类型兼容|
|值比较|如果希望类的实例可以用标准的C#运算符进行比较，就必须在类定义中重载这些运算符。对于其他类型的值比较，可使用实现了IComparable或iComparer接口的类。这些接口特别适用于集合的排序|
|as运算符|可使用as运算符把一个值转换为引用类型。如果不能进行转换，as运算符就返回null值|

🔚