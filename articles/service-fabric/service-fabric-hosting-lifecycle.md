---
title: Azure Service Fabric 호스팅 활성화 및 비활성화 수명 주기
description: 노드에서의 애플리케이션 및 ServicePackage 수명 주기에 대해 알아봅니다.
author: tugup
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: tugup
ms.openlocfilehash: d8585d0b39e4a4ef9cf77f40ea878ddb47bcb0de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97831825"
---
# <a name="azure-service-fabric-hosting-life-cycle"></a>Azure Service Fabric 호스팅 수명 주기

이 문서에서는 노드에서 애플리케이션이 활성화될 때 Azure Service Fabric에서 발생하는 이벤트에 대한 개요를 제공하고 동작을 제어하는 다양한 클러스터 구성에 대해 설명합니다.

계속하기 전에 [Service Fabric의 애플리케이션 모델링][a1]에서 설명한 개념과 관계를 이해해야 합니다. 

> [!NOTE]
> 이 문서에서는 특수한 방식으로 몇 가지 용어를 사용합니다. 별도로 지정되지 않은 경우
>
> - *복제본* 은 상태 저장 서비스 복제본 및 상태 비저장 서비스 인스턴스를 모두 가리킵니다.
> - *CodePackage* 는 ServiceType을 등록하는 ServiceHost 프로세스와 동일한 것으로 간주되며 해당 ServiceType의 서비스 복제본을 호스트합니다.
>

## <a name="activate-an-applicationpackage-or-servicepackage"></a>ApplicationPackage 또는 ServicePackage 활성화

ApplicationPackage 또는 ServicePackage를 활성화하려면 다음을 수행합니다.

1. ApplicationPackage를 다운로드합니다(예: *ApplicationManifest.xml*).
2. 애플리케이션 환경을 설정합니다. 예를 들어 사용자 만들기 단계가 포함됩니다.
3. 비활성화를 위해 애플리케이션 추적을 시작합니다.
4. ServicePackage(예: *ServiceManifest.xml*, 코드, 구성 파일, 데이터 패키지)를 다운로드합니다.
5. ServicePackage 환경을 설정합니다. 예를 들어 방화벽을 설정하고 엔드포인트용 포트를 할당하는 단계가 포함됩니다.
6. 비활성화를 위해 ServicePackage 추적을 시작합니다.
7. CodePackages의 SetupEntryPoint를 시작하고, 완료되기를 기다립니다.
8. CodePackages의 MainEntryPoint를 시작합니다.

### <a name="servicetype-blocklisting"></a>ServiceType 차단 목록 등재
`ServiceTypeDisableFailureThreshold`는 허용되는 활성화 수, 다운로드 수, CodePackage 실패 횟수를 결정합니다. 임계값에 도달하면 해당 ServiceType이 차단 목록에 등재됩니다. 첫 번째 활성화 실패, 다운로드 실패 또는 CodePackage 크래시가 발생하면 ServiceType 차단 목록에 등재됩니다. 

`ServiceTypeDisableGraceInterval` 구성은 ServiceType이 노드의 차단 목록에 등재되기 전 유예 간격을 결정합니다. 이 프로세스가 진행되면 활성화, 다운로드, CodePackage 재시작을 동시에 다시 시도합니다. 다시 시도한다는 의미는 예를 들어, 크래시가 발생한 후 CodePackage를 다시 시작하도록 예약하거나 Service Fabric에서 패키지를 다시 다운로드하려고 하는 것입니다.

ServiceType이 차단 목록에 등재되면 `'System.Hosting' reported Error for property 'ServiceTypeRegistration:ServiceType'. The ServiceType was disabled on the node.` 상태 오류가 표시됩니다.

다음 이벤트 중 하나가 발생하면 노드에서 ServiceType을 다시 사용하도록 설정됩니다.
- 활성화에 성공합니다. 실패하는 경우에는 `ActivationMaxFailureCount` 최대 다시 시도 횟수에 도달합니다.
- 다운로드에 성공합니다. 실패하는 경우에는 `DeploymentMaxFailureCount` 최대 다시 시도 횟수에 도달합니다.
- 크래시가 발생한 CodePackage가 시작하여 ServiceType을 성공적으로 등록합니다.

`ActivationMaxFailureCount` 및 `DeploymentMaxFailureCount`는 Service Fabric이 노드에서 애플리케이션을 활성화하거나 다운로드할 최대 시도 횟수입니다. 최댓값에 도달하면 Service Fabric이 ServiceType을 다시 활성화합니다. 

