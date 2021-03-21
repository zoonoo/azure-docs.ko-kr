---
title: 고가용성 및 재해 복구
titleSuffix: Azure Digital Twins
description: 재해 복구 기능을 통해 항상 사용 가능한 Azure IoT 솔루션을 빌드하는 데 도움이 되는 Azure 및 Azure Digital Twins 기능에 대해 설명 합니다.
author: baanders
ms.author: baanders
ms.date: 10/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 3336a086fbe8f4291f752836a610cd80b773ec2d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98790819"
---
# <a name="azure-digital-twins-high-availability-and-disaster-recovery"></a>Azure Digital Twins 고가용성 및 재해 복구

복원 IoT 솔루션에 대 한 고려 사항은 비즈니스 연속성 및 재해 복구의 핵심 영역입니다. HA ( **고가용성)** 및 **DR (재해 복구)** 디자인은 솔루션에 대 한 적절 한 가동 시간 목표를 정의 하 고 달성 하는 데 도움이 될 수 있습니다.

이 문서에서는 Azure Digital Twins 서비스에서 특별히 제공 하는 HA 및 DR 기능을 설명 합니다.

Azure Digital Twins는 다음과 같은 기능 옵션을 지원 합니다.
* *지역 간 HA* – 서비스의 작동 시간을 제공 하는 기본 제공 중복성
* *지역 간 DR* – 예기치 않은 데이터 센터 오류가 발생 하는 경우 지역 쌍으로 연결 된 Azure 지역으로 장애 조치 (Failover)

