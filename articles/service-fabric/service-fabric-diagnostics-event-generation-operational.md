---
title: Azure Service Fabric 이벤트 목록
description: 클러스터를 모니터링할 수 있도록 Azure Service Fabric에서 제공하는 이벤트의 포괄적인 목록입니다.
author: srrengar
ms.topic: reference
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: e69b407bc7d58a83616daa44272ec008ccff9fad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85846653"
---
# <a name="list-of-service-fabric-events"></a>Service Fabric 이벤트 목록 

Service Fabric은 [Service Fabric 이벤트](service-fabric-diagnostics-events.md)로 클러스터의 상태를 알리도록 클러스터 이벤트의 기본 집합을 노출합니다. 노드 및 클러스터의 Service Fabric에서 수행하는 작업 또는 클러스터 소유자/연산자에서 만드는 관리 결정을 기반으로 합니다. 이러한 이벤트는 [클러스터를 사용 하 여 Azure Monitor 로그](service-fabric-diagnostics-oms-setup.md)를 구성 하거나 [eventstore](service-fabric-diagnostics-eventstore.md)를 쿼리 하는 등 여러 가지 방법으로를 구성 하 여 액세스할 수 있습니다. Windows 머신에서 이러한 이벤트는 EventLog에 반영되므로 이벤트 뷰어에서 Service Fabric 이벤트를 볼 수 있습니다. 

이러한 이벤트에 대한 몇 가지 특징은 다음과 같습니다.
* 각 이벤트는 클러스터의 특정 엔터티(예: 애플리케이션, 서비스, 노드, 복제본)에 연결됩니다.
* 각 이벤트에는 EventInstanceId, EventName 및 Category의 공통 필드 집합이 포함 되어 있습니다.
* 각 이벤트는 연결된 엔터티로 이벤트를 다시 연결하는 필드를 포함합니다. 예를 들어 ApplicationCreated 이벤트에는 생성된 애플리케이션의 이름을 식별하는 필드가 있습니다.
* 이벤트는 추가 분석을 수행하는 다양한 도구에서 사용될 수 있는 방식으로 구성됩니다. 또한 이벤트에 대한 관련 세부 정보는 긴 문자열 대신 개별 속성으로 정의됩니다. 
* 이벤트는 아래 원본(태스크)로 식별되는 Service Fabric의 다른 하위 시스템으로 작성됩니다. [Service Fabric 아키텍처](service-fabric-architecture.md) 및 [Service Fabric 기술 개요](service-fabric-technical-overview.md)에서 이러한 하위 시스템에 대한 자세한 내용을 확인할 수 있습니다.

다음은 엔터티별로 구성된 Service Fabric 이벤트의 목록입니다.

## <a name="cluster-events"></a>클러스터 이벤트

**클러스터 업그레이드 이벤트**

클러스터 업그레이드에 대한 자세한 내용은 [여기](service-fabric-cluster-upgrade-windows-server.md)를 참조하세요.

| EventId | 속성 | Category | 설명 |원본(태스크) | Level | 
| --- | --- | --- | --- | --- | --- | 
| 29627 | ClusterUpgradeStarted | 업그레이드 | 클러스터 업그레이드가 시작되었습니다. | CM | 정보 제공 |
| 29628 | ClusterUpgradeCompleted | 업그레이드 | 클러스터 업그레이드가 완료되었습니다. | CM | 정보 제공 | 
| 29629 | ClusterUpgradeRollbackStarted | 업그레이드 | 클러스터 업그레이드 롤백이 시작되었습니다.  | CM | Warning | 
| 29630 | ClusterUpgradeRollbackCompleted | 업그레이드 | 클러스터 업그레이드 롤백이 완료되었습니다. | CM | Warning | 
| 29631 | ClusterUpgradeDomainCompleted | 업그레이드 | 업그레이드 도메인이 클러스터 업그레이드 중 업그레이드를 완료했습니다. | CM | 정보 제공 | 

## <a name="node-events"></a>노드 이벤트

**노드 수명 주기 이벤트** 

