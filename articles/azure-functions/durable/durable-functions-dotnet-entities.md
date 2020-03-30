---
title: .NET - Azure 함수의 지속성 엔터티에 대한 개발자 가이드
description: Azure 함수에 대한 지속형 함수 확장을 사용하여 .NET에서 지속적 엔터티로 작업하는 방법
author: sebastianburckhardt
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: azfuncdf
ms.openlocfilehash: 01e07eaee705634b03cc4462c4058e290daa8bc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278130"
---
# <a name="developers-guide-to-durable-entities-in-net"></a>.NET의 지속성 엔터티에 대한 개발자 가이드

이 문서에서는 예제 및 일반적인 조언을 포함하여 .NET을 사용하여 내구성 있는 엔터티를 개발하는 데 사용할 수 있는 인터페이스에 대해 자세히 설명합니다. 

엔터티 함수는 서버가 없는 응용 프로그램 개발자에게 응용 프로그램 상태를 세분화된 엔터티의 컬렉션으로 구성하는 편리한 방법을 제공합니다. 기본 개념에 대한 자세한 내용은 [지속적 엔터티: 개념](durable-functions-entities.md) 문서를 참조하십시오.

현재 엔터티를 정의하기 위한 두 개의 API를 제공합니다.

- **클래스 기반 구문은** 엔터티 및 작업을 클래스 및 메서드로 나타냅니다. 이 구문은 쉽게 읽을 수 있는 코드를 생성하고 인터페이스를 통해 형식 이 확인된 방식으로 작업을 호출할 수 있도록 합니다. 

- **함수 기반 구문은** 엔터티를 함수로 나타내는 하위 수준 인터페이스입니다. 엔터티 작업이 디스패치되는 방법과 엔터티 상태를 관리하는 방법을 정확하게 제어할 수 있습니다.  

