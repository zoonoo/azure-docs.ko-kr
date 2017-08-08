---
title: "Azure Service Fabric 작동 채널 | Microsoft Docs"
description: "Azure Service Fabric 클러스터의 작동 채널에서 생성된 로그의 포괄적인 목록입니다."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/19/2017
ms.author: dekapur
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 934719868ab9968db352db2b440014d35dbc0274
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---

# <a name="operational-channel"></a>작동 채널 

작동 채널은 노드 및 클러스터의 Service Fabric에서 수행되는 상위 수준 작업의 로그로 구성됩니다. 클러스터에 대해 "진단"을 사용하도록 설정하면 Azure 진단 에이전트가 클러스터에 배포되고 기본적으로 작동 패널의 로그를 읽도록 구성됩니다. 추가 로그 또는 성능 카운터를 확인하려면 진단 구성을 수정하도록 [Azure 진단 에이전트](service-fabric-diagnostics-event-aggregation-wad.md)를 구성하는 방법에 대한 자세한 내용을 읽어보세요. 

## <a name="operational-channel-logs"></a>작동 채널 로그 

다음은 작동 채널에서 Service Fabric이 제공하는 로그의 포괄적인 목록입니다. 

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

