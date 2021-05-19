---
title: Azure Monitor 통합 문서 데이터 원본 | Microsoft docs
description: 여러 데이터 원본에서 빌드된 미리 작성된 사용자 지정 매개 변수화된 Azure Monitor 통합 문서를 사용하여 복잡한 보고 간소화
services: azure-monitor
documentationcenter: ''
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/29/2020
ms.openlocfilehash: 862c6c5253c1bb4481476b67c7cfb203c2568e24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101700578"
---
# <a name="azure-monitor-workbooks-data-sources"></a>Azure Monitor 통합 문서 데이터 원본

통합 문서는 많은 수의 데이터 원본과 호환됩니다. 이 문서에서는 Azure Monitor 통합 문서에 현재 사용할 수 있는 데이터 원본을 안내합니다.

## <a name="logs"></a>로그

통합 문서는 다음 원본에서 로그를 쿼리할 수 있습니다.

* Azure Monitor Logs (Application Insights Resources 및 Log Analytics Workspaces.)
* 리소스 중심 데이터(활동 로그)

통합 문서 작성자는 기본 리소스 데이터를 변환하는 KQL 쿼리를 사용하여 텍스트, 차트 또는 표로 시각화할 수 있는 결과 집합을 선택할 수 있습니다.

![통합 문서 로그 보고서 인터페이스 스크린샷](./media/workbooks-data-sources/logs.png)

통합 문서 작성자는 여러 리소스에 대해 쉽게 쿼리하여 완전히 통합된 풍부한 보고 환경을 만들 수 있습니다.

## <a name="metrics"></a>메트릭

Azure 리소스는 통합 문서를 통해 액세스할 수 있는 [메트릭을](../essentials/data-platform-metrics.md) 내보냅니다. 대상 리소스, 원하는 메트릭 및 그 집계를 지정하는 데 사용할 수 있는 특수한 컨트롤을 통해 통합 문서에서 메트릭에 액세스할 수 있습니다. 그런 다음 이 데이터를 차트 또는 표로 표시될 수 있습니다.

![CPU 사용률에 대한 통합 문서 메트릭 차트의 스크린샷](./media/workbooks-data-sources/metrics-graph.png)

![통합 문서 메트릭 인터페이스 스크린샷](./media/workbooks-data-sources/metrics.png)

## <a name="azure-resource-graph"></a>Azure Resource Graph

통합 문서는 ARG (Azure Resource Graph)를 사용하여 리소스 및 해당 메타 데이터의 쿼리를 지원합니다. 이 기능은 주로 보고서의 사용자 지정 쿼리 범위를 작성하는 데 사용됩니다. 리소스 범위는 ARG가 지원하는 KQL 하위 집합을 통해 표현되며, 이는 일반적인 사용 사례에서는 충분한 경우가 많습니다.

이 데이터 원본을 사용하여 쿼리 제어를 만들려면, 쿼리 유형 드롭다운을 사용하여 Azure Resource Graph를 선택하고 대상 구독을 선택합니다. 쿼리 제어를 사용하여 관심 있는 리소스 하위 집합을 선택하는 ARG KQL 하위 집합을 추가합니다.

![Azure Resource Graph KQL 쿼리 스크린샷](./media/workbooks-data-sources/azure-resource-graph.png)

## <a name="azure-resource-manager"></a>Azure 리소스 관리자

통합 문서는 Azure Resource Manager REST 작업을 지원합니다. 이렇게 하면 고유한 인증 헤더 토큰을 제공할 필요 없이 management.azure.com 엔드포인트를 쿼리할 수 있습니다.

이 데이터 원본을 사용하여 쿼리 제어를 만들려면, 데이터 원본 드롭다운을 사용하여 Azure Resource Manager를 선택합니다. Http 메서드, url 경로, 헤더, url 매개 변수 및/또는 본문과 같은 적절한 매개 변수를 제공합니다.

> [!NOTE]
> 현재 `GET`, `POST` 및 `HEAD` 작업만이 지원됩니다.

## <a name="azure-data-explorer"></a>Azure Data Explorer

이제 통합 문서에서 강력한 [Kusto](/azure/kusto/query/index) 쿼리 언어를 사용하여 [Azure Data Explorer](/azure/data-explorer/) 클러스터에서 쿼리하는 것을 지원합니다.

