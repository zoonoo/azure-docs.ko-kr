---
title: Azure Service Fabric 호스팅 활성화 및 비활성화 수명 주기
description: 노드의 응용 프로그램 및 ServicePackage 수명 주기에 대해 설명 합니다.
author: tugup
ms.topic: conceptual
ms.date: 05/1/2020
ms.author: tugup
ms.openlocfilehash: b106061805ea5485893df292c40974d3ee9bcadb
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86258828"
---
# <a name="azure-service-fabric-hosting-lifecycle"></a>Azure Service Fabric 호스팅 수명 주기
이 문서에서는 응용 프로그램이 노드에서 활성화 될 때 발생 하는 이벤트와 동작을 제어 하는 데 사용 되는 다양 한 클러스터 configs에 대 한 개요를 제공 합니다.

계속하기 전에 [Service Fabric에서 애플리케이션 모델링][a1]에서 설명한 다양한 개념 및 관계를 이해해야 합니다. 

> [!NOTE]
> 이 아티클에서는 다른 의미로 명시적으로 언급되지 않으면:
>
> - *복제본* 은 상태 저장 서비스의 복제본과 상태 비저장 서비스의 인스턴스를 모두 참조 합니다.
> - *CodePackage* 는 *servicetype*을 등록 하 고 해당 *servicetype*의 서비스 복제본을 호스트 하는 *ServiceHost* 프로세스와 동일한 것으로 간주 됩니다.
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
**ServiceTypeDisableFailureThreshold** 는 차단 된 블록 목록에 대해 ServiceType이 예약 된 실패 (활성화, 다운로드, CodePackage 실패) 수를 결정 합니다. 따라서 첫 번째 활성화/다운로드 실패 또는 CodePackage 크래시는 ServiceType 블록 목록의 일정을 트리거해야 합니다. **ServiceTypeDisableGraceInterval** Config는 ServiceType이 해당 노드에 나열 된 블록으로 마지막으로 표시 되는 유예 간격을 결정 합니다. 모든이를 수행 하기 위해 활성화/다운로드/CodePackage 다시 시작은 계속 해 서 다시 시도 모드에 있어야 하며, 호스트 하위 시스템에서 추적 됩니다. 다시 시도 하는 중입니다. 예를 들어 CodePackage가 충돌 후 다시 시작 되도록 예약 되거나, Service Fabric에서 패키지 다운로드를 다시 시도 합니다.
블록을 나열 하면 ' ServiceTypeRegistration: ServiceType ' 속성에 대해 "시스템 호스팅 ' 오류가 보고 됩니다. ServiceType이 노드에서 사용 하지 않도록 설정 되었습니다. "

ServiceType이 노드에서 다시 사용 하도록 설정 됩니다. 
- 활성화 작업이 성공 하거나 실패 한 경우 **ActivationMaxFailureCount** 재시도에 도달 하면입니다.
- 다운로드 작업이 성공 하거나 실패 한 경우 **DeploymentMaxFailureCount** 재시도에 도달 하면이 경고가 해결 됩니다.
- 크래시 된 CodePackage가 다시 시작 되 면 ServiceType이 성공적으로 등록 됩니다.

**ActivationMaxFailureCount**DeploymentMaxFailureCount 재시도 후에 ServiceType을 다시 사용 하도록 설정 하는 이유는 / **DeploymentMaxFailureCount** 노드에서 응용 프로그램을 활성화/다운로드 하기 위해 수행할 수 있는 최대 시도 Service Fabric입니다. 성공 하지 못하면 현재 작업을 다시 시도 하지 않으며, Service Fabric에서 다른 정품 인증을 수행할 수 있는 다른 기회를 제공 하려고 합니다 .이는 성공할 수 있으며, 문제가 자동으로 복구 되 면 정품 인증/다운로드 작업의 수명 주기와 연결 됩니다. 복제본의 배치로 트리거된 새 정품 인증/다운로드 작업은 ServiceType 차단 목록 작성를 다시 트리거하거나 성공할 수 있습니다.

> [!NOTE]
> ServiceType을 등록 하지 않은 CodePackage가 충돌 하는 경우 ServiceType에 영향을 주지 않습니다. 복제본 충돌을 호스트 하는 CodePackage ServiceType에 영향을 줍니다.
>

