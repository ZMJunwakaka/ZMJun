## 基本概念与应用
### 一、 [委托（Delegate）](https://learn.microsoft.com/zh-cn/dotnet/csharp/programming-guide/delegates/)

> 定义和用法:
> 委托是一种类型安全的函数指针，允许你封装一个或多个方法。
> 定义委托时，需要指定返回类型和参数类型。
> 委托是一种引用类型，表示对具有特定参数列表和返回类型的方法的引用。 在实例化委托时，你可以将其实例与任何具有兼容签名和返回类型的方法相关联。 你可以通过委托实例调用方法。
> 
> 委托用于将方法作为参数传递给其他方法。 事件处理程序就是通过委托调用的方法。 你可以创建一个自定义方法，当发生特定事件时，某个类（如 Windows 控件）就可以调用你的方法。 

1. 基本的委托使用
示例：基本委托

```csharp

using System;

public delegate int MathOperation(int x, int y);

public class Program
{
    public static void Main()
    {
        // 实例化委托
        MathOperation add = Add;
        MathOperation subtract = Subtract;

        // 使用委托
        int sum = add(5, 3); // 8
        int difference = subtract(5, 3); // 2

        Console.WriteLine($"Sum: {sum}, Difference: {difference}");
    }

    public static int Add(int x, int y) => x + y;
    public static int Subtract(int x, int y) => x - y;
}
```
2. 委托作为参数
示例：将委托作为方法参数

```csharp

using System;

public class Program
{
    public static void Main()
    {
        // 调用计算方法，传入不同的操作
        Console.WriteLine(Calculate(5, 3, Add)); // 8
        Console.WriteLine(Calculate(5, 3, Subtract)); // 2
    }

    public static int Calculate(int x, int y, MathOperation operation)
    {
        return operation(x, y);
    }

    public static int Add(int x, int y) => x + y;
    public static int Subtract(int x, int y) => x - y;
}
```
3. 委托与事件
示例：使用委托实现事件

```csharp
using System;

public delegate void Notify(); // 委托定义

public class Process
{
    public event Notify ProcessCompleted; // 事件

    public void StartProcess()
    {
        Console.WriteLine("Process Started...");
        // 模拟处理
        System.Threading.Thread.Sleep(2000);
        OnProcessCompleted(); // 触发事件
    }

    protected virtual void OnProcessCompleted()
    {
        ProcessCompleted?.Invoke(); // 调用事件处理程序
    }
}

public class Program
{
    public static void Main()
    {
        Process process = new Process();
        process.ProcessCompleted += ProcessCompletedHandler; // 订阅事件
        process.StartProcess();
    }

    static void ProcessCompletedHandler()
    {
        Console.WriteLine("Process Completed!");
    }
}
```
4. 多播委托
示例：使用多播委托

```csharp

using System;

public delegate void MultiDelegate();

public class Program
{
    public static void Main()
    {
        MultiDelegate multiDelegate = MethodOne;
        multiDelegate += MethodTwo; // 添加多个方法

        multiDelegate(); // 调用所有添加的方法
    }

    public static void MethodOne()
    {
        Console.WriteLine("Method One Executed");
    }

    public static void MethodTwo()
    {
        Console.WriteLine("Method Two Executed");
    }
}
```

### 二、 [Action<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.action-1?view=net-6.0)

> 定义:
> Action 是一个内置的委托类型，表示一个不返回值的方法（返回类型为 void）。
> 可以有最多 16 个输入参数。

1.  使用 Action 作为回调
示例：异步操作的回调
```C#
using System;

public class Program
{
    public static void Main()
    {
        PerformAsyncOperation(() => Console.WriteLine("Operation completed!")); // 使用 Action 作为回调
    }

    public static void PerformAsyncOperation(Action callback)
    {
        Console.WriteLine("Performing async operation...");
        // 模拟异步操作
        System.Threading.Thread.Sleep(2000); 
        callback(); // 调用回调
    }
}

```
2. 使用 Action 处理多个操作
示例：多个操作的执行
```C#
using System;

public class Program
{
    public static void Main()
    {
        Action operation1 = () => Console.WriteLine("Operation 1 executed.");
        Action operation2 = () => Console.WriteLine("Operation 2 executed.");
        
        ExecuteOperations(operation1, operation2); // 将多个 Action 作为参数传递
    }

    public static void ExecuteOperations(params Action[] operations)
    {
        foreach (var operation in operations)
        {
            operation(); // 执行每个操作
        }
    }
}

```
3. 使用 Action 处理集合中的元素
示例：对集合中的每个元素执行操作
```C#
using System;
using System.Collections.Generic;

public class Program
{
    public static void Main()
    {
        List<string> names = new List<string> { "Alice", "Bob", "Charlie" };
        ProcessNames(names, name => Console.WriteLine($"Hello, {name}!")); // 使用 Action 处理每个名字
    }

    public static void ProcessNames(List<string> names, Action<string> process)
    {
        foreach (var name in names)
        {
            process(name); // 执行传入的 Action
        }
    }
}

```
#### 思想类似于PMS中Business处理上下文，只不过传preProcessors对象，对象中的多个方法处理上下文。
```C#
List<ICheckProcess<ParkingStateChangedContext>> preProcessors = new List<ICheckProcess<ParkingStateChangedContext>>();

DoBusiness(preProcessors, context);

protected void DoBusiness(List<ICheckProcess<T>> processors, T context)
{
    foreach (var item in processors)
    {
        item.CheckBeforeProcess(context);

        item.Process(context);

        item.CheckAfterProcess(context);
    }
}
```
### 三、[Func<TResult>](https://learn.microsoft.com/zh-cn/dotnet/api/system.func-1?view=net-7.0)

> 定义:
> Func 是一个内置的委托类型，表示一个返回值的方法。
> 可以有最多 16 个输入参数，最后一个类型参数是返回类型。
1. 基本使用
示例：简单的加法运算

```csharp
using System;

public class Program
{
    public static void Main()
    {
        Func<int, int, int> add = (x, y) => x + y; // 定义 Func
        int result = add(5, 3); // 调用 Func
        Console.WriteLine($"Result of addition: {result}"); // 输出 Result of addition: 8
    }
}
```
2. 返回随机数
示例：生成随机数的 Func

```csharp

using System;

public class Program
{
    public static void Main()
    {
        Func<int> getRandomNumber = () => new Random().Next(1, 101); // 生成1到100之间的随机数
        int randomNumber = getRandomNumber(); // 调用 Func
        Console.WriteLine($"Random Number: {randomNumber}");
    }
}
```
### 4.总结
委托：用于定义函数指针，可以自定义复杂的委托类型。可以将函数作为参数传入另一个方法。
Action：适合执行没有返回值的方法，如事件处理。
Func：适合执行有返回值的方法，如计算和数据转换。
通过使用 Func 和 Action，可以减少委托定义的样板代码，使得代码更加简洁和可读。