해당 임계값은 서비스에 활성화를 위한 또 다른 기회를 제공합니다. 활성화에 성공하면 문제가 자동으로 해결됩니다. 

새로 배치되거나 활성화된 복제본은 새 활성화 또는 다운로드 작업을 트리거할 수 있습니다. 이 작업은 성공하거나 ServiceType 차단 목록 등재를 다시 트리거합니다.

> [!NOTE]
> ServiceType을 등록하지 않는 CodePackage는 크래시가 발생하더라도 ServiceType에 영향을 미치지 않습니다. 복제본을 호스트하는 CodePackage에 크래시가 발생하는 경우에만 ServiceType에 영향을 미칩니다.
>

### <a name="codepackage-crash"></a>CodePackage 크래시
CodePackage에 크래시가 발생하면 Service Fabric은 백오프를 사용하여 이를 다시 시작합니다. 백오프는 CodePackage가 Service Fabric 런타임에 형식을 등록했는지 여부와 관계없이 적용됩니다.

백오프 값은 `Min(RetryTime, ActivationMaxRetryInterval)`입니다. 이 값은 `ActivationRetryBackoffExponentiationBase` 구성 설정에 따라 상수, 선형 또는 지수 크기로 증가합니다.

- **상수**: `ActivationRetryBackoffExponentiationBase == 0`인 경우 `RetryTime = ActivationRetryBackoffInterval`입니다.
- **선형**: `ActivationRetryBackoffExponentiationBase == 0`이면 `RetryTime = ContinuousFailureCount ActivationRetryBackoffInterval`입니다. 이때 `ContinuousFailureCount`는 CodePackage의 크래시 횟수 또는 활성화 실패 횟수입니다.
- **지수**: `RetryTime = (ActivationRetryBackoffInterval in seconds) * (ActivationRetryBackoffExponentiationBase ^ ContinuousFailureCount)`입니다.
    
이 값을 변경하여 동작을 제어할 수 있습니다. 예를 들어 여러 번 빠르게 다시 시작을 시도하려는 경우 `ActivationRetryBackoffExponentiationBase`를 `0`으로 설정하고 `ActivationRetryBackoffInterval`을 `10`으로 설정하여 선형 크기를 사용할 수 있습니다. 따라서 CodePackage에 크래시가 발생하는 경우 시작 간격은 10초 이후입니다. 패키지에 계속 크래시가 발생하면 CodePackage 활성화가 성공하거나 CodePackage가 비활성화될 때까지 백오프가 20초, 30초, 40초 등으로 변경됩니다. 
    
오류가 `ActivationMaxRetryInterval`에 의해 제어된 후 Service Fabric이 백오프(대기)하는 최대 시간입니다.
    
CodePackage에 크래시가 발생한 후 다시 정상화되면 `CodePackageContinuousExitFailureResetInterval`에서 지정한 시간 동안 상태가 유지되어야 합니다. 이 간격이 지나야 Service Fabric이 해당 CodePackage를 정상으로 간주합니다. 그렇게 되면 Service Fabric이 이전 오류 상태 보고서를 정상으로 덮어쓰고 `ContinuousFailureCount`를 다시 설정합니다.

### <a name="codepackage-not-registering-servicetype"></a>CodePackage가 ServiceType을 등록하지 않음
CodePackage가 유지되고 ServiceType을 등록해야 하지만 그러지 않는 경우 Service Fabric은 `ServiceTypeRegistrationTimeout`이 지난 후 경고 상태 보고서를 생성합니다. 이 보고서는 ServiceType이 예상 시간 내에 등록되지 않았음을 나타냅니다.

### <a name="activation-failure"></a>활성화 오류
Service Fabric이 활성화 중에 오류를 발견하면 항상 CodePackage에서 크래시가 발생하는 경우와 마찬가지로 선형 백오프를 사용합니다. 활성화 작업은 (0 + 10 + 20 + 30 + 40) = 100초 간격으로 다시 시도한 후 포기합니다. 첫 번째 다시 시도는 즉시 이루어지며 이 시퀀스 후에는 활성화가 다시 시도되지 않습니다.
    
최대 활성화 백오프는 `ActivationMaxRetryInterval`일 수 있습니다. 다시 시도는 `ActivationMaxFailureCount`일 수 있습니다.