![Kusto 쿼리 창 스크린샷](./media/workbooks-data-sources/data-explorer.png)

## <a name="workload-health"></a>워크로드 상태

Azure Monitor에는 Windows 또는 Linux 게스트 운영 체제의 가용성과 성능을 사전에 모니터링하는 기능이 있습니다. Azure Monitor는 주요 구성 요소 및 그 관계, 이러한 구성 요소의 상태를 측정하는 방법에 대한 조건, 비정상 조건이 검색되면 경고하는 구성 요소 등을 모델링합니다. 통합 문서를 사용하면 사용자가 이 정보를 사용하여 풍부한 대화형 보고서를 만들 수 있습니다.

이 데이터 원본을 사용하여 쿼리 제어를 만들려면, **쿼리 유형** 드롭다운을 사용하여 Workload Health를 선택하고 구독, 리소스 그룹 또는 VM 리소스를 대상으로 선택합니다. 상태 필터 드롭다운을 사용하여 분석 요구 사항에 중요한 상태 인시던트의 관심 있는 하위 집합을 선택합니다.

![경고 쿼리 스크린샷](./media/workbooks-data-sources/workload-health.png)

## <a name="azure-resource-health"></a>Azure Resource Health

통합 문서는 Azure 리소스 상태를 가져오고 다른 데이터 원본과 결합하여 풍부한 대화형 상태 보고서를 만들 수 있도록 지원합니다.

이 데이터 원본을 사용하여 쿼리 제어를 만들려면, **쿼리 유형** 드롭다운을 사용하여 Azure health를 선택하고 리소스를 대상으로 지정합니다. 상태 필터 드롭다운을 사용하여 분석 요구 사항에 대한 리소스 문제의 관심 있는 하위 집합을 선택합니다.

![상태 필터 목록을 표시하는 경고 쿼리의 스크린샷](./media/workbooks-data-sources/resource-health.png)

## <a name="change-analysis-preview"></a>변경 분석(미리 보기)

[애플리케이션 변경 분석](../app/change-analysis.md)을 데이터 원본으로 사용하여 쿼리 제어를 만들려면, *데이터 원본* 드롭다운을 사용하여 *변경 분석(미리 보기)* 을 선택하고 단일 리소스를 선택합니다. 최근 14일에 대한 변경 내용을 표시할 수 있습니다. *수준* 드롭다운은 "중요", "보통", "잡음이 있음" 으로 변경 사항을 필터링하는 데 사용할 수 있으며, 이 드롭다운은 [드롭다운](workbooks-dropdowns.md) 형식의 통합 문서 매개 변수를 지원합니다.

> [!div class="mx-imgBorder"]
> ![변경 분석이 포함된 통합 문서의 스크린샷](./media/workbooks-data-sources/change-analysis-data-source.png)

## <a name="merge-data-from-different-sources"></a>여러 소스로의 데이터 통합

종종 인사이트 경험을 향상 시키는 다양한 원본의 데이터를 함께 가져와야 합니다. 이러한 예로는 관련 메트릭 데이터로 활성 경고 정보를 보강하는 것이 있습니다. 이를 통해 사용자는 영향(활성 경고)뿐만 아니라 잠재적 원인(예: 높은 CPU 사용량)도 볼 수 있습니다. 모니터링 도메인에는 종종 심사 및 진단 워크플로에 중요한 많은 상호 연결 가능한 데이터 원본이 있습니다.

통합 문서를 사용하면 다양한 데이터 원본에 대한 쿼리뿐만 아니라 데이터를 병합하거나 조인하여 풍부한 인사이트를 제공할 수 있는 간단한 제어도 제공합니다. `merge` 제어는 이를 수행하는 방법입니다.

아래 예제에서는 경고 데이터를 로그 분석 VM 성능 데이터와 결합하여 풍부한 인사이트 표를 가져옵니다.

> [!div class="mx-imgBorder"]
> ![경고 및 로그 분석 데이터를 결합하는 병합 제어를 포함하는 통합 문서의 스크린샷](./media/workbooks-data-sources/merge-control.png)

통합 문서는 다음과 같은 다양한 병합을 지원합니다.

* 내부 고유 조인
* 완전 내부 조인
* 완전 외부 조인
* 왼쪽 우선 외부 조인
* 오른쪽 우선 외부 조인
* 왼쪽 우선 부분 조인
* 오른쪽 우선 부분 조인
* 왼쪽 우선 안티 조인
* 오른쪽 우선 안티 조인
* Union
* 테이블 복제

