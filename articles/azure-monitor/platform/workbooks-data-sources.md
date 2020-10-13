---
title: 통합 문서 데이터 원본 Azure Monitor | Microsoft 문서
description: 여러 데이터 원본에서 빌드된 미리 작성 된 사용자 지정 매개 변수화 된 Azure Monitor 통합 문서를 사용 하 여 복잡 한 보고 간소화
services: azure-monitor
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: mbullwin
ms.openlocfilehash: bbd231ca527b4c01509230e839b97187de29febd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91825741"
---
# <a name="azure-monitor-workbooks-data-sources"></a>Azure Monitor 통합 문서 데이터 원본

통합 문서는 많은 수의 데이터 원본과 호환 됩니다. 이 문서에서는 Azure Monitor 통합 문서에 대해 현재 사용할 수 있는 데이터 원본을 안내 합니다.

## <a name="logs"></a>로그

통합 문서는 다음 원본에서 로그를 쿼리할 수 있습니다.

* Azure Monitor 로그 (Application Insights 리소스 및 Log Analytics 작업 영역)
* 리소스 중심 데이터 (활동 로그)

통합 문서 작성자는 기본 리소스 데이터를 변환 하는 KQL 쿼리를 사용 하 여 텍스트, 차트 또는 표로 시각화할 수 있는 결과 집합을 선택할 수 있습니다.

![통합 문서 로그 보고서 인터페이스 스크린샷](./media/workbooks-overview/logs.png)

통합 문서 작성자는 완전히 통합 된 풍부한 보고 환경을 만들어 여러 리소스에 대해 쉽게 쿼리할 수 있습니다.

## <a name="metrics"></a>메트릭

Azure 리소스는 통합 문서를 통해 액세스할 수 있는 [메트릭을](data-platform-metrics.md) 내보냅니다. 대상 리소스, 원하는 메트릭 및 집계를 지정 하는 데 사용할 수 있는 특수 한 컨트롤을 통해 통합 문서에서 메트릭을 액세스할 수 있습니다. 그런 다음이 데이터를 차트 또는 표로 그릴 수 있습니다.

![Cpu 사용률에 대 한 통합 문서 메트릭 차트의 스크린샷](./media/workbooks-overview/metrics-graph.png)

![통합 문서 메트릭 인터페이스 스크린샷](./media/workbooks-overview/metrics.png)

## <a name="azure-resource-graph"></a>Azure Resource Graph

통합 문서는 인수 (Azure 리소스 그래프)를 사용 하 여 리소스 및 해당 메타 데이터 쿼리를 지원 합니다. 이 기능은 주로 보고서에 대 한 사용자 지정 쿼리 범위를 작성 하는 데 사용 됩니다. 리소스 범위는 인수가 지 원하는 KQL 하위 집합을 통해 표현 되며, 일반적인 사용 사례에는 종종 충분 합니다.

쿼리 컨트롤에서이 데이터 원본을 사용 하도록 하려면 쿼리 유형 드롭다운을 사용 하 여 Azure 리소스 그래프를 선택 하 고 대상 구독을 선택 합니다. 쿼리 컨트롤을 사용 하 여 흥미로운 리소스 하위 집합을 선택 하는 ARG KQL를 추가 합니다.

![Azure 리소스 그래프 KQL 쿼리 스크린샷](./media/workbooks-overview/azure-resource-graph.png)

## <a name="azure-resource-manager"></a>Azure 리소스 관리자

통합 문서는 Azure Resource Manager REST 작업을 지원 합니다. 이렇게 하면 고유한 권한 부여 헤더 토큰을 제공할 필요 없이 management.azure.com 끝점을 쿼리할 수 있습니다.

쿼리 컨트롤에서이 데이터 원본을 사용 하도록 하려면 데이터 원본 드롭다운을 사용 하 여 Azure Resource Manager을 선택 합니다. Http 메서드, url 경로, 헤더, url 매개 변수 및/또는 본문과 같은 적절 한 매개 변수를 제공 합니다.

> [!NOTE]
> `GET`, `POST` 및 `HEAD` 작업만 현재 지원 됩니다.

## <a name="azure-data-explorer"></a>Azure Data Explorer

이제 통합 문서에서 강력한 [Kusto](/azure/kusto/query/index) 쿼리 언어를 사용 하 여 [Azure 데이터 탐색기](/azure/data-explorer/) 클러스터에서 쿼리를 지원 합니다.   

![Kusto 쿼리 창의 스크린샷](./media/workbooks-overview/data-explorer.png)

## <a name="workload-health"></a>작업 상태

Azure Monitor에는 Windows 또는 Linux 게스트 운영 체제의 가용성과 성능을 사전에 모니터링 하는 기능이 있습니다. Azure Monitor는 주요 구성 요소 및 해당 관계, 이러한 구성 요소의 상태를 측정 하는 방법에 대 한 조건, 비정상 조건이 검색 되 면 경고 하는 구성 요소를 모델링 합니다. 통합 문서를 사용 하면 사용자가이 정보를 사용 하 여 풍부한 대화형 보고서를 만들 수 있습니다.