### <a name="codepackage-crash"></a>CodePackage 충돌
CodePackage 작동이 중단 되 면 Service Fabric 백오프를 사용 하 여 다시 시작 하 고, 백오프는 코드 패키지에서 형식에 등록 했는지 여부와는 독립적입니다.

백오프 값은 항상 Min (RetryTime, **ActivationMaxRetryInterval**)이 고이 값은 **ActivationRetryBackoffExponentiationBase** config를 기반으로 하는 상수, 선형 또는 지 수입니다.

- 상수: If **ActivationRetryBackoffExponentiationBase** = = 0 then RetryTime = **ActivationRetryBackoffInterval**;
- 선형: If **ActivationRetryBackoffExponentiationBase** = = 0 then RetryTime = ContinuousFailureCount * **ActivationRetryBackoffInterval** where ContinousFailureCount은 CodePackage 충돌 또는 활성화에 실패 한 횟수입니다.
- 지 수: RetryTime = (**ActivationRetryBackoffInterval** ) * (**ActivationRetryBackoffExponentiationBase** ^ ContinuousFailureCount);
    
신속 하 게 다시 시작 하는 등의 동작을 제어할 수 있습니다. 선형에 대해 알아보겠습니다. 즉, CodePackage가 충돌 하는 경우 시작 간격은 CodePackage가 비활성화 될 때까지 10, 20, 30 40 초 이후가 됩니다. 
    
**ActivationMaxRetryInterval** 가 장애를 적용 한 후에 Service Fabric에서 백업 (대기) 하는 최대 시간입니다.
    
CodePackage 작동이 중단 되 고 백업 되는 경우 Service Fabric **CodePackageContinuousExitFailureResetInterval** 에 대 한 최신 상태를 유지 하 여 정상으로 간주 해야 합니다 .이 시점에서 상태 보고서를 확인 하 고 ContinousFailureCount를 다시 설정 합니다.

### <a name="codepackage-not-registering-servicetype"></a>ServiceType을 등록 하지 CodePackage
CodePackage가 활성 상태를 유지 하 고 ServiceType을 us에 등록 해야 하지만이 경우에는 발생 하지 않습니다 .이 Service Fabric 경우에는 ServiceType이 제한 시간 내에 구성 되지 않았다는 것을 **ServiceTypeRegistrationTimeout** 후 경고 HealthReport 생성 됩니다.

### <a name="activation-failure"></a>활성화 실패
Service Fabric는 활성화 하는 동안 오류가 발견 되 면 항상 선형 백오프 (CodePackage crash와 동일)를 사용 합니다. 즉, 활성화 작업은 (0 + 10 + 20 + 30 + 40) = 100 초 (첫 번째 다시 시도는 즉시)를 제공 합니다. 이 활성화는 다시 시도 되지 않습니다.
    
최대 활성화 백오프 **ActivationMaxRetryInterval** 하 고 **ActivationMaxFailureCount**를 다시 시도할 수 있습니다.

### <a name="download-failure"></a>다운로드 실패
Service Fabric은 다운로드 중에 오류가 발생할 때 항상 선형 백오프를 사용 합니다. 즉, 활성화 작업은 (0 + 10 + 20 + 30 + 40) = 100 초 (첫 번째 다시 시도는 즉시)를 제공 합니다. 이 다운로드는 다시 시도 되지 않습니다. 다운로드를 위한 선형 백오프는 ContinuousFailureCount ***DeploymentRetryBackoffInterval** 와 같으며 **DeploymentMaxRetryInterval**에 대 한 최대 백오프를 수행할 수 있습니다. 활성화와 마찬가지로 다운로드 작업은 **ActivationMaxFailureCount**를 다시 시도할 수 있습니다.

> [!NOTE]
> Configs을 변경 하기 전에 다음 몇 가지 예를 염두에 두어야 합니다.

* CodePackage가 계속 해 서 충돌을 발생 하 고 백업 하면 ServiceType이 사용 하지 않도록 설정 됩니다. 그러나 정품 인증 구성에서 빠른 다시 시작을 수행 하는 경우에는 CodePackage가 ServiceType의 비활성화를 확인할 수 있을 때까지 몇 번만 나타날 수 있습니다. Ex: CodePackage이 발생 했다고 가정 하 고 Service Fabric에 ServiceType을 등록 한 다음 충돌 합니다. 이 경우 호스팅에서 형식 등록을 받으면 **ServiceTypeDisableGraceInterval** 기간이 취소 됩니다. 이는 CodePackage가 **ServiceTypeDisableGraceInterval** 보다 큰 값으로 백업 될 때까지 반복 될 수 있으며, ServiceType이 노드에서 사용 하지 않도록 설정 됩니다. 따라서 노드에서 ServiceType을 사용 하지 않도록 설정 하기 전에이 오류가 발생할 수 있습니다.

