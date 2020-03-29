---
title: 고급 응용 프로그램 업그레이드 항목
description: 이 문서에서는 서비스 패브릭 애플리케이션 업그레이드와 관련된 고급 항목을 다룹니다.
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 09f3fdf1f26a13c6722eb039e132256f33be38ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845439"
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>서비스 패브릭 응용 프로그램 업그레이드: 고급 항목

## <a name="add-or-remove-service-types-during-an-application-upgrade"></a>응용 프로그램 업그레이드 중 서비스 유형 추가 또는 제거

게시된 애플리케이션에 업그레이드의 일부로 새 서비스 유형이 추가되는 경우 배포된 애플리케이션에도 새 서비스 유형이 추가됩니다. 이러한 업그레이드는 이미 애플리케이션에 포함된 서비스 인스턴스에 영향을 주지 않지만, 추가된 서비스 유형의 인스턴스를 만들어야만 새 서비스 유형이 활성화됩니다([New-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps) 참조).

마찬가지로, 업그레이드의 일부로 애플리케이션에서 서비스 유형을 제거할 수도 있습니다. 그러나 제거하려는 서비스 유형의 모든 서비스 인스턴스를 제거한 후 업그레이드를 진행해야 합니다([Remove-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice?view=azureservicefabricps) 참조).

## <a name="avoid-connection-drops-during-stateless-service-planned-downtime-preview"></a>상태 비수기 서비스 계획된 가동 중지 시간 동안 연결 중단 방지(미리 보기)

응용 프로그램/클러스터 업그레이드 또는 노드 비활성화와 같은 계획된 상태 비누비 인스턴스 가동 중단 의 경우 종료 후 노출된 끝점이 제거되어 연결이 끊어질 수 있습니다.

이를 방지하려면 서비스 구성에서 복제본 *인스턴스 닫기 지연 기간을* 추가하여 *RequestDrain(미리* 보기) 기능을 구성합니다. 이렇게 하면 지연 타이머가 인스턴스를 닫기 *시작하기 전에* 상태 비해제 인스턴스에서 보급하는 끝점이 제거됩니다. 이 지연을 통해 인스턴스가 실제로 중단되기 전에 기존 요청을 정상적으로 드레인할 수 있습니다. 클라이언트는 콜백 함수를 통해 끝점 변경에 대한 알림을 받게 되므로 끝점을 다시 해결하고 인스턴스에 새 요청을 보내지 않도록 할 수 있습니다.

### <a name="service-configuration"></a>서비스 구성

서비스 측에서 지연을 구성하는 방법에는 여러 가지가 있습니다.

 * **새 서비스를 만들 때 다음을**지정합니다. `-InstanceCloseDelayDuration`

    ```powershell
    New-ServiceFabricService -Stateless [-ServiceName] <Uri> -InstanceCloseDelayDuration <TimeSpan>`
    ```

 * **응용 프로그램 매니페스트의 기본값 섹션에서 서비스를 정의하는** `InstanceCloseDelayDurationSeconds` 동안 속성을 할당합니다.

    ```xml
          <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]" InstanceCloseDelayDurationSeconds="15">
              <SingletonPartition />
          </StatelessService>
    ```

 * **기존 서비스를 업데이트할 때 다음을** `-InstanceCloseDelayDuration`지정합니다.

    ```powershell
    Update-ServiceFabricService [-Stateless] [-ServiceName] <Uri> [-InstanceCloseDelayDuration <TimeSpan>]`
    ```

### <a name="client-configuration"></a>클라이언트 구성

끝점이 변경되면 알림을 받으려면 클라이언트는 다음과 같이`ServiceManager_ServiceNotificationFilterMatched`콜백()을 등록할 수 있습니다. 

```csharp
    var filterDescription = new ServiceNotificationFilterDescription
    {
        Name = new Uri(serviceName),
        MatchNamePrefix = true
    };
    fbClient.ServiceManager.ServiceNotificationFilterMatched += ServiceManager_ServiceNotificationFilterMatched;
    await fbClient.ServiceManager.RegisterServiceNotificationFilterAsync(filterDescription);

private static void ServiceManager_ServiceNotificationFilterMatched(object sender, EventArgs e)
{
      // Resolve service to get a new endpoint list
}
```

변경 알림은 끝점이 변경되고 클라이언트가 끝점을 다시 해결해야 하며 곧 중단될 때 더 이상 보급되지 않는 끝점을 사용하지 않음을 나타냅니다.

### <a name="optional-upgrade-overrides"></a>선택적 업그레이드 재정의

서비스당 기본 지연 기간을 설정하는 것 외에도 동일한 ()`InstanceCloseDelayDurationSec`옵션을 사용하여 응용 프로그램/클러스터 업그레이드 중 지연을 재정의할 수도 있습니다.

