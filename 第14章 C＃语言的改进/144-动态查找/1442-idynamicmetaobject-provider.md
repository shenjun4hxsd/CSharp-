##IDynamicMetaObjectProvider

&emsp;&emsp;在继续之前，应注意如何使用动态类型，或者更确切地讲，在运行期间对成员访问应用某种技术时会发生什么。实际上，有3种不同的方式访问成员：

```
    • 如果动态值是COM对象，就使用COM技术访问成员（通过IUnknown接口，但这里不需要了解它）。
    • 如果动态值支持IDynamicMetaObjectProvider接口，就使用该接口访问类型成员。
    • 如果不能使用上述两种技术，就使用反射。
```

&emsp;&emsp;第二种情形比较有趣，它涉及`IDynamicMetaObjectProvider`接口。这里不探讨具体细节，只是注意可以实现这个接口，来准确地控制在运行期间访问成员时会发生什么。但这是高级编程图书的一个主题，因此这里不予讨论。


🔚