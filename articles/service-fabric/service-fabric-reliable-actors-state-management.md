---
title: Reliable Actors 상태 관리 | Microsoft Docs
description: 고가용성을 위해 Reliable Actors 상태를 관리, 유지 및 복제하는 방법을 설명합니다.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/06/2016
ms.author: vturecek

---
# Reliable Actors 상태 관리
Reliable Actors는 논리와 상태를 모두 캡슐화할 수 있는 단일 스레드 개체입니다. 행위자가 Reliable Services에서 실행되므로 Reliable Services에서 사용되는 동일한 지속성 및 복제 메커니즘을 사용하여 안전하게 상태를 유지할 수 있습니다. 이러한 방식으로 행위자는 실패한 후에 해당 상태, 가비지 수집 후에 다시 활성화를 손실하지 않습니다. 또한 리소스 균형 조정 또는 업그레이드로 인해 클러스터의 노드 간에 이동하는 경우에도 그렇습니다.

## 상태 지속성 및 복제
각 행위자 인스턴스가 고유 ID에 매핑되기 때문에 모든 Reliable Actors는 *상태 저장*이라고 여겨집니다. 즉, 동일한 행위자 ID에 대한 반복된 호출이 동일한 행위자 인스턴스에 라우팅됩니다. 이것은 클라이언트 호출이 매번 동일한 서버에 라우팅되도록 보장되지 않는 상태 비저장 시스템과 대조적입니다. 따라서 행위자 서비스는 항상 상태 저장 서비스입니다.

그러나 행위자가 상태 저장을 고려하더라도 상태를 안정적으로 저장해야 하는 것은 아닙니다. 행위자는 해당 데이터 저장소 요구 사항에 따라 상태 지속성 및 복제의 수준을 선택할 수 있습니다.

* **지속된 상태:** 상태가 디스크에 유지되고 3개 이상의 복제본에 복제됩니다. 상태가 전체 클러스터 가동 중단을 통해 유지할 수 있는 가장 지속적인 상태 저장 옵션입니다.
* **일시적 상태:** 상태는 3개 이상의 복제본에 복제되고 메모리에만 보관됩니다. 업그레이드 및 리소스 균형을 조정하는 동안 노드 실패, 행위자 실패에 대한 복원력을 제공합니다. 그러나 상태가 디스크에 유지되지 않으므로 한 번에 모든 복제본이 손실되면 상태도 손실됩니다.
* **지속된 상태 없음:** 상태는 복제되거나 디스크에 기록되지 않습니다. 단순히 상태를 안정적으로 유지할 필요가 없는 행위자의 경우입니다.

지속성의 수준은 각각 단순히 *상태 제공자* 및 서비스의 *복제* 구성에 따라 다릅니다. 상태가 디스크에 기록되는지 여부는 상태를 저장하는 Reliable Service의 구성 요소인 *상태 제공자*에 따라 달라지고 복제는 서비스를 배포한 복제본 수에 따라 달라집니다. Reliable Services와 마찬가지로 상태 제공자와 복제본 수는 모두 쉽게 수동으로 설정될 수 있습니다. 행위자 프레임워크는 특성을 제공합니다. 이 특성은 행위자에 사용될 경우 자동으로 기본 상태 제공자를 선택하고 이러한 세 가지 지속성 설정 중 하나를 달성하기 위해 복제본 수에 대한 설정을 자동으로 생성합니다.

### 지속된 상태
```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
}
```  
이 설정은 디스크에 데이터를 저장하는 상태 제공자를 사용하고 자동으로 서비스 복제본 수를 3으로 설정합니다.

### 일시적 상태
```csharp
[StatePersistence(StatePersistence.Volatile)]
class MyActor : Actor, IMyActor
{
}
```
이 설정은 메모리내 전용 상태 제공자를 사용하고 복제본 수를 3으로 설정합니다.

### 지속된 상태 없음
```csharp
[StatePersistence(StatePersistence.None)]
class MyActor : Actor, IMyActor
{
}
```
이 설정은 메모리내 전용 상태 제공자를 사용하고 복제본 수를 1로 설정합니다.

