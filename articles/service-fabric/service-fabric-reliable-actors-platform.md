---
title: "Service Fabric의 Reliable Actors| Microsoft Docs"
description: "Reliable Actors를 Reliable Services에 계층화하고 서비스 패브릭 플랫폼의 기능을 사용하는 방법을 설명합니다."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: 45839a7f-0536-46f1-ae2b-8ba3556407fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/19/2016
ms.author: vturecek
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 12565a57415642d68397982decb834a0c573ccbe


---
# <a name="how-reliable-actors-use-the-service-fabric-platform"></a>신뢰할 수 있는 행위자가 서비스 패브릭 플랫폼을 사용하는 방법
이 문서에서는 서비스 패브릭 플랫폼에서 Reliable Actors가 작동하는 방법을 설명합니다. Reliable Actors는 *행위자 서비스*라는 상태 저장 Reliable Service의 구현에서 호스팅되는 프레임워크에서 실행됩니다. 행위자 서비스는 행위자에게 발송되는 수명 주기 및 메시지를 관리하는 데 필요한 모든 구성 요소를 포함합니다.

* 행위자 런타임은 수명 주기, 가비지 수집을 관리하고 단일 스레드 액세스를 적용합니다.
* 행위자 서비스 원격 수신기는 행위자에 대한 원격 액세스 호출을 허용하고 적절한 행위자 인스턴스를 라우팅하는 디스패처에게 보냅니다.
* 행위자 상태 제공자는 상태 공급자(예: 신뢰할 수 있는 컬렉션 상태 공급자)를 래핑하고 행위자 상태 관리에 대한 어댑터를 제공합니다.

이러한 구성 요소는 Reliable Actor 프레임워크를 함께 구성합니다. 