HA/DR 디자인에 대 한 일반적인 Azure 지침에 대 한 [*모범 사례*](#best-practices) 섹션도 볼 수 있습니다.

## <a name="intra-region-ha"></a>역내 HA
 
Azure Digital Twins는 서비스 내에서 중복을 구현 하 여 지역 간 HA를 제공 합니다. 이는 작동 시간에 대 한 [서비스 SLA](https://azure.microsoft.com/support/legal/sla/digital-twins) 에 반영 됩니다. **이러한 HA 기능을 활용 하기 위해 Azure Digital Twins 솔루션 개발자가 추가 작업을 수행할 필요는 없습니다.** Azure Digital Twins는 합리적으로 높은 가동 시간을 보장 하지만 모든 분산 컴퓨팅 플랫폼과 마찬가지로 일시적인 오류가 여전히 예상 될 수 있습니다. 일시적인 오류를 처리 하기 위해 클라우드 응용 프로그램과 상호 작용 하는 구성 요소에 적절 한 재시도 정책을 빌드해야 합니다.

## <a name="cross-region-dr"></a>지역 간 DR

데이터 센터에서 정전 또는 지역에 있는 다른 이벤트로 인 한 가동 중단이 발생할 수 있는 드문 경우도 있습니다. 이러한 이벤트는 드물게 발생 하며 이러한 오류가 발생 하는 동안 위에서 설명한 지역 간 HA 기능이 도움이 되지 않을 수 있습니다. Azure 디지털 쌍은 Microsoft에서 시작한 장애 조치 (failover)를 통해이를 해결 합니다.

**Microsoft에서 시작 하는 장애 조치 (failover)** 는 드문 경우에 영향을 받는 지역에서 해당 지역 쌍으로 연결 된 지역으로 모든 Azure 디지털 twins 인스턴스를 장애 조치 (failover) 합니다. 이 프로세스는 기본 옵션으로, 사용자가 옵트아웃 (opt out) 할 수 없습니다. Microsoft는 이 옵션을 실행할 시기를 판단할 권리를 보유합니다. 사용자의 인스턴스를 장애 조치 (failover) 하기 전에이 메커니즘에 사용자 동의가 포함 되지 않습니다.

>[!NOTE]
> 또한 일부 Azure 서비스는 고객이 **시작한 장애 조치**(failover) 라는 추가 옵션을 제공 하 여 고객이 DR 드릴을 실행 하는 등의 인스턴스에 대해서만 장애 조치 (failover)를 시작할 수 있도록 합니다. 이 메커니즘은 현재 Azure Digital Twins에서 **지원 되지** 않습니다. 

## <a name="monitor-service-health"></a>서비스 상태 모니터링

Azure Digital Twins 인스턴스가 장애 조치 (failover) 되 고 복구 되 면 [Azure Service Health](../service-health/service-health-overview.md) 도구를 사용 하 여 프로세스를 모니터링할 수 있습니다. Service Health는 서로 다른 지역 및 구독에서 Azure 서비스의 상태를 추적 하 고 중단 및 가동 중지 시간에 대해 서비스에 영향을 주는 통신을 공유 합니다.

장애 조치 (failover) 이벤트 중에는 서비스가 다운 된 시기와 백업 시점을 표시 하는 Service Health을 제공할 수 있습니다.

Service Health 이벤트를 보려면 ...
1. Azure Portal에서 [Service Health](https://portal.azure.com/?feature.customportal=false#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues) 로 이동 합니다 .이 링크를 사용 하거나 포털 검색 표시줄을 사용 하 여 검색할 수 있습니다.
1. 왼쪽 메뉴를 사용 하 여 *상태 기록* 페이지로 전환 합니다.
1. **Azure Digital Twins** 로 시작 하는 *문제 이름을* 찾아 선택 합니다.

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/navigate.png" alt-text="상태 기록 페이지를 표시 하는 Azure Portal의 스크린샷 지난 며칠 동안의 몇 가지 문제에 대 한 목록이 있으며 ' Azure Digital Twins-유럽 서부-완화 ' 라는 문제가 강조 표시 되어 있습니다." lightbox="media/concepts-high-availability-disaster-recovery/navigate.png":::

1. 작동 중단에 대 한 일반 정보를 보려면 *요약* 탭을 확인 하십시오.

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/summary.png" alt-text="상태 기록 페이지에서 요약 탭이 강조 표시 됩니다. 이 탭에는 영향을 받는 리소스, 해당 지역 및 해당 구독과 같은 일반 정보가 표시 됩니다." lightbox="media/concepts-high-availability-disaster-recovery/summary.png":::
1. 시간에 따른 문제에 대 한 자세한 내용 및 업데이트는 *문제 업데이트* 탭을 참조 하세요.

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/issue-updates.png" alt-text="상태 기록 페이지에서 업데이트 문제 탭이 강조 표시 됩니다. 이 탭은 하루 전 까지의 현재 상태를 보여 주는 여러 항목을 표시 합니다." lightbox="media/concepts-high-availability-disaster-recovery/issue-updates.png":::


이 도구에 표시 된 정보는 하나의 Azure 디지털 인스턴스에만 국한 되지 않습니다. Service Health를 사용 하 여 특정 지역 또는 구독에서 Azure Digital Twins 서비스의 진행 상황을 이해 한 후에는 [리소스 상태 도구](troubleshoot-resource-health.md) 를 사용 하 여 특정 인스턴스로 드릴 다운 하 고 영향을 받는지 여부를 확인 하 여 단계를 추가로 모니터링할 수 있습니다.

## <a name="best-practices"></a>모범 사례

HA/DR에 대 한 모범 사례는이 항목에 대 한 다음 Azure 지침을 참조 하세요. 
* [*Azure 비즈니스 연속성 기술 지침*](/azure/architecture/framework/resiliency/overview) 문서에서는 비즈니스 연속성 및 재해 복구를 고려 하는 데 도움이 되는 일반적인 프레임 워크에 대해 설명 합니다. 
* [*Azure 응용 프로그램에 대 한 재해 복구 및*](/azure/architecture/framework/resiliency/backup-and-recovery) 고가용성 백서에서는 HA (고가용성) 및 DR (재해 복구)을 위해 azure 응용 프로그램 전략에 대 한 아키텍처 지침을 제공 합니다.

## <a name="next-steps"></a>다음 단계 

Azure Digital Twins 솔루션 시작 하기에 대 한 자세한 내용을 알아보세요.
 
* [*Azure Digital Twins란?*](overview.md)
* [*빠른 시작: 샘플 시나리오 살펴보기*](quickstart-adt-explorer.md)