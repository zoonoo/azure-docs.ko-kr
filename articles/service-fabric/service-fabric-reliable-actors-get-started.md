---
title: "Service Fabric Reliable Actors 시작 | Microsoft Docs"
description: "이 자습서에서는 서비스 패브릭 Reliable Actors를 사용하여 간단한 행위자 기반 서비스를 생성, 디버깅 및 배포하는 과정을 단계별로 안내합니다."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: d4aebe72-1551-4062-b1eb-54d83297f139
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/25/2016
ms.author: vturecek
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4d3d7c05e6def1ad8ec6f30fa96655eb8f6b2fda


---
# <a name="getting-started-with-reliable-actors"></a>Reliable Actors 시작
> [!div class="op_single_selector"]
> * [Windows에서 C#](service-fabric-reliable-actors-get-started.md)
> * [Linux에서 Java](service-fabric-reliable-actors-get-started-java.md)
> 
> 

이 문서는 Azure 서비스 패브릭 Reliable Actors의 기본 개념을 설명하고 Visual Studio에서 간단한 Reliable Actor 응용 프로그램을 생성, 디버깅 및 배포하는 과정을 안내합니다.

## <a name="installation-and-setup"></a>설치 및 설정
시작하기 전에 컴퓨터에 서비스 패브릭 개발 환경이 설정되어 있는지 확인합니다.
이를 설정하려면 [개발 환경을 설정하는 방법](service-fabric-get-started.md)에 대한 자세한 지침을 참조하세요.

## <a name="basic-concepts"></a>기본 개념
Reliable Actors를 시작하려면 몇 가지 기본 개념만 이해하면 됩니다.

* **행위자 서비스**. Reliable Actors는 서비스 패브릭 인프라에 배포될 수 있는 Reliable Services에 패키징됩니다. 행위자 인스턴스는 명명된 서비스 인스턴스에서 활성화됩니다.
* **행위자 등록**. Reliable Services와 마찬가지로 Reliable Actor 서비스를 Service Fabric 런타임에 등록해야 합니다. 또한 행위자 형식을 행위자 런타임에 등록해야 합니다.
* **행위자 인터페이스**. 행위자 인터페이스는 행위자에 대한 강력한 형식의 공용 인터페이스를 정의하는 데 사용됩니다. Reliable Actor 모델 용어에서 행위자 인터페이스는 행위자가 이해하고 처리할 수 있는 메시지의 유형을 정의합니다. 행위자 인터페이스는 다른 행위자 또는 클라이언트 응용 프로그램에서 메시지를 행위자에게 "보내는"(비동기) 데 사용됩니다. Reliable Actors는 여러 인터페이스를 구현할 수 있습니다.
* **ActorProxy 클래스**. ActorProxy 클래스는 클라이언트 응용 프로그램에서 행위자 인터페이스를 통해 노출되는 메서드를 호출하는 데 사용됩니다. ActorProxy 클래스는 다음 두 가지 중요한 기능을 제공합니다.
  
  * 이름 확인: 클러스터에서 행위자를 찾을 수 있습니다(호스트되는 클러스터의 노드 찾기).
  * 오류 처리: 메서드 호출을 다시 시도하고 행위자를 클러스터의 다른 노드로 재배치해야 하는 경우 등의 오류가 발생한 후 행위자의 위치를 다시 파악할 수 있습니다.

행위자 인터페이스와 관련된 다음 규칙을 확인하면 도움이 됩니다.

* 행위자 인터페이스 메서드는 오버로드할 수 없습니다.
* 행위자 인터페이스 메서드에는 out, ref 또는 선택적 매개 변수가 없어야 합니다.
* 제네릭 인터페이스는 지원되지 않습니다.

## <a name="create-a-new-project-in-visual-studio"></a>Visual Studio에서 새 프로젝트 만들기
Visual Studio용 서비스 패브릭 도구를 설치한 후에 새 프로젝트 형식을 만들 수 있습니다. 새 프로젝트 형식은 **새 프로젝트** 대화 상자의 **클라우드** 범주에 표시됩니다.

![Visual Studio용 서비스 패브릭 도구 - 새 프로젝트][1]

다음 대화 상자에서 만들려는 프로젝트의 형식을 선택할 수 있습니다.

![서비스 패브릭 프로젝트 템플릿][5]

HelloWorld 프로젝트의 경우 서비스 패브릭 Reliable Actors 서비스를 사용해 보겠습니다.

솔루션을 만들고 나면 다음과 같은 구조로 표시됩니다.

![서비스 패브릭 프로젝트 구조][2]

## <a name="reliable-actors-basic-building-blocks"></a>신뢰할 수 있는 행위자 기본 구성 요소
일반적으로 Reliable Actors 솔루션은 다음 3개 프로젝트로 구성됩니다.

* **응용 프로그램 프로젝트(MyActorApplication)**. 배포를 위해 모든 서비스를 함께 패키지하는 프로젝트입니다. 응용 프로그램을 관리하기 위한 *ApplicationManifest.xml* 및 PowerShell 스크립트가 포함되어 있습니다.
* **인터페이스 프로젝트(MyActor.Interfaces)**. 행위자에 대한 인터페이스 정의가 포함된 프로젝트입니다. MyActor.Interfaces 프로젝트에서 솔루션의 행위자에 의해 사용될 인터페이스를 정의할 수 있습니다. 행위자 인터페이스는 모든 프로젝트에서 어떤 이름으로든 정의될 수 있지만 인터페이스는 행위자 구현과 행위자를 호출하는 클라이언트에서 공유되는 행위자 계약을 정의하므로 일반적으로 행위자 구현과 별개이고 다른 여러 프로젝트에서 공유될 수 있는 어셈블리에서 정의하는 것이 좋습니다.

```csharp
public interface IMyActor : IActor
{
    Task<string> HelloWorld();
}
```

* **행위자 서비스 프로젝트(MyActor)**. 행위자를 호스트할 서비스 패브릭 서비스를 정의하는 데 사용되는 프로젝트입니다. 행위자의 구현을 포함합니다. 행위자 구현은 기본 형식( `Actor` )에서 파생되는 클래스로서, MyActor.Interfaces 프로젝트에 정의된 인터페이스를 구현합니다. 또한 행위자 클래스는 `ActorService` 인스턴스 및 `ActorId`를 허용하고 이를 기본 `Actor` 클래스에 전달하는 생성자를 구현해야 합니다. 그러면 플랫폼 종속성의 생성자 종속성 주입이 허용됩니다.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<string> HelloWorld()
    {
        return Task.FromResult("Hello world!");
    }
}
```

행위자 서비스는 서비스 패브릭 런타임에서 서비스 유형에 등록되어야 합니다. 행위자 서비스에서 행위자 인스턴스를 실행하려면 행위자 서비스에 행위자 유형이 등록되어야 합니다. `ActorRuntime` 등록 메서드가 행위자에 대한 이 작업을 수행합니다.

```csharp
internal static class Program
{
    private static void Main()
    {
        try
        {
            ActorRuntime.RegisterActorAsync<MyActor>(
                (context, actorType) => new ActorService(context, actorType, () => new MyActor())).GetAwaiter().GetResult();

            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
            throw;
        }
    }
}

