<properties
   pageTitle="신뢰할 수 있는 서비스 시작하기 |Microsoft Azure "
   description="상태 비저장 및 상태 저장 서비스를 사용하여 Microsoft Azure 서비스 패브릭 응용 프로그램 만들기 소개"
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="jessebenson"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/15/2015"
   ms.author="vturecek"/>

# Microsoft Azure 서비스 패브릭 신뢰할 수 있는 서비스 시작하기

서비스 패브릭 응용 프로그램에는 코드를 실행하는 하나 이상의 서비스가 포함되어 있습니다. 이 자습서는 [*신뢰할 수 있는 서비스* 프로그래밍 모델](service-fabric-reliable-services-introduction.md)을 사용하여 상태 비저장 및 상태 저장 "Hello World" 서비스 패브릭 응용 프로그램을 만드는 단계를 안내합니다.

상태 비저장 서비스는 현재 주로 클라우드 응용 프로그램에 존재하는 서비스 유형입니다. 서비스는 상태 비저장으로 간주됩니다. 서비스 자체에 안정적으로 저장하거나 항상 사용 가능하게 만들어야 하는 데이터가 포함되어 있지 않기 때문입니다. 즉, 상태 비저장 서비스의 인스턴스가 종료되면 모든 내부 상태가 손실됩니다. 이러한 서비스 유형에서는 Azure 테이블 또는 SQL 데이터베이스와 같은 외부 저장소에 상태를 항상 유지하고, 높은 가용성과 안정성을 유지해야 합니다.

서비스 패브릭은 서비스 자체 내에서 안정적으로 상태를 유지할 수 있고 사용 중인 코드와 같은 위치에 배치되는 새로운 종류의 상태 저장 서비스를 도입했습니다. 외부 저장소에 상태를 유지하지 않고도 서비스 패브릭에서 사용자 상태의 높은 가용성을 유지합니다.

이 자습서에서는 상태 비저장 서비스와 내부 카운터를 유지하는 상태 저장 서비스를 구현하겠습니다. 상태 비저장 서비스에서는 서비스가 다시 시작되거나 이동할 때 카운터의 값이 손실됩니다. 그러나 상태 저장 서비스에서는 계산 중에 어떤 이유로 서비스 실행이 중단되는 경우 종료된 위치에서 바로 시작할 수 있도록 서비스 패브릭에서 카운터 상태를 안정적으로 만듭니다.

## 상태 비저장 서비스 만들기

상태 비저장 서비스부터 살펴보겠습니다.

**관리자** 권한으로 Visual Studio 2015 RC를 시작하고 *HelloWorld*라는 새로운 **서비스 패브릭 응용 프로그램** 프로젝트를 만듭니다.

