---
title: Azure Service Fabric 호스팅 활성화 및 비활성화 수명 주기
description: 응용 프로그램의 수명 주기와 노드의 ServicePackage 대해 알아봅니다.
author: tugup
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: tugup
ms.openlocfilehash: d8585d0b39e4a4ef9cf77f40ea878ddb47bcb0de
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97831825"
---
# <a name="azure-service-fabric-hosting-life-cycle"></a>Azure Service Fabric 호스팅 수명 주기

이 문서에서는 응용 프로그램이 노드에서 활성화 될 때 Azure Service Fabric에서 발생 하는 이벤트에 대 한 개요를 제공 합니다. 이를 통해 동작을 제어 하는 다양 한 클러스터 구성을 설명 합니다.

계속 하기 전에 [Service Fabric에서 응용 프로그램 모델링][a1]에 설명 된 개념 및 관계를 이해 하 고 있어야 합니다. 

> [!NOTE]
> 이 문서에서는 특수 한 방법으로 몇 가지 용어를 사용 합니다. 달리 명시 되지 않은 경우:
>
> - *복제본* 은 상태 저장 서비스 복제본 및 상태 비저장 서비스 인스턴스를 모두 가리킵니다.
> - *CodePackage* 는 ServiceType을 등록 하는 ServiceHost 프로세스와 동일한 것으로 간주 됩니다. 해당 ServiceType의 서비스 복제본을 호스팅합니다.
>

## <a name="activate-an-applicationpackage-or-servicepackage"></a>ApplicationPackage 또는 ServicePackage를 활성화 합니다.

ApplicationPackage 또는 ServicePackage를 활성화 하려면:

1. ApplicationPackage를 다운로드 합니다 (예: *ApplicationManifest.xml*).
2. 응용 프로그램에 대 한 환경을 설정 합니다. 단계에는 사용자 만들기 등이 포함 됩니다.
3. 비활성화를 위해 응용 프로그램 추적을 시작 합니다.
4. ServicePackage (예: *ServiceManifest.xml*, 코드, 구성 파일 및 데이터 패키지)를 다운로드 합니다.
5. ServicePackage에 대 한 환경을 설정 합니다. 단계에는 방화벽을 설정 하 고 끝점에 대 한 포트를 할당 하는 작업이 포함 됩니다.
6. 비활성화를 위해 ServicePackage 추적을 시작 합니다.
7. CodePackages에 대 한 SetupEntryPoint를 시작 하 고 완료 될 때까지 기다립니다.
8. CodePackages에 대 한 MainEntryPoint를 시작 합니다.

### <a name="servicetype-blocklisting"></a>ServiceType 블록 목록
`ServiceTypeDisableFailureThreshold` 허용 되는 활성화, 다운로드 및 CodePackage 실패 횟수를 결정 합니다. 임계값에 도달 하면 ServiceType이 차단 목록에 대해 예약 됩니다. 첫 번째 활성화 실패, 다운로드 실패 또는 CodePackage 크래시는 ServiceType 블록 목록을 예약 합니다. 

`ServiceTypeDisableGraceInterval`구성에 따라 ServiceType이 노드에 나열 되기 전의 유예 간격이 결정 됩니다. 이 프로세스가 진행 되 면 활성화, 다운로드 및 CodePackage 다시 시작이 병렬로 다시 시도 됩니다. 다시 시도 하는 것은 예를 들어, 충돌이 발생 한 후 CodePackage가 다시 시작 되도록 예약 되어 있거나 Service Fabric에서 패키지를 다시 다운로드 하려고 시도 하는 것입니다.

ServiceType이 표시 되 면 상태 오류가 표시 됩니다. `'System.Hosting' reported Error for property 'ServiceTypeRegistration:ServiceType'. The ServiceType was disabled on the node.`

다음 이벤트 중 하나가 발생 하는 경우 노드에 ServiceType을 다시 사용 하도록 설정 합니다.
- 활성화가 성공 합니다. 실패 하는 경우 `ActivationMaxFailureCount` 최대 다시 시도 횟수에 도달 합니다.
- 다운로드가 성공 합니다. 실패 하는 경우 `DeploymentMaxFailureCount` 최대 다시 시도 횟수에 도달 합니다.
- 충돌을 시작 하 고 ServiceType을 성공적으로 등록 한 CodePackage입니다.