* 활성화의 경우 Service Fabric 시스템에서 노드에 복제본을 저장 해야 하는 경우 RA (ReconfigurationAgent)는 호스트 하위 시스템에 응용 프로그램을 활성화 하 고 15 초 (**RAPMessageRetryInterval**) 마다 활성화 요청을 다시 시도 하도록 요청 합니다. ServiceType이 사용 하지 않도록 설정 되었다는 Service Fabric 시스템의 경우, 호스트의 정품 인증 작업은 재시도 간격 및 **ServiceTypeDisableGraceInterval**보다 오랜 기간 동안 라이브 되어야 합니다. 예: 클러스터에 configs **ActivationMaxFailureCount** 가 5로 설정 되 고 **ActivationRetryBackoffInterval** 가 1 초로 설정 되어 있습니다. 즉, 활성화 작업은 (0 + 1 + 2 + 3 + 4) = 10 초 (첫 번째 다시 시도는 즉시 수행 됨)이 고 해당 호스팅을 다시 시도 하는 것을 의미 합니다. 이 경우 정품 인증 작업이 완료 되 고 15 초 후에 다시 시도 되지 않습니다. 이는 Service Fabric 15 초 내에 모든 재시도를 모두 사용 했기 때문에 발생 합니다. 따라서 ReconfigurationAgent의 모든 재시도는 호스팅 하위 시스템에서 새 활성화 작업을 만들지만 패턴은 계속 해 서 반복 되 고 ServiceType은 노드에서 사용 하지 않도록 설정 되지 않습니다. ServiceType은 노드 Sf 시스템의 구성 요소 FM (FailoverManager)에서 사용 하지 않도록 설정 되지 않으므로 복제본을 다른 노드로 이동 하지 않습니다.
> 

## <a name="deactivation"></a>비활성화

노드에서 활성화 된 ServicePackage는 비활성화를 위해 추적 됩니다. Deactivator는 추적을 유지 하는 엔터티입니다.
Deactivator는 다음과 같은 두 가지 방법으로 작동 합니다.

1.  정기적으로: 모든 **DeactivationScanInterval**에서 복제본을 호스트 하지 않은 ServicePackages를 확인 하 고 비활성화를 위한 후보로 표시 합니다.
2.  ReplicaClose: 복제본이 닫히면 Deactivator는 DecrementUsageCount를 가져옵니다. 개수가 0으로 이동 하는 경우 ServicePackage는 복제본을 호스팅하지 않으므로 비활성화의 후보가 됩니다.

 활성화 모드 [Exclusive/Shared][a2]를 기반으로 하는 **DeactivationGraceInterval** 는 Sharedmode/ **ExclusiveModeDeactivationGraceInterval** for ExclusiveMode에 대해 예약 됩니다. 이 시간 사이에 새 복제본 배치가 제공 되 면 비활성화가 취소 됩니다.

### <a name="periodically"></a>주기적으로
예제 1: Deactivator가 T (**DeactivationScanInterval**) 시간에 검색을 수행 한다고 가정해 보겠습니다. 다음 검색은 2T에 있습니다. T + 1에서 ServicePackage 활성화가 발생 했다고 가정 합니다. 이 ServicePackage는 복제본을 호스팅하지 않으므로 비활성화 해야 합니다. ServicePackage가 비활성화의 후보가 되려면 적어도 T 시간에 대 한 복제본 없음 상태 여야 합니다. 즉, 2T + 1에서 비활성화 하는 것이 가능 합니다. 따라서 2T에서 검색을 통해이 ServicePackage를 비활성화의 후보로 찾지 못합니다. 이제 다음 비활성화 주기 3T는 시간 T에 대 한 복제본 상태가 아니기 때문에이 ServicePackage 비활성화를 예약 합니다.  

예제 2: ServicePackage이 T-1 번에 활성화 되 고 Deactivator가 T에서 스캔을 수행 한다고 가정해 보겠습니다. ServicePackage는 복제본을 호스팅하지 않습니다. 그런 다음, 다음 검색 2 t에서이 ServicePackage는 비활성화를 위한 후보로 검색 되므로 비활성화를 위해 예약 됩니다.  

