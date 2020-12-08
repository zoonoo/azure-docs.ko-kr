---
title: Azure Service Fabric 호스팅 활성화 및 비활성화 수명 주기
description: 노드의 응용 프로그램 및 ServicePackage 수명 주기에 대해 설명 합니다.
author: tugup
ms.topic: conceptual
ms.date: 05/1/2020
ms.author: tugup
ms.openlocfilehash: f049b19703d37412d1ee1961aee6cb327efabe7c
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96779603"
---
# <a name="azure-service-fabric-hosting-lifecycle"></a>Azure Service Fabric 호스팅 수명 주기

이 문서에서는 응용 프로그램이 노드에서 활성화 될 때와 동작을 제어 하는 데 사용 되는 다양 한 클러스터 configs를 사용 하 여 Azure Service Fabric에서 발생 하는 이벤트의 개요를 제공 합니다.

계속하기 전에 [Service Fabric에서 애플리케이션 모델링][a1]에서 설명한 다양한 개념 및 관계를 이해해야 합니다. 

> [!NOTE]
> 이 아티클에서는 다른 의미로 명시적으로 언급되지 않으면:
>
> - *복제본* 은 상태 저장 서비스의 복제본과 상태 비저장 서비스의 인스턴스를 모두 참조 합니다.
> - *CodePackage* 는 *servicetype* 을 등록 하 고 해당 *servicetype* 의 서비스 복제본을 호스트 하는 *ServiceHost* 프로세스와 동일한 것으로 간주 됩니다.
>

## <a name="activation-of-applicationservicepackage"></a>Application/ServicePackage 활성화

활성화를 위한 파이프라인은 다음과 같습니다.

1. ApplicationPackage를 다운로드 합니다. 예: ApplicationManifest.xml 등
2. 예: 사용자 만들기 등의 응용 프로그램에 대 한 환경을 설정 합니다.
3. 비활성화를 위해 추적 응용 프로그램을 시작 합니다.
4. ServicePackage를 다운로드 합니다. 예: ServiceManifest.xml, 코드, 구성, 데이터 패키지 등
5. 서비스 패키지에 대 한 환경 설정 예: 방화벽 설정, 끝점에 대 한 포트 할당 등
6. 비활성화를 위해 ServicePackage 추적을 시작 합니다.
7. CodePackages의 SetupEntryPoint를 시작 하 고 완료 될 때까지 기다립니다.
8. CodePackages의 MainEntryPoint를 시작 합니다.

### <a name="servicetype-blocklisting"></a>ServiceType 블록 목록
**ServiceTypeDisableFailureThreshold** 는 차단 된 블록 목록에 대해 ServiceType이 예약 된 실패 (활성화, 다운로드, CodePackage 실패) 수를 결정 합니다. 첫 번째 활성화 실패, 다운로드 실패 또는 CodePackage 크래시는 ServiceType 블록 목록을 예약 합니다. **ServiceTypeDisableGraceInterval** Config는 ServiceType이 해당 노드에 나열 된 블록으로 표시 되는 유예 간격을 결정 합니다. 이 모든 상황이 발생 하지만 활성화, 다운로드 및 CodePackage 다시 시작은 병렬로 다시 시도 됩니다. 다시 시도 하는 경우 예를 들어, CodePackage가 충돌 후 다시 시작 되도록 예약 Service Fabric 하거나 패키지 다운로드를 다시 시도 합니다.

ServiceType이 목록에 표시 되 면 ' ServiceTypeRegistration: ServiceType ' 속성에 대해 상태 오류 "' s t a t e s t '가 보고 된 오류가 표시 됩니다. ServiceType이 노드에서 사용 하지 않도록 설정 되었습니다. "

다음 중 하나가 발생 하는 경우 노드에 ServiceType을 다시 사용 하도록 설정 합니다.
- 정품 인증이 성공 하거나 실패 하면 **ActivationMaxFailureCount** 재시도에 도달 합니다.
- 다운로드가 성공 하거나 실패 하면 **DeploymentMaxFailureCount** 재시도에 도달 합니다.
- 충돌을 시작 하 고 ServiceType을 성공적으로 등록 한 CodePackage입니다.