| EventId | 속성 | Category | 설명 |원본(태스크) | Level |
| --- | --- | ---| --- | --- | --- | 
| 18602 | NodeDeactivateCompleted | StateTransition | 노드 비활성화가 완료되었습니다. | FM | 정보 제공 | 
| 18603 | NodeUp | StateTransition | 클러스터에서 노드가 시작되었음을 감지했습니다. | FM | 정보 제공 | 
| 18604 | NodeDown | StateTransition | 클러스터에서 노드가 종료되었음을 감지했습니다. 노드를 다시 시작하는 동안 NodeDown 이벤트 다음으로 NodeUp 이벤트가 표시됩니다 |  FM | Error | 
| 18605 | NodeAddedToCluster | StateTransition |  새 노드가 클러스터에 추가되었으며 Service Fabric에서 이 노드에 애플리케이션을 배포할 수 있습니다. | FM | 정보 제공 | 
| 18606 | NodeRemovedFromCluster | StateTransition |  노드가 클러스터에서 제거되었습니다. Service Fabric에서 이 노드에 애플리케이션을 더 이상 배포하지 않습니다. | FM | 정보 제공 | 
| 18607 | NodeDeactivateStarted | StateTransition |  노드 비활성화가 시작되었습니다. | FM | 정보 제공 | 
| 25621 | NodeOpenSucceeded | StateTransition |  노드가 성공적으로 시작되었습니다. | FabricNode | 정보 제공 | 
| 25622 | NodeOpenFailed | StateTransition |  노드가 시작되지 못하고 링에 조인하지 못했습니다. | FabricNode | Error | 
| 25624 | NodeClosed | StateTransition |  노드가 성공적으로 종료되었습니다. | FabricNode | 정보 제공 | 
| 25626 | NodeAborted | StateTransition |  노드가 비정상적으로 종료되었습니다. | FabricNode | Error | 

## <a name="application-events"></a>애플리케이션 이벤트

**애플리케이션 수명 주기 이벤트**

| EventId | 속성 | Category | 설명 |원본(태스크) | Level | 
| --- | --- | --- | --- | --- | --- | 
| 29620 | ApplicationCreated | LifeCycle | 새 애플리케이션이 만들어졌습니다. | CM | 정보 제공 | 
| 29625 | ApplicationDeleted | LifeCycle | 기존 애플리케이션이 삭제되었습니다. | CM | 정보 제공 | 
| 23083 | ApplicationProcessExited | LifeCycle | 애플리케이션 내 프로세스가 종료되었습니다. | Hosting | 정보 제공 | 

**애플리케이션 업그레이드 이벤트**

애플리케이션 업그레이드에 대한 자세한 내용은 [여기](service-fabric-application-upgrade.md)를 참조하세요.

| EventId | 속성 | Category | 설명 |원본(태스크) | Level | 
| --- | --- | ---| --- | --- | --- | 
| 29621 | ApplicationUpgradeStarted | 업그레이드 | 애플리케이션 업그레이드가 시작되었습니다. | CM | 정보 제공 | 
| 29622 | ApplicationUpgradeCompleted | 업그레이드 | 애플리케이션 업그레이드가 완료되었습니다. | CM | 정보 제공 | 
| 29623 | ApplicationUpgradeRollbackStarted | 업그레이드 | 애플리케이션 업그레이드 롤백이 시작되었습니다. |CM | Warning | 
| 29624 | ApplicationUpgradeRollbackCompleted | 업그레이드 | 애플리케이션 롤백이 완료되었습니다. | CM | Warning | 
| 29626 | ApplicationUpgradeDomainCompleted | 업그레이드 | 업그레이드 도메인이 애플리케이션 업그레이드 중 업그레이드를 완료했습니다. | CM | 정보 제공 | 

## <a name="service-events"></a>서비스 이벤트

**서비스 수명 주기 이벤트**

| EventId | 속성 | Category | 설명 |원본(태스크) | Level | 
| --- | --- | ---| --- | --- | --- |
| 18657 | ServiceCreated | LifeCycle | 새 서비스가 만들어졌습니다. | FM | 정보 제공 | 
| 18658 | ServiceDeleted | LifeCycle | 기존 서비스가 삭제되었습니다. | FM | 정보 제공 | 

## <a name="partition-events"></a>파티션 이벤트

**파티션 이동 이벤트**

| EventId | 속성 | Category | 설명 |원본(태스크) | Level | 
| --- | --- | ---| --- | --- | --- |
| 18940 | PartitionReconfigured | LifeCycle | 파티션 재구성이 완료되었습니다. | RA | 정보 제공 | 

