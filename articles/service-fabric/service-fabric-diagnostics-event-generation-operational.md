---
title: Azure Service Fabric 이벤트 목록 | Microsoft Docs
description: 클러스터를 모니터링할 수 있도록 Azure Service Fabric에서 제공하는 이벤트의 포괄적인 목록입니다.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: 03dac03405588ba00a0f8ca5b127956c40853e36
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48868516"
---
# <a name="list-of-service-fabric-events"></a>Service Fabric 이벤트 목록 

Service Fabric은 [Service Fabric 이벤트](service-fabric-diagnostics-events.md)로 클러스터의 상태를 알리도록 클러스터 이벤트의 기본 집합을 노출합니다. 노드 및 클러스터의 Service Fabric에서 수행하는 작업 또는 클러스터 소유자/연산자에서 만드는 관리 결정을 기반으로 합니다. 이러한 이벤트는 클러스터에서 [EventStore](service-fabric-diagnostics-eventstore.md)를 쿼리하거나 작업 채널을 통해 액세스할 수 있습니다. Windows 컴퓨터에서 작업 채널은 EventLog에도 연결되므로 이벤트 뷰어에서 Service Fabric을 볼 수 있습니다. 

>[!NOTE]
>버전 < 6.2에서 클러스터에 대한 Service Fabric 이벤트 목록의 경우 다음 섹션을 참조하세요. 

다음은 매핑되는 엔터티별로 정렬된 플랫폼에서 사용할 수 있는 모든 이벤트의 목록입니다.

## <a name="cluster-events"></a>클러스터 이벤트

**클러스터 업그레이드 이벤트**

| EventId | 이름 | 설명 |원본(태스크) | Level | 버전 |
| --- | --- | --- | --- | --- | --- |
| 29627 | ClusterUpgradeStarted | 클러스터 업그레이드가 시작되었습니다. | CM | 정보 제공 | 1 |
| 29628 | ClusterUpgradeCompleted | 클러스터 업그레이드가 완료되었습니다.| CM | 정보 제공 | 1 |
| 29629 | ClusterUpgradeRollbackStarted | 클러스터 업그레이드 롤백이 시작되었습니다. | CM | 정보 제공 | 1 |
| 29630 | ClusterUpgradeRollbackCompleted | 클러스터 업그레이드 롤백이 완료되었습니다. | CM | 정보 제공 | 1 |
| 29631 | ClusterUpgradeDomainCompleted | 클러스터 업그레이드 중에 도메인 업그레이드가 완료되었습니다. | CM | 정보 제공 | 1 |

## <a name="node-events"></a>노드 이벤트

**노드 수명 주기 이벤트** 

| EventId | 이름 | 설명 |원본(태스크) | Level | 버전 |
| --- | --- | ---| --- | --- | --- |
| 18602 | NodeDeactivateCompleted | 노드 비활성화가 완료되었습니다. | FM | 정보 제공 | 1 |
| 18603 | NodeUp | 클러스터에서 노드가 시작되었음을 감지했습니다. | FM | 정보 제공 | 1 |
| 18604 | NodeDown | 클러스터에서 노드가 종료되었음을 감지했습니다. |  FM | 정보 제공 | 1 |
| 18605 | NodeAddedToCluster | 새 노드가 클러스터에 추가되었습니다. | FM | 정보 제공 | 1 |
| 18606 | NodeRemovedFromCluster | 노드가 클러스터에서 제거되었습니다. | FM | 정보 제공 | 1 |
| 18607 | NodeDeactivateStarted | 노드 비활성화가 시작되었습니다. | FM | 정보 제공 | 1 |
| 25620 | NodeOpening | 노드를 시작하고 있습니다. 노드 수명 주기의 첫 번째 단계입니다. | FabricNode | 정보 제공 | 1 |
| 25621 | NodeOpenSucceeded | 노드가 성공적으로 시작되었습니다. | FabricNode | 정보 제공 | 1 |
| 25622 | NodeOpenFailed | 노드가 시작되지 못했습니다. | FabricNode | 정보 제공 | 1 |
| 25623 | NodeClosing | 노드를 종료하고 있습니다. 노드 수명 주기의 마지막 단계가 시작됩니다. | FabricNode | 정보 제공 | 1 |
| 25624 | NodeClosed | 노드가 성공적으로 종료되었습니다. | FabricNode | 정보 제공 | 1 |
| 25625 | NodeAborting | 노드가 비정상적으로 종료되고 있습니다. | FabricNode | 정보 제공 | 1 |
| 25626 | NodeAborted | 노드가 비정상적으로 종료되었습니다. | FabricNode | 정보 제공 | 1 |