`ActivationMaxFailureCount` 및 `DeploymentMaxFailureCount` 는 노드에서 응용 프로그램을 활성화 하거나 다운로드 하는 Service Fabric 최대 시도 횟수입니다. 최대값에 도달한 후에는 Service Fabric에서 ServiceType을 다시 활성화 하도록 설정 합니다. 

이러한 임계값은 서비스에 활성화를 위한 다른 기회를 제공 합니다. 정품 인증에 성공 하면 문제가 자동으로 치료 됩니다. 

새로 배치 되거나 활성화 된 복제본은 새 활성화 또는 다운로드 작업을 트리거할 수 있습니다. 이 작업은 성공 하거나 ServiceType 블록 목록을 다시 트리거합니다.

> [!NOTE]
> ServiceType을 등록 하지 않은 충돌 CodePackage는 ServiceType에 영향을 주지 않습니다. 복제본을 호스팅하는 충돌 CodePackage ServiceType에 영향을 줍니다.
>

### <a name="codepackage-crash"></a>CodePackage 충돌
CodePackage 작동이 중단 되 면 Service Fabric 백오프를 사용 하 여 다시 시작 합니다. 백오프는 CodePackage가 Service Fabric 런타임에 형식을 등록 했는지 여부에 관계 없이 적용 됩니다.

백오프 값은 `Min(RetryTime, ActivationMaxRetryInterval)` 입니다. 값은 구성 설정에 따라 상수, 선형 또는 지 수 양만큼 증가 합니다 `ActivationRetryBackoffExponentiationBase` .

- **상수**: 이면이 `ActivationRetryBackoffExponentiationBase == 0` 고, 그렇지 않으면 `RetryTime = ActivationRetryBackoffInterval` 입니다.
- **선형**: 인 경우  `ActivationRetryBackoffExponentiationBase == 0` `RetryTime = ContinuousFailureCount ActivationRetryBackoffInterval` `ContinuousFailureCount` 입니다. 여기서은 CodePackage가 충돌 하거나 활성화 하지 못한 횟수입니다.
- **지** 수: `RetryTime = (ActivationRetryBackoffInterval in seconds) * (ActivationRetryBackoffExponentiationBase ^ ContinuousFailureCount)` .
    
값을 변경 하 여 동작을 제어할 수 있습니다. 예를 들어 몇 번의 빠른 다시 시작을 원하는 경우 `ActivationRetryBackoffExponentiationBase` 를로 설정 하 `0` 고를로 설정 하 `ActivationRetryBackoffInterval` 여 선형 금액을 사용할 수 있습니다 `10` . 따라서 CodePackage가 충돌 하는 경우 시작 간격은 10 초 이후가 됩니다. 패키지가 계속 충돌 하는 경우 백오프는 CodePackage 활성화가 성공 하거나 CodePackage가 비활성화 될 때까지 20 초, 30 초, 40 초 등으로 변경 됩니다. 
    
오류가의 영향을 받을 때까지 대기 하는 (즉, 대기) Service Fabric 최대 시간입니다 `ActivationMaxRetryInterval` .
    
CodePackage 작동이 중단 되 고 다시 온라인 상태가 되 면에 지정 된 기간 동안 유지 되어야 `CodePackageContinuousExitFailureResetInterval` 합니다. 이 간격 후 Service Fabric CodePackage 정상으로 간주 됩니다. 그런 다음 Service Fabric 이전 오류 상태 보고서를 확인으로 덮어쓰고를 다시 설정 `ContinuousFailureCount` 합니다.

### <a name="codepackage-not-registering-servicetype"></a>ServiceType을 등록 하지 CodePackage
CodePackage가 계속 작동 하 고 ServiceType을 등록 해야 하는 경우에는 Service Fabric 후에 경고 상태 보고서를 생성 합니다 `ServiceTypeRegistrationTimeout` . 이 보고서는 ServiceType이 예상 시간 내에 등록 되지 않았음을 나타냅니다.

### <a name="activation-failure"></a>활성화 실패
Service Fabric 활성화 하는 동안 오류가 발견 되 면 CodePackage 충돌 시와 마찬가지로 항상 선형 백오프을 사용 합니다. 활성화 작업은 (0 + 10 + 20 + 30 + 40) = 100 초 간격으로 재시도 후에 제공 됩니다. 첫 번째 다시 시도는 즉시 실행 됩니다. 이 시퀀스 후에 활성화는 다시 시도 되지 않습니다.
    
최대 활성화 백오프는 일 수 있습니다 `ActivationMaxRetryInterval` . 다시 시도는 일 수 있습니다 `ActivationMaxFailureCount` .