## <a name="replica-events"></a>복제본 이벤트

**복제본 수명 주기 이벤트**

| EventId | 속성 | Category | 설명 |원본(태스크) | Level |
| --- | --- | ---| --- | --- | --- |
| 61701 | ReliableDictionaryOpened | LifeCycle | 신뢰할 수 있는 사전이 열렸습니다. | DistributedDictionary | 정보 제공 |
| 61702 | ReliableDictionaryClosed | LifeCycle | 신뢰할 수 있는 사전이 닫혔습니다. | DistributedDictionary | 정보 제공 |
| 61703 | ReliableDictionaryCheckpointRecovered | LifeCycle | 신뢰할 수 있는 사전이 해당 검사점을 복구 했습니다. | DistributedDictionary | 정보 제공 |
| 61704 | ReliableDictionaryCheckpointFilesSent | LifeCycle | 복제본이 신뢰할 수 있는 사전의 검사점 파일을 보냈습니다. | DistributedDictionary | 정보 제공 |
| 61705 | ReliableDictionaryCheckpointFilesReceived | LifeCycle | 복제본이 신뢰할 수 있는 사전의 검사점 파일을 받았습니다. | DistributedDictionary | 정보 제공 |
| 61963 | ReliableQueueOpened | LifeCycle | 신뢰할 수 있는 큐가 열렸습니다. | DistributedQueue | 정보 제공 |
| 61964 | ReliableQueueClosed | LifeCycle | 신뢰할 수 있는 큐가 닫혔습니다. | DistributedQueue | 정보 제공 |
| 61965 | ReliableQueueCheckpointRecovered | LifeCycle | 신뢰할 수 있는 큐가 해당 검사점을 복구 했습니다. | DistributedQueue | 정보 제공 |
| 61966 | ReliableQueueCheckpointFilesSent | LifeCycle | 복제본이 신뢰할 수 있는 큐의 검사점 파일을 보냈습니다. | DistributedQueue | 정보 제공 |
| 63647 | ReliableQueueCheckpointFilesReceived | LifeCycle | 복제본이 신뢰할 수 있는 큐의 검사점 파일을 받았습니다. | DistributedQueue | 정보 제공 |
| 63648 | ReliableConcurrentQueueOpened | LifeCycle | 신뢰할 수 있는 동시 큐가 열렸습니다. | ReliableConcurrentQueue | 정보 제공 |
| 63649 | ReliableConcurrentQueueClosed | LifeCycle | 신뢰할 수 있는 동시 큐가 닫혔습니다. | ReliableConcurrentQueue | 정보 제공 |
| 63650 | ReliableConcurrentQueueCheckpointRecovered | LifeCycle | 신뢰할 수 있는 동시 큐가 해당 검사점을 복구 했습니다. | ReliableConcurrentQueue | 정보 제공 |
| 61687 | TStoreError | 실패 | 신뢰할 수 있는 컬렉션에서 예기치 않은 오류를 수신 했습니다. | TStore | Error |
| 63831 | PrimaryFullCopyInitiated 됨 | LifeCycle | 주 복제본에서 전체 복사를 시작 했습니다. | TReplicator | 정보 제공 |
| 63832 | PrimaryPartialCopyInitiated | LifeCycle | 주 복제본이 부분 복사를 시작 했습니다. | TReplicator | 정보 제공 |
| 16831 | BuildIdleReplicaStarted | LifeCycle | 주 복제본이 유휴 복제본 빌드를 시작 했습니다. | 복제 | 정보 제공 |
| 16832 | BuildIdleReplicaCompleted | LifeCycle | 주 복제본이 유휴 복제본 빌드를 완료 했습니다. | 복제 | 정보 제공 |
| 16833 | BuildIdleReplicaFailed | LifeCycle | 주 복제본에서 유휴 복제본을 빌드하지 못했습니다. | 복제 | Warning |
| 16834 | PrimaryReplicationQueueFull | 의료 | 주 복제본의 복제 큐가 꽉 찼습니다. | 복제 | Warning |
| 16835 | PrimaryReplicationQueueWarning | 의료 | 주 복제본의 복제 큐가 거의 꽉 찼습니다. | 복제 | Warning |
| 16836 | PrimaryReplicationQueueWarningMitigated | 의료 | 주 복제본의 복제 큐가 양호 합니다. | 복제 | 정보 제공 |
| 16837 | SecondaryReplicationQueueFull | 의료 | 보조 복제본의 복제 큐가 꽉 찼습니다. | 복제 | Warning |
| 16838 | SecondaryReplicationQueueWarning | 의료 | 보조 복제본의 복제 큐가 거의 꽉 찼습니다. | 복제 | Warning |
| 16839 | SecondaryReplicationQueueWarningMitigated | 의료 | 보조 복제본의 복제 큐가 양호 합니다. | 복제 | 정보 제공 |
| 16840 | PrimaryFaultedSlowSecondary | 의료 | 주 복제본에서 저속 보조 복제본에 오류가 발생 했습니다. | 복제 | Warning |
| 16841 | ReplicatorFaulted | 의료 | 복제본에 오류가 발생 했습니다. | 복제 | Warning |