쿼리 컨트롤에서이 데이터 원본을 사용 하도록 하려면 **쿼리 유형** 드롭다운을 사용 하 여 작업 상태를 선택 하 고 구독, 리소스 그룹 또는 대상으로 할 VM 리소스를 선택 합니다. 상태 필터 삭제를 사용 하 여 분석 요구 사항에 대 한 중요 한 상태 인시던트 하위 집합을 선택 합니다.

![경고 쿼리 스크린샷](./media/workbooks-overview/workload-health.png)

## <a name="azure-resource-health"></a>Azure 리소스 상태

통합 문서는 Azure 리소스 상태를 가져오고 다른 데이터 원본과 결합 하 여 풍부한 대화형 상태 보고서를 만들 수 있도록 지원 합니다.

쿼리 컨트롤에서이 데이터 원본을 사용 하도록 하려면 **쿼리 유형** 드롭다운을 사용 하 여 Azure health를 선택 하 고 대상으로 할 리소스를 선택 합니다. 상태 필터 삭제를 사용 하 여 분석 요구 사항에 대 한 리소스 문제의 흥미로운 하위 집합을 선택 합니다.

![경고 쿼리 스크린샷](./media/workbooks-overview/resource-health.png)

## <a name="json"></a>JSON

JSON 공급자를 사용 하면 정적 JSON 콘텐츠에서 쿼리 결과를 만들 수 있습니다. 일반적으로 매개 변수에서 정적 값의 드롭다운 매개 변수를 만드는 데 사용 됩니다. 단순 JSON 배열 또는 개체는 표 형태 창의 행과 열로 자동으로 변환 됩니다.  보다 구체적인 동작을 위해 결과 탭 및 JSONPath 설정을 사용 하 여 열을 구성할 수 있습니다.

## <a name="alerts-preview"></a>경고(미리 보기)

> [!NOTE]
> Azure 경고 정보를 쿼리 하는 제안 된 방법은 테이블을 쿼리하여 [Azure 리소스 그래프](#azure-resource-graph) 데이터 원본을 사용 하는 것입니다 `AlertsManagementResources` .
>
> 예제는 [Azure 리소스 그래프 테이블 참조](../../governance/resource-graph/reference/supported-tables-resources.md)또는 [경고 템플릿](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Azure%20Resources/Alerts/Alerts.workbook) 을 참조 하세요.
>
> 만든이가 ARG를 사용 하도록 전환 하는 동안 경고 데이터 원본은 일정 시간 동안 계속 사용할 수 있습니다. 템플릿에서이 데이터 원본을 사용 하지 않는 것이 좋습니다. 

사용자는 통합 문서를 통해 해당 리소스와 관련 된 활성 경고를 시각화할 수 있습니다. 제한 사항: 경고 데이터 원본에는 리소스를 쿼리 하기 위해 구독에 대 한 읽기 권한이 필요 하며, 최신 종류의 경고가 표시 되지 않을 수 있습니다. 

쿼리 컨트롤에서이 데이터 원본을 사용 하도록 하려면 _데이터 원본_ 드롭다운을 사용 하 여 _경고 (미리 보기)_ 를 선택 하 고 대상으로 할 구독, 리소스 그룹 또는 리소스를 선택 합니다. 경고 필터 삭제를 사용 하 여 분석 요구 사항에 대 한 경고의 흥미로운 하위 집합을 선택 합니다.

## <a name="custom-endpoint"></a>사용자 지정 엔드포인트

통합 문서는 외부 소스에서 데이터를 가져오는 기능을 지원 합니다. 데이터가 Azure 외부에 있는 경우이 데이터 원본 유형을 사용 하 여 통합 문서로 가져올 수 있습니다.

쿼리 컨트롤에서이 데이터 원본을 사용 하도록 하려면 _데이터 원본_ 드롭다운을 사용 하 여 _사용자 지정 끝점_을 선택 합니다. `Http method`,, `url` `headers` `url parameters` 및/또는와 같은 적절 한 매개 변수를 제공 `body` 합니다. 데이터 원본에서 [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) 를 지원 하는지 확인 합니다. 그렇지 않으면 요청이 실패 합니다.

템플릿을 사용할 때 트러스트 되지 않은 호스트를 자동으로 호출 하지 않으려면 사용자가 사용 된 호스트를 신뢰할 수 있는 것으로 표시 해야 합니다. 이 작업은 _트러스트 된 것으로 추가_ 단추를 클릭 하거나 통합 문서 설정에서 신뢰할 수 있는 호스트로 추가 하 여 수행할 수 있습니다. 이러한 설정은 웹 작업자와 함께 IndexDb를 지 원하는 브라우저에 저장 되며 [여기](https://caniuse.com/#feat=indexeddb)에서 자세한 정보를 제공 합니다.

> [!NOTE]
> `headers` `parameters` `body` `url` 모든 통합 문서 사용자에 게 표시 되므로 모든 필드 (,,,)에는 비밀을 쓰지 마십시오.

## <a name="next-steps"></a>다음 단계

* 통합 문서에 대 한 자세한 내용은 다양 한 기능을 갖춘 시각화 옵션을 [시작](workbooks-visualizations.md) 하세요.
* 통합 문서 리소스에 대 한 액세스를 [제어](workbooks-access-control.md) 하 고 공유 합니다.
* [Log Analytics 쿼리 최적화 팁](../log-query/query-optimization.md)