### 기본값 및 생성된 설정
`StatePersistence` 특성을 사용하는 경우 행위자 서비스를 시작할 때 런타임 시 상태 제공자가 자동으로 선택됩니다. 그러나 복제본 수는 Visual Studio 행위자 빌드 도구에 의해 컴파일 시점에 설정됩니다. 빌드 도구는 ApplicationManifest.xml의 행위자 서비스에 대한 *기본 서비스*를 자동으로 생성합니다. **최소 복제본 세트 크기** 및 **대상 복제본 세트 크기**에 대한 매개 변수를 만듭니다. 이러한 매개 변수를 수동으로 변경할 수 있지만 `StatePersistence` 특성을 변경할 때마다 매개 변수는 이전 값을 재정의하여 선택된 `StatePersistence` 특성에 대한 기본 복제본 세트 크기 값으로 설정됩니다. 즉, ServiceManifest.xml에서 설정한 값은 `StatePersistence` 특성 값을 변경하는 경우 빌드 시 재정의되기**만** 합니다.

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application12Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="MyActorService_PartitionCount" DefaultValue="10" />
      <Parameter Name="MyActorService_MinReplicaSetSize" DefaultValue="2" />
      <Parameter Name="MyActorService_TargetReplicaSetSize" DefaultValue="3" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyActorPkg" ServiceManifestVersion="1.0.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MyActorService" GeneratedIdRef="77d965dc-85fb-488c-bd06-c6c1fe29d593|Persisted">
         <StatefulService ServiceTypeName="MyActorServiceType" TargetReplicaSetSize="[MyActorService_TargetReplicaSetSize]" MinReplicaSetSize="[MyActorService_MinReplicaSetSize]">
            <UniformInt64Partition PartitionCount="[MyActorService_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatefulService>
      </Service>
   </DefaultServices>
</ApplicationManifest>
```

## 상태 관리자
모든 행위자 인스턴스에는 안정적으로 키-값 쌍을 저장하는 사전 형식의 데이터 구조인 고유한 상태 관리자가 있습니다. 상태 관리자는 상태 제공자에 대한 래퍼입니다. 상태 관리자는 사용한 지속성 설정에 관계없이 데이터를 저장하는 데 사용될 수 있지만 실행 중인 행위자 서비스가 데이터를 유지하는 동안 롤링 업그레이드를 통해 지속된 상태로 설정된 일시적인(메모리내 전용) 상태에서 변경될 수 있음을 보증하지 않습니다. 그러나 실행 중인 서비스에 대한 복제본 수를 변경할 수 있습니다.

값은 사용자 지정 형식을 포함하여 일반적인 모든 형식이 될 수 있지만 상태 관리자 키는 문자열이어야 합니다. 복제하는 동안 네트워크를 통해 다른 노드로 전송될 수 있기 때문에 상태 관리자에 저장된 값은 데이터 계약을 직렬화 가능해야 하며 행위자의 상태 지속성 설정에 따라 디스크에 기록할 수 있습니다.

상태 관리자는 신뢰할 수 있는 사전에 있는 것과 비슷한 상태를 관리하기 위한 일반적인 사전 메서드를 제공합니다.

### 상태 액세스
상태 관리자를 통해 키로 상태에 액세스할 수 있습니다. 상태 관리자 메서드는 행위자가 지속된 상태인 경우 디스크 I/O를 필요할 수 있기에 모두 비동기적입니다. 첫 번째 액세스에서 상태 개체는 메모리에 캐시됩니다. 반복 액세스 작업은 메모리에서 직접 개체에 액세스하고 오버헤드를 전환하는 디스크 I/O 또는 비동기 컨텍스트를 발생시키지 않고 동기적으로 반환합니다. 상태 개체는 다음과 같은 경우에 캐시에서 제거됩니다.

* 행위자 메서드는 상태 관리자에서 개체를 검색한 후에 처리되지 않은 예외를 throw합니다.
* 비활성화된 후에 또는 오류로 인해 행위자가 다시 활성화됩니다.
* 상태 제공자 디스크에는 상태를 페이징한 경우입니다. 이 동작은 상태 제공자 구현에 따라 다릅니다. `Persisted` 설정에 대한 기본 상태 제공자는 이 동작을 포함합니다.

지정된 키에 대한 항목이 없는 경우 `KeyNotFoundException`를 throw하는 표준 *가져오기* 작업을 사용하여 상태를 검색할 수 있습니다.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public Task<int> GetCountAsync()
    {
        return this.StateManager.GetStateAsync<int>("MyState");
    }
}
```