## <a name="application-events"></a>응용 프로그램 이벤트

**응용 프로그램 수명 주기 이벤트**

| EventId | 이름 | 설명 |원본(태스크) | Level | 버전 |
| --- | --- | ---| --- | --- | --- |
| 29620 | ApplicationCreated | 새 응용 프로그램이 만들어졌습니다. | CM | 정보 제공 | 1 |
| 29625 | ApplicationDeleted | 기존 응용 프로그램이 삭제되었습니다. | CM | 정보 제공 | 1 |
| 23083 | ApplicationProcessExited | 응용 프로그램 내 프로세스가 종료되었습니다. | Hosting | 정보 제공 | 1 |

**응용 프로그램 업그레이드 이벤트**

| EventId | 이름 | 설명 |원본(태스크) | Level | 버전 |
| --- | --- | ---| --- | --- | --- |
| 29621 | ApplicationUpgradeStarted | 응용 프로그램 업그레이드가 시작되었습니다. | CM | 정보 제공 | 1 |
| 29622 | ApplicationUpgradeCompleted | 응용 프로그램 업그레이드가 완료되었습니다. | CM | 정보 제공 | 1 |
| 29623 | ApplicationUpgradeRollbackStarted | 응용 프로그램 업그레이드 롤백이 시작되었습니다. |CM | 정보 제공 | 1 |
| 29624 | ApplicationUpgradeRollbackCompleted | 응용 프로그램 롤백이 완료되었습니다. | CM | 정보 제공 | 1 |
| 29626 | ApplicationUpgradeDomainCompleted | 응용 프로그램 업그레이드 중에 도메인 업그레이드가 완료되었습니다. | CM | 정보 제공 | 1 |

## <a name="service-events"></a>서비스 이벤트

**서비스 수명 주기 이벤트**

| EventId | 이름 | 설명 |원본(태스크) | Level | 버전 |
| --- | --- | ---| --- | --- | --- |
| 18657 | ServiceCreated | 새 서비스가 만들어졌습니다. | FM | 정보 제공 | 1 |
| 18658 | ServiceDeleted | 기존 서비스가 삭제되었습니다. | FM | 정보 제공 | 1 |

## <a name="partition-events"></a>파티션 이벤트

**파티션 이동 이벤트**

| EventId | 이름 | 설명 |원본(태스크) | Level | 버전 |
| --- | --- | ---| --- | --- | --- |
| 18940 | PartitionReconfigured | 파티션 재구성이 완료되었습니다. | RA | 정보 제공 | 1 |

## <a name="container-events"></a>컨테이너 이벤트

**컨테이너 수명 주기 이벤트** 

| EventId | 이름 | 설명 |원본(태스크) | Level | 버전 |
| --- | --- | ---| --- | --- | --- |
| 23074 | ContainerActivated | 컨테이너가 시작되었습니다. | Hosting | 정보 제공 | 1 |
| 23075 | ContainerDeactivated | 컨테이너가 중지되었습니다. | Hosting | 정보 제공 | 1 |
| 23082 | ContainerExited | 컨테이너가 종료되었습니다. UnexpectedTermination 플래그를 확인하세요. | Hosting | 정보 제공 | 1 |

## <a name="health-reports"></a>상태 보고서

**클러스터 상태 보고서 이벤트**

| EventId | 이름 | 설명 |원본(태스크) | Level | 버전 |
| --- | --- | --- | --- | --- | --- |
| 54428 | ClusterNewHealthReport | 새 클러스터 상태 보고서를 사용할 수 있습니다. | HM | 정보 제공 | 1 |
| 54437 | ClusterHealthReportExpired | 기존 클러스터 상태 보고서가 만료되었습니다. | HM | 정보 제공 | 1 |

