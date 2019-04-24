---
title: Azure Migrate의 종속성 시각화 | Microsoft Docs
description: Azure Migrate 서비스의 평가 계산에 대한 개요를 제공합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: raynew
ms.openlocfilehash: 8df587db7655e2aafd876d80581f3296c8c99fbf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60201564"
---
# <a name="dependency-visualization"></a>종속성 시각화

[Azure Migrate](migrate-overview.md) 서비스는 Azure로의 마이그레이션에 대한 온-프레미스 컴퓨터 그룹을 평가합니다. Azure Migrate의 종속성 시각화 기능을 사용하면 그룹을 만들 수 있습니다. 이 문서에서는 이 기능에 대한 정보를 제공합니다.

> [!NOTE]
> Azure Government에서는 종속성 시각화 기능을 사용할 수 없습니다.

## <a name="overview"></a>개요

Azure Migrate의 종속성 시각화를 사용하면 마이그레이션 평가용으로 신뢰도가 높은 그룹을 만들 수 있습니다. 종속성 시각화를 통해 컴퓨터의 네트워크 종속성을 확인할 수 있으며, Azure로 함께 마이그레이션해야 하는 관련 컴퓨터를 파악할 수 있습니다. 이 기능은 애플리케이션의 일부분이어서 Azure로 함께 마이그레이션해야 하는 컴퓨터가 확실치 않은 시나리오에서 유용합니다.

## <a name="how-does-it-work"></a>어떻게 작동합니까?

Azure Migrate 사용 합니다 [서비스 맵](../operations-management-suite/operations-management-suite-service-map.md) 에서 솔루션 [Azure Monitor 로그](../log-analytics/log-analytics-overview.md) 종속성 시각화에 대 한 합니다.
- 종속성 시각화를 활용하려면 신규 또는 기존 Log Analytics 작업 영역을 Azure Migrate 프로젝트와 연결해야 합니다.
- 작업 영역은 마이그레이션 프로젝트를 만든 것과 같은 구독에서만 만들거나 연결할 수 있습니다.
- 프로젝트에 Log Analytics 작업 영역을 연결하려면 프로젝트 **개요** 페이지의 **기본 정보** 섹션으로 이동하고 **구성 필요**를 클릭합니다.

    ![Log Analytics 작업 영역 연결](./media/concepts-dependency-visualization/associate-workspace.png)