## <a name="container-events"></a>컨테이너 이벤트

**컨테이너 수명 주기 이벤트** 

| EventId | 속성 | 설명 |원본(태스크) | Level | 버전 |
| --- | --- | ---| --- | --- | --- |
| 23074 | ContainerActivated | 컨테이너가 시작되었습니다. | Hosting | 정보 제공 | 1 |
| 23075 | ContainerDeactivated | 컨테이너가 중지되었습니다. | Hosting | 정보 제공 | 1 |
| 23082 | ContainerExited | 컨테이너가 종료되었습니다. UnexpectedTermination 플래그를 확인하세요. | Hosting | 정보 제공 | 1 |

## <a name="health-reports"></a>상태 보고서

[Service Fabric 상태 모델](service-fabric-health-introduction.md)은 다양하고 유연하며 확장 가능한 상태 평가 및 보고 기능을 제공합니다. Service Fabric 버전 6.2부터, 상태 데이터는 과거의 상태 기록을 제공하기 위한 플랫폼 이벤트로 기록됩니다. 상태 이벤트의 볼륨을 낮게 유지하기 위해 다음 항목만 Service Fabric 이벤트로 작성됩니다.

* 모든 `Error` 또는 `Warning` 상태 보고서
* 전환 중 `Ok` 상태 보고서
* `Error` 또는 `Warning` 상태 이벤트가 만료되는 경우. 엔터티가 비정상이었던 기간을 확인하는 데 사용됩니다.

**클러스터 상태 보고서 이벤트**

| EventId | 속성 | 설명 |원본(태스크) | Level | 버전 |
| --- | --- | --- | --- | --- | --- |
| 54428 | ClusterNewHealthReport | 새 클러스터 상태 보고서를 사용할 수 있습니다. | HM | 정보 제공 | 1 |
| 54437 | ClusterHealthReportExpired | 기존 클러스터 상태 보고서가 만료되었습니다. | HM | 정보 제공 | 1 |

**노드 상태 보고서 이벤트**

| EventId | 속성 | 설명 |원본(태스크) | Level | 버전 |
| --- | --- | ---| --- | --- | --- |
| 54423 | NodeNewHealthReport | 새 노드 상태 보고서를 사용할 수 있습니다. | HM | 정보 제공 | 1 |
| 54432 | NodeHealthReportExpired | 기존 노드 상태 보고서가 만료되었습니다. | HM | 정보 제공 | 1 |

**애플리케이션 상태 보고서 이벤트**

| EventId | 속성 | 설명 |원본(태스크) | Level | 버전 |
| --- | --- | ---| --- | --- | --- |
| 54425 | ApplicationNewHealthReport | 새 애플리케이션 상태 보고서가 만들어졌습니다. 배포되지 않은 애플리케이션에 대한 보고서입니다. | HM | 정보 제공 | 1 |
| 54426 | DeployedApplicationNewHealthReport | 새로 배포된 애플리케이션 상태 보고서가 만들어졌습니다. | HM | 정보 제공 | 1 |
| 54427 | DeployedServicePackageNewHealthReport | 새로 배포된 서비스 상태 보고서가 만들어졌습니다. | HM | 정보 제공 | 1 |
| 54434 | ApplicationHealthReportExpired | 기존 애플리케이션 상태 보고서가 만료되었습니다. | HM | 정보 제공 | 1 |
| 54435 | DeployedApplicationHealthReportExpired | 기존 배포된 애플리케이션 상태 보고서가 만료되었습니다. | HM | 정보 제공 | 1 |
| 54436 | DeployedServicePackageHealthReportExpired | 기존 배포된 서비스 상태 보고서가 만료되었습니다. | HM | 정보 제공 | 1 |