### <a name="download-failure"></a>다운로드 실패
Service Fabric은 다운로드 중에 오류를 발견할 경우 항상 선형 백오프을 사용 합니다. 활성화 작업은 (0 + 10 + 20 + 30 + 40) = 100 초 간격으로 재시도 후에 제공 됩니다. 첫 번째 다시 시도는 즉시 실행 됩니다. 이 시퀀스 후에는 다운로드를 다시 시도 하지 않습니다. 

다운로드에 대 한 선형 백오프는과 같습니다 `ContinuousFailureCount`  *  `DeploymentRetryBackoffInterval` . 최대 백오프는 일 수 있습니다 `DeploymentMaxRetryInterval` . 활성화와 마찬가지로 다운로드 작업에서 한도를 다시 시도할 수 있습니다 `ActivationMaxFailureCount` .

> [!NOTE]
> 이러한 설정을 변경 하려면 먼저 다음 예제를 염두에 두어야 합니다.
>
>* CodePackage가 계속 해 서 충돌 하 고 백업이 중단 되 면 ServiceType이 사용 하지 않도록 설정 됩니다. 그러나 활성화 구성에서 빠른 다시 시작을 수행 하는 경우에는 ServiceType이 실제로 차단 되기 전에 CodePackage 몇 번 발생할 수 있습니다. 
>
>    예를 들어 CodePackage이 표시 되 고 Service Fabric에 ServiceType을 등록 한 후 작동이 중단 되는 경우를 가정해 보겠습니다. 이 경우 호스팅에서 형식 등록을 받은 후 `ServiceTypeDisableGraceInterval` 기간이 취소 됩니다. CodePackage가 기간 보다 큰 값으로 백업 될 때까지이 프로세스를 반복할 수 있습니다 `ServiceTypeDisableGraceInterval` . 그러면 ServiceType이 노드에 나열 됩니다. ServiceType 차단 목록 작성 때 예상치 못한 시간이 소요 될 수 있습니다.
>
>* 활성화의 경우 Service Fabric 시스템에서 노드에 복제본을 저장 해야 하는 경우에는 재구성 에이전트가 호스팅 하위 시스템에 응용 프로그램을 활성화 하도록 요청 합니다. 15 초 마다 활성화 요청을 다시 시도 합니다. 기간은 구성 설정에 따라 결정 됩니다 `RAPMessageRetryInterval` . 호스팅에 대 한 활성화 작업이 재시도 간격 및 보다 더 오래 된 경우를 제외 하 고는 ServiceType이 차단 된 것을 알 수 Service Fabric `ServiceTypeDisableGraceInterval` . 
>
>    예를 들어 클러스터의 `ActivationMaxFailureCount` 가 5로 설정 되어 있고 `ActivationRetryBackoffInterval` 가 1 초로 설정 되어 있다고 가정 합니다. 이 경우 활성화 작업은 (0 + 1 + 2 + 3 + 4)의 간격이 10 초인 후에 제공 됩니다. 첫 번째 다시 시도는 즉시 실행 됩니다. 이 시퀀스 후에는 호스팅을 통해 다시 시도 합니다. 활성화 작업이 완료 되 고 15 초 후에 다시 시도 되지 않습니다. 
>
>    Service Fabric 15 초 내에 허용 되는 모든 재시도를 모두 사용 했습니다. 따라서 재구성 에이전트의 모든 재시도는 호스팅 하위 시스템에서 새 활성화 작업을 만들며 패턴은 계속 반복 됩니다. 따라서 ServiceType은 노드에 표시 되지 않습니다. ServiceType이 노드에 나열 되지 않기 때문에 복제본이 이동 하 고 다른 노드에서 시도 되지 않습니다.
> 

## <a name="deactivation"></a>비활성화

노드에서 활성화 된 ServicePackage는 비활성화를 위해 추적 됩니다. 비활성화는 다음과 같은 두 가지 방법으로 작동 합니다.

- **정기적 비활성화**: 모든 `DeactivationScanInterval` 시스템에서 복제본을 호스트 *하지* 않은 서비스 패키지를 확인 하 고 비활성화를 위한 후보로 표시 합니다.
- **ReplicaClose 비활성화**: 복제본이 닫히면 deactivator는를 가져옵니다 `DecrementUsageCount` . ServicePackage는 복제본을 호스트 하지 않는 경우 0입니다. 따라서 ServicePackage는 비활성화의 후보가 됩니다.