**노드 상태 보고서 이벤트**

| EventId | 이름 | 설명 |원본(태스크) | Level | 버전 |
| --- | --- | ---| --- | --- | --- |
| 54423 | NodeNewHealthReport | 새 노드 상태 보고서를 사용할 수 있습니다. | HM | 정보 제공 | 1 |
| 54432 | NodeHealthReportExpired | 기존 노드 상태 보고서가 만료되었습니다. | HM | 정보 제공 | 1 |

**응용 프로그램 상태 보고서 이벤트**

| EventId | 이름 | 설명 |원본(태스크) | Level | 버전 |
| --- | --- | ---| --- | --- | --- |
| 54425 | ApplicationNewHealthReport | 새 응용 프로그램 상태 보고서가 만들어졌습니다. 배포되지 않은 응용 프로그램에 대한 보고서입니다. | HM | 정보 제공 | 1 |
| 54426 | DeployedApplicationNewHealthReport | 새로 배포된 응용 프로그램 상태 보고서가 만들어졌습니다. | HM | 정보 제공 | 1 |
| 54427 | DeployedServicePackageNewHealthReport | 새로 배포된 서비스 상태 보고서가 만들어졌습니다. | HM | 정보 제공 | 1 |
| 54434 | ApplicationHealthReportExpired | 기존 응용 프로그램 상태 보고서가 만료되었습니다. | HM | 정보 제공 | 1 |
| 54435 | DeployedApplicationHealthReportExpired | 기존 배포된 응용 프로그램 상태 보고서가 만료되었습니다. | HM | 정보 제공 | 1 |
| 54436 | DeployedServicePackageHealthReportExpired | 기존 배포된 서비스 상태 보고서가 만료되었습니다. | HM | 정보 제공 | 1 |

**서비스 상태 보고서 이벤트**

| EventId | 이름 | 설명 |원본(태스크) | Level | 버전 |
| --- | --- | ---| --- | --- | --- |
| 54424 | ServiceNewHealthReport | 새 서비스 상태 보고서가 만들어졌습니다. | HM | 정보 제공 | 1 |
| 54433 | ServiceHealthReportExpired | 기존 서비스 상태 보고서가 만료되었습니다. | HM | 정보 제공 | 1 |

**파티션 상태 보고서 이벤트**

| EventId | 이름 | 설명 |원본(태스크) | Level | 버전 |
| --- | --- | ---| --- | --- | --- |
| 54422 | PartitionNewHealthReport | 새 파티션 상태 보고서가 만들어졌습니다. | HM | 정보 제공 | 1 |
| 54431 | PartitionHealthReportExpired | 기존 파티션 상태 보고서가 만료되었습니다. | HM | 정보 제공 | 1 |

**복제본 상태 보고서 이벤트**

| EventId | 이름 | 설명 |원본(태스크) | Level | 버전 |
| --- | --- | ---| --- | --- | --- |
| 54429 | StatefulReplicaNewHealthReport | 상태 저장 복제본 상태 보고서가 만들어졌습니다. | HM | 정보 제공 | 1 |
| 54430 | StatelessInstanceNewHealthReport | 새 상태 비저장 인스턴스 상태 보고서가 만들어졌습니다. | HM | 정보 제공 | 1 |
| 54438 | StatefulReplicaHealthReportExpired | 기존 상태 저장 복제본 상태 보고서가 만료되었습니다. | HM | 정보 제공 | 1 |
| 54439 | StatelessInstanceHealthReportExpired | 기존 상태 비저장 인스턴스 상태 보고서가 만료되었습니다. | HM | 정보 제공 | 1 |

## <a name="chaos-testing-events"></a>카오스 테스트 이벤트 

**카오스 세션 이벤트**

