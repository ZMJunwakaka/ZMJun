#### FirstOrDefault()：返回集合中匹配指定条件的第一个元素。如果没有元素匹配，则返回该类型的默认值（例如，对于引用类型返回 null，对于整数等值类型返回 0）。
### 1.值类型
```C#
List<int> numbers = new List<int> { 1, 2, 3, 4, 5 };
int firstNumber = numbers.FirstOrDefault(); // Returns 1

List<int> numbers = new List<int> { 1, 2, 3, 4, 5 };
int firstEvenNumber = numbers.FirstOrDefault(n => n % 2 == 0); // Returns 2

List<int> emptyList = new List<int>();
int result = emptyList.FirstOrDefault(); // Returns 0 (default value for int)
```
### 2.引用类型
```C#
List<Person> emptyList = new List<Person>();

Person result = emptyList.FirstOrDefault(); // 返回 null
//如果 emptyList 是 null，调用 FirstOrDefault() 将抛出一个 NullReferenceException。
//如果 emptyList 不为 null，调用 FirstOrDefault() 获取第一个元素或返回 null（如果列表为空）。

Person result = emptyList?.FirstOrDefault();
//使用空条件操作符
//如果 emptyList 是 null， result 会是 null，不会执行 FirstOrDefault()。
//如果 emptyList 不为 null，调用 FirstOrDefault() 获取第一个元素或返回 null（如果列表为空）。

```