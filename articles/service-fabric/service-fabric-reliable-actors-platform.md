<properties
   pageTitle="서비스 패브릭의 신뢰할 수 있는 행위자| Microsoft Azure"
   description="신뢰할 수 있는 행위자가 행위자 개발자의 견지에서 서비스 패브릭 플랫폼의 기능을 어떻게 사용하는지 설명합니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="myamanbh"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/15/2016"
   ms.author="amanbha"/>

# 신뢰할 수 있는 행위자가 서비스 패브릭 플랫폼을 사용하는 방법

행위자는 Azure 서비스 패브릭 응용 프로그램 모델을 사용하여 응용 프로그램 수명 주기를 관리합니다. 모든 행위자 형식이 서비스 패브릭 [서비스 유형](service-fabric-application-model.md#describe-a-service)에 매핑됩니다. 행위자 코드는 서비스 패브릭 응용 프로그램으로 [패키지](service-fabric-application-model.md#package-an-application)되며 클러스터에 [배포](service-fabric-deploy-remove-applications.md#deploy-an-application)됩니다.

## 행위자에 대한 응용 프로그램 모델 개념 예

[Visual Studio를 사용하여 만든](service-fabric-reliable-actors-get-started.md) 행위자 프로젝트의 예를 통해 위의 개념 중 몇 가지를 살펴보겠습니다.

응용 프로그램 매니페스트, 서비스 매니페스트 및 Settings.xml 구성 파일은 Visual Studio에서 솔루션을 만들 때 행위자 서비스를 위한 프로젝트에 포함됩니다. 여기에 대해서 아래 스크린샷에 나와 있습니다.

![Visual Studio를 통해 만든 프로젝트][1]

행위자가 패키지되는 응용 프로그램 유형 및 응용 프로그램 버전은 해당 행위자 서비스에 대한 프로젝트에 포함된 응용 프로그램 매니페스트를 살펴보면 찾을 수 있습니다. 응용 프로그램 매니페스트에서 가져온 다음 코드 조각을 예로 들 수 있습니다.

~~~
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                     ApplicationTypeName="VoiceMailBoxApplication"
                     ApplicationTypeVersion="1.0.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric">
~~~

행위자 형식이 매핑되는 서비스 유형은 행위자 서비스에 대한 프로젝트에 포함된 서비스 매니페스트를 살펴보면 찾을 수 있습니다. 서비스 매니페스트에서 가져온 다음 코드 조각을 예로 들 수 있습니다.

~~~
<StatefulServiceType ServiceTypeName="VoiceMailBoxActorServiceType" HasPersistedState="true">
~~~

Visual Studio를 통해 응용 프로그램 패키지를 만들면 빌드 출력 창의 로그가 응용 프로그램 패키지의 위치를 나타냅니다. 예:

    -------- Package started: Project: VoiceMailBoxApplication, Configuration: Debug x64 ------
    VoiceMailBoxApplication -> C:\samples\Samples\Actors\VS2015\VoiceMailBox\VoiceMailBoxApplication\pkg\Debug

다음은 위의 위치 목록의 일부입니다(간단한 표시를 위해 전체 목록 생략).

    C:\samples\Samples\Actors\VS2015\VoiceMailBox\VoiceMailBoxApplication\pkg\Debug>tree /f
    Folder PATH listing
    Volume serial number is 303F-6F91
    C:.
    │   ApplicationManifest.xml
    │
    ├───VoiceMailBoxActorServicePkg
    │   │   ServiceManifest.xml
    │   │
    │   ├───Code
    │   │   │   Microsoft.ServiceFabric.Actors.dll
    │   │   │       :
    │   │   │   Microsoft.ServiceFabric.Services.dll
    │   │   │   ServiceFabricServiceModel.dll
    │   │   │   System.Fabric.Common.Internal.dll
    │   │   │   System.Fabric.Common.Internal.Strings.dll
    │   │   │   VoiceMailBox.exe
    │   │   │   VoiceMailBox.exe.config
    │   │   │   VoiceMailBox.Interfaces.dll
    │   │   │
    │   │   └───ko-KR
    │   │           System.Fabric.Common.Internal.Strings.resources.dll
    │   │
    │   └───Config
    │           Settings.xml
    │
    └───VoicemailBoxWebServicePkg
        │   ServiceManifest.xml
        │
        └───Code
            │   Microsoft.Owin.dll
            │       :
            │   Microsoft.ServiceFabric.Services.dll
            │       :
            │   System.Fabric.Common.Internal.dll
            │   System.Fabric.Common.Internal.Strings.dll
            │       :
            │   VoiceMailBox.Interfaces.dll
            │   VoicemailBoxWebService.exe
            │   VoicemailBoxWebService.exe.config
            │
            └───ko-KR
                    System.Fabric.Common.Internal.Strings.resources.dll

위 목록에서는 응용 프로그램 패키지 내 서비스 패키지 내에 있는 코드 패키지에 포함된 VoicemailBox 행위자 가져오기 작업을 구현하는 어셈블리를 보여줍니다.

또한 응용 프로그램의 후속 관리(즉, 업그레이드 및 최종 삭제)는 서비스 패브릭 응용 프로그램 관리 메커니즘을 통해 수행됩니다. 자세한 내용은 [응용 프로그램 모델](service-fabric-application-model.md), [응용 프로그램 배포 및 제거](service-fabric-deploy-remove-applications.md), [응용 프로그램 업그레이드](service-fabric-application-upgrade.md)에 대한 항목을 참조하세요.

## 행위자 서비스에 대한 확장성
클러스터 관리자는 클러스터의 각 서비스 유형에 대해 하나 이상의 행위자 서비스를 만들 수 있습니다. 각 행위자 서비스는 하나 이상의 파티션(다른 서비스 패브릭 서비스와 비슷)을 가질 수 있습니다. 한 서비스 유형(행위자 형식에 매핑)의 서비스를 여러 개 만드는 기능과 한 서비스에 대해 여러 파티션을 만들 수 있는 기능을 사용하면 행위자 응용 프로그램을 확장할 수 있습니다. 자세한 내용은 [확장성](service-fabric-concepts-scalability.md)에 대한 문서를 참조하세요.

> [AZURE.NOTE] 상태 비저장 행위자 서비스는 [인스턴스](service-fabric-availability-services.md#availability-of-service-fabric-stateless-services) 수로 1이 필요합니다. 파티션 내에서 상태 비저장 행위자 서비스에 대해 1을 초과하는 인스턴스는 지원되지 않습니다. 따라서, 상태 비저장 행위자 서비스는 확장성을 달성하기 위해 인스턴스 수를 증가하는 옵션을 두지 않습니다. [확장성 문서](service-fabric-concepts-scalability.md)에 설명되어 있는 확장성 옵션을 반드시 사용해야 합니다.

## 행위자에 대한 서비스 패브릭 파티션 개념
행위자의 ID는 행위자 서비스의 파티션에 매핑됩니다. 행위자는 해당 행위자 ID가 매핑되는 파티션 내에 생성됩니다. 행위자를 만들 때 행위자 런타임은 행위자를 만든 파티션을 나타내는 [EventSource 이벤트](service-fabric-reliable-actors-diagnostics.md#eventsource-events)를 기록합니다. 아래에는 ID `-5349766044453424161`인 행위자가 서비스 `fabric:/VoicemailBoxAdvancedApplication/VoicemailBoxActorService`, 응용 프로그램 `fabric:/VoicemailBoxAdvancedApplication`의 파티션 `b6afef61-be9a-4492-8358-8f473e5d2487` 내에 만들어졌음을 나타내는 이벤트의 예가 나와 있습니다.

    {
      "Timestamp": "2015-04-26T10:12:20.2485941-07:00",
      "ProviderName": "Microsoft-ServiceFabric-Actors",
      "Id": 5,
      "Message": "Actor activated. Actor type: Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor, actor ID: -5349766044453424161, stateful: True, replica/instance ID: 130,745,418,574,851,853, partition ID: 0583c745-1bed-43b2-9545-29d7e3448156.",
      "EventName": "ActorActivated",
      "Payload": {
        "actorType": "Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor",
        "actorId": "-5349766044453424161",
        "isStateful": "True",
        "replicaOrInstanceId": "130906628008120392",
        "partitionId": "b6afef61-be9a-4492-8358-8f473e5d2487",
        "serviceName": "fabric:/VoicemailBoxAdvancedApplication/VoicemailBoxActorService",
        "applicationName": "fabric:/VoicemailBoxAdvancedApplication",
      }
    }

아래 이벤트에 나온 대로 ID가 `-4952641569324299627`인 또 다른 행위자가 동일한 서비스의 다른 파티션(`5405d449-2da6-4d9a-ad75-0ec7d65d1a2a`) 내에 만들어졌습니다.

    {
      "Timestamp": "2015-04-26T15:06:56.93882-07:00",
      "ProviderName": "Microsoft-ServiceFabric-Actors",
      "Id": 5,
      "Message": "Actor activated. Actor type: Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor, actor ID: -4952641569324299627, stateful: True, replica/instance ID: 130,745,418,574,851,853, partition ID: c146fe53-16d7-4d96-bac6-ef54613808ff.",
      "EventName": "ActorActivated",
      "Payload": {
        "actorType": "Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor",
        "actorId": "-4952641569324299627",
        "isStateful": "True",
        "replicaOrInstanceId": "130745418574851853",
        "partitionId": "5405d449-2da6-4d9a-ad75-0ec7d65d1a2a",
        "serviceName": "fabric:/VoicemailBoxAdvancedApplication/VoicemailBoxActorService",
        "applicationName": "fabric:/VoicemailBoxAdvancedApplication",
      }
    }

> [AZURE.NOTE] 위 이벤트의 일부 필드는 간단한 설명을 위해 생략되었습니다.

파티션 ID는 해당 파티션에 대한 다른 정보를 얻는 데 사용할 수 있습니다. 예를 들어, [서비스 패브릭 탐색기](service-fabric-visualizing-your-cluster.md) 도구는 파티션 및 해당 파티션에 속한 서비스와 응용 프로그램에 대한 정보를 보는 데 사용할 수 있습니다. 아래 스크린샷에서는 위의 예제에서 ID가 `-4952641569324299627`인 행위자가 포함된 파티션 `5405d449-2da6-4d9a-ad75-0ec7d65d1a2a`에 대한 정보를 보여줍니다.

![서비스 패브릭 탐색기의 파티션에 대한 정보][3]

행위자는 행위자 형식이 파생된 기본 클래스의 `Host.ActivationContext` 및 `Host.StatelessServiceInitialization` 또는 `Host.StatefulServiceInitializationParameters` 멤버를 통해 프로그래밍 방식으로 파티션 ID, 서비스 이름, 응용 프로그램 이름 및 기타 서비스 패브릭 플랫폼 관련 정보를 얻을 수 있습니다. 다음 코드 조각에 예제가 나와 있습니다.

```csharp
public void ActorMessage(StatefulActorBase actor, string message, params object[] args)
{
    if (this.IsEnabled())
    {
        string finalMessage = string.Format(message, args);
        ActorMessage(
            actor.GetType().ToString(),
            actor.Id.ToString(),
            actor.ActorService.ServiceInitializationParameters.CodePackageActivationContext.ApplicationTypeName,
            actor.ActorService.ServiceInitializationParameters.CodePackageActivationContext.ApplicationName,
            actor.ActorService.ServiceInitializationParameters.ServiceTypeName,
            actor.ActorService.ServiceInitializationParameters.ServiceName.ToString(),
            actor.ActorService.ServiceInitializationParameters.PartitionId,
            actor.ActorService.ServiceInitializationParameters.ReplicaId,
            FabricRuntime.GetNodeContext().NodeName,
            finalMessage);
    }
}
```

### 상태 비저장 행위자에 대한 서비스 패브릭 파티션 개념
상태 비저장 행위자는 서비스 패브릭 상태 비저장 서비스의 파티션 내에 생성됩니다. 행위자 ID는 행위자가 생성되는 파티션을 결정합니다. 상태 비저장 행위자 서비스에 대한 [인스턴스](service-fabric-availability-services.md#availability-of-service-fabric-stateless-services) 수는 반드시 1이어야 합니다. 인스턴스 수를 그 밖의 다른 값으로 변경하는 것은 지원되지 않습니다. 따라서 행위자는 파티션에 포함된 단일 서비스 인스턴스 내에 만들어집니다.

> [AZURE.TIP] 패브릭 행위자 런타임에서는 일부 [상태 비저장 행위자 인스턴스 관련 이벤트](service-fabric-reliable-actors-diagnostics.md#events-related-to-stateless-actor-instances)를 내보냅니다. 진단 및 성능 모니터링에 유용합니다.

상태 비저장 행위자를 만들 때 행위자 런타임은 행위자를 만든 파티션 및 인스턴스를 나타내는 [EventSource 이벤트](service-fabric-reliable-actors-diagnostics.md#eventsource-events)를 작성합니다. 이 이벤트의 예제는 아래와 같습니다. 이 예제는 ID가 `abc`인 행위자가 파티션 `8c828833-ccf1-4e21-b99d-03b14d4face3`, 서비스 `fabric:/HelloWorldApplication/HelloWorldActorService`, 응용 프로그램 `fabric:/HelloWorldApplication`의 인스턴스 `130745709600495974` 내에 만들어졌음을 나타냅니다.

    {
      "Timestamp": "2015-04-26T18:17:46.1453113-07:00",
      "ProviderName": "Microsoft-ServiceFabric-Actors",
      "Id": 5,
      "Message": "Actor activated. Actor type: HelloWorld.HelloWorld, actor ID: abc, stateful: False, replica/instance ID: 130,745,709,600,495,974, partition ID: 8c828833-ccf1-4e21-b99d-03b14d4face3.",
      "EventName": "ActorActivated",
      "Payload": {
        "actorType": "HelloWorld.HelloWorld",
        "actorId": "abc",
        "isStateful": "False",
        "replicaOrInstanceId": "130745709600495974",
        "partitionId": "8c828833-ccf1-4e21-b99d-03b14d4face3",
        "serviceName": "fabric:/HelloWorldApplication/HelloWorldActorService",
        "applicationName": "fabric:/HelloWorldApplication",
      }
    }

> [AZURE.NOTE] 위 이벤트의 일부 필드는 간단한 설명을 위해 생략되었습니다.

### 상태 저장 행위자에 대한 서비스 패브릭 파티션 개념
상태 저장 행위자는 서비스 패브릭 상태 저장 서비스의 파티션 내에 생성됩니다. 행위자 ID는 행위자가 생성되는 파티션을 결정합니다. 서비스의 각 파티션은 클러스터의 여러 노드에 배치된 하나 이상의 [복제본](service-fabric-availability-services.md#availability-of-service-fabric-stateful-services)을 가질 수 있습니다. 복제본이 여러 개 있으면 행위자 상태에 대한 안정성이 제공됩니다. Azure 리소스 관리자는 클러스터에서 사용 가능한 오류 및 업그레이드 도메인을 기준으로 배치를 최적화합니다. 동일한 파티션의 두 복제본은 동일한 노드에 배치할 수 없습니다. 행위자는 항상 해당 행위자 ID가 매핑되는 파티션의 주 복제본에 생성됩니다.

> [AZURE.TIP] 패브릭 행위자 런타임에서는 일부 [상태 저장 행위자 복제본 관련 이벤트](service-fabric-reliable-actors-diagnostics.md#events-related-to-stateful-actor-replicas)를 내보냅니다. 진단 및 성능 모니터링에 유용합니다.

[이전에 나온 VoiceMailBoxActor 예](#service-fabric-partition-concepts-for-actors)에서 ID가 `-4952641569324299627`인 행위자를 파티션 `5405d449-2da6-4d9a-ad75-0ec7d65d1a2a` 내에서 만들었습니다. 해당 예제에서 EventSource 이벤트는 행위자가 해당 파티션의 복제본 `130745418574851853`에 만들어졌음을 나타냅니다. 이 복제본은 행위자를 만들었을 당시 해당 파티션의 주 복제본이었습니다. 아래의 서비스 패브릭 탐색기 스크린샷에서 이를 확인할 수 있습니다.

![서비스 패브릭 탐색기의 기본 복제본][4]

## 행위자 상태 제공자 선택
일부 기본 행위자 상태 제공자가 행위자 런타임에 포함되어 있습니다. 행위자 서비스에 적절한 상태 제공자를 선택하기 위해서는 상태 제공자가 기본 서비스 패브릭 플랫폼 기능을 사용하여 행위자 상태를 항상 사용할 수 있도록 만드는 방법을 알아야 합니다.

기본적으로 상태 저장 행위자는 키-값 저장소의 행위자 상태 제공자를 사용합니다. 이 상태 제공자는 서비스 패브릭 플랫폼에서 제공하는 분산된 키-값 저장소에 빌드되어 있습니다. 상태는 기본 [복제본](service-fabric-availability-services.md#availability-of-service-fabric-stateful-services)을 호스트하는 노드의 논리 디스크에 영구적으로 저장됩니다. 또한 보조 복제본을 호스트하는 노드의 로컬 디스크에 복제되고 영구적으로 저장됩니다. 복제본의 쿼럼이 상태를 로컬 디스크에 커밋한 경우에만 상태 저장이 완료됩니다. 키-값 저장소에는 거짓 진행률 등의 불일치를 감지하여 자동으로 수정하는 첨단 기능이 있습니다.

또한 행위자 런타임에는 `VolatileActorStateProvider`도 포함됩니다. 이 상태 제공자는 상태를 복제본(상태 복사본)으로 복제하지만 해당 상태는 각 복제본의 메모리에 남아 있습니다. 하나의 복제본이 중단되었다가 다시 가동되면 다른 복제본으로부터 상태가 다시 작성됩니다. 그러나 모든 복제본이 동시에 중단되면 상태 데이터는 손실됩니다. 따라서 이 상태 제공자는 일부 복제본에서 오류가 발생해도 데이터가 살아남을 수 있고 업그레이드와 같이 계획된 장애 조치에서도 살아남을 수 있는 응용 프로그램에 적합합니다. 모든 복제본을 잃게 되면 서비스 패브릭 외부 메커니즘을 통해 데이터를 다시 만들어야 합니다. `VolatileActorStateProvider` 특성을 행위자 클래스 또는 어셈블리 수준 특성에 추가하여 상태 저장 행위자가 휘발성 행위자 상태 제공자를 사용하도록 구성할 수 있습니다.

다음 코드 조각은 `VolatileActorStateProvider`를 사용하는 명시적 상태 제공자 특성이 없는 어셈블리에서 모든 행위자를 변경하는 방법을 보여줍니다.

```csharp
[assembly:Microsoft.ServiceFabric.Actors.VolatileActorStateProvider]
```

다음 코드 조각에서는 특정 행위자 형식(이 경우 `VoicemailBox`)에 대한 상태 제공자를 `VolatileActorStateProvider`가 되도록 변경하는 방법을 보여줍니다.

```csharp
[VolatileActorStateProvider]
public class VoicemailBoxActor : StatefulActor<VoicemailBox>, IVoicemailBoxActor
{
    public Task<List<Voicemail>> GetMessagesAsync()
    {
        return Task.FromResult(State.MessageList);
    }
    ...
}
```

상태 제공자를 변경하면 행위자 서비스를 다시 만들어야 합니다. 상태 제공자는 응용 프로그램 업그레이드의 일부로 변경할 수 없습니다.

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/manifests-in-vs-solution.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png

<!---HONumber=AcomDC_0316_2016-->