![새 프로젝트 대화 상자를 사용하여 새 서비스 패브릭 응용 프로그램 만들기](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

그런 다음 *HelloWorldStateless*라는 **상태 비저장 서비스** 프로젝트를 만듭니다.

![두 번째 대화 상자에서 상태 비저장 서비스 만들기](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

이제 솔루션에는 2개의 프로젝트가 있습니다.

 + **HelloWorld**: *서비스*가 포함된 *응용 프로그램* 프로젝트입니다. 또한 응용 프로그램과 응용 프로그램을 배포하는 데 도움이 되는 다양한 PowerShell 스크립트를 설명하는 응용 프로그램 매니페스트가 포함되어 있습니다.
 + **HelloWorldStateless**: 상태 비저장 서비스 구현을 포함하는 서비스 프로젝트입니다.


## 서비스 구현

서비스 프로젝트에서 **HelloWorld.cs** 파일을 엽니다. 서비스 패브릭에서 서비스는 모든 비즈니스 논리를 실행할 수 있습니다. 서비스 API는 코드에 대한 두 진입점을 제공합니다.

 - 장기 실행 계산 워크로드와 같이 원하는 모든 워크로드의 실행을 시작할 수 있는 *RunAsync*라는 개방형 진입점 메서드.

```C#
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

 - Web API와 같은 원하는 통신 스택을 연결할 수 있고, 사용자 또는 다른 서비스에서 요청 수신을 시작할 수 있는 통신 진입점.

```C#
protected override ICommunicationListener CreateCommunicationListener()
{
    ...
}
```

이 자습서에서는 코드 실행을 바로 시작할 수 있는 `RunAsync()` 진입점 메서드를 중점적으로 다룹니다. 프로젝트 템플릿에는 롤링 횟수를 증분하는 `RunAsync()`의 예제 구현이 포함됩니다.

> [AZURE.NOTE]통신 스택 사용에 대한 자세한 내용은 [OWIN 자체 호스트를 지원하는 Microsoft Azure 서비스 패브릭 Web API 서비스 시작하기](service-fabric-reliable-services-communication-webapi.md)를 확인하세요.


### RunAsync

```C#
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following with your own logic.

    int iterations = 0;
    while (!cancellationToken.IsCancellationRequested)
    {
        ServiceEventSource.Current.ServiceMessage(this, "Working-{0}", iterations++);
        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
}
```

서비스의 인스턴스가 배치되어 실행할 준비가 되면 플랫폼이 이 메서드를 호출합니다. 상태 비저장 서비스의 경우 이는 간단히 말해 서비스 인스턴스가 열릴 때를 의미합니다. 서비스 인스턴스를 종료해야 하는 경우 취소 토큰이 제공됩니다. 서비스 패브릭에서 이러한 서비스 인스턴스의 열림-닫힘 주기는 서비스의 수명 동안 다음과 같이 다양한 이유로 인해 전체적으로 여러 번 발생할 수 있습니다.

- 시스템은 리소스 분산을 위해 서비스 인스턴스를 이동할 수 있습니다.
- 코드 내에 오류가 발생합니다.
- 응용 프로그램 또는 시스템 업그레이드 하는 동안 발생합니다.
- 기본 하드웨어가 중단되는 경우에 발생합니다.

이러한 오케스트레이션은 서비스의 가용성을 높게 유지하고 제대로 균형을 유지하기 위해 시스템에 의해 관리됩니다.

`RunAsync()`는 자체 **태스크**에서 실행됩니다. 위의 코드 조각에서는 **while** 루프로 바로 이동합니다. 워크로드에 대한 별도의 태스크를 예약할 필요가 없습니다. 워크로드 취소는 제공된 취소 토큰에 의해 조정된 공동의 노력입니다. 시스템은 계속하기 전에 태스크가 종료(성공적으로 완료, 취소 또는 오류 발생)될 때까지 기다립니다. 시스템에서 취소를 요청할 때는 취소 토큰을 받아들이고, 모든 작업을 마치고, `RunAsync()`를 가능한 한 빨리 종료하는 것이 **중요**합니다.

이 상태 비저장 서비스 예에서는 개수가 로컬 변수에 저장됩니다. 하지만 이는 상태 비저장 서비스이므로 저장되는 값이 해당 서비스 인스턴스의 현재 주기에만 존재합니다. 서비스가 이동하거나 다시 시작되면 값이 손실됩니다.

## 상태 저장 서비스 만들기

서비스가 이동하거나 다시 시작하는 경우에도 카운터 값을 상태 비저장에서 항상 사용 가능하고 지속되게 만들려면 상태 저장 서비스가 필요합니다.

동일한 **HelloWorld** 응용 프로그램에서 응용 프로그램 프로젝트를 마우스 오른쪽 단추로 클릭하고 **새 패브릭 서비스**를 선택하여 새 서비스를 추가합니다.

![서비스 패브릭 응용 프로그램에 서비스 추가](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

**서비스 패브릭 상태 저장 서비스**를 선택하고 "HelloWorldStateful"이라는 이름을 지정합니다. **추가**를 클릭합니다.

![새 프로젝트 대화 상자를 사용하여 새 서비스 패브릭 상태 저장 서비스 만들기](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

응용 프로그램에 이제 상태 비저장 서비스 *HelloWorld* 및 상태 저장 서비스 *HelloWorldStateful*의 두 서비스가 있어야 합니다.

다음과 같은 `RunAsync` 메서드를 포함하는 *HelloWorldStateful*에서 **HelloWorldStateful.cs**를 엽니다.

```C#
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following with your own logic.
    var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

    while (!cancellationToken.IsCancellationRequested)
    {
        using (var tx = this.StateManager.CreateTransaction())
        {
            var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");
            ServiceEventSource.Current.ServiceMessage(
                this,
                "Current Counter Value: {0}",
                result.HasValue ? result.Value.ToString() : "Value does not exist.");

            await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

            await tx.CommitAsync();
        }

        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
}
```

### RunAsync

상태 저장 서비스에는 상태 비저장 서비스와 동일한 진입점이 있습니다. 주요 차이점은 *신뢰할 수 있는 컬렉션* 및 *상태 관리자*의 가용성입니다. 상태 저장 서비스의 `RunAsync()`는 상태 비저장 서비스와 유사하게 작동합니다. 단, 상태 저장 서비스에서는 `RunAsync()`를 실행하기 전에 *상태 관리자* 및 *신뢰할 수 있는 컬렉션*이 사용할 준비가 되었는지 확인하는 등의 추가 작업을 사용자 대신 수행합니다.

### 신뢰할 수 있는 컬렉션 및 상태 관리자

```C#
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

**IReliableDictionary**는 서비스에서 상태를 안정적으로 저장할 수 있도록 하는 사전 구현입니다. 이것은 서비스 패브릭에 기본 제공되는 [신뢰할 수 있는 컬렉션](service-fabric-reliable-services-reliable-collections.md)의 일부입니다. 이제 서비스 패브릭 및 신뢰할 수 있는 컬렉션을 사용하면 외부 영구 저장소 없이도 서비스에 데이터를 직접 저장할 수 있고 데이터를 항상 사용할 수 있게 됩니다. 서비스 패브릭은 서비스의 여러 *복제본*을 자동으로 만들고 관리함과 동시에 해당 복제본 및 해당 상태 전환 관리의 복잡성을 추상화하는 API를 제공함으로써 이를 달성합니다.

신뢰할 수 있는 컬렉션은 사용자 지정 형식을 포함하여 모든 .NET 유형을 저장할 수 있습니다. 단, 몇 가지 주의 사항이 있습니다.

 1. 서비스 패브릭은 노드의 상태를 *복제*하고 로컬 디스크에 저장하여 상태를 항상 사용할 수 있게 만듭니다. 즉, 신뢰할 수 있는 컬렉션에 저장된 모든 항목이 *직렬화 가능* 상태가 됩니다. 신뢰할 수 있는 컬렉션은 기본적으로 [DataContract](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractattribute%28v=vs.110%29.aspx)를 사용하여 직렬화를 수행하므로 기본 직렬 변환기를 사용할 때 해당 유형이 [데이터 계약 직렬 변환기에서 지원되는지](https://msdn.microsoft.com/library/ms731923%28v=vs.110%29.aspx) 확인하는 것이 중요합니다.

 2. 신뢰할 수 있는 컬렉션의 트랜잭션을 커밋하면 고가용성을 위해 개체가 복제됩니다. 신뢰할 수 있는 컬렉션에 저장된 개체는 서비스의 로컬 메모리에 보관됩니다. 즉, 개체에 대한 로컬 참조입니다.

    트랜잭션의 신뢰할 수 있는 컬렉션에서 업데이트 작업을 수행하지 않고 해당 개체의 로컬 인스턴스를 변환하지 않는 것이 중요합니다. 이러한 변경 내용은 자동으로 복제되지 않기 때문입니다.

*StateManager*는 신뢰할 수 있는 컬렉션을 자동으로 관리합니다. 언제든지 서비스의 어느 위치에서든 StateManager에 신뢰할 수 있는 컬렉션을 이름으로 요청하면 참조를 다시 확인할 수 있습니다. 신뢰할 수 있는 컬렉션 인스턴스에 대한 참조를 클래스 멤버 변수 또는 속성에 저장하는 것은 권장하지 않습니다. 서비스 수명 주기에서는 항상 참조가 인스턴스로 설정되도록 특별한 주의를 기울여야 합니다. StateManager는 반복 방문에 최적화되도록 사용자를 대신해 이 작업을 처리합니다.

### 트랜잭션 및 비동기

```C#
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

신뢰할 수 있는 컬렉션에는 LINQ를 비롯하여 `System.Collections.Generic` 및 `System.Collections.Concurrent` 해당 항목과 동일한 작업이 많이 있습니다. 하지만 신뢰할 수 있는 컬렉션의 작업은 비동기적입니다. 이것은 신뢰할 수 있는 컬렉션의 쓰기 작업이 *복제*되기 때문에 즉, 고가용성을 위해 작업이 여러 노드의 다른 서비스 복제본에 전송되기 때문입니다.

또한 *트랜잭션* 작업을 지원하므로 여러 신뢰할 수 있는 컬렉션 간에 상태를 일관되게 유지할 수 있습니다. 예를 들어 신뢰할 수 있는 큐에서 작업 항목을 제거하고, 작업을 수행하고, 신뢰할 수 있는 사전의 결과를 모두 단일 트랜잭션 내에 저장할 수 있습니다. 이는 원자성 작업으로 처리되므로 전체 작업이 성공하거나 실패하게 됩니다. 따라서 항목을 큐에서 제거한 후 결과를 저장하기 전에 오류가 발생할 경우 전체 트랜잭션이 롤백되고 항목이 처리를 위해 큐에 남아 있습니다.

## 응용 프로그램 실행

*HelloWorld* 응용 프로그램으로 돌아갑니다. 이제 서비스를 빌드하고 배포할 수 있습니다. **F5** 키를 누르면 응용 프로그램이 빌드되고 로컬 클러스터에 배포됩니다.

서비스가 실행되고 나면 **진단 이벤트** 창에서 생성된 ETW 이벤트를 볼 수 있습니다. 응용 프로그램의 상태 비저장 서비스 및 상태 저장 서비스 모두의 이벤트가 표시됩니다. *일시 중지* 단추를 클릭하여 스트림을 일시 중지한 후 메시지를 확장하여 메시지 정보를 검토할 수 있습니다.

>[AZURE.NOTE]응용 프로그램을 실행하기 전에 로컬 개발 클러스터가 실행 중인지 확인합니다. [시작 가이드](service-fabric-get-started.md)를 확인하여 로컬 환경 설정을 가져옵니다.

![Visual Studio에서 진단 이벤트 보기](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)


## 다음 단계

[Visual Studio에서 서비스 패브릭 응용 프로그램 디버깅](service-fabric-debugging-your-application.md)

[OWIN 자체 호스팅으로 Microsoft Azure 서비스 패브릭 웹 API 서비스 시작](service-fabric-reliable-services-communication-webapi.md)

[신뢰할 수 있는 컬렉션에 대한 자세한 정보](service-fabric-reliable-services-reliable-collections.md)

[서비스 패브릭 서비스 관리](service-fabric-manage-your-service-index.md)

[신뢰할 수 있는 서비스에 대한 개발자 참조](https://msdn.microsoft.com/library/azure/dn706529.aspx)

<!---HONumber=Nov15_HO2-->