```

Visual Studio에서 새 프로젝트를 시작하고 하나의 행위자 정의만 있는 경우, 등록은 기본적으로 Visual Studio에서 생성하는 코드에 포함됩니다. 서비스에서 다른 행위자를 정의하는 경우 다음을 사용하여 행위자 등록을 추가해야 합니다.

```csharp
 ActorRuntime.RegisterActorAsync<MyOtherActor>();

```

> [!TIP]
> 서비스 패브릭 행위자 런타임에서는 일부 [행위자 메서드와 관련된 이벤트 및 성능 카운터](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters)를 내보냅니다. 진단 및 성능 모니터링에 유용합니다.
> 
> 

## <a name="debugging"></a>디버그
Visual Studio용 서비스 패브릭 도구는 로컬 컴퓨터에서 디버깅을 지원합니다. F5 키를 눌러서 디버깅 세션을 시작할 수 있습니다. Visual Studio는 (필요한 경우) 패키지를 작성합니다. 또한 로컬 서비스 패브릭 클러스터에 응용 프로그램을 배포하고 디버거를 연결합니다.

배포 프로세스가 진행되는 동안 **출력** 창에서 진행률을 볼 수 있습니다.

![서비스 패브릭 디버깅 출력 창][3]

## <a name="next-steps"></a>다음 단계
* [신뢰할 수 있는 행위자가 서비스 패브릭 플랫폼을 사용하는 방법](service-fabric-reliable-actors-platform.md)
* [행위자 상태 관리](service-fabric-reliable-actors-state-management.md)
* [행위자 수명 주기 및 가비지 수집](service-fabric-reliable-actors-lifecycle.md)
* [행위자 API 참조 설명서](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [샘플 코드](https://github.com/Azure/servicefabric-samples)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG



<!--HONumber=Nov16_HO3-->


