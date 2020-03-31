---
title: Azure 모니터 통합 문서 데이터 원본 | 마이크로 소프트 문서
description: 여러 데이터 원본으로 빌드된 미리 빌드되고 사용자 지정 매개 변수화된 Azure Monitor 통합 문서로 복잡한 보고 간소화
services: azure-monitor
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: d57910ae31d4db9be17b3dc46b5920a925ab4fcf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248581"
---
# <a name="azure-monitor-workbooks-data-sources"></a>Azure 모니터 통합 문서 데이터 원본

통합 문서는 많은 수의 데이터 원본과 호환됩니다. 이 문서에서는 Azure Monitor 통합 문서에서 현재 사용할 수 있는 데이터 원본을 안내합니다.

## <a name="logs"></a>로그

통합 문서를 사용하면 다음 소스에서 로그를 쿼리할 수 있습니다.

* Azure 모니터 로그(응용 프로그램 인사이트 리소스 및 로그 분석 작업 영역))
* 리소스 중심 데이터(활동 로그)

통합 문서 작성자는 기본 리소스 데이터를 변환하는 KQL 쿼리를 사용하여 텍스트, 차트 또는 그리드로 시각화할 수 있는 결과 집합을 선택할 수 있습니다.

![통합 문서 로그 보고서 인터페이스의 스크린샷](./media/workbooks-overview/logs.png)

통합 문서 작성자는 여러 리소스에서 쉽게 쿼리하여 진정으로 통합된 풍부한 보고 환경을 만들 수 있습니다.

## <a name="metrics"></a>메트릭

Azure 리소스는 통합 문서를 통해 액세스할 수 있는 [메트릭을](data-platform-metrics.md) 내보전합니다. 메트릭은 대상 리소스, 원하는 메트릭 및 해당 집계를 지정할 수 있는 특수 제어를 통해 통합 문서에서 액세스할 수 있습니다. 그런 다음 이 데이터를 차트 또는 그리드로 플롯할 수 있습니다.

![CPU 사용률의 통합 문서 메트릭 차트의 스크린샷](./media/workbooks-overview/metrics-graph.png)

![통합 문서 메트릭 인터페이스의 스크린샷](./media/workbooks-overview/metrics.png)

## <a name="azure-resource-graph"></a>Azure Resource Graph 

통합 문서는 ARG(Azure 리소스 그래프)를 사용하여 리소스 및 해당 메타데이터에 대한 쿼리를 지원합니다. 이 기능은 주로 보고서에 대한 사용자 지정 쿼리 범위를 작성하는 데 사용됩니다. 리소스 범위는 ARG가 지원하는 KQL 하위 집합을 통해 표현되며, 일반적인 사용 사례에 충분한 경우가 많습니다.

쿼리 컨트롤이 이 데이터 원본을 사용하도록 하려면 쿼리 유형 드롭다운을 사용하여 Azure 리소스 그래프를 선택하고 대상 구독을 선택합니다. 쿼리 컨트롤을 사용하여 흥미로운 리소스 하위 집합을 선택하는 ARG KQL 하위 집합을 추가합니다.


![Azure 리소스 그래프 KQL 쿼리의 스크린샷](./media/workbooks-overview/azure-resource-graph.png)

## <a name="alerts-preview"></a>경고(미리 보기)

통합 문서를 사용하면 사용자가 리소스와 관련된 활성 경고를 시각화할 수 있습니다. 이 기능을 사용하면 알림 데이터(경고) 및 진단 정보(메트릭, 로그)를 하나의 보고서로 통합하는 보고서를 만들 수 있습니다. 이 정보를 함께 결합하여 이러한 데이터 원본 간에 통찰력을 결합하는 풍부한 보고서를 만들 수도 있습니다.

쿼리 컨트롤이 이 데이터 원본을 사용하도록 하려면 쿼리 유형 드롭다운을 사용하여 경고를 선택하고 대상 구독, 리소스 그룹 또는 리소스를 선택합니다. 경고 필터 드롭다운을 사용하여 분석 요구 사항에 대한 흥미로운 경고 하위 집합을 선택합니다.

![경고 쿼리의 스크린샷](./media/workbooks-overview/alerts.png)

## <a name="workload-health-preview"></a>워크로드 상태(미리 보기)

Azure Monitor에는 Windows 또는 Linux 게스트 운영 체제의 가용성과 성능을 사전에 모니터링하는 기능이 있습니다. Azure Monitor는 주요 구성 요소 및 해당 관계, 해당 구성 요소의 상태를 측정하는 방법에 대한 기준 및 비정상 조건이 검색될 때 경고하는 구성 요소를 모델합니다. 통합 문서를 사용하면 이 정보를 사용하여 풍부한 대화형 보고서를 만들 수 있습니다.

쿼리 컨트롤이 이 데이터 원본을 사용하도록 하려면 **쿼리 유형** 드롭다운을 사용하여 워크로드 상태를 선택하고 대상구독, 리소스 그룹 또는 VM 리소스를 선택합니다. 상태 필터 드롭다운을 사용하여 분석 요구에 맞는 상태 인시던트의 흥미로운 하위 집합을 선택합니다.

![경고 쿼리의 스크린샷](./media/workbooks-overview/workload-health.png)

## <a name="azure-resource-health"></a>Azure 리소스 상태 

통합 문서는 Azure 리소스 상태를 얻고 이를 다른 데이터 원본과 결합하여 풍부한 대화형 상태 보고서를 만드는 것을 지원합니다.

쿼리 컨트롤이 이 데이터 원본을 사용하도록 하려면 **쿼리 유형** 드롭다운을 사용하여 Azure 상태를 선택하고 대상 리소스를 선택합니다. 상태 필터 드롭다운을 사용하여 분석 요구에 맞는 리소스 문제의 흥미로운 하위 집합을 선택합니다.

![경고 쿼리의 스크린샷](./media/workbooks-overview/resource-health.png)

## <a name="azure-data-explorer-preview"></a>Azure 데이터 탐색기(미리 보기)

통합 문서는 이제 강력한 [Kusto](https://docs.microsoft.com/azure/kusto/query/index) 쿼리 언어를 사용하여 [Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/) 클러스터에서 쿼리할 수 있습니다.   

![쿠스토 쿼리 창의 스크린 샷](./media/workbooks-overview/data-explorer.png)

## <a name="next-steps"></a>다음 단계

* 통합 문서에 대해 자세히 알아보고 다양한 다양한 시각화 옵션을 [알아보세요.](workbooks-visualizations.md)
* 통합 문서 리소스에 대한 액세스를 [제어하고](workbooks-access-control.md) 공유합니다.