**ActivationMaxFailureCount** 및 **DeploymentMaxFailureCount** 은 노드에서 응용 프로그램을 활성화/다운로드 하기 위해 수행할 수 있는 최대 Service Fabric 시도 횟수입니다. 그 후에는 Service Fabric에서 ServiceType을 다시 활성화 하도록 설정 합니다. 이는 서비스에 정품 인증을 수행할 수 있는 다른 기회를 제공 하기 위한 것입니다 .이로 인해 성공 하면 문제가 자동으로 복구 됩니다. 복제본의 배치 및 활성화에 의해 트리거되는 새 정품 인증/다운로드 작업은 ServiceType 블록 목록을 다시 트리거하거나 성공할 수 있습니다.

> [!NOTE]
> ServiceType을 등록 하지 않은 CodePackage가 충돌 하는 경우 ServiceType에 영향을 주지 않습니다. 복제본 충돌을 호스트 하는 CodePackage ServiceType에 영향을 줍니다.
>

### <a name="codepackage-crash"></a>CodePackage 충돌
CodePackage 작동이 중단 되 면 Service Fabric 백오프를 사용 하 여 다시 시작 합니다. 백오프는 코드 패키지가 Service Fabric 런타임에 형식을 등록 했는지 여부와는 독립적입니다.

백오프 값은 Min (RetryTime, **ActivationMaxRetryInterval**) 이며이 백오프 값은 **ActivationRetryBackoffExponentiationBase** 구성 설정에 따라 상수, 선형 또는 지 수 양만큼 증가 합니다.

- 상수: If **ActivationRetryBackoffExponentiationBase** = = 0 then RetryTime = **ActivationRetryBackoffInterval**;
- 선형: If  **ActivationRetryBackoffExponentiationBase** = = 0 then RetryTime = ContinuousFailureCount * **ActivationRetryBackoffInterval** where ContinousFailureCount은 CodePackage 충돌 또는 활성화에 실패 한 횟수입니다.
- 지 수: RetryTime = (**ActivationRetryBackoffInterval** ) * (**ActivationRetryBackoffExponentiationBase** ^ ContinuousFailureCount);
    
값을 변경 하 여 동작을 제어할 수 있습니다. 예를 들어 몇 번의 빠른 다시 시작을 원하는 경우 **ActivationRetryBackoffExponentiationBase** 를 0으로 설정 하 고 **ActivationRetryBackoffInterval** 를 10으로 설정 하 여 선형을 사용할 수 있습니다. 이러한 설정을 사용 하면 CodePackage가 충돌 하는 경우 시작 간격은 10 초 이후가 됩니다. 패키지가 중단 될 경우 백오프는 CodePackage 활성화가 성공 하거나 코드 패키지가 비활성화 될 때까지, 20, 30, 40 초 등으로 변경 됩니다. 
    
오류가 **ActivationMaxRetryInterval** 에 의해 제어 되는 경우 Service Fabric에서 백업 (대기) 하는 최대 시간입니다.
    
CodePackage 작동이 중단 되 고 백업 되 면 Service Fabric에 대 한 **CodePackageContinuousExitFailureResetInterval** 를 유지 하 여 정상으로 간주 해야 합니다 .이 시점에서 이전 오류 상태 보고서를 확인으로 덮어쓰고 ContinousFailureCount를 다시 설정 합니다.

### <a name="codepackage-not-registering-servicetype"></a>ServiceType을 등록 하지 CodePackage
CodePackage가 활성 상태로 유지 되 고 ServiceType을에 등록 하는 것으로 예상 되지만 그렇지 Service Fabric 않은 경우에는 ServiceType이 예상 시간 내에 등록 되지 않았다는 것을 **ServiceTypeRegistrationTimeout** 후 경고 HealthReport 생성 됩니다.

### <a name="activation-failure"></a>활성화 실패
Service Fabric는 활성화 하는 동안 오류가 발견 되 면 항상 선형 백오프 (CodePackage crash와 동일)를 사용 합니다. 즉, 활성화 작업은 (0 + 10 + 20 + 30 + 40) = 100 초 (첫 번째 다시 시도는 즉시)를 제공 합니다. 이 활성화는 다시 시도 되지 않습니다.
    
최대 활성화 백오프 **ActivationMaxRetryInterval** 하 고 **ActivationMaxFailureCount** 를 다시 시도할 수 있습니다.

