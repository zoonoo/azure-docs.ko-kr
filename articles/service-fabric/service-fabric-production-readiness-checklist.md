---
title: Azure Service Fabric 프로덕션 준비 검사 목록 | Microsoft Docs
description: 모범 사례에 따라 Service Fabric 응용 프로그램 및 클러스터 프로덕션을 준비합니다.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 7/10/2018
ms.author: aljo-microsoft
ms.openlocfilehash: 4e6d5cb3191be7188c1a7c4753200cf049800f04
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/15/2018
ms.locfileid: "53436010"
---
# <a name="production-readiness-checklist"></a>프로덕션 준비 검사 목록

응용 프로그램 및 클러스터가 프로덕션 트래픽을 허용할 준비가 되었나요? 응용 프로그램 및 클러스터를 실행하고 테스트한다고 해서 프로덕션으로 이동할 준비가 된 것은 아닙니다. 다음 검사 목록을 진행하여 응용 프로그램 및 클러스터가 원활하게 실행되도록 유지합니다. 이러한 항목을 모두 검사하는 것이 좋습니다. 특정 라인 항목(예: 고유한 진단 프레임워크)의 경우, 대체 솔루션을 사용하도록 선택할 수 있습니다.


## <a name="pre-requisites-for-production"></a>프로덕션을 위한 필수 조건
1. [Azure Service Fabric 모범 사례](https://docs.microsoft.com/azure/security/azure-service-fabric-security-best-practices)는 다음과 같습니다. 
* X.509 인증서 사용
* 보안 정책 구성
* Azure Service Fabric에 대해 SSL 구성
* Azure Service Fabric을 통한 네트워크 격리 및 보안 사용
* 보안을 위해 Azure Key Vault 설정
* 역할에 사용자 할당
* Actors 프로그래밍 모델을 사용하는 경우 Reliable Actors 보안 구성 구현
2. 코어 20개 또는 노드 10개를 초과하는 클러스터의 경우, 시스템 서비스를 위한 전용 기본 노드 유형을 만듭니다. [배치 제약 조건](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)을 추가하여 기본 노드 유형을 시스템 서비스에 예약합니다. 
3. 기본 노드 유형에 대해 D2v2 이상 SKU를 사용합니다. 하드 디스크 용량이 50GB 이상인 SKU를 선택하는 것이 좋습니다.
4. 프로덕션 클러스터는 [안전](service-fabric-cluster-security.md)해야 합니다. 보안 클러스터 설정 예제는 이 [클러스터 템플릿](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/7-VM-Windows-3-NodeTypes-Secure-NSG)을 참조하세요. 인증서에 일반 이름을 사용하고, 자체 서명된 인증서를 사용하지 않습니다.
5. 노드 리소스의 75% 이상을 사용하지 않도록 [컨테이너 및 서비스에 대한 리소스 제약 조건](service-fabric-resource-governance.md)을 추가합니다. 
6. [내구성 수준](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster)을 이해하고 설정합니다. 상태 저장 워크로드를 실행하는 노드 유형에는 실버 이상의 내구성 수준을 사용하는 것이 좋습니다. 기본 노드 유형은 내구성 수준이 실버 이상으로 설정되어야 합니다.
7. 노드 유형의 [안정성 수준](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster)을 이해하고 선택합니다. 실버 이상의 안정성을 사용하는 것이 좋습니다.
8. 워크로드를 로드하고 규모 테스트를 수행하여 클러스터의 [용량 요구 사항](service-fabric-cluster-capacity.md)을 파악합니다. 
9. 서비스 및 응용 프로그램이 모니터링되고, 응용 프로그램 로그가 생성되어 경고와 함께 저장됩니다. 예를 들어, [Service Fabric 응용 프로그램에 로깅 추가](service-fabric-how-to-diagnostics-log.md) 및 [Log Analytics를 사용하여 컨테이너 모니터링](service-fabric-diagnostics-oms-containers.md)을 참조하세요.
10. 클러스터는 경고(예: [Log Analytics](service-fabric-diagnostics-event-analysis-oms.md))를 사용하여 모니터링됩니다. 
11. 기본 가상 머신 확장 집합 인프라는 경고(예: [Log Analytics](service-fabric-diagnostics-oms-agent.md))를 사용하여 모니터링됩니다.
12. 클러스터에는 항상 [기본 및 보조 인증서](service-fabric-cluster-security-update-certs-azure.md)가 있습니다(잠기지 않도록 방지).
13. 개발, 스테이징 및 프로덕션을 위해 별도의 클러스터를 유지 관리합니다. 
14. [응용 프로그램 업그레이드](service-fabric-application-upgrade.md) 및 [클러스터 업그레이드](service-fabric-tutorial-upgrade-cluster.md)는 먼저 개발 및 스테이징 클러스터에서 테스트됩니다. 
15. 프로덕션 클러스터에서 자동 업그레이드를 끄고, 개발 및 스테이징 클러스터에서 켭니다(필요한 경우 롤백). 
16. 서비스에 대해 RPO(복구 지점 목표)를 설정하고, [재해 복구 프로세스](service-fabric-disaster-recovery.md)를 설정한 후 테스트합니다.
17. 수동 또는 프로그래밍 방식의 클러스터 [크기 조정](service-fabric-cluster-scaling.md)을 계획합니다.
18. 클러스터 노드 [패치 적용](service-fabric-patch-orchestration-application.md)을 계획합니다. 
19. 최신 변경 내용이 지속적으로 테스트되도록 CI/CD 파이프라인을 설정합니다. 예를 들어 [Azure DevOps](service-fabric-tutorial-deploy-app-with-cicd-vsts.md) 또는 [Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md)를 사용합니다.
20. [Fault Analysis Service](service-fabric-testability-overview.md)를 사용하여 부하 상태에서 개발 및 스테이징 클러스터를 테스트하고, 제어된 [혼란](service-fabric-controlled-chaos.md)을 유도합니다. 
21. 응용 프로그램 [크기 조정](service-fabric-concepts-scalability.md)을 계획합니다. 


Service Fabric Reliable Services 또는 Reliable Actors 프로그래밍 모델을 사용하는 경우, 다음 항목을 검사해야 합니다.
22. 로컬 개발 중에 응용 프로그램을 업그레이드하여 서비스 코드가 `RunAsync` 메서드의 취소 토큰을 인식하고 사용자 지정 통신 수신기를 닫는지 확인합니다.
23. 신뢰할 수 있는 컬렉션을 사용하는 경우, [일반적인 문제](service-fabric-work-with-reliable-collections.md)를 방지합니다.
24. 부하 테스트를 실행할 때 .NET CLR 메모리 성능 카운터를 모니터링하고, 가비지 수집 또는 런어웨이 힙 증가 비율이 높은지 확인합니다.
25. [Reliable Services 및 Reliable Actors](service-fabric-reliable-services-backup-restore.md)의 오프라인 백업을 유지 관리하고 복원 프로세스를 테스트합니다. 


## <a name="optional-best-practices"></a>선택적 모범 사례

위의 목록이 프로덕션으로 이동하기 위한 필수 조건이지만, 다음 항목도 고려해야 합니다.
26. 기본 제공 상태 평가 및 보고를 확장하기 위해 [Service Fabric 상태 모델](service-fabric-health-introduction.md)에 연결합니다.
27. 응용 프로그램을 모니터링하고, [리소스 부하 분산](service-fabric-cluster-resource-manager-balancing.md)을 위해 [부하](service-fabric-cluster-resource-manager-metrics.md)를 보고하는 사용자 지정 Watchdog를 배포합니다. 


## <a name="next-steps"></a>다음 단계
* [Service Fabric Windows 클러스터 배포](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Service Fabric Linux 클러스터 배포](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Service Fabric [응용 프로그램 수명 주기](service-fabric-application-lifecycle.md)에 대해 자세히 알아봅니다.