## <a name="service-layering"></a>서비스 계층
행위자 서비스 자체가 Reliable Service이므로 Reliable Services의 [응용 프로그램 모델](service-fabric-application-model.md), 수명 주기, [패키징](service-fabric-application-model.md#package-an-application), [배포]((service-fabric-deploy-remove-applications.md#deploy-an-application), 업그레이드 및 개념 확장은 모두 행위자 서비스에 동일한 방식으로 적용됩니다. 

![행위자 서비스 계층][1]

위의 다이어그램은 서비스 패브릭 응용 프로그램 프레임워크와 사용자 코드 간의 관계를 보여줍니다. 블루 요소는 Reliable Services 응용 프로그램 프레임워크를 나타내고 오렌지는 Reliable Actor 프레임워크를 나타내며 그린은 사용자 코드를 나타냅니다. 

Reliable Services에서 서비스는 자체가 `StatefulServiceBase`에서 파생된 `StatefulService` 클래스를 상속합니다(또는 상태 비저장 서비스의 경우 `StatelessService`). Reliable Actors에서 행위자를 실행하는 행위자 패턴을 `StatefulServiceBase` 클래스의 다른 구현인 행위자 서비스를 사용합니다. 행위자 서비스 자체는 `StatefulServiceBase`의 구현일 뿐이기 때문에 `ActorService`에서 파생된 고유한 서비스를 작성할 수 있고 다음과 같이 `StatefulService`을 상속하는 경우와 동일한 방식으로 서비스 수준 기능을 구현할 수 있습니다.

* 서비스 백업 및 복원입니다.
* 예를 들어 회로 차단기 등 모든 행위자에 대한 공유 기능입니다.
* 원격 프로시저는 각 개별 행위자 뿐만 아니라 행위자 서비스 자체를 호출합니다. 

### <a name="using-the-actor-service"></a>행위자 서비스 사용
행위자 인스턴스는 실행 중인 행위자 서비스에 액세스할 수 있습니다. 행위자 서비스임에도 행위자 인스턴스는 파티션 ID, 서비스 이름, 응용 프로그램 이름 및 기타 서비스 패브릭 플랫폼 관련 정보를 가진 서비스 콘텍스트를 프로그래밍 방식으로 가져올 수 있습니다.

```csharp
Task MyActorMethod()
{
    Guid partitionId = this.ActorService.Context.PartitionId;
    string serviceTypeName = this.ActorService.Context.ServiceTypeName;
    Uri serviceInstanceName = this.ActorService.Context.ServiceName;
    string applicationInstanceName = this.ActorService.Context.CodePackageActivationContext.ApplicationName;
}
```

Reliable Services처럼 행위자 서비스는 서비스 패브릭 런타임에서 서비스 유형으로 등록되어야 합니다. 행위자 서비스에서 행위자 인스턴스를 실행하려면 행위자 서비스에 행위자 유형이 등록되어야 합니다. `ActorRuntime` 등록 메서드가 행위자에 대한 이 작업을 수행합니다. 가장 간단한 경우 행위자 형식만을 등록할 수 있고 기본 설정이 있는 행위자 서비스는 암시적으로 다음과 같은 경우 사용됩니다.

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>().GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```  

또는 행위자 서비스를 직접 생성하는 등록 메서드가 제공하는 람다를 사용할 수 있습니다. 이 옵션을 사용하면 생성자를 통해 행위자에 종속성을 주입할 수 있는 행위자 인스턴스를 명시적으로 생성할 뿐만 아니라 행위자 서비스를 구성할 수 있습니다.

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new ActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```

### <a name="actor-service-methods"></a>행위자 서비스 메서드
행위자 서비스는 `IService`를 구현하는 `IActorService`을 구현합니다. 그러면 원격 서비스 메서드에서 프로시저 호출을 허용하지 않는 Reliable Services 원격 서비스에서 사용되는 인터페이스입니다. 원격 서비스를 사용하여 원격으로 호출할 수 있는 서비스 수준 메서드가 포함되어 있습니다.

#### <a name="enumerating-actors"></a>행위자 열거
행위자 서비스를 사용하면 클라이언트가 서비스에 의해 호스트되는 행위자에 대한 메타데이터를 열거할 수 있습니다. 행위자 서비스는 분할된 상태 저장 서비스이므로 열거는 파티션에 따라 수행됩니다. 각 파티션이 많은 수의 행위자를 포함할 수 있기 때문에 열거는 일련의 페이징된 결과로 반환됩니다. 페이지는 모든 페이지를 읽을 때까지 반복됩니다. 다음 예제에서는 행위자 서비스의 한 파티션에 있는 모든 활성 행위자의 목록을 만드는 방법을 보여줍니다.

```csharp
IActorService actorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new List<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = await actorServiceProxy.GetActorsAsync(continuationToken, cancellationToken);

    activeActors.AddRange(page.Items.Where(x => x.IsActive));

    continuationToken = page.ContinuationToken;
}
while (continuationToken != null);
```

#### <a name="deleting-actors"></a>행위자 삭제
행위자 서비스에서도 행위자를 삭제하는 기능을 제공합니다.

```csharp
ActorId actorToDelete = new ActorId(id);

IActorService myActorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

await myActorServiceProxy.DeleteActorAsync(actorToDelete, cancellationToken)
```

행위자와 해당 상태를 삭제하는 데에 대한 자세한 내용은 [행위자 수명 주기 설명서](service-fabric-reliable-actors-lifecycle.md)를 참조합니다.

### <a name="custom-actor-service"></a>사용자 지정 행위자 서비스
행위자 등록 람다를 사용하여 고유한 서비스 수준 기능을 구현할 수 있는 `ActorService` 에서 파생되는 고유한 사용자 지정 행위자 서비스를 등록할 수도 있습니다. `ActorService`를 상속하는 서비스 클래스를 작성하여 수행합니다. 사용자 지정 행위자 서비스는 `ActorService` 으로부터 행위자 런타임 기능을 모두 상속하고 고유한 서비스 메서드를 구현하는 데 사용될 수 있습니다.

```csharp
class MyActorService : ActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }
}
```

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new MyActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```


#### <a name="implementing-actor-back-up-and-restore"></a>행위자 백업 및 복원 구현
 다음 예제에서는 사용자 지정 행위자 서비스가 `ActorService`에 이미 나타난 원격 수신기를 활용하여 행위자 데이터를 백업하는 메서드를 노출합니다.

```csharp
public interface IMyActorService : IService
{
    Task BackupActorsAsync();
}

class MyActorService : ActorService, IMyActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }

    public Task BackupActorsAsync()
    {
        return this.BackupAsync(new BackupDescription(PerformBackupAsync));
    }

    private async Task<bool> PerformBackupAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
    {
        try
        {
           // store the contents of backupInfo.Directory
           return true;
        }
        finally
        {
           Directory.Delete(backupInfo.Directory, recursive: true);
        }
    }
}
```

이 예제에서 `IMyActorService`은 `IService`를 구현하는 원격 계약이고 `MyActorService`에서 구현됩니다. 이 원격 서비스 계약을 추가하면 `IMyActorService`의 메서드도 `ActorServiceProxy`를 사용하는 원격 프록시를 만들어 클라이언트에 사용할 수 있게 됩니다.

```csharp
IMyActorService myActorServiceProxy = ActorServiceProxy.Create<IMyActorService>(
    new Uri("fabric:/MyApp/MyService"), ActorId.CreateRandom());