### <a name="download-failure"></a>다운로드 실패
Service Fabric은 다운로드 중에 오류가 발생할 때 항상 선형 백오프를 사용 합니다. 즉, 활성화 작업은 (0 + 10 + 20 + 30 + 40) = 100 초 (첫 번째 다시 시도는 즉시)를 제공 합니다. 그러면 다운로드가 다시 시도 되지 않습니다. 다운로드를 위한 선형 백오프는 ContinuousFailureCount **_DeploymentRetryBackoffInterval_* 와 같으며 **DeploymentMaxRetryInterval** 에 대 한 최대 백오프를 수행할 수 있습니다. 활성화와 마찬가지로 다운로드 작업은 **ActivationMaxFailureCount** 를 다시 시도할 수 있습니다.

> [!NOTE]
> 이러한 설정을 변경 하기 전에 다음 몇 가지 예를 염두에 두어야 합니다.

* CodePackage가 계속 해 서 충돌을 발생 하 고 백업 하면 ServiceType이 사용 하지 않도록 설정 됩니다. 그러나 활성화 구성에서 빠른 다시 시작을 수행 하는 경우에는 ServiceType이 실제로 차단 되기 전에 CodePackage이 몇 번 발생할 수 있습니다. Ex: CodePackage이 발생 했다고 가정 하 고 Service Fabric에 ServiceType을 등록 한 다음 충돌 합니다. 이 경우 호스팅에서 형식 등록을 받으면 **ServiceTypeDisableGraceInterval** 기간이 취소 됩니다. 이는 CodePackage가  **ServiceTypeDisableGraceInterval** 보다 큰 값으로 백업 될 때까지 반복 될 수 있으며, ServiceType이 노드에 나열 됩니다. Maytake는 ServiceType 블록을 표시 하는 것 보다 오래 걸립니다.