## <a name="json"></a>JSON

JSON 공급자를 사용하면 고정적인 JSON 콘텐츠에서 쿼리 결과를 만들 수 있습니다. 이는 가장 일반적으로 매개 변수에서 사용되어 정적 값의 드롭다운 매개 변수를 만듭니다. 단순 JSON 배열 또는 개체는 그리드 행과 열로 자동으로 변환됩니다.  보다 구체적인 동작을 위해, 결과 탭 및 JSONPath 설정을 사용하여 열을 구성할 수 있습니다.

이 공급자는 [JSONPath](workbooks-jsonpath.md)를 지원합니다.

## <a name="alerts-preview"></a>경고(미리 보기)

> [!NOTE]
> Azure 경고 정보를 쿼리하는 제안된 방법은 `AlertsManagementResources` 테이블을 쿼리하여 [Azure Resource Graph](#azure-resource-graph) 데이터 원본을 사용하는 것입니다.
>
> [Azure Resource Graph 테이블 참조](../../governance/resource-graph/reference/supported-tables-resources.md) 또는 [경고 템플릿](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Azure%20Resources/Alerts/Alerts.workbook)에서 사례를 참조하세요.
>
> 작성자가 ARG 사용으로 전환하는 동안 경고 데이터 원본은 일정 시간 동안 계속 사용할 수 있습니다. 템플릿에서 이 데이터 원본을 사용하지 않는 것이 좋습니다. 

사용자는 통합 문서를 통해 해당 리소스와 관련된 활성 경고를 시각화할 수 있습니다. 제한 사항: 경고 데이터 원본에는 리소스를 쿼리하기 위해 구독에 대한 읽기 권한이 필요하며, 최신 종류의 경고가 표시되지 않을 수 있습니다. 

이 데이터 원본을 사용하여 쿼리 제어를 만들려면, _데이터 원본_ 드롭다운을 사용하여 _경고(미리 보기)_ 를 선택하고 구독, 리소스 그룹 또는 리소스를 대상으로 지정합니다. 경고 필터 드롭다운을 사용하여 분석 요구 사항에 중요한 경고의 관심 있는 하위 집합을 선택합니다.

## <a name="custom-endpoint"></a>사용자 지정 엔드포인트

통합 문서는 외부 소스에서 데이터를 가져오는 기능을 지원합니다. 데이터가 Azure 외부에 있는 경우, 이 데이터 원본 유형을 사용하여 통합 문서로 해당 데이터를 가져올 수 있습니다.

이 데이터 원본을 사용하여 쿼리 제어를 만들려면, _데이터 원본_ 드롭다운을 사용하여 _Custom Endpoint_ 를 선택합니다. `Http method`, `url`, `headers`, `url parameters` 및/또는 `body`와 같은 적절한 매개 변수를 제공합니다. 데이터 원본에서 [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)를 지원하는지 확인합니다. 그렇지 않으면 요청이 실패합니다.

템플릿을 사용할 때 신뢰할 수 없는 호스트를 자동으로 호출하지 않으려면, 사용자가 사용된 호스트를 신뢰할 수 있음으로 표시해야 합니다. 이 작업은 _신뢰할 수 있음으로 추가_ 단추를 클릭하거나, 통합 문서 설정에서 해당 호스트를 신뢰할 수 있는 호스트로 추가하여 수행할 수 있습니다. 이러한 설정은 [IndexDb에 작업자를 지 원하는 브라우저](https://caniuse.com/#feat=indexeddb)에 저장됩니다.

> [!NOTE]
> 모든 통합 문서 사용자에게 표시되므로 필드(`headers`, `parameters`, `body`, `url`)에는 비밀을 쓰지 마십시오.

이 공급자는 [JSONPath](workbooks-jsonpath.md)를 지원합니다.

## <a name="next-steps"></a>다음 단계

* 시작 다양한 시각화 옵션을 갖춘 통합 문서에 대해 자세히 알아보기를 [시작하세요](./workbooks-overview.md#visualizations).
* 통합 문서 리소스에 대한 액세스를 [제어](./workbooks-access-control.md) 및 공유합니다.
* [Log Analytics 쿼리 최적화 팁](../logs/query-optimization.md)