활성화 모드는 후보가 비활성화 되도록 예약 된 시기를 결정 합니다. 공유 모드에서는 비활성화 후보가 이후에 예약 됩니다 `DeactivationGraceInterval` . 단독 모드에서는 이후에 예약 `ExclusiveModeDeactivationGraceInterval` 됩니다. 이러한 시간 사이에 새 복제본 배치가 도착 하면 비활성화가 취소 됩니다. 

자세한 내용은 [배타 모드 및 공유 모드][a2]를 참조 하세요.

### <a name="periodic-deactivation"></a>정기적 비활성화
정기 비활성화의 몇 가지 예는 다음과 같습니다.

* **예제 1**: Deactivator가 T () 시간에 검색을 시작 한다고 가정해 보겠습니다 `DeactivationScanInterval` . 다음 검색은 2T에 있습니다. T + 1에서 ServicePackage 활성화가 발생 했다고 가정 합니다. 이 ServicePackage는 복제본을 호스팅하지 않으므로 비활성화 해야 합니다. 

    비활성화의 후보가 되려면 ServicePackage는 최소 T 시간 동안 복제본을 호스트 하지 않아야 합니다. 2T + 1에서 비활성화할 수 있습니다. 따라서 2T에서 검색은이 ServicePackage를 비활성화의 후보로 식별 하지 않습니다. 

    다음 비활성화 주기 3T는 이제 패키지가 시간 T에 대 한 복제본 없음 상태에 있기 때문에이 ServicePackage의 비활성화를 예약 합니다.  

* **예제 2**: ServicePackage이 t-1 번에 활성화 되 고 Deactivator가 t에서 검색을 시작 한다고 가정 합니다. ServicePackage는 복제본을 호스팅하지 않습니다. 다음 번에 2T 검색에서 ServicePackage는 비활성화를 위한 후보로 식별 되므로 비활성화가 예약 됩니다.  

* **예제 3**: ServicePackage이 t – 1에서 활성화 되 고 Deactivator는 t에서 검색을 시작 한다고 가정 합니다. ServicePackage는 아직 복제본을 호스팅하지 않습니다. 이제 T + 1에서 복제본이 배치 됩니다. 즉, 호스트에서 `IncrementUsageCount` 을 (를) 가져옵니다 .이는 복제본이 생성 됨을 의미 합니다. 

    2T에서이 ServicePackage는 비활성화를 예약 하지 않습니다. 패키지에는 복제본이 포함 되어 있으므로이 문서의 다음 섹션에서 설명 하는 것 처럼 비활성화는 ReplicaClose 논리를 따릅니다.

* **예제 4**: ServicePackage이 10gb 라고 가정 하겠습니다. 패키지가 크기 때문에 노드에서 다운로드 하는 데 시간이 걸립니다. 응용 프로그램이 활성화 되 면 deactivator는 해당 수명 주기를 추적 합니다. `DeactivationScanInterval`이 작은 값으로 설정 된 경우 다운로드 하는 데 걸리는 시간 때문에 ServicePackage에 노드에서 활성화 하는 데 시간이 없을 수 있습니다. 이 문제를 해결 하기 위해 [노드에서 미리 ServicePackage를 다운로드][p1] 하거나를 늘릴 수 있습니다 `DeactivationScanInterval` . 

> [!NOTE]
> ServicePackage는 ( `DeactivationScanInterval` ~ 2 *) + 사이에서 비활성화 될 수 있습니다 `DeactivationScanInterval` `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` . 
>

### <a name="replicaclose-deactivation"></a>ReplicaClose 비활성화

> [!NOTE]
> 이 섹션에서는 다음 구성 설정을 참조 합니다.
> - **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**: 복제본을 이미 호스팅한 경우 ServicePackage에 게 다른 복제본을 호스트 하는 데 지정 된 시간입니다. 
> - **DeactivationScanInterval**: 복제본을 호스팅한 *적* 이 없는 경우 복제본을 호스트 하는 ServicePackage에 제공 되는 최소 시간입니다.
>

시스템은 ServicePackage 보유 한 복제본의 수를 유지 합니다. ServicePackage에서 복제본을 보유 하 고 있고 복제본이 닫히거나 중단 된 경우 호스팅에서을 가져옵니다 `DecrementUsageCount` . 복제본이 열리면 호스트는를 가져옵니다 `IncrementUsageCount` . 

감소는 ServicePackage에서 호스팅하는 복제본 수가 하나의 복제본으로 감소 했음을 나타냅니다. 개수가 0으로 떨어지면 ServicePackage가 비활성화 되도록 예약 됩니다. 비활성화 되는 시간은 `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` 입니다. 