이 문서에서는 주로 클래스 기반 구문에 중점을 두며 대부분의 응용 프로그램에 더 적합할 것으로 예상합니다. 그러나 [함수 기반 구문은](#function-based-syntax) 엔터티 상태 및 작업에 대한 자체 추상화를 정의하거나 관리하려는 응용 프로그램에 적합할 수 있습니다. 또한 현재 클래스 기반 구문에서 지원되지 않는 일반성이 필요한 라이브러리를 구현하는 데 적합할 수 있습니다. 

> [!NOTE]
> 클래스 기반 구문은 함수 기반 구문 위에 있는 계층일 뿐이므로 두 변형을 동일한 응용 프로그램에서 서로 바꿔 사용할 수 있습니다. 
 
## <a name="defining-entity-classes"></a>엔터티 클래스 정의

다음 예제는 형식 정수의 단일 값을 저장 하 고 4 개의 `Add` `Reset`작업을 `Get`제공 `Delete`하는 `Counter` 엔터티의 구현입니다.

```csharp
[JsonObject(MemberSerialization.OptIn)]
public class Counter
{
    [JsonProperty("value")]
    public int Value { get; set; }

    public void Add(int amount) 
    {
        this.Value += amount;
    }

    public Task Reset() 
    {
        this.Value = 0;
        return Task.CompletedTask;
    }

    public Task<int> Get() 
    {
        return Task.FromResult(this.Value);
    }

    public void Delete() 
    {
        Entity.Current.DeleteState();
    }

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

이 `Run` 함수에는 클래스 기반 구문을 사용하는 데 필요한 상용구가 포함되어 있습니다. *정적* Azure 함수여야 합니다. 엔터티에서 처리 되는 각 작업 메시지에 대해 한 번 실행 됩니다. 엔터티가 호출되고 엔터티가 아직 메모리에 없는 경우 `DispatchAsync<T>` 형식의 `T` 개체를 생성하고 저장소에 있는 마지막 지속JSON에서 해당 필드를 채웁니다(있는 경우). 그런 다음 일치하는 이름으로 메서드를 호출합니다.

> [!NOTE]
> 클래스 기반 엔터티의 상태는 엔터티가 작업을 처리하기 전에 **암시적으로 만들어지며** 을 호출하여 `Entity.Current.DeleteState()`작업에서 **명시적으로 삭제할** 수 있습니다.

### <a name="class-requirements"></a>클래스 요구 사항
 
엔터티 클래스는 특별한 수퍼클래스, 인터페이스 또는 특성이 필요하지 않은 POCO(일반 이전 CLR 개체)입니다. 단,

- 클래스는 생성 가능해야 [합니다(엔터티 생성](#entity-construction)참조).
- 클래스는 JSON 직렬화 가능해야 [합니다(엔터티 직렬화](#entity-serialization)참조).

또한 작업으로 호출하려는 메서드는 추가 요구 사항을 충족해야 합니다.

- 작업에는 하나의 인수가 있어야 하며 오버로드 또는 제네릭 형식 인수가 없어야 합니다.
- 인터페이스를 사용하여 오케스트레이션에서 호출되는 작업은 `Task` `Task<T>`반환하거나 .
- 인수 및 반환 값은 직렬화 가능한 값 또는 개체여야 합니다.

### <a name="what-can-operations-do"></a>작업은 무엇을 할 수 있습니까?

모든 엔터티 작업은 엔터티 상태를 읽고 업데이트할 수 있으며 상태 변경 사항은 저장소에 자동으로 유지됩니다. 또한 작업은 모든 Azure Functions에 공통적인 일반적인 제한 내에서 외부 I/O 또는 기타 계산을 수행할 수 있습니다.

작업은 컨텍스트에서 `Entity.Current` 제공하는 기능에 도 액세스할 수 있습니다.

* `EntityName`: 현재 실행 중인 엔터티의 이름입니다.
* `EntityKey`: 현재 실행 중인 엔터티의 키입니다.
* `EntityId`: 현재 실행 중인 엔터티의 ID(이름 및 키 포함)입니다.
* `SignalEntity`: 엔터티에 단방향 메시지를 보냅니다.
* `CreateNewOrchestration`: 새 오케스트레이션을 시작합니다.
* `DeleteState`: 이 엔터티의 상태를 삭제합니다.

예를 들어 카운터가 100에 도달하면 오케스트레이션을 시작하고 엔터티 ID를 입력 인수로 전달되도록 카운터 엔터티를 수정할 수 있습니다.

```csharp
    public void Add(int amount) 
    {
        if (this.Value < 100 && this.Value + amount > 100)
        {
            Entity.Current.StartNewOrchestration("MilestoneReached", Entity.Current.EntityId)
        }
        this.Value += amount;      
    }
```

## <a name="accessing-entities-directly"></a>엔터티에 직접 액세스

엔터티 및 해당 작업에 대 한 명시적 문자열 이름을 사용 하 여 클래스 기반 엔터티에 직접 액세스할 수 있습니다. 아래에서 몇 가지 예를 제공합니다. 기본 개념(예: 신호 대 호출)에 대한 자세한 설명은 [Access 엔터티에서](durable-functions-entities.md#access-entities)토론을 참조하십시오. 

> [!NOTE]
> 가능한 경우 더 많은 형식 검사를 제공하므로 [인터페이스를 통해 엔터티에 액세스하는](#accessing-entities-through-interfaces)것이 좋습니다.

### <a name="example-client-signals-entity"></a>예: 클라이언트 신호 엔티티

다음 Azure Http Function은 REST 규칙을 사용하여 DELETE 작업을 구현합니다. URL 경로에서 키가 전달되는 카운터 엔터티에 삭제 신호를 보냅니다.

```csharp
[FunctionName("DeleteCounter")]
public static async Task<HttpResponseMessage> DeleteCounter(
    [HttpTrigger(AuthorizationLevel.Function, "delete", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    await client.SignalEntityAsync(entityId, "Delete");    
    return req.CreateResponse(HttpStatusCode.Accepted);
}
```

### <a name="example-client-reads-entity-state"></a>예: 클라이언트가 엔터티 상태를 읽습니다.

다음 Azure Http Function은 REST 규칙을 사용하여 GET 작업을 구현합니다. URL 경로에서 키가 전달되는 카운터 엔터티의 현재 상태를 읽습니다.

```csharp
[FunctionName("GetCounter")]
public static async Task<HttpResponseMessage> GetCounter(
    [HttpTrigger(AuthorizationLevel.Function, "get", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    var state = await client.ReadEntityStateAsync<Counter>(entityId); 
    return req.CreateResponse(state);
}
```

> [!NOTE]
> 반환되는 `ReadEntityStateAsync` 개체는 로컬 복사본, 즉 이전 시점의 엔터티 상태의 스냅숏일 뿐입니다. 특히 부실일 수 있으며 이 개체를 수정하면 실제 엔터티에 영향을 주지 않습니다. 

### <a name="example-orchestration-first-signals-then-calls-entity"></a>예: 오케스트레이션 첫 번째 신호, 다음 엔터티를 호출합니다.

다음 오케스트레이션은 카운터 엔터티에 증분 신호를 표시한 다음 동일한 엔터티를 호출하여 최신 값을 읽습니다.

```csharp
[FunctionName("IncrementThenGet")]
public static async Task<int> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId("Counter", "myCounter");

    // One-way signal to the entity - does not await a response
    context.SignalEntity(entityId, "Add", 1);

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");

    return currentValue;
}
```

## <a name="accessing-entities-through-interfaces"></a>인터페이스를 통한 엔터티 액세스

인터페이스는 생성된 프록시 개체를 통해 엔터티에 액세스하는 데 사용할 수 있습니다. 이 방법을 사용하면 작업의 이름 및 인수 유형이 구현된 형식과 일치하도록 합니다. 가능하면 엔터티에 액세스하기 위해 인터페이스를 사용하는 것이 좋습니다.

예를 들어 다음과 같이 카운터 예제를 수정할 수 있습니다.

```csharp
public interface ICounter
{
    void Add(int amount);
    Task Reset();
    Task<int> Get();
    void Delete();
}

public class Counter : ICounter
{
    ...
}
```

엔터티 클래스 및 엔터티 인터페이스는 [올리언스](https://www.microsoft.com/research/project/orleans-virtual-actors/)에서 널리 사용하는 그레인 및 그레인 인터페이스와 유사합니다. 지속적 엔터티와 올리언스 간의 유사성 및 차이점에 대한 자세한 내용은 [가상 액터와의 비교를](durable-functions-entities.md#comparison-with-virtual-actors)참조하십시오.

인터페이스는 형식 검사를 제공하는 것 외에도 응용 프로그램 내에서 문제를 더 잘 분리하는 데 유용합니다. 예를 들어 엔터티가 여러 인터페이스를 구현할 수 있기 때문에 단일 엔터티가 여러 역할을 제공할 수 있습니다. 또한 인터페이스는 여러 엔터티에 의해 구현될 수 있기 때문에 일반 통신 패턴을 재사용 가능한 라이브러리로 구현할 수 있습니다.

### <a name="example-client-signals-entity-through-interface"></a>예: 인터페이스를 통한 클라이언트 신호 엔티티

클라이언트 코드는 `SignalEntityAsync<TEntityInterface>` 을 구현하는 `TEntityInterface`엔터티에 신호를 보내는 데 사용할 수 있습니다. 예를 들어:

```csharp
[FunctionName("DeleteCounter")]
public static async Task<HttpResponseMessage> DeleteCounter(
    [HttpTrigger(AuthorizationLevel.Function, "delete", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    await client.SignalEntityAsync<ICounter>(entityId, proxy => proxy.Delete());    
    return req.CreateResponse(HttpStatusCode.Accepted);
}
```

이 예제에서 `proxy` 매개 변수는 내부적으로 호출을 `ICounter` `Delete` 신호로 변환하는 동적으로 생성된 인스턴스입니다.

> [!NOTE]
> API는 `SignalEntityAsync` 단방향 작업에만 사용할 수 있습니다. 작업이 반환되더라도 `Task<T>`매개 변수값은 `T` 항상 null이거나 `default`실제 결과가 아닙니다.
예를 들어 값이 반환되지 않으므로 `Get` 작업에 신호를 보내도 적합하지 않습니다. 대신 클라이언트는 카운터 `ReadStateAsync` 상태에 직접 액세스하거나 작업을 호출하는 오케스트레이터 `Get` 함수를 시작할 수 있습니다. 

### <a name="example-orchestration-first-signals-then-calls-entity-through-proxy"></a>예: 오케스트레이션 첫 번째 신호, 프록시를 통해 엔터티 호출

오케스트레이션 내에서 엔터티를 호출하거나 `CreateEntityProxy` 신호를 표시하려면 인터페이스 유형과 함께 엔터티에 대한 프록시를 생성하는 데 사용할 수 있습니다. 그런 다음 이 프록시를 사용하여 작업을 호출하거나 신호를 표시할 수 있습니다.

```csharp
[FunctionName("IncrementThenGet")]
public static async Task<int> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId("Counter", "myCounter");
    var proxy = context.CreateEntityProxy<ICounter>(entityId);

    // One-way signal to the entity - does not await a response
    proxy.Add(1);

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await proxy.Get();

    return currentValue;
}
```

암시적으로 반환되는 `void` 모든 작업과 반환되거나 `Task` `Task<T>` 호출되는 모든 작업이 신호를 받습니다. 메서드를 명시적으로 사용 하 여 `SignalEntity<IInterfaceType>` 이 기본 동작을 변경할 수 있으며 Task를 반환 하는 경우에 시그널 작업 수 있습니다.

### <a name="shorter-option-for-specifying-the-target"></a>대상 을 지정하기 위한 짧은 옵션

인터페이스를 사용하여 엔터티를 호출하거나 신호를 지정할 때 첫 번째 인수는 대상 엔터티를 지정해야 합니다. 대상은 엔터티 ID를 지정하여 지정하거나 엔터티를 구현하는 클래스가 하나뿐인 경우 엔터티 키만 지정할 수 있습니다.

```csharp
context.SignalEntity<ICounter>(new EntityId(nameof(Counter), "myCounter"), ...);
context.SignalEntity<ICounter>("myCounter", ...);
```

엔터티 키만 지정되고 런타임시 고유한 구현을 찾을 수 `InvalidOperationException` 없는 경우 throw됩니다. 

### <a name="restrictions-on-entity-interfaces"></a>엔터티 인터페이스에 대한 제한 사항

평소와 같이 모든 매개 변수 및 반환 형식은 JSON serializable이어야합니다. 그렇지 않으면 직렬화 예외는 런타임에 throw됩니다.

또한 다음과 같은 몇 가지 추가 규칙을 시행합니다.
* 엔터티 인터페이스는 메서드만 정의해야 합니다.
* 엔터티 인터페이스에는 제네릭 매개 변수가 포함되어서는 안 됩니다.
* 엔터티 인터페이스 메서드에는 두 개 이상의 매개 변수가 없어야 합니다.
* 엔터티 인터페이스 메서드는 을 반환해야 `void` `Task`합니다.`Task<T>` 

이러한 규칙을 위반하는 경우 인터페이스가 `InvalidOperationException` `SignalEntity` 형식 인수 또는 `CreateProxy`에 대한 형식 인수로 사용될 때 런타임에 throw됩니다. 예외 메시지는 어떤 규칙이 손상됐는지 설명합니다.

> [!NOTE]
> 반환되는 `void` 인터페이스 메서드는 호출되지 않은 단방향(양방향)만 신호를 받을 수 있습니다. 인터페이스 메서드가 반환되거나 `Task` 호출되거나 `Task<T>` 신호될 수 있습니다. 호출된 경우 작업 결과를 반환하거나 작업에서 throw된 예외를 다시 throw합니다. 그러나 신호를 받으면 작업에서 실제 결과 또는 예외를 반환하지 않고 기본값만 반환합니다.

## <a name="entity-serialization"></a>엔터티 직렬화

엔터티의 상태가 지속적으로 유지되므로 엔터티 클래스는 serializ가능해야 합니다. 지속형 함수 런타임은 이 목적을 위해 [Json.NET](https://www.newtonsoft.com/json) 라이브러리를 사용하며, 이 라이브러리는 여러 정책 및 특성을 지원하여 직렬화 및 역직렬화 프로세스를 제어합니다. 가장 일반적으로 사용되는 C# 데이터 형식(배열 및 컬렉션 형식 포함)은 이미 직렬화할 수 있으며 내구성 있는 엔터티의 상태를 정의하는 데 쉽게 사용할 수 있습니다.

예를 들어 Json.NET 다음 클래스를 쉽게 직렬화하고 역직렬화할 수 있습니다.

```csharp
[JsonObject(MemberSerialization = MemberSerialization.OptIn)]
public class User
{
    [JsonProperty("name")]
    public string Name { get; set; }

    [JsonProperty("yearOfBirth")]
    public int YearOfBirth { get; set; }

    [JsonProperty("timestamp")]
    public DateTime Timestamp { get; set; }

    [JsonProperty("contacts")]
    public Dictionary<Guid, Contact> Contacts { get; set; } = new Dictionary<Guid, Contact>();

    [JsonObject(MemberSerialization = MemberSerialization.OptOut)]
    public struct Contact
    {
        public string Name;
        public string Number;
    }

    ...
}
```

### <a name="serialization-attributes"></a>직렬화 특성

위의 예제에서는 기본 직렬화를 보다 잘 볼 수 있도록 몇 가지 특성을 포함하기로 결정했습니다.
- 클래스는 직렬화할 `[JsonObject(MemberSerialization.OptIn)]` 수 있어야 하며 JSON 속성으로 명시적으로 표시된 멤버만 유지하도록 클래스에 추가합니다.
-  유지될 `[JsonProperty("name")]` 필드에 대해 필드가 지속성 엔터티 상태의 일부임을 상기시키고 JSON 표현에 사용할 속성 이름을 지정하기 위해 필드에 추가합니다.

그러나 이러한 특성은 필요하지 않습니다. 다른 규칙이나 특성은 Json.NET 함께 작업하는 한 허용됩니다. 예를 들어 특성을 `[DataContract]` 사용하거나 특성을 전혀 사용하지 않을 수 있습니다.

```csharp
[DataContract]
public class Counter
{
    [DataMember]
    public int Value { get; set; }
    ...
}

public class Counter
{
    public int Value;
    ...
}
```

기본적으로 클래스의 이름은 JSON 표현의 일부로 *저장되지 않습니다.* `TypeNameHandling.None` 이 기본 동작은 사용 `JsonObject` 하거나 `JsonProperty` 특성을 재정의할 수 있습니다.

### <a name="making-changes-to-class-definitions"></a>클래스 정의를 변경합니다.

저장된 JSON 개체가 더 이상 새 클래스 정의와 일치하지 않을 수 있으므로 응용 프로그램을 실행한 후 클래스 정의를 변경할 때는 주의가 필요합니다. 그러나 에서 사용하는 직렬화 프로세스를 이해하는 한 데이터 형식 변경을 올바르게 처리할 `JsonConvert.PopulateObject`수 있는 경우가 많습니다.

예를 들어 변경 사항과 그 효과에 대한 몇 가지 예는 다음과 같습니다.

1. 저장된 JSON에 없는 새 속성이 추가되면 기본값을 가정합니다.
1. 저장된 JSON에 있는 속성이 제거되면 이전 콘텐츠가 손실됩니다.
1. 속성의 이름이 바뀌면 이전 속성의 이름을 제거하고 새 속성을 추가하는 것처럼 효과가 발생합니다.
1. 저장된 JSON에서 더 이상 직렬화할 수 없도록 속성 형식이 변경되면 예외가 throw됩니다.
1. 속성의 형식이 변경되었지만 저장된 JSON에서 역직렬화될 수 있는 경우 해당 속성은 변경됩니다.

Json.NET 동작을 사용자 지정하는 데 사용할 수 있는 많은 옵션이 있습니다. 예를 들어 저장된 JSON에 클래스에 없는 필드가 포함된 경우 예외를 강제로 `JsonObject(MissingMemberHandling = MissingMemberHandling.Error)`지정하려면 특성을 지정합니다. 임의의 형식으로 저장된 JSON을 읽을 수 있는 직렬화를 위한 사용자 지정 코드를 작성할 수도 있습니다.

## <a name="entity-construction"></a>엔터티 구성

때로는 엔터티 개체가 생성되는 방법을 보다 세한 제어하려고 합니다. 이제 엔터티 개체를 생성할 때 기본 동작을 변경하기 위한 몇 가지 옵션에 대해 설명합니다. 

### <a name="custom-initialization-on-first-access"></a>첫 번째 액세스시 사용자 지정 초기화

경우에 따라 액세스되지 않았거나 삭제된 엔터티에 작업을 디스패치하기 전에 몇 가지 특수 초기화를 수행해야 합니다. 이 동작을 지정하려면 `DispatchAsync`다음 전에 조건을 추가할 수 있습니다.

```csharp
[FunctionName(nameof(Counter))]
public static Task Run([EntityTrigger] IDurableEntityContext ctx)
{
    if (!ctx.HasState)
    {
        ctx.SetState(...);
    }
    return ctx.DispatchAsync<Counter>();
}
```

### <a name="bindings-in-entity-classes"></a>엔터티 클래스의 바인딩

일반 함수와 달리 엔터티 클래스 메서드는 입력 및 출력 바인딩에 직접 액세스할 수 없습니다. 대신, 진입점 함수 선언에서 바인딩 데이터를 캡처한 다음, `DispatchAsync<T>` 메서드에 전달해야 합니다. `DispatchAsync<T>`에 전달된 모든 개체는 자동으로 엔터티 클래스 생성자에 인수로 전달됩니다.

다음 예제에서는 [Blob 입력 바인딩](../functions-bindings-storage-blob-input.md)의 `CloudBlobContainer` 참조를 클래스 기반 엔터티에 사용할 수 있게 하는 방법을 보여 줍니다.

```csharp
public class BlobBackedEntity
{
    [JsonIgnore]
    private readonly CloudBlobContainer container;

    public BlobBackedEntity(CloudBlobContainer container)
    {
        this.container = container;
    }

    // ... entity methods can use this.container in their implementations ...
    
    [FunctionName(nameof(BlobBackedEntity))]
    public static Task Run(
        [EntityTrigger] IDurableEntityContext context,
        [Blob("my-container", FileAccess.Read)] CloudBlobContainer container)
    {
        // passing the binding object as a parameter makes it available to the
        // entity class constructor
        return context.DispatchAsync<BlobBackedEntity>(container);
    }
}
```

Azure Functions의 바인딩에 대한 자세한 내용은 [Azure Functions 트리거 및 바인딩](../functions-triggers-bindings.md) 설명서를 참조하세요.

### <a name="dependency-injection-in-entity-classes"></a>엔터티 클래스의 종속성 주입

엔터티 클래스는 [Azure Functions 종속성 주입](../functions-dotnet-dependency-injection.md)을 지원합니다. 다음 예제에서는 `IHttpClientFactory` 서비스를 클래스 기반 엔터티에 등록하는 방법을 보여 줍니다.

```csharp
[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();
        }
    }
}
```

다음 코드 조각에서는 주입된 서비스를 엔터티 클래스에 통합하는 방법을 보여 줍니다.

```csharp
public class HttpEntity
{
    [JsonIgnore]
    private readonly HttpClient client;

    public class HttpEntity(IHttpClientFactory factory)
    {
        this.client = factory.CreateClient();
    }

    public Task<int> GetAsync(string url)
    {
        using (var response = await this.client.GetAsync(url))
        {
            return (int)response.StatusCode;
        }
    }

    [FunctionName(nameof(HttpEntity))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<HttpEntity>();
}
```

> [!NOTE]
> 직렬화 문제를 방지하려면 직렬화에서 주입된 값을 저장하는 필드를 제외해야 합니다.

> [!NOTE]
> 일반 .NET Azure Functions에서 생성자 주입을 사용하는 경우와 달리 클래스 기반 엔터티에 대한 함수 진입점 메서드를 `static`으로 *선언해야* 합니다. 비정적 함수 진입점을 선언하면 일반 Azure Functions 개체 이니셜라이저와 지속성 엔터티 개체 이니셜라이저 간에 충돌이 발생할 수 있습니다.

## <a name="function-based-syntax"></a>함수 기반 구문

지금까지 는 대부분의 응용 프로그램에 더 적합 할 것으로 예상하기 때문에 클래스 기반 구문에 중점을 두어 했습니다. 그러나 함수 기반 구문은 엔터티 상태 및 작업에 대한 자체 추상화를 정의하거나 관리하려는 응용 프로그램에 적합할 수 있습니다. 또한 현재 클래스 기반 구문에서 지원되지 않는 일반성이 필요한 라이브러리를 구현할 때 적절할 수 있습니다. 

함수 기반 구문을 사용하면 Entity Function은 작업 디스패치를 명시적으로 처리하고 엔터티의 상태를 명시적으로 관리합니다. 예를 들어 다음 코드는 함수 기반 구문을 사용하여 구현된 *Counter* 엔터티를 보여 주며 있습니다.  

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            ctx.SetState(ctx.GetState<int>() + ctx.GetInput<int>());
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(ctx.GetState<int>()));
            break;
        case "delete":
            ctx.DeleteState();
            break;
    }
}
```

### <a name="the-entity-context-object"></a>엔터티 컨텍스트 개체

엔터티 별 기능은 형식의 `IDurableEntityContext`컨텍스트 개체를 통해 액세스할 수 있습니다. 이 컨텍스트 개체는 엔터티 함수및 비동기 로컬 속성을 `Entity.Current`통해 매개 변수로 사용할 수 있습니다.

다음 멤버는 현재 작업에 대한 정보를 제공하고 반환 값을 지정할 수 있도록 합니다. 

* `EntityName`: 현재 실행 중인 엔터티의 이름입니다.
* `EntityKey`: 현재 실행 중인 엔터티의 키입니다.
* `EntityId`: 현재 실행 중인 엔터티의 ID(이름 및 키 포함)입니다.
* `OperationName`: 현재 작업의 이름입니다.
* `GetInput<TInput>()`: 현재 작업에 대한 입력을 가져옵니다.
* `Return(arg)`: 작업을 호출한 오케스트레이션에 값을 반환합니다.

다음 멤버는 엔터티의 상태(생성, 읽기, 업데이트, 삭제)를 관리합니다. 

* `HasState`: 엔터티가 있는지 여부, 즉 일부 상태가 있습니다. 
* `GetState<TState>()`: 엔터티의 현재 상태를 가져옵니다. 아직 존재하지 않으면 만들어집니다.
* `SetState(arg)`: 엔터티의 상태를 만들거나 업데이트합니다.
* `DeleteState()`: 엔터티가 있는 경우 엔터티의 상태를 삭제합니다. 

반환되는 `GetState` 상태가 개체인 경우 응용 프로그램 코드에서 직접 수정할 수 있습니다. 끝에 다시 호출 `SetState` 할 필요가 없습니다 (뿐만 아니라 해를 끼치지 않습니다). 여러 `GetState<TState>` 번 호출되는 경우 동일한 형식을 사용해야 합니다.

마지막으로 다음 멤버는 다른 엔터티에 신호를 표시하거나 새 오케스트레이션을 시작하는 데 사용됩니다.

* `SignalEntity(EntityId, operation, input)`: 엔터티에 단방향 메시지를 보냅니다.
* `CreateNewOrchestration(orchestratorFunctionName, input)`: 새 오케스트레이션을 시작합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [엔터티 개념에 대해 알아보기](durable-functions-entities.md)