| EventId | 이름 | 설명 |원본(태스크) | Level | 버전 |
| --- | --- | ---| --- | --- | --- |
| 50021 | ChaosStarted | 카오스 테스트 세션이 시작되었습니다. | 테스트 가능성 | 정보 제공 | 1 |
| 50023 | ChaosStopped | 카오스 테스트 세션이 중지되었습니다. | 테스트 가능성 | 정보 제공 | 1 |

**카오스 노드 이벤트**

| EventId | 이름 | 설명 |원본(태스크) | Level | 버전 |
| --- | --- | ---| --- | --- | --- |
| 50033 | ChaosNodeRestartScheduled | 노드가 카오스 테스트 세션의 일부로 다시 시작하도록 예약되었습니다. | 테스트 가능성 | 정보 제공 | 1 |
| 50087 | ChaosNodeRestartCompleted | 노드가 카오스 테스트 세션의 일부로 다시 시작되었습니다. | 테스트 가능성 | 정보 제공 | 1 |

**카오스 응용 프로그램 이벤트**

| EventId | 이름 | 설명 |원본(태스크) | Level | 버전 |
| --- | --- | ---| --- | --- | --- |
| 50053 | ChaosCodePackageRestartScheduled | 코드 패키지 다시 시작이 카오스 테스트 세션 중에 예약되었습니다. | 테스트 가능성 | 정보 제공 | 1 |
| 50101 | ChaosCodePackageRestartCompleted | 코드 패키지 다시 시작이 카오스 테스트 세션 중에 완료되었습니다. | 테스트 가능성 | 정보 제공 | 1 |

**카오스 파티션 이벤트**

| EventId | 이름 | 설명 |원본(태스크) | Level | 버전 |
| --- | --- | ---| --- | --- | --- |
| 50069 | ChaosPartitionPrimaryMoveScheduled | 주 파티션이 카오스 테스트 세션의 일부로 이동하도록 예약되었습니다. | 테스트 가능성 | 정보 제공 | 1 |
| 50077 | ChaosPartitionSecondaryMoveScheduled | 보조 파티션이 카오스 테스트 세션의 일부로 이동하도록 예약되었습니다. | 테스트 가능성 | 정보 제공 | 1 |
| 65003 | PartitionPrimaryMoveAnalysis | 주 파티션 이동에 대한 심층 분석을 사용할 수 있습니다. | 테스트 가능성 | 정보 제공 | 1 |

**카오스 복제본 이벤트**

| EventId | 이름 | 설명 |원본(태스크) | Level | 버전 |
| --- | --- | ---| --- | --- | --- |
| 50047 | ChaosReplicaRestartScheduled | 복제본 다시 시작이 카오스 테스트 세션의 일부로 예약되었습니다. | 테스트 가능성 | 정보 제공 | 1 |
| 50051 | ChaosReplicaRemovalScheduled | 복제본 제거가 카오스 테스트 세션의 일부로 예약되었습니다. | 테스트 가능성 | 정보 제공 | 1 |
| 50093 | ChaosReplicaRemovalCompleted | 복제본 제거가 카오스 테스트 세션의 일부로 완료되었습니다. | 테스트 가능성 | 정보 제공 | 1 |

## <a name="other-events"></a>기타 이벤트

**상관 관계 이벤트**

| EventId | 이름 | 설명 |원본(태스크) | Level | 버전 |
| --- | --- | ---| --- | --- | --- |
| 65011 | CorrelationOperational | 상관 관계가 검색되었습니다. | 테스트 가능성 | 정보 제공 | 1 |

## <a name="events-prior-to-version-62"></a>버전 6.2 이전 이벤트

다음은 버전 6.2 이전의 Service Fabric이 제공하는 이벤트의 포괄적인 목록입니다.

| EventId | 이름 | 원본(태스크) | 수준 |
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

* Service Fabric에서 전반적인 [플랫폼 수준의 이벤트 생성](service-fabric-diagnostics-event-generation-infra.md)에 대해 자세히 알아보기
* 더 많은 로그를 수집하도록 [Azure 진단](service-fabric-diagnostics-event-aggregation-wad.md) 구성 수정
* 작동 채널 로그를 표시하도록 [Application Insights 설정](service-fabric-diagnostics-event-analysis-appinsights.md)