### <a name="download-failure"></a>다운로드 오류
Service Fabric은 다운로드하는 동안 오류가 발견되면 항상 선형 백오프를 사용합니다. 활성화 작업은 (0 + 10 + 20 + 30 + 40) = 100초 간격으로 다시 시도한 후 포기합니다. 첫 번째 다시 시도는 즉시 이루어지며 이 시퀀스 후에는 다운로드가 다시 시도되지 않습니다. 

다운로드 선형 백오프는 `ContinuousFailureCount` * `DeploymentRetryBackoffInterval`과 같습니다. 최대 백오프는 `DeploymentMaxRetryInterval`일 수 있습니다. 활성화와 마찬가지로 다운로드 작업은 `ActivationMaxFailureCount` 제한까지 다시 시도할 수 있습니다.

> [!NOTE]
> 관련 설정을 변경하기 전에 다음 예제에 유의하세요.
>
>* CodePackage에서 크래시와 백오프가 계속 이루어지는 경우 ServiceType이 비활성화됩니다. 단, 활성화 구성에 빠른 다시 시작이 있는 경우 ServiceType이 실제로 차단 목록에 등재되기 전에 CodePackage가 여러 번 생성될 수 있습니다. 
>
>    예를 들어 CodePackage가 생성되고 ServiceType을 Service Fabric에 등록한 후, 크래시가 발생한다고 가정해 보겠습니다. 이 경우 호스팅에서 형식 등록을 받으면 `ServiceTypeDisableGraceInterval` 기간이 취소됩니다. 이 프로세스는 CodePackage가 `ServiceTypeDisableGraceInterval` 기간보다 큰 값으로 백오프할 때까지 반복될 수 있습니다. 그러면 ServiceType이 노드에서 차단 목록에 등재됩니다. ServiceType 차단 목록 등재 작업은 예상보다 오래 걸릴 수 있습니다.
>
>* 활성화의 경우 Service Fabric 시스템이 노드에 복제본을 배치해야 할 때, 재구성 에이전트는 호스팅 하위 시스템에 애플리케이션을 활성화하도록 요청하며 15초마다 활성화 요청을 다시 시도합니다. 해당 기간은 `RAPMessageRetryInterval`의 구성 설정에 따라 결정됩니다. 호스팅의 활성화 작업이 다시 시도 간격과 `ServiceTypeDisableGraceInterval`보다 긴 시간 이루어지지 않는 한 Service Fabric은 ServiceType이 차단 목록에 등재되었는지 알 수 없습니다. 
>
>    예를 들어 클러스터의 `ActivationMaxFailureCount`가 5로 설정되어 있고 `ActivationRetryBackoffInterval`이 1초로 설정되어 있다고 가정합니다. 이 경우 활성화 작업은 (0 + 1 + 2 + 3 + 4) = 10초 간격이 지나면 포기합니다. 첫 번째 다시 시도는 즉시 이루어지며, 이 시퀀스가 지나면 호스팅은 다시 시도를 포기합니다. 활성화 작업이 완료되며 15초 후에 다시 시도되지 않습니다. 
>
>    Service Fabric이 15초 이내에 허용되는 다시 시도 횟수를 모두 소진했습니다. 따라서 재구성 에이전트에서 다시 시도할 때마다 호스팅 하위 시스템에 새 활성화 작업을 만들며 이 패턴이 계속 반복됩니다. 그러면 ServiceType이 노드의 차단 목록에 등재되지 않습니다. ServiceType이 노드의 차단 목록에 등재되지 않으므로 복제본이 이동해 다른 노드에서 시도되지 않습니다.
> 

## <a name="deactivation"></a>비활성화

노드에서 활성화된 ServicePackage는 비활성화를 위해 추적됩니다. 비활성화는 다음과 같은 두 가지 방법으로 작동합니다.

- **주기적 비활성화**: `DeactivationScanInterval`마다 시스템은 복제본을 호스트하지 ‘않은’ 서비스 패키지를 확인하고 이를 비활성화 후보로 표시합니다.
- **ReplicaClose 비활성화**: 복제본이 닫히면 비활성화기가 `DecrementUsageCount`를 얻습니다. ServicePackage가 복제본을 호스트하지 않는 경우 0이 되어 ServicePackage는 비활성화 후보가 됩니다.

활성화 모드는 후보의 비활성화가 예약될 시기를 결정합니다. 공유 모드에서 후보는 `DeactivationGraceInterval`이 지나면 비활성화가 예약됩니다. 배타 모드에서는 `ExclusiveModeDeactivationGraceInterval`이 지나면 예약됩니다. 그 사이에 새 복제본 배치가 이루어지면 비활성화가 취소됩니다. 