또한 지정된 키에 대한 항목이 없는 경우 throw하지 않는 *TryGet* 메서드를 사용하여 상태를 검색할 수 있습니다.

```csharp
class MyActor : Actor, IMyActor
{
    public async Task<int> GetCountAsync()
    {
        ConditionalValue<int> result = await this.StateManager.TryGetStateAsync<int>("MyState");
        if (result.HasValue)
        {
            return result.Value;
        }

        return 0;
    }
}
```

### 상태 저장
상태 관리자 검색 메서드는 로컬 메모리에 개체에 대한 참조를 반환합니다. 로컬 메모리에서 이 개체를 수정하는 것만으로는 지속적으로 저장하지 못합니다. 개체를 상태 관리자에서 검색하고 수정하는 경우 지속적으로 저장되도록 상태 관리자에 다시 삽입해야 합니다.

`dictionary["key"] = value` 구문과 동등한 무조건 *설정*을 사용하여 상태를 삽입할 수 있습니다.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public Task SetCountAsync(int value)
    {
        return this.StateManager.SetStateAsync<int>("MyState", value);
    }
}
```

이미 있는 키를 추가하려는 경우 `InvalidOperationException`를 throw하는 *추가* 메서드를 사용하여 상태를 추가할 수 있습니다.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public Task AddCountAsync(int value)
    {
        return this.StateManager.AddStateAsync<int>("MyState", value);
    }
}
```

또한 이미 있는 키를 추가하려는 경우 throw하지 않는 *TryAdd* 메서드를 사용하여 상태를 추가할 수 있습니다.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public async Task AddCountAsync(int value)
    {
        bool result = await this.StateManager.TryAddStateAsync<int>("MyState", value);

        if (result)
        {
            // Added successfully!
        }
    }
}
```

행위자 메서드가 끝날 때 상태 관리자는 삽입 또는 업데이트 작업에 의해 추가되거나 수정된 모든 값을 자동으로 저장합니다. "저장"은 사용된 설정에 따라 디스크와 복제를 지속하도록 포함할 수 있습니다. 수정되지 않은 값은 지속되거나 복제되지 않습니다. 수정된 값이 없는 경우 저장 작업은 아무 것도 수행하지 않습니다. 실패를 저장하는 이벤트에서 수정된 상태는 삭제되고 원래 상태를 다시 로드합니다.

또한 상태는 수동으로 저장되고 행위자 기초의 `SaveStateAsync` 메서드를 호출할 수 있습니다.

```csharp
async Task IMyActor.SetCountAsync(int count)
{
    await this.StateManager.AddOrUpdateStateAsync("count", count, (key, value) => count > value ? count : value);

    await this.SaveStateAsync();
}
```

### 상태 제거
*제거* 메서드 호출하여 행위자의 상태 관리자에서 상태를 영구적으로 제거할 수 있습니다. 이 메서드는 존재하지 않는 키를 제거하려고 할 때 `KeyNotFoundException`를 throw합니다.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public Task RemoveCountAsync()
    {
        return this.StateManager.RemoveStateAsync("MyState");
    }
}
```

상태는 *TryRemove* 메서드를 사용하여 영구적으로 제거될 수도 있으며 이는 존재하지 않는 키를 제거하려고 할 때 throw하지 않습니다.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public async Task RemoveCountAsync()
    {
        bool result = await this.StateManager.TryRemoveStateAsync("MyState");

        if (result)
        {
            // State removed!
        }
    }
}
```

## 다음 단계
* [행위자 형식 직렬화](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)
* [행위자 다형성 및 개체 지향 디자인 패턴](service-fabric-reliable-actors-polymorphism.md)
* [행위자 진단 및 성능 모니터링](service-fabric-reliable-actors-diagnostics.md)
* [행위자 API 참조 설명서](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [샘플 코드](https://github.com/Azure/servicefabric-samples)

<!---HONumber=AcomDC_0713_2016-->