```powershell
Start-ServiceFabricApplicationUpgrade [-ApplicationName] <Uri> [-ApplicationTypeVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]

Start-ServiceFabricClusterUpgrade [-CodePackageVersion] <String> [-ClusterManifestVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]
```

지연 기간은 호출된 업그레이드 인스턴스에만 적용되며 개별 서비스 지연 구성은 변경되지 않습니다. 예를 들어 미리 구성된 업그레이드 지연을 `0` 건너뛰기 위해 지연을 지정하는 데 사용할 수 있습니다.

## <a name="manual-upgrade-mode"></a>수동 업그레이드 모드

> [!NOTE]
> *Monitored* 업그레이드 모드는 모든 Service Fabric 업그레이드에 권장하는 모드입니다.
> *UnmonitoredManual* 모드는 업그레이드가 실패 또는 일시 중단된 경우에만 고려해야 합니다. 
>
>

*Monitored* 모드에서는 업그레이드가 진행되는 동안 애플리케이션이 정상 상태를 유지하도록 Service Fabric에서 상태 정책을 적용합니다. 상태 정책을 위반하는 경우 지정된 *FailureAction*에 따라 업그레이드가 일시 중지되거나 자동으로 롤백됩니다.

*UnmonitoredManual* 모드에서는 애플리케이션 관리자가 업그레이드의 진행 상황을 총체적으로 제어합니다. 이 모드는 상태 모니터링을 우회하기 위해 사용자 지정 상태 평가 정책을 적용하거나 일반적이지 않은 업그레이드를 수행하는 경우에 유용합니다(예: 애플리케이션 데이터가 이미 손실). 이 모드에서 실행되는 업그레이드는 각 UD가 완료된 후 스스로 일시 중단되며 [Resume-servicefabricapplicationupgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps)를 사용하여 명시적으로 재개해야 합니다. 업그레이드가 일시 중단되고 사용자가 업그레이드를 다시 시작할 준비가 완료되면 업그레이드 상태가 *RollforwardPending*으로 표시됩니다([UpgradeState](https://docs.microsoft.com/dotnet/api/system.fabric.applicationupgradestate?view=azure-dotnet) 참조).

마지막으로, *UnmonitoredAuto* 모드는 사용자 입력이 필요 없고 애플리케이션 상태 정책을 평가하지 않으므로 서비스를 개발하거나 테스트하는 동안 빠른 업그레이드 반복을 수행하는 데 유용합니다.

## <a name="upgrade-with-a-diff-package"></a>diff 패키지로 업그레이드

전체 애플리케이션 패키지를 프로비전하는 대신, 전체 애플리케이션 매니페스트 및 전체 서비스 매니페스트와 함께 업데이트된 코드/config/데이터 패키지만 포함하는 diff 패키지를 프로비전하여 업그레이드를 수행할 수도 있습니다. 전체 애플리케이션 패키지는 클러스터에 애플리케이션을 처음으로 설치하는 경우에만 필요합니다. 후속 업그레이드는 전체 애플리케이션 패키지 또는 diff 패키지에서 수행할 수 있습니다.  

애플리케이션 패키지에서 찾을 수 없는 diff 패키지의 애플리케이션 매니페스트 또는 서비스 매니페스트는 자동으로 현재 프로비전된 버전으로 대체됩니다.

diff 패키지를 사용하는 시나리오는 다음과 같습니다.

* 여러 서비스 매니페스트 파일 및/또는 여러 코드 패키지, config 패키지 또는 데이터 패키지를 참조하는 대형 애플리케이션 패키지가 있는 경우.
* 애플리케이션 빌드 프로세스에서 직접 빌드 레이아웃을 생성하는 배포 시스템을 사용하는 경우. 이 경우 코드가 변경되지 않았더라도 새로 빌드된 어셈블리는 다른 체크섬을 갖습니다. 전체 애플리케이션 패키지를 사용하려면 모든 코드 패키지의 버전을 업데이트해야 합니다. diff 패키지를 사용하면 변경된 파일과 버전이 변경된 매니페스트 파일만 제공하면 됩니다.

Visual Studio를 사용하여 애플리케이션이 업그레이드되는 경우 diff 패키지가 자동으로 게시됩니다. diff 패키지를 수동으로 만들려면 애플리케이션 매니페스트 및 서비스 매니페스트를 업데이트해야 하지만 변경된 패키지만 최종 애플리케이션 패키지에 포함되어야 합니다.

예를 들어 다음 애플리케이션을 시작하겠습니다(이해하기 쉽도록 버전 번호 제공).

```text
app1           1.0.0
  service1     1.0.0
    code       1.0.0
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

diff 패키지를 사용하여 service1의 코드 패키지만 업데이트하려 한다고 가정해 봅시다. 업데이트된 애플리케이션은 다음과 같은 버전 변화를 거쳤습니다.

```text
app1           2.0.0      <-- new version
  service1     2.0.0      <-- new version
    code       2.0.0      <-- new version
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

이 경우 애플리케이션 매니페스트를 2.0.0으로 업데이트하고 service1에 대한 서비스 매니페스트가 코드 패키지 업데이트를 반영하도록 업데이트합니다. 애플리케이션 패키지에 대한 폴더 구조는 다음과 같습니다.

```text
app1/
  service1/
    code/
```

즉, 일반적인 방법으로 전체 애플리케이션 패키지를 만든 다음, 버전이 변경되지 않은 코드/config/데이터 패키지 폴더를 제거합니다.

## <a name="upgrade-application-parameters-independently-of-version"></a>버전과 독립적으로 애플리케이션 매개 변수 업그레이드

경우에 따라 매니페스트 버전을 변경하지 않고 서비스 패브릭 응용 프로그램의 매개 변수를 변경하는 것이 바람직합니다. 이 작업은 **-Application매개** 플래그를 **Start-ServiceFabricApplication업그레이드** Azure 서비스 패브릭 PowerShell cmdlet과 함께 사용하여 편리하게 수행할 수 있습니다. 다음과 같은 속성이 있는 서비스 패브릭 응용 프로그램을 가정합니다.

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "1"; "NewParameter" = "testBefore" }
```

이제 **시작 서비스패브릭응용 프로그램을** 사용하여 응용 프로그램을 업그레이드합니다. 이 예제에서는 모니터링되는 업그레이드를 보여 주지만 모니터링되지 않은 업그레이드도 사용할 수 있습니다. 이 cmdlet에서 허용하는 플래그에 대한 전체 설명을 보려면 [Azure 서비스 패브릭 PowerShell 모듈 참조를](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps#parameters) 참조하십시오.

```PowerShell
PS C:\> $appParams = @{ "ImportantParameter" = "2"; "NewParameter" = "testAfter"}

PS C:\> Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/Application1 -ApplicationTypeVers
ion 1.0.0 -ApplicationParameter $appParams -Monitored

```

업그레이드 후 응용 프로그램에 업데이트된 매개 변수와 동일한 버전이 있는지 확인합니다.

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "2"; "NewParameter" = "testAfter" }
```

## <a name="roll-back-application-upgrades"></a>응용 프로그램 업그레이드 롤백

업그레이드를 세 가지 모드(*Monitored*, *UnmonitoredAuto* 또는 *UnmonitoredManual*) 중 하나로 롤포워드할 수 있지만, 롤백은 *UnmonitoredAuto* 또는 *UnmonitoredManual* 모드에서만 가능합니다. *UnmonitoredAuto* 모드에서 수행하는 롤백은 *UpgradeReplicaSetCheckTimeout*의 기본값이 다르다는 점을 제외하고 롤포워드와 동일한 방식으로 작동합니다([애플리케이션 업그레이드 매개 변수](service-fabric-application-upgrade-parameters.md) 참조). *UnmonitoredManual* 모드에서 수행하는 롤백은 롤포워드와 동일한 방식으로 작동합니다. 롤백은 각 UD가 완료되면 자체적으로 일시 중단한 후 [ Resume-servicefabricapplicationupgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps)를 사용하여 명시적으로 다시 시작하고 롤백을 계속 진행해야 합니다.

*Monitored* 모드에서 *롤백*의 *FailureAction*이 지정된 업그레이드 상태 정책을 위반하거나([애플리케이션 업그레이드 매개 변수](service-fabric-application-upgrade-parameters.md) 참조) 명시적으로 [Start-ServiceFabricApplicationRollback](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback?view=azureservicefabricps)을 사용하는 경우 롤백을 자동으로 트리거할 수 있습니다.

롤백 중에 언제든지 [Update-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade?view=azureservicefabricps)를 사용하여 *UpgradeReplicaSetCheckTimeout* 값과 모드를 변경할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[Visual Studio를 사용하여 응용 프로그램을 업그레이드하면](service-fabric-application-upgrade-tutorial.md) Visual Studio를 사용하여 응용 프로그램 업그레이드를 진행합니다.

[Powershell을 사용하여 응용 프로그램을 업그레이드하면](service-fabric-application-upgrade-tutorial-powershell.md) PowerShell을 사용하여 응용 프로그램 업그레이드를 진행합니다.

[업그레이드 매개 변수](service-fabric-application-upgrade-parameters.md)를 사용하여 애플리케이션 업그레이드 방법을 제어합니다.

[데이터 직렬화를](service-fabric-application-upgrade-data-serialization.md)사용하는 방법을 학습하여 응용 프로그램 업그레이드를 호환되도록 합니다.

응용 프로그램 업그레이드 문제 해결의 단계를 참조하여 응용 프로그램 업그레이드의 일반적인 문제를 [해결합니다.](service-fabric-application-upgrade-troubleshooting.md)