**서비스 상태 보고서 이벤트**

| EventId | 속성 | 설명 |원본(태스크) | Level | 버전 |
| --- | --- | ---| --- | --- | --- |
| 54424 | ServiceNewHealthReport | 새 서비스 상태 보고서가 만들어졌습니다. | HM | 정보 제공 | 1 |
| 54433 | ServiceHealthReportExpired | 기존 서비스 상태 보고서가 만료되었습니다. | HM | 정보 제공 | 1 |

**파티션 상태 보고서 이벤트**

| EventId | 속성 | 설명 |원본(태스크) | Level | 버전 |
| --- | --- | ---| --- | --- | --- |
| 54422 | PartitionNewHealthReport | 새 파티션 상태 보고서가 만들어졌습니다. | HM | 정보 제공 | 1 |
| 54431 | PartitionHealthReportExpired | 기존 파티션 상태 보고서가 만료되었습니다. | HM | 정보 제공 | 1 |

**복제본 상태 보고서 이벤트**

| EventId | 속성 | 설명 |원본(태스크) | Level | 버전 |
| --- | --- | ---| --- | --- | --- |
| 54429 | StatefulReplicaNewHealthReport | 상태 저장 복제본 상태 보고서가 만들어졌습니다. | HM | 정보 제공 | 1 |
| 54430 | StatelessInstanceNewHealthReport | 새 상태 비저장 인스턴스 상태 보고서가 만들어졌습니다. | HM | 정보 제공 | 1 |
| 54438 | StatefulReplicaHealthReportExpired | 기존 상태 저장 복제본 상태 보고서가 만료되었습니다. | HM | 정보 제공 | 1 |
| 54439 | StatelessInstanceHealthReportExpired | 기존 상태 비저장 인스턴스 상태 보고서가 만료되었습니다. | HM | 정보 제공 | 1 |

## <a name="chaos-testing-events"></a>카오스 테스트 이벤트 

**카오스 세션 이벤트**

| EventId | 속성 | 설명 |원본(태스크) | Level | 버전 |
| --- | --- | ---| --- | --- | --- |
| 50021 | ChaosStarted | 카오스 테스트 세션이 시작되었습니다. | 테스트 가능성 | 정보 제공 | 1 |
| 50023 | ChaosStopped | 카오스 테스트 세션이 중지되었습니다. | 테스트 가능성 | 정보 제공 | 1 |

**카오스 노드 이벤트**

| EventId | 속성 | 설명 |원본(태스크) | Level | 버전 |
| --- | --- | ---| --- | --- | --- |
| 50033 | ChaosNodeRestartScheduled | 노드가 카오스 테스트 세션의 일부로 다시 시작하도록 예약되었습니다. | 테스트 가능성 | 정보 제공 | 1 |
| 50087 | ChaosNodeRestartCompleted | 노드가 카오스 테스트 세션의 일부로 다시 시작되었습니다. | 테스트 가능성 | 정보 제공 | 1 |

**카오스 애플리케이션 이벤트**

| EventId | 속성 | 설명 |원본(태스크) | Level | 버전 |
| --- | --- | ---| --- | --- | --- |
| 50053 | ChaosCodePackageRestartScheduled | 코드 패키지 다시 시작이 카오스 테스트 세션 중에 예약되었습니다. | 테스트 가능성 | 정보 제공 | 1 |
| 50101 | ChaosCodePackageRestartCompleted | 코드 패키지 다시 시작이 카오스 테스트 세션 중에 완료되었습니다. | 테스트 가능성 | 정보 제공 | 1 |

**카오스 파티션 이벤트**

