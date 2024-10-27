### 1.事件

> [类](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/class) 或对象可以通过事件向其他类或对象通知发生的相关事情。 发送（或引发）事件的类称为“发布者”，接收（或处理）事件的类称为“订阅者”。

### 2.using与dispose方法结合，用于单元测试mock（单元测试持久化一条数据->测试->using结束调用dispose方法删除该数据）
#### PMS代码总结：
这段代码定义了一个模拟上下文类 `ParkingOrderMockContext`，并包含了事件处理、资源清理和停车订单的初始化逻辑。以下是对这段代码的详细讲解。
```C#
public delegate void EventHandler(object? sender, EventArgs e);
public class ParkingOrderMockContext
{
    public ParkingOrderMockContext()
    {
        OnCleanEvent += MockContext_OnCleanEvent;
    }

    public event EventHandler OnCleanEvent;
    public void Dispose()
    {
        OnCleanEvent?.Invoke(this, null);
    }

    public ParkingOrdersEntity InitParkingOrder_新建(SelectedSoltVMExt SelectedSoltVM, VehicleInfoVM VehicleInfoVM)
    {
        ParkingOrdersEntity ParkingOrder = new ParkingOrdersEntity();
        var Builder = new ParkingOrderBuilder(ParkingOrder);
        Builder.ConstructBasicInfo();
        Builder.ConstructSlotInfo(SelectedSoltVM);
        Builder.ConstructVehicleInfo(VehicleInfoVM);
    
        OnCleanEvent += (sender, e) => TestDataBase.Delete("tdy_parkingorders", "id", ParkingOrder.Id);
    
        return ParkingOrder;
    }

    public ParkingOrderMockContext InitParkingOrder_InDB(ParkingOrdersEntity ParkingOrder)
    {
        var repo = new ParkingOrderRepository();
        repo.InsertParkingOrder(ParkingOrder);

        return this;
    }

}

//单元测试
[TestMethod()]
public void 下发查询队列任务状态_任务不存在()
{
    using (ParkingOrderMockContext ParingOrderMockContext = new ParkingOrderMockContext())
    {
        //构建数据，添加删除事件
        var parkingOrder = ParingOrderMockContext.InitParkingOrder_新建(SlotResult, VehicleInfo);
        //持久化一条数据
        ParingOrderMockContext.InitParkingOrder_InDB(parkingOrder);
        // 单元测试代码
    }
        //using结束调用Dispose()代码删除持久化的测试数据
}
```

### 1. 委托定义

```csharp
public delegate void EventHandler(object? sender, EventArgs e);
```

- **EventHandler** 是一个委托类型，定义了事件处理方法的签名。它接受两个参数：
  - `object? sender`：触发事件的对象，可以为 `null`。
  - `EventArgs e`：包含事件相关数据的对象，通常是 `EventArgs` 类型或其子类。

### 2. ParkingOrderMockContext 类

```csharp
public class ParkingOrderMockContext
{
    public ParkingOrderMockContext()
    {
        OnCleanEvent += MockContext_OnCleanEvent;
    }
```

- **ParkingOrderMockContext** 类用于模拟停车订单的上下文。构造函数中，订阅了 `OnCleanEvent` 事件，指定了处理程序 `MockContext_OnCleanEvent`。

#### 2.1 事件定义

```csharp
public event EventHandler OnCleanEvent;
```

- **OnCleanEvent** 是一个事件，使用 `EventHandler` 委托类型。其他类可以订阅这个事件，以响应清理操作。

#### 2.2 Dispose 方法

```csharp
public void Dispose()
{
    OnCleanEvent?.Invoke(this, null);
}
```

- **Dispose** 方法用于释放资源。当调用 `Dispose` 时，触发 `OnCleanEvent` 事件。使用了空合并运算符 `?.` 确保只有在有订阅者时才调用事件。

### 3. InitParkingOrder_新建 方法

```csharp
public ParkingOrdersEntity InitParkingOrder_新建(SelectedSoltVMExt SelectedSoltVM, VehicleInfoVM VehicleInfoVM)
{
    ParkingOrdersEntity ParkingOrder = new ParkingOrdersEntity();
    var Builder = new ParkingOrderBuilder(ParkingOrder);
    Builder.ConstructBasicInfo();
    Builder.ConstructSlotInfo(SelectedSoltVM);
    Builder.ConstructVehicleInfo(VehicleInfoVM);
```

- **InitParkingOrder_新建** 方法用于初始化一个新的停车订单。
- 创建一个 `ParkingOrdersEntity` 对象，并使用 `ParkingOrderBuilder` 构建基本信息、槽位信息和车辆信息。

#### 3.1 事件处理程序

```csharp
OnCleanEvent += (sender, e) => TestDataBase.Delete("tdy_parkingorders", "id", ParkingOrder.Id);
```

- 在初始化停车订单时，订阅 `OnCleanEvent` 事件。当事件被触发时，调用 `TestDataBase.Delete` 方法删除对应 ID 的停车订单记录。这确保在清理上下文时，相关的数据库记录也会被删除。

### 4. 使用示例

```csharp
using (ParkingOrderMockContext ParingOrderMockContext = new ParkingOrderMockContext())
{
    var parkingOrder = ParingOrderMockContext.InitParkingOrder_新建(SlotResult, VehicleInfo);
    // 单元测试代码
}
```

- 使用 `using` 语句块创建 `ParkingOrderMockContext` 的实例。这样可以确保在块结束时自动调用 `Dispose` 方法，触发 `OnCleanEvent`。
- 调用 `InitParkingOrder_新建` 方法以初始化停车订单。此时，停车订单的相关信息将被构建。

### 总结

1. **委托和事件**：代码使用自定义的 `EventHandler` 委托和 `OnCleanEvent` 事件来处理清理操作。
2. **资源管理**：通过实现 `Dispose` 方法来释放资源，并触发事件以清理数据库中的相关记录。
3. **初始化停车订单**：在 `InitParkingOrder_新建` 方法中，创建并初始化停车订单，同时设置清理逻辑。

此结构有助于在单元测试或模拟场景中创建和管理停车订单，同时确保在不再需要上下文时能够清理相关数据。如果你有更多问题或需要更详细的解释，请告诉我！