예제 3: ServicePackage이 T – 1에서 활성화 되 고 Deactivator가 T에서 스캔을 수행 한다고 가정해 보겠습니다. ServicePackage는 아직 복제본을 호스팅하지 않습니다. 이제 T + 1에서 복제본이 배치 됩니다. 즉, 호스트는 IncrementUsageCount을 가져옵니다. 즉, 복제본이 생성 됩니다. 이제 2T에서이 ServicePackage는 비활성화를 예약 하지 않습니다. 이제 비활성화는 아래 설명 된 ReplicaClose 논리로 이동 합니다.

예 4:10gb와 같이 ServicePackage 크면 노드에서 다운로드 하는 데 약간의 시간이 걸릴 수 있습니다. 응용 프로그램이 활성화 되 면 Deactivator는 해당 수명 주기를 추적 합니다. 이제 **DeactivationScanInterval** 구성이 작은 경우 모든 시간이 다운로드 되기 때문에 ServicePackage가 노드에서 활성화 하는 데 시간을 주지 않는 문제가 발생할 수 있습니다. 문제를 해결 하기 위해 [노드에 ServicePackage를 미리 다운로드할][p1]수 있습니다. 

> [!NOTE]
> 따라서 (**DeactivationScanInterval** 에서 2 ***DeactivationScanInterval**) + **DeactivationGraceInterval**ExclusiveModeDeactivationGraceInterval 사이의 모든 위치에서 ServicePackage를 비활성화할 수 있습니다 / **ExclusiveModeDeactivationGraceInterval**. 
>

### <a name="replica-close"></a>복제본 닫기:
Deactivator는 ServicePackage에서 보유 한 복제본 수를 유지 합니다. ServicePackage에서 복제본을 보유 하 고 있고 복제본이 닫혀 있거나 다운 된 경우 호스트는 DecrementUsageCount를 가져옵니다. 복제본이 열리면 호스트에서 IncrementUsageCount을 가져옵니다. 감소는 이제 ServicePackage가 한 개 이하의 복제본을 호스팅하고 있으며 개수가 0으로 떨어지면 ServicePackage가 비활성화 되도록 예약 되 고 비활성화 되는 시간은 **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**입니다. 

Ex:에서 감소를 수행 하 고 ServicePackage가 2t + X (**DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**)에서 비활성화 되도록 예약 된 경우를 가정해 보겠습니다. 이 시간 동안를 호스트 하는 동안 복제본이 IncrementUsage는 경우 비활성화가 취소 됩니다.

> [!NOTE]
>따라서 이러한 구성은 기본적으로 다음을 의미 합니다. **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**: 복제본을 호스팅한 후에 다른 복제본을 다시 호스트 하는 ServicePackage에 지정 된 시간입니다. 
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

### <a name="activation"></a>정품 인증
**ActivationRetryBackoffInterval**: 모든 정품 인증 오류에 대 한 백오프 간격의 기본값은 10 초입니다.
**ActivationMaxFailureCount**: 기본값은 20입니다. 등록 하기 전에 시스템에서 다시 시도 하는 최대 횟수입니다. 
**ActivationRetryBackoffExponentiationBase**: 기본값은 1.5입니다.
**ActivationMaxRetryInterval**: 기본값은 3600 초입니다. 실패 시 활성화를 위해 최대 백오프 합니다.
**CodePackageContinuousExitFailureResetInterval**: 기본값은 300 초입니다. CodePackage에 대 한 연속 종료 실패 횟수를 다시 설정 하는 시간 제한입니다.

### <a name="download"></a>다운로드
**DeploymentRetryBackoffInterval**: 기본값은 10입니다. 배포 실패에 대한 백오프 간격입니다.
**DeploymentMaxRetryInterval**: 기본 3600 초. 실패 시 배포에 대 한 최대 백오프입니다.
**DeploymentMaxFailureCount**: 기본값은 20입니다. 노드에서 애플리케이션의 배포가 실패하기 전에 DeploymentMaxFailureCount 시간 동안 해당 애플리케이션의 배포가 다시 시도됩니다.

### <a name="deactivation"></a>비활성화
**DeactivationScanInterval**: 기본값은 600 초입니다. 복제본을 호스트 하지 않은 경우 복제본을 호스트 하는 ServicePackage에 지정 된 최소 시간입니다. 사용 되지 않는 경우입니다.
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