- 작업 영역을 연결하는 동안 새 작업 영역을 만들거나 기존 작업 영역을 연결하는 옵션이 제공됩니다.
  - 새 작업 영역을 만들 때는 작업 영역의 이름을 지정해야 합니다. 그러면 마이그레이션 프로젝트와 같은 [Azure 지리적 위치](https://azure.microsoft.com/global-infrastructure/geographies/)에 해당하는 지역에서 작업 영역이 작성됩니다.
  - 기존 작업 영역을 연결하는 경우에는 마이그레이션 프로젝트와 동일한 구독에서 사용 가능한 모든 작업 영역을 선택할 수 있습니다. [서비스 맵이 지원](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-configure#supported-azure-regions)되는 지역에서 만든 작업 영역만 나열됩니다. 작업 영역을 연결하려면 작업 영역에 대한 ‘읽기 권한자’ 액세스 권한이 있어야 합니다.

  > [!NOTE]
  > 작업 영역을 프로젝트에 연결한 후에는 나중에 변경할 수 없습니다.

- 연결된 작업 영역에는 **마이그레이션 프로젝트** 키와 **프로젝트 이름** 값으로 태그가 지정됩니다. 이 키와 값은 Azure Portal에서 검색하는 데 사용할 수 있습니다.
- 프로젝트에 연결된 작업 영역으로 이동하려는 경우 프로젝트 **개요** 페이지의 **기본 정보** 섹션으로 이동한 다음 작업 영역에 액세스하면 됩니다.

    ![Log Analytics 작업 영역 탐색](./media/concepts-dependency-visualization/oms-workspace.png)

종속성 시각화를 사용하려면 분석할 각 온-프레미스 컴퓨터에 에이전트를 다운로드하여 설치해야 합니다.  

- 각 머신에 [MMA(Microsoft Monitoring Agent)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)를 설치해야 합니다.
- 각 머신에 [종속성 에이전트](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure)를 설치해야 합니다.
- 또한 인터넷에 연결되지 않은 머신이 있으면 해당 머신에 Log Analytics 게이트웨이를 다운로드하여 설치해야 합니다.

종속성 시각화를 사용하지 않는다면 평가하려는 머신에 이러한 에이전트가 필요하지 않습니다.

## <a name="do-i-need-to-pay-for-it"></a>요금이 발생하나요?

Azure Migrate는 추가 요금 없이 사용할 수 있습니다. Azure Migrate의 종속성 시각화 기능을 사용 하려면 서비스 맵이 있어야 하며, 신규 또는 기존 Log Analytics 작업 영역을 Azure Migrate 프로젝트와 연결해야 합니다. Azure Migrate의 종속성 시각화 기능은 Azure Migrate 사용 시작일부터 180일 동안 무료로 제공됩니다.

1. 이 Log Analytics 작업 영역 내에서 서비스 맵 이외의 다른 솔루션을 사용하면 [표준 Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) 요금이 발생합니다.
2. 추가 비용 없이 마이그레이션 시나리오를 지원하기 위해, Azure Migrate 프로젝트와 Log Analytics 작업 영역을 연결한 날로부터 180일 동안은 서비스 맵 솔루션에서 요금이 부과되지 않습니다. 180일 후에는 표준 Log Analytics 요금이 적용됩니다.

작업 영역에 에이전트를 등록하는 경우에는 에이전트 설치 단계 페이지에서 프로젝트에 제공된 ID와 키를 사용합니다.

Azure Migrate 프로젝트가 삭제될 때 작업 영역은 함께 삭제되지 않습니다. 프로젝트 삭제 후에는 서비스 맵을 무료로 사용할 수 없으며, 각 노드에는 Log Analytics 작업 영역의 유료 계층에 따라 요금이 부과됩니다.

> [!NOTE]
> 종속성 시각화 기능은 Log Analytics 작업 영역을 통해 서비스 맵을 사용합니다. 2018년 2월 28일 이후로 Azure Migrate의 일반 공급이 발표되면서 이 기능을 추가 비용 없이 사용할 수 있습니다. 무료 사용량 작업 영역을 사용하려면 새 프로젝트를 만들어야 합니다. 일반 공급 이전의 기존 작업 영역에는 계속 대금이 청구될 수 있으므로, 새 프로젝트로 이동하는 것이 좋습니다.

[여기](https://azure.microsoft.com/pricing/details/azure-migrate/)에서 Azure Migrate 가격 책정에 대해 자세히 알아보세요.

## <a name="how-do-i-manage-the-workspace"></a>작업 영역은 어떻게 관리하나요?

Azure Migrate 외부에서 Log Analytics 작업 영역을 사용할 수 있습니다. 작업 영역이 생성된 마이그레이션 프로젝트를 삭제해도 작업 영역은 삭제되지 않습니다. 작업 영역이 더 이상 필요 없는 경우 수동으로 [작업 영역을 삭제](../azure-monitor/platform/manage-access.md)해야 합니다.

마이그레이션 프로젝트를 삭제하지 않는 한 Azure Migrate가 만든 작업 영역을 삭제하지 마세요. 작업 영역을 삭제하면 종속성 시각화 기능이 정상적으로 작동하지 않습니다.

## <a name="next-steps"></a>다음 단계
- [컴퓨터 종속성을 사용하여 컴퓨터 그룹화](how-to-create-group-machine-dependencies.md)
- 종속성 시각화 관련 FAQ를 [자세히 확인](https://docs.microsoft.com/azure/migrate/resources-faq#dependency-visualization)해 보세요.