| EventId | 속성 | 설명 |원본(태스크) | Level | 버전 |
| --- | --- | ---| --- | --- | --- |
| 50069 | ChaosPartitionPrimaryMoveScheduled | 주 파티션이 카오스 테스트 세션의 일부로 이동하도록 예약되었습니다. | 테스트 가능성 | 정보 제공 | 1 |
| 50077 | ChaosPartitionSecondaryMoveScheduled | 보조 파티션이 카오스 테스트 세션의 일부로 이동하도록 예약되었습니다. | 테스트 가능성 | 정보 제공 | 1 |
| 65003 | PartitionPrimaryMoveAnalysis | 주 파티션 이동에 대한 심층 분석을 사용할 수 있습니다. | 테스트 가능성 | 정보 제공 | 1 |

**카오스 복제본 이벤트**

| EventId | 속성 | 설명 |원본(태스크) | Level | 버전 |
| --- | --- | ---| --- | --- | --- |
| 50047 | ChaosReplicaRestartScheduled | 복제본 다시 시작이 카오스 테스트 세션의 일부로 예약되었습니다. | 테스트 가능성 | 정보 제공 | 1 |
| 50051 | ChaosReplicaRemovalScheduled | 복제본 제거가 카오스 테스트 세션의 일부로 예약되었습니다. | 테스트 가능성 | 정보 제공 | 1 |
| 50093 | ChaosReplicaRemovalCompleted | 복제본 제거가 카오스 테스트 세션의 일부로 완료되었습니다. | 테스트 가능성 | 정보 제공 | 1 |

## <a name="other-events"></a>기타 이벤트

**상관 관계 이벤트**

| EventId | 속성 | 설명 |원본(태스크) | Level | 버전 |
| --- | --- | ---| --- | --- | --- |
| 65011 | CorrelationOperational | 상관 관계가 검색되었습니다. | 테스트 가능성 | 정보 제공 | 1 |

## <a name="events-prior-to-version-62"></a>버전 6.2 이전 이벤트

다음은 버전 6.2 이전의 Service Fabric이 제공하는 이벤트의 포괄적인 목록입니다.

| EventId | 속성 | 원본(태스크) | Level |
| --- | --- | --- | --- |
| 25620 | NodeOpening | FabricNode | 정보 제공 |
| 25621 | NodeOpenedSuccess | FabricNode | 정보 제공 |
| 25622 | NodeOpenedFailed | FabricNode | 정보 제공 |
| 25623 | NodeClosing | FabricNode | 정보 제공 |
| 25624 | NodeClosed | FabricNode | 정보 제공 |
| 25625 | NodeAborting | FabricNode | 정보 제공 |
| 25626 | NodeAborted | FabricNode | 정보 제공 |
| 29627 | ClusterUpgradeStart | CM | 정보 제공 |
| 29628 | ClusterUpgradeComplete | CM | 정보 제공 |
| 29629 | ClusterUpgradeRollback | CM | 정보 제공 |
| 29630 | ClusterUpgradeRollbackComplete | CM | 정보 제공 |
| 29631 | ClusterUpgradeDomainComplete | CM | 정보 제공 |
| 23074 | ContainerActivated | Hosting | 정보 제공 |
| 23075 | ContainerDeactivated | Hosting | 정보 제공 |
| 29620 | ApplicationCreated | CM | 정보 제공 |
| 29621 | ApplicationUpgradeStart | CM | 정보 제공 |
| 29622 | ApplicationUpgradeComplete | CM | 정보 제공 |
| 29623 | ApplicationUpgradeRollback | CM | 정보 제공 |
| 29624 | ApplicationUpgradeRollbackComplete | CM | 정보 제공 |
| 29625 | ApplicationDeleted | CM | 정보 제공 |
| 29626 | ApplicationUpgradeDomainComplete | CM | 정보 제공 |
| 18566 | ServiceCreated | FM | 정보 제공 |
| 18567 | ServiceDeleted | FM | 정보 제공 |

## <a name="next-steps"></a>다음 단계

* [Service Fabric의 진단](service-fabric-diagnostics-overview.md)에 대한 개요를 가져옵니다.
* [Service Fabric Eventstore 개요](service-fabric-diagnostics-eventstore.md)에서 EventStore에 대해 자세히 알아보기
* 더 많은 로그를 수집하도록 [Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) 구성 수정
* 작동 채널 로그를 표시하도록 [Application Insights 설정](service-fabric-diagnostics-event-analysis-appinsights.md)