예를 들어, 감소는 T에서 발생 하 고 ServicePackage는 2T + X ()에서 비활성화 하도록 예약 되어 있다고 가정해 보겠습니다 `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` . 이 시간 동안에는 복제본이 만들어지기 때문에 호스트가를 가져오는 경우 `IncrementUsage` 비활성화가 취소 됩니다.

### <a name="ctrl--c"></a>Ctrl + C
ServicePackage가를 전달 `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` 하 고 여전히 복제본을 호스팅하지 않는 경우 비활성화는 취소할 수 없습니다. CodePackages는 Ctrl + C 처리기를 수신 합니다. 이제 비활성화 파이프라인이 완료 되어야 프로세스를 종료할 수 있습니다. 

이 시간 동안 동일한 ServicePackage에 대 한 새 복제본이 배치 되려고 하는 경우 비활성화에서 활성화로 전환할 수 없으므로 프로세스가 실패 합니다.

## <a name="cluster-configurations"></a>클러스터 구성

이 섹션에서는 활성화 및 비활성화에 영향을 주는 기본값이 있는 구성을 나열 합니다.

### <a name="servicetype"></a>ServiceType
- **ServiceTypeDisableFailureThreshold**: 기본값: 1. 실패 수에 대 한 임계값입니다. 이 임계값에 도달한 후에는 노드에서 서비스 유형을 사용 하지 않도록 설정 하 고 배치에 다른 노드를 사용 하도록 설정 하 라는 알림이 표시 됩니다.
- **ServiceTypeDisableGraceInterval**: 기본값: 30 초. 서비스 유형을 사용 하지 않도록 설정할 수 있는 시간 간격입니다.
- **ServiceTypeRegistrationTimeout**: 기본값: 300 초 ServiceType이 Service Fabric에 등록 하기 위한 시간 제한입니다.

### <a name="activation"></a>정품 인증
- **ActivationRetryBackoffInterval**: 기본값: 10 초. 모든 활성화 오류에 대 한 백오프 간격입니다.
- **ActivationMaxFailureCount**: 기본값: 20. 포기 하기 전에 시스템에서 실패 한 정품 인증을 다시 시도 하는 최대 횟수입니다. 
- **ActivationRetryBackoffExponentiationBase**: 기본값: 1.5.
- **ActivationMaxRetryInterval**: 기본값: 3600 초 실패 후 활성화를 위한 최대 백오프 재시도 간격입니다.
- **CodePackageContinuousExitFailureResetInterval**: 기본값: 300 초 CodePackage의 연속 종료 실패 횟수를 다시 설정 하는 시간 제한 간격입니다.

### <a name="download"></a>다운로드
- **DeploymentRetryBackoffInterval**: 기본값: 10 배포 실패에 대 한 백오프 간격입니다.
- **DeploymentMaxRetryInterval**: 기본값: 3600 초 실패 후 배포에 대 한 최대 백오프 간격입니다.
- **DeploymentMaxFailureCount**: 기본값: 20. 응용 프로그램 배포는 `DeploymentMaxFailureCount` 노드에 해당 응용 프로그램의 배포에 실패 하기 전에 시간을 다시 시도 합니다.

### <a name="deactivation"></a>비활성화
- **DeactivationScanInterval**: 기본값: 600 초 복제본을 호스트 하지 않는 경우 (즉, 사용 되지 않는 경우) 복제본을 ServicePackage에 지정 된 최소 시간입니다.
- **DeactivationGraceInterval**: 기본값: 60 초 *공유* 프로세스 모델에서 ServicePackage에 지정 된 시간은 복제본을 이미 호스팅한 후 다른 복제본을 다시 호스트 하는 시간입니다.
- **ExclusiveModeDeactivationGraceInterval**: 기본값: 1 초 *단독* 프로세스 모델에서 ServicePackage에 지정 된 시간은 복제본을 이미 호스팅한 후 다른 복제본을 다시 호스트 하는 시간입니다.

## <a name="next-steps"></a>다음 단계

- [애플리케이션을 패키지][a3]하고 배포를 준비합니다.
- PowerShell에서 [응용 프로그램을 배포 하 고 제거][a4] 합니다.

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-hosting-model.md
[a3]: service-fabric-package-apps.md
[a4]: service-fabric-deploy-remove-applications.md

[p1]: /powershell/module/servicefabric/copy-servicefabricservicepackagetonode