* 활성화의 경우 Service Fabric 시스템에서 노드에 복제본을 두어야 하는 경우 RA (ReconfigurationAgent)는 호스트 하위 시스템에 응용 프로그램을 활성화 하 고 15 초 마다 활성화 요청을 다시 시도 하도록 요청 합니다 ( **RAPMessageRetryInterval** 구성 설정에 따라 결정 됨). ServiceType이 차단 된 것을 알 수 있도록 하기 위해 호스트의 정품 인증 작업은 재시도 간격 및 **ServiceTypeDisableGraceInterval** 보다 오랜 기간 동안 라이브 되어야 합니다. Service Fabric 예: 클러스터에 **ActivationMaxFailureCount** 가 5로 설정 되 고 **ActivationRetryBackoffInterval** 가 1 초로 설정 되어 있다고 가정 합니다. 즉, 활성화 작업은 (0 + 1 + 2 + 3 + 4) = 10 초 (첫 번째 다시 시도가 즉시 수행 됨 이 경우 정품 인증 작업이 완료 되 고 15 초 후에 다시 시도 되지 않습니다. 이는 Service Fabric 15 초 내에 허용 되는 모든 재시도를 모두 사용 하기 때문에 발생 합니다. 따라서 ReconfigurationAgent의 모든 재시도는 호스팅 하위 시스템에서 새 활성화 작업을 만들며 패턴은 계속 반복 됩니다. 결과적으로 ServiceType은 노드에 나열 되지 않습니다. ServiceType이 노드에 나열 되지 않으므로 복제본은 이동 하 고 다른 노드에서 시도 되지 않습니다.
> 

## <a name="deactivation"></a>비활성화

노드에서 활성화 된 ServicePackage는 비활성화를 위해 추적 됩니다. 

비활성화는 다음과 같은 두 가지 방법으로 작동 합니다.

- 정기적으로: 모든 **DeactivationScanInterval** 에서 복제본을 호스트 하지 않은 ServicePackages를 확인 하 고 비활성화를 위한 후보로 표시 합니다.
- ReplicaClose: 복제본이 닫히면 Deactivator는 DecrementUsageCount를 가져옵니다. 개수가 0이 되 면 ServicePackage는 복제본을 호스팅하지 않으므로 비활성화의 후보가 됩니다.

 활성화 모드를 [Exclusive/Shared][a2]로 설정 하는 경우 **DeactivationGraceInterval** 는 sharedmode를 사용 하 고 **ExclusiveModeDeactivationGraceInterval** 는 ExclusiveMode에 적용 한 후에 비활성화 됩니다. 이 시간 사이에 새 복제본 배치가 제공 되는 경우 비활성화가 취소 됩니다.

### <a name="periodically"></a>주기적으로
정기적 비활성화의 몇 가지 예를 살펴보겠습니다.

예제 1: Deactivator가 T (**DeactivationScanInterval**) 시간에 검색을 수행 한다고 가정해 보겠습니다. 다음 검색은 2T에 있습니다. T + 1에서 ServicePackage 활성화가 발생 했다고 가정 합니다. 이 ServicePackage는 복제본을 호스팅하지 않으므로 비활성화 해야 합니다. ServicePackage가 비활성화의 후보가 되려면 적어도 T 시간에 대 한 복제본 없음 상태 여야 합니다. 즉, 2T + 1에서 비활성화 하는 것이 가능 합니다. 따라서 2T에서 검색을 통해이 ServicePackage를 비활성화의 후보로 찾지 못합니다. 이제 다음 비활성화 주기 3T는 시간 T에 대 한 복제본 상태가 아니기 때문에이 ServicePackage 비활성화를 예약 합니다.  

예제 2: ServicePackage이 T-1 번에 활성화 되 고 Deactivator가 T에서 스캔을 수행 한다고 가정해 보겠습니다. ServicePackage는 복제본을 호스팅하지 않습니다. 그런 다음, 다음 검색 2 t에서이 ServicePackage는 비활성화를 위한 후보로 검색 되므로 비활성화를 위해 예약 됩니다.  

예제 3: ServicePackage이 T – 1에서 활성화 되 고 Deactivator가 T에서 스캔을 수행 한다고 가정해 보겠습니다. ServicePackage는 아직 복제본을 호스팅하지 않습니다. 이제 T + 1에서 복제본이 배치 됩니다. 즉, 호스트는 IncrementUsageCount을 가져옵니다. 즉, 복제본이 생성 됩니다. 이제 2T에서이 ServicePackage는 비활성화를 예약 하지 않습니다. 복제본이 포함 되어 있기 때문에 비활성화는 아래 설명 된 ReplicaClose 논리로 이동 합니다.

예제 4: ServicePackage는 10gb이 고 노드에서 다운로드 하는 데 약간의 시간이 걸릴 수 있다고 가정해 보겠습니다. 응용 프로그램이 활성화 되 면 Deactivator는 해당 수명 주기를 추적 합니다. 이제 **DeactivationScanInterval** 구성이 작은 값으로 설정 된 경우 모든 시간이 다운로드 되기 때문에 ServicePackage가 노드에서 활성화 하는 데 시간을 주지 않는 문제가 발생할 수 있습니다. 문제를 해결 하기 위해 [노드에 ServicePackage를 미리 다운로드][p1] 하거나 **DeactivationScanInterval** 를 늘릴 수 있습니다. 

> [!NOTE]
> ServicePackage는 (**DeactivationScanInterval** 에서 2 **_DeactivationScanInterval_*) + **DeactivationGraceInterval** /** ExclusiveModeDeactivationGraceInterval * * 사이에 있는 어디에서 나 비활성화할 수 있습니다. 
>

### <a name="replica-close"></a>복제본 닫기:
비활성화는 ServicePackage 보유 한 복제본 수를 유지 합니다. ServicePackage에서 복제본을 보유 하 고 있고 복제본이 닫혀 있거나 다운 된 경우 호스트는 DecrementUsageCount를 가져옵니다. 복제본이 열리면 호스트에서 IncrementUsageCount을 가져옵니다. 감소는 이제 ServicePackage가 한 개 이하의 복제본을 호스팅하고 있으며 개수가 0으로 떨어지면 ServicePackage가 비활성화 되도록 예약 되 고 비활성화 되는 시간은 **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval** 입니다. 

Ex:에서 감소를 수행 하 고 ServicePackage가 2t + X (**DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**)에서 비활성화 되도록 예약 된 경우를 가정해 보겠습니다. 이 시간 동안를 호스트 하는 동안 복제본이 IncrementUsage는 경우 비활성화가 취소 됩니다.

> [!NOTE]
> 이러한 구성 설정은 무엇을 의미 하나요?
**DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**: 복제본을 호스팅한 후 다른 복제본을 다시 호스팅하도록 ServicePackage에 지정 된 시간입니다. 
**DeactivationScanInterval**: 복제본을 호스트 하지 않은 경우 ServicePackage에 복제본을 호스트 하는 데 지정 된 최소 시간 (예: 사용 되지 않는 경우입니다.
>

### <a name="ctrlc"></a>Ctrl+C
ServicePackage가 **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval** 를 통과 하 고 복제본을 아직 호스팅하지 않는 경우 비활성화는 비 취소 가능한입니다. CodePackage는 Ctrl + C 처리기를 실행 합니다. 즉, 이제 비활성화 파이프라인이 프로세스를 종료 해야 합니다. 이 시간 동안 동일한 ServicePackage에 대 한 새 복제본이 배치 되려고 하면 비활성화에서 활성화로 전환할 수 없기 때문에 실패 합니다.

## <a name="cluster-configs"></a>클러스터 Configs

Configs는 활성화/decativation에 영향을 주는 기본값이 있습니다.

### <a name="servicetype"></a>ServiceType
**ServiceTypeDisableFailureThreshold**: 기본값은 1입니다. 해당 노드에서 서비스 유형을 사용 하지 않도록 설정 하 고 배치에 다른 노드를 사용 하 여 FM (FailoverManager)이 수신 되는 실패 횟수의 임계값입니다.
**ServiceTypeDisableGraceInterval**: 기본값은 30 초입니다. 서비스 유형을 사용 하지 않도록 설정할 수 있는 시간 간격입니다.
**ServiceTypeRegistrationTimeout**: 기본값은 300 초입니다. ServiceType이 Service Fabric에 등록 하기 위한 시간 제한입니다.

### <a name="activation"></a>활성화
**ActivationRetryBackoffInterval**: 기본값은 10 초입니다. 모든 활성화 실패 시 간격을 백오프 합니다.
**ActivationMaxFailureCount**: 기본값은 20입니다. 등록 하기 전에 시스템에서 다시 시도 하는 최대 횟수입니다. 
**ActivationRetryBackoffExponentiationBase**: 기본값은 1.5입니다.
**ActivationMaxRetryInterval**: 기본값은 3600 초입니다. 실패 시 활성화를 위한 최대 백오프
**CodePackageContinuousExitFailureResetInterval**: 기본값은 300 초입니다. CodePackage에 대 한 연속 종료 실패 횟수를 다시 설정 하는 시간 제한입니다.

### <a name="download"></a>다운로드
**DeploymentRetryBackoffInterval**: 기본값은 10입니다. 배포 실패에 대한 백오프 간격입니다.
**DeploymentMaxRetryInterval**: 기본값은 3600 초입니다. 실패 시 배포에 대 한 최대 백오프
**DeploymentMaxFailureCount**: 기본값은 20입니다. 노드에서 애플리케이션의 배포가 실패하기 전에 DeploymentMaxFailureCount 시간 동안 해당 애플리케이션의 배포가 다시 시도됩니다.

### <a name="deactivation"></a>비활성화
**DeactivationScanInterval**: 기본값은 600 초입니다. 복제본을 호스트 하지 않는 경우 복제본을 ServicePackage에 제공 되는 최소 시간입니다. 즉, 사용 되지 않는 경우입니다.
**DeactivationGraceInterval**: 기본값은 60 초입니다. **공유** 프로세스 모델의 경우 복제본을 호스트 한 후에 다른 복제본을 다시 호스트 하는 ServicePackage 지정 된 시간입니다.
**ExclusiveModeDeactivationGraceInterval**: 기본값은 1 초입니다. **단독** 프로세스 모델의 경우 복제본을 호스팅한 후 다른 복제본을 다시 호스트 하는 ServicePackage 지정 된 시간입니다.

## <a name="next-steps"></a>다음 단계
[애플리케이션을 패키지][a3]하고 배포를 준비합니다.

[애플리케이션을 배포하고 제거][a4]합니다. 이 아티클에서는 PowerShell을 사용하여 애플리케이션 인스턴스를 관리하는 방법을 설명합니다.

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-hosting-model.md
[a3]: service-fabric-package-apps.md
[a4]: service-fabric-deploy-remove-applications.md

[p1]: /powershell/module/servicefabric/copy-servicefabricservicepackagetonode