자세한 내용은 [배타 모드 및 공유 모드][a2]를 참조하세요.

### <a name="periodic-deactivation"></a>주기적 비활성화
다음은 주기적 비활성화의 몇 가지 예입니다.

* **예제 1**: 비활성화기가 T(`DeactivationScanInterval`) 시점에 검사를 시작한다고 가정해 보겠습니다. 다음 검사는 2T에 이루어집니다. ServicePackage 활성화가 T + 1에 이루어졌다고 가정합니다. 해당 ServicePackage는 복제본을 호스트하지 않으므로 비활성화되어야 합니다. 

    비활성화 후보가 되려면 ServicePackage가 T시간 동안 복제본을 호스트하지 않아야 합니다. 그러면 2T + 1에 비활성화 자격을 갖추게 됩니다. 따라서 2T에 이루어진 검사는 해당 ServicePackage를 비활성화 후보로 식별하지 않습니다. 

    다음 비활성화 주기인 3T일 때, 패키지가 T시간 동안 복제 상태인 적이 없었으므로 비로소 해당 ServicePackage의 비활성화가 예약됩니다.  

* **예제 2**: ServicePackage가 T - 1 시점에 활성화되고 비활성화기가 T에 검사를 시작한다고 가정해 보겠습니다. 이때 ServicePackage는 복제본을 호스트하지 않습니다. 다음 검사인 2T에 ServicePackage는 비활성화 후보로 식별되므로 비활성화가 예약됩니다.  

* **예제 3**: ServicePackage가 T - 1에 활성화되고 비활성화기가 T에 검사를 시작한다고 가정해 보겠습니다. 이때 ServicePackage는 아직 복제본을 호스트하지 않습니다. 이제 T + 1에서 복제본이 배치됩니다. 즉, 호스팅은 `IncrementUsageCount`를 얻습니다. 이는 복제본이 생성되었다는 의미입니다. 

    2T에는 해당 ServicePackage의 비활성화가 예약되지 않습니다. 패키지에 복제본이 포함되어 있으므로 이 문서의 다음 섹션에서 설명하는 대로 비활성화는 ReplicaClose 논리를 따릅니다.

* **예제 4**: ServicePackage가 10GB라고 가정해 보겠습니다. 패키지가 크므로 노드에 다운로드하는 데 시간이 걸립니다. 애플리케이션이 활성화되면 비활성화기가 수명 주기를 추적합니다. `DeactivationScanInterval`을 작은 값으로 설정하면 다운로드 소요 시간으로 인해 ServicePackage를 노드에서 활성화할 시간이 없을 수 있습니다. 이 문제를 해결하려면 [노드에 미리 ServicePackage를 다운로드][p1]하거나 `DeactivationScanInterval`을 늘리면 됩니다. 

> [!NOTE]
> ServicePackage는 (`DeactivationScanInterval`~2 * `DeactivationScanInterval`) + `DeactivationGraceInterval`/`ExclusiveModeDeactivationGraceInterval` 사이 어느 시점에서나 비활성화될 수 있습니다. 
>

### <a name="replicaclose-deactivation"></a>ReplicaClose 비활성화

