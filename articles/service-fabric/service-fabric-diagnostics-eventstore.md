---
title: Azure Service Fabric EventStore  | Microsoft Docs
description: Azure Service Fabric의 EventStore 알아보기
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/17/2019
ms.author: srrengar
ms.openlocfilehash: 520961fb4bd126ef878a779c10fb5689b8692c73
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64683738"
---
# <a name="eventstore-service-overview"></a>EventStore 서비스 개요

>[!NOTE]
>Service Fabric 버전 6.4부터 EventStore API는 Azure에서만 실행되는 Windows 클러스터에 제공됩니다. 이 기능을 Linux 및 독립 실행형 클러스터에도 이식하려고 노력하고 있습니다.

## <a name="overview"></a>개요

6.2 버전에 도입된 EventStore 서비스는 Service Fabric의 모니터링 옵션입니다. EventStore는 일정한 시기에 클러스터 또는 워크로드의 상태를 파악할 수 있는 방법을 제공합니다. EventStore는 클러스터에서 이벤트를 유지 관리하는 상태 저장 Service Fabric 서비스입니다. 이벤트는 Service Fabric Explorer, REST 및 API를 통해 노출됩니다. EventStore는 클러스터를 직접 쿼리하여 클러스터의 모든 엔터티에 대한 진단 데이터를 획득하고 다음 용도로 사용할 수 있습니다.

* 개발 또는 테스트의 문제 또는 모니터링 파이프라인을 사용할 위치의 문제 진단
* 클러스터에 대해 수행하는 관리 작업이 제대로 처리되고 있는지 확인
* Service Fabric이 특정 엔터티와 상호 작용하는 방법의 "스냅샷" 가져오기

![EventStore](media/service-fabric-diagnostics-eventstore/eventstore.png)

EventStore에서 사용할 수 있는 전체 이벤트 목록은 [Service Fabric 이벤트](service-fabric-diagnostics-event-generation-operational.md)를 참조하세요.

>[!NOTE]
>Service Fabric 버전 6.2부터 EventStore API는 현재 Azure에서만 실행되는 Windows 클러스터에 대한 미리 보기로 제공됩니다. 이 기능을 Linux 및 독립 실행형 클러스터에도 이식하려고 노력하고 있습니다.

클러스터의 각 엔터티 및 엔터티 형식에 사용할 수 있는 이벤트에 대해 EventStore 서비스를 쿼리할 수 있습니다. 즉, 다음 수준에서 이벤트를 쿼리할 수 있습니다.
* 클러스터: 클러스터 자체에 한정된 이벤트(예: 클러스터 업그레이드)
* 노드: 모든 노드 수준 이벤트
* 노드: `nodeName`으로 식별되는 한 노드에 한정된 이벤트
* 애플리케이션: 모든 애플리케이션 수준 이벤트
* 애플리케이션: `applicationId`로 식별되는 한 애플리케이션에 한정된 이벤트
* 서비스: 클러스터에 있는 모든 서비스의 이벤트
* 서비스: `serviceId`로 식별되는 특정 서비스의 이벤트
* 파티션: 모든 파티션의 이벤트
* 파티션: `partitionId`로 식별되는 특정 파티션의 이벤트
* 파티션 복제본: `partitionId`로 식별되는 특정 파티션 내 복제본/인스턴스의 이벤트
* 파티션 복제본: `replicaId` 및 `partitionId`로 식별되는 특정 복제본/인스턴스의 이벤트

API에 대해 자세히 알아보려면 [EventStore API 참조](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-eventsstore)를 확인하세요.

EventStore 서비스에는 클러스터의 이벤트 간에 상관 관계를 지정하는 기능도 있습니다. EventStore 서비스는 서로 영향을 미칠 수 있는 서로 다른 엔터티에서 동시에 기록된 이벤트를 살펴보고 이러한 이벤트를 연결하여 클러스터의 작업에 대한 원인을 식별하는 데 도움을 줍니다. 예를 들어 아무런 변경 작업 없이 애플리케이션 중 하나가 비정상 상태가 되면 EventStore는 플랫폼에서 노출되는 다른 이벤트를 살펴보고 `Error` 또는 `Warning` 이벤트로 상관 관계를 지정합니다. 이렇게 하면 더 빠르게 오류를 감지하고 근본 원인을 분석할 수 있습니다.

## <a name="enable-eventstore-on-your-cluster"></a>클러스터에서 EventStore를 사용하도록 설정

### <a name="local-cluster"></a>로컬 클러스터

[클러스터의 fabricSettings.json](service-fabric-cluster-fabric-settings.md)에서 EventStoreService를 추가 기능으로 추가하고 클러스터 업그레이드를 수행합니다.

```json
    "addOnFeatures": [
        "EventStoreService"
    ],
```

### <a name="azure-cluster"></a>Azure 클러스터

클러스터의 Azure Resource Manager 템플릿에서 켜면 EventStore 서비스를 수행 하 여는 [클러스터 구성 업그레이드](service-fabric-cluster-config-upgrade-azure.md) 및 다음 코드를 추가, 사용 하 여의 PlacementConstraints EventStore의 복제본을 배치 하려면 시스템 서비스에 대 한 전용 된 NodeType에 예를 들어 특정 NodeType의 서비스입니다. `upgradeDescription` 섹션은 노드에서 다시 시작을 트리거하도록 구성 업그레이드를 구성합니다. 다른 업데이트에서 섹션을 제거할 수 있습니다.

```json
    "fabricSettings": [
          …
          …
          …,
         {
            "name": "EventStoreService",
            "parameters": [
              {
                "name": "TargetReplicaSetSize",
                "value": "3"
              },
              {
                "name": "MinReplicaSetSize",
                "value": "1"
              },
              {
                "name": "PlacementConstraints",
                "value": "(NodeType==<node_type_name_here>)"
              }
            ]
          }
        ],
        "upgradeDescription": {
          "forceRestart": true,
          "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807",
          "healthCheckWaitDuration": "00:01:00",
          "healthCheckStableDuration": "00:01:00",
          "healthCheckRetryTimeout": "00:5:00",
          "upgradeTimeout": "1:00:00",
          "upgradeDomainTimeout": "00:10:00",
          "healthPolicy": {
            "maxPercentUnhealthyNodes": 100,
            "maxPercentUnhealthyApplications": 100
          },
          "deltaHealthPolicy": {
            "maxPercentDeltaUnhealthyNodes": 0,
            "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0,
            "maxPercentDeltaUnhealthyApplications": 0
          }
        }
```


## <a name="next-steps"></a>다음 단계
* EventStore API 시작 - [Azure Service Fabric 클러스터에서 EventStore API를 사용](service-fabric-diagnostics-eventstore-query.md)
* EventStore - [Service Fabric 이벤트](service-fabric-diagnostics-event-generation-operational.md)에서 제공되는 이벤트의 목록에 대해 자세히 알아봅니다.
* Service Fabric의 모니터링 및 진단 개요 - [Service Fabric에 대한 모니터링 및 진단](service-fabric-diagnostics-overview.md)
* API 호출의 전체 목록 보기 - [EventStore REST API 참조](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-eventsstore)
* 클러스터 모니터링에 대해 자세히 알아보기 - [클러스터 및 플랫폼 모니터링](service-fabric-diagnostics-event-generation-infra.md).