await myActorServiceProxy.BackupActorsAsync();
```


## <a name="application-model"></a>응용 프로그램 모델
행위자 서비스는 Reliable Services에 속하므로 응용 프로그램 모델이 동일합니다. 그러나 행위자 프레임워크 빌드 도구는 대부분의 응용 프로그램 모델 파일을 생성합니다.

### <a name="service-manifest"></a>서비스 매니페스트
행위자 서비스의 ServiceManifest.xml 콘텐츠는 행위자 프레임워크 빌드 도구에 의해 자동으로 생성됩니다. 다음 내용이 포함됩니다.

* 행위자 서비스 유형. 유형 이름은 행위자 프로젝트 이름에 따라 생성됩니다. 행위자의 지속성 특성에 따라 HasPersistedState 플래그도 적절하게 설정됩니다.
* 코드 패키지.
* 구성 패키지.
* 장치 및 끝점

### <a name="application-manifest"></a>응용 프로그램 매니페스트
행위자 프레임워크 빌드 도구는 행위자 서비스에 대한 기본 서비스 정의를 자동으로 만듭니다. 기본 서비스 속성은 빌드 도구에 의해 채워집니다.

* 복제본 세트 수는 행위자의 지속성 특성에 의해 결정됩니다. 행위자의 지속성 특성이 변경될 때마다 기본 서비스 정의에 있는 복제본 세트 수는 적절하게 다시 설정됩니다.
* 파티션 구성표와 범위는 전체 Int64 키 범위인 균일한 Int64로 설정됩니다.

## <a name="service-fabric-partition-concepts-for-actors"></a>행위자에 대한 서비스 패브릭 파티션 개념
행위자 서비스는 분할된 상태 저장 서비스입니다. 행위자 서비스의 각 파티션은 일련의 행위자를 포함합니다. 서비스 파티션은 서비스 패브릭에 있는 여러 노드에 자동으로 배포됩니다. 따라서 결과적으로 행위자 인스턴스가 배포됩니다.

![행위자 분할 및 배포][5]

Reliable Services는 다른 파티션 구성표와 파티션 키 범위로 만들어질 수 있습니다. 행위자 서비스는 전체 Int64 키 범위를 가진 Int64 파티션 구성표를 사용하여 파티션에 행위자를 매핑합니다. 

### <a name="actor-id"></a>행위자 ID
서비스에서 만들어진 각 행위자에는 그와 관련된 고유한 ID가 있고 `ActorId` 클래스에서 나타납니다. `ActorId` 는 임의의 ID를 생성하여 서비스 파티션에 행위자를 균일하게 배포하기 위해 사용될 수 있는 불투명 ID 값입니다.

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```

모든 `ActorId` 은 Int64로 해시되며 이는 행위자 서비스가 전체 Int64 키 범위가 있는 Int64 파티션 구성표를 사용해야 하는 이유입니다. 그러나 사용자 지정 ID 값은 GUID, 문자열 및 Int64를 비롯한 `ActorID`에 사용할 수 있습니다. 

```csharp
ActorProxy.Create<IMyActor>(new ActorId(Guid.NewGuid()));
ActorProxy.Create<IMyActor>(new ActorId("myActorId"));
ActorProxy.Create<IMyActor>(new ActorId(1234));
```

GUID 및 문자열을 사용하는 경우 값은 Int64로 해시됩니다. 그러나 명시적으로 `ActorId`에 대한 Int64를 제공하는 경우 Int64는 해시를 추가하지 않고 파티션에 직접 매핑됩니다. 파티션 행위자를 배치하는 위치를 제어하는 데 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [행위자 상태 관리](service-fabric-reliable-actors-state-management.md)
* [행위자 수명 주기 및 가비지 수집](service-fabric-reliable-actors-lifecycle.md)
* [행위자 API 참조 설명서](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [샘플 코드](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png



<!--HONumber=Nov16_HO3-->