> [!NOTE]
> 이 섹션에서는 다음 구성 설정을 참조합니다.
> - **DeactivationGraceInterval**/**ExclusiveModeDeactivationGraceInterval**: 복제본을 이미 호스트한 경우 ServicePackage가 다른 복제본을 호스트하는 데 주어진 시간입니다. 
> - **DeactivationScanInterval**: 어떤 복제본도 호스트한 적이 ‘없는’ 경우 ServicePackage가 복제본을 호스트하는 데 주어진 최소 시간입니다.
>

시스템은 ServicePackage가 보유한 복제본의 수를 유지합니다. ServicePackage가 복제본을 보유하고 있고 복제본이 닫히거나 중단된 경우 호스팅은 `DecrementUsageCount`를 얻습니다. 복제본이 열리면 호스팅은 `IncrementUsageCount`를 얻습니다. 

감소는 ServicePackage가 호스트하는 복제본 수에서 복제본 1개가 감소했음을 의미합니다. 개수가 0으로 떨어지면 ServicePackage가 비활성화되도록 예약됩니다. 비활성화되는 시점은 `DeactivationGraceInterval`/`ExclusiveModeDeactivationGraceInterval` 이후입니다. 

예를 들어, T에 감소가 발생하고 2T + X(`DeactivationGraceInterval`/`ExclusiveModeDeactivationGraceInterval`)에 ServicePackage가 비활성화되도록 예약되어 있다고 가정해 보겠습니다. 이 시간 동안에 복제본이 만들어지기 때문에 호스팅에서 `IncrementUsage`를 얻으면 비활성화가 취소됩니다.

### <a name="ctrl--c"></a>Ctrl + C
ServicePackage가 `DeactivationGraceInterval`/`ExclusiveModeDeactivationGraceInterval`이 지난 시점에도 복제본을 호스트하지 않으면 비활성화를 취소할 수 없습니다. CodePackage는 Ctrl + C 처리기를 수신합니다. 이제 비활성화 파이프라인이 완료되어야 프로세스를 종료할 수 있습니다. 

이 시간 동안 동일한 ServicePackage의 새 복제본이 배치되려고 하는 경우 비활성화에서 활성화로 전환할 수 없으므로 프로세스는 실패합니다.

## <a name="cluster-configurations"></a>클러스터 구성

이 섹션에서는 활성화와 비활성화에 영향을 주는 기본값이 있는 구성을 나열합니다.

### <a name="servicetype"></a>ServiceType
- **ServiceTypeDisableFailureThreshold**: 기본값: 1. 해당 노드에서 서비스 유형을 사용하지 않도록 설정하고 다른 노드에 배치를 시도하도록 FailoverManager에 알리기까지 허용되는 실패 횟수에 대한 임계값입니다.
- **ServiceTypeDisableGraceInterval**: 기본값: 30초. 서비스 유형을 사용 해제할 수 있는 시간 간격입니다.
- **ServiceTypeRegistrationTimeout**: 기본값: 300초. ServiceType이 Service Fabric에 등록되기까지의 시간 제한입니다.

### <a name="activation"></a>정품 인증
- **ActivationRetryBackoffInterval**: 기본값: 10초. 각 활성화 실패에 대한 백오프 간격입니다.
- **ActivationMaxFailureCount**: 기본값: 20. 시스템에서 실패한 활성화를 포기하기 전에 다시 시도할 최대 횟수입니다. 
- **ActivationRetryBackoffExponentiationBase**: 기본값: 1.5.
- **ActivationMaxRetryInterval**: 기본값: 3,600초. 실패 후 활성화하기 위한 최대 백오프 다시 시도 간격입니다.
- **CodePackageContinuousExitFailureResetInterval**: 기본값: 300초. CodePackage의 연속 종료 실패 횟수를 다시 설정하는 시간 제한 간격입니다.

### <a name="download"></a>다운로드
- **DeploymentRetryBackoffInterval**: 기본값: 10. 배포 실패에 대한 백오프 간격입니다.
- **DeploymentMaxRetryInterval**: 기본값: 3,600초. 실패 후 배포에 대한 최대 백오프 간격입니다.
- **DeploymentMaxFailureCount**: 기본값: 20. 노드에서 애플리케이션의 배포가 실패하기 전에 `DeploymentMaxFailureCount` 시간 동안 해당 애플리케이션의 배포가 다시 시도됩니다.

### <a name="deactivation"></a>비활성화
- **DeactivationScanInterval**: 기본값: 600초. 복제본을 호스트하지 않은 경우(즉, 사용하지 않은 경우) ServicePackage가 복제본을 호스트하는 데 주어진 최소 시간입니다.
- **DeactivationGraceInterval**: 기본값: 60초. ‘공유’ 프로세스 모델에서 이미 복제본을 호스트한 후 ServicePackage가 다른 복제본을 다시 호스트하는 데 주어진 시간입니다.
- **ExclusiveModeDeactivationGraceInterval**: 기본값: 1초. ‘배타’ 프로세스 모델에서 이미 복제본을 호스트한 후 ServicePackage가 다른 복제본을 다시 호스트하는 데 주어진 시간입니다.

## <a name="next-steps"></a>다음 단계

- [애플리케이션을 패키지][a3]하고 배포를 준비합니다.
- PowerShell에서 [애플리케이션을 배포하고 제거][a4]합니다.

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-hosting-model.md
[a3]: service-fabric-package-apps.md
[a4]: service-fabric-deploy-remove-applications.md

[p1]: /powershell/module/servicefabric/copy-servicefabricservicepackagetonode
