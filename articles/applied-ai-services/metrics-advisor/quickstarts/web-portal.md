---
title: '빠른 시작: Metrics Advisor 웹 포털'
titleSuffix: Azure Cognitive Services
description: Metrics Advisor 웹 포털 사용을 시작하는 방법을 알아봅니다.
services: cognitive-services
author: mrbullwinkle
ms.author: mbullwin
manager: nitinme
ms.date: 09/30/2020
ms.topic: quickstart
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.custom:
- mode-portal
ms.openlocfilehash: 324b84255bd5939940a7819c521a452c1c49c8e8
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114342491"
---
# <a name="quickstart-monitor-your-first-metric-using-the-web-portal"></a>빠른 시작: 웹 포털을 사용하여 첫 번째 메트릭 모니터링

Metrics Advisor 인스턴스를 프로비저닝할 때 API 및 웹 기반 작업 영역을 사용하여 서비스 작업을 수행할 수 있습니다. 웹 기반 작업 영역은 서비스를 신속하게 시작할 수 있는 편리한 방법입니다. 또한 설정을 구성하고, 모델을 사용자 지정하고, 근본 원인 분석을 수행하는 시각적 방법도 제공합니다. 

* 메트릭 데이터 온보딩
* 메트릭 및 시각화 보기
* 검색 구성 미세 조정
* 진단 인사이트 살펴보기
* 변칙 경고 만들기 및 구독

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
* Azure 구독이 있으면 Azure Portal에서 <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="Metrics Advisor 리소스를 생성"  target="_blank">Metrics Advisor 리소스를 생성</a>하여 Metrics Advisor 인스턴스를 배포합니다.  

    
> [!TIP]
> * Metrics Advisor 리소스를 배포하는 데 10~30분 정도 걸릴 수 있습니다. 성공적으로 배포되면 **리소스로 이동** 을 선택합니다.
> * REST API를 사용하여 서비스와 상호 작용하려는 경우에는 사용자가 만든 리소스의 키와 엔드포인트가 필요합니다. 이는 생성된 리소스의 **키 및 엔드포인트** 탭에서 찾을 수 있습니다.


이 문서에서는 SQL Database 예제를 사용하여 첫 번째 모니터를 만듭니다.

## <a name="sign-in-to-your-workspace"></a>작업 영역에 로그인

리소스를 만든 후 Active Directory 계정을 사용하여 [Metrics Advisor 포털](https://go.microsoft.com/fwlink/?linkid=2143774)에 로그인합니다. 방문 페이지에서 **디렉터리**, **구독**, 방금 만든 **작업 영역** 을 선택한 다음, **시작** 을 선택합니다. 시계열 데이터를 온보딩하려면 왼쪽 메뉴에서 **데이터 피드 추가** 를 선택합니다.

 
현재 사용 가능한 각 지역에서 하나의 Metrics Advisor 리소스를 만들 수 있습니다. 언제든지 Metrics Advisor 포털에서 작업 영역을 전환할 수 있습니다.


## <a name="onboard-time-series-data"></a>시계열 데이터 온보딩

Metrics Advisor는 SQL Database, Azure Data Explorer 및 Azure Table Storage 같은 다양한 데이터 원본에 대한 커넥터를 제공합니다. 데이터를 연결하는 단계는 커넥터마다 유사하지만 일부 구성 매개 변수는 다를 수 있습니다. 다양한 데이터 연결 설정은 [다양한 데이터 피드 원본 연결](../data-feeds-from-different-sources.md)을 참조하세요.

이 빠른 시작에서는 SQL Database를 예로 사용합니다. 동일한 단계에 따라 사용자 고유의 데이터를 수집할 수도 있습니다.


### <a name="data-schema-requirements-and-configuration"></a>데이터 스키마 요구 사항 및 구성

[!INCLUDE [data schema requirements](../includes/data-schema-requirements.md)]

### <a name="configure-connection-settings-and-query"></a>연결 설정 및 쿼리 구성

시계열 데이터 원본에 연결하여 [데이터 피드를 추가](../how-tos/onboard-your-data.md)합니다. 다음 매개 변수를 선택하여 시작합니다.

* **원본 유형**: 시계열 데이터가 저장되는 데이터 원본의 유형입니다.
* **세분성**: 시계열 데이터의 연속 데이터 요소 간 간격입니다(예: 연도별, 월별, 매일). 사용자 지정 가능한 최저 간격은 60초입니다.
* **데이터 수집 시작 시간(UTC)** : 수집할 첫 번째 타임스탬프의 시작 시간입니다. 


<!-- Next, specify the **Connection string** with the credentials for your data source, and a custom **Query**, see [how to write a valid query](../tutorials/write-a-valid-query.md) for more information. -->

:::image type="content" source="../media/connection-settings.png" alt-text="연결 설정" lightbox="../media/connection-settings.png":::


### <a name="load-data"></a>데이터 로드

연결 문자열과 쿼리 문자열을 입력한 후 **데이터 로드** 를 선택합니다. 이 작업 내에서 Metrics Advisor는 연결과 데이터 로드 권한, 쿼리에서 사용되어야 하는 필수 매개 변수(@IntervalStart 및 @IntervalEnd), 데이터 원본의 열 이름을 확인합니다. 

이 단계에서 오류가 발생하는 경우 다음을 확인합니다.
1. 먼저 연결 문자열이 유효한지 확인합니다. 
2. 또한 충분한 권한이 있는지, 수집 작업자 IP 주소에 액세스 권한이 부여되었는지 확인합니다.
3. 쿼리에서 필수 매개 변수(@IntervalStart 및 @IntervalEnd)가 사용되었는지도 확인합니다. 

### <a name="schema-configuration"></a>스키마 구성

쿼리를 실행하여 데이터가 로드되고 아래와 같이 표시되면 적절한 필드를 선택합니다.


|선택 영역  |설명  |참고  |
|---------|---------|---------|
|**Timestamp**     | 데이터 요소의 타임스탬프. 생략하면 Metrics Advisor가 데이터 요소가 수집된 시간의 타임스탬프를 대신 사용합니다. 각 데이터 피드에 최대 하나의 열을 타임스탬프로 지정할 수 있습니다.        | 선택 사항입니다. 최대 하나의 열에 지정해야 합니다.       |
|**측정값**     |  데이터 피드의 숫자 값. 각 데이터 피드에 여러 측정값을 지정할 수 있지만 하나 이상의 열을 측정값으로 선택해야 합니다.        | 하나 이상의 열에 지정해야 합니다.        |
|**크기**     | 범주별 값. 여러 값의 조합이 특정 단일 차원 시계열(예: 국가, 언어, 테넌트)을 식별합니다. 0개 또는 임의 개수의 열을 차원으로 선택할 수 있습니다. 참고: 문자열이 아닌 열을 차원으로 선택하는 경우에는 차원 폭발에 주의해야 합니다. | 선택 사항입니다.        |
|**무시**     | 선택된 열을 무시하세요.        | 선택 사항입니다. 쿼리를 사용한 데이터 가져오기를 지원하는 데이터 원본의 경우 ‘무시’ 옵션이 없습니다.       |


:::image type="content" source="../media/schema-configuration.png" alt-text="스키마 구성" lightbox="../media/schema-configuration.png":::

스키마를 구성한 후 **스키마 확인** 을 선택합니다. 이 작업 내에서 Metrics Advisor는 다음 검사를 수행합니다.
- 쿼리된 데이터의 타임스탬프가 단일 간격에 속하는지 여부 
- 하나의 메트릭 간격 내에서 동일한 차원 조합에 대해 반환된 중복 값이 있는지 여부  

### <a name="automatic-roll-up-settings"></a>자동 롤업 설정

> [!IMPORTANT]
> **근본 원인 분석** 및 기타 진단 기능을 사용하도록 설정하려면 '자동 롤업 설정'을 구성해야 합니다. 사용하도록 설정된 자동 롤업 설정은 변경할 수 없습니다.

Metrics Advisor는 수집 중에 각 차원에서 집계(SUM/MAX/MIN ...)를 자동으로 수행한 후 근본 원인 분석과 기타 진단 기능에서 사용할 계층 구조를 빌드할 수 있습니다. 자세한 내용은 [자동 롤업 설정](../how-tos/onboard-your-data.md#automatic-roll-up-settings)을 참조하세요.

작업 영역에 표시될 데이터 피드의 사용자 지정 이름을 지정합니다. **제출** 을 선택합니다. 

## <a name="tune-detection-configuration"></a>검색 구성 튜닝

데이터 피드가 추가되면 Metrics Advisor가 지정된 시작 날짜부터 메트릭 데이터 수집을 시도합니다. 데이터를 완전히 수집하려면 시간이 좀 걸리며, 데이터 피드 페이지 맨 위에 있는 **수집 진행률** 을 선택하여 수집 상태를 볼 수 있습니다. 데이터가 수집되면 Metrics Advisor가 검색을 적용하고 원본에서 새 데이터를 계속 모니터링합니다.

검색이 적용되면 데이터 피드에 나열된 메트릭 중 하나를 선택하여 **메트릭 세부 정보 페이지** 를 찾아 다음을 수행합니다. 
- 이 메트릭의 모든 시계열 조각에 대한 시각화 보기
- 예상 결과를 충족하도록 검색 구성 업데이트
- 검색된 변칙에 대한 알림 설정

:::image type="content" source="../media/metric-details.png" alt-text="메트릭 세부 정보" lightbox="../media/metric-details.png":::

## <a name="view-the-diagnostic-insights"></a>진단 인사이트 보기

검색 구성을 튜닝한 후 발견된 변칙에는 데이터의 실제 변칙이 반영되어야 합니다. Metrics Advisor는 다차원 메트릭 분석을 수행하여 특정 차원에 대한 근본 원인을 찾고 “메트릭 그래프”를 사용하여 메트릭 간 분석도 수행합니다. 

진단 인사이트를 보려면 검색된 변칙을 나타내는 시계열 시각화의 빨간색 점을 선택합니다. 인시던트 분석 페이지에 대한 링크가 포함된 창이 표시됩니다. 

:::image type="content" source="../media/incident-link.png" alt-text="인시던트 링크" lightbox="../media/incident-link.png":::

링크를 선택하면 풍부한 진단 인사이트로 관련 변칙 그룹을 분석하는 인시던트 분석 페이지로 피벗됩니다. 인시던트 진단에는 다음 세 가지 주요 단계가 있습니다.

### <a name="check-summary-of-current-incident"></a>현재 인시던트 요약 확인

맨 위에는 기본 정보, 작업 및 추적, 분석된 근본 원인을 포함하는 요약이 있습니다. 기본 정보에는 “영향을 받는 상위 계열”과 다이어그램, “영향 시작 및 종료 시간”, “인시던트 심각도”, “포함된 총 변칙 수”가 포함됩니다.

분석된 근본 원인은 자동 분석 결과입니다. Metrics Advisor는 동일한 타임스탬프의 다양한 차원 값을 사용하여 하나의 메트릭 내에서 시계열에 캡처된 모든 변칙을 분석합니다. 그런 다음, 상관 관계를 파악하고 클러스터링을 통해 관련 변칙을 그룹화하고 근본 원인 조언을 생성합니다.

:::image type="content" source="../media/diagnostics/incident-summary.png" alt-text="인시던트 진단 요약" lightbox="../media/diagnostics/incident-summary.png":::

이 결과를 통해 현재 비정상 상태와 인시던트 및 가장 가능성이 높은 근본 원인의 영향을 한눈에 쉽게 확인할 수 있습니다. 따라서 즉각적인 조치를 통해 인시던트를 가능한 한 빨리 해결할 수 있습니다. 

### <a name="view-cross-dimension-diagnostic-insights"></a>차원 간 진단 인사이트 보기

기본 정보와 자동 분석 인사이트를 가져온 후 **“진단 트리”** 를 사용하여 전체적인 방식으로 동일한 메트릭 내 다른 차원의 비정상 상태에 대한 자세한 정보를 얻을 수 있습니다.

여러 차원이 있는 메트릭의 경우 Metrics Advisor는 “진단 트리”라는 계층 구조로 시계열을 분류합니다. 예를 들어 “revenue” 메트릭은 “region”과 “category”라는 두 가지 차원에서 모니터링됩니다. 구체적인 차원 값뿐 아니라 **“SUM”** 과 같은 **집계된** 차원 값도 있어야 합니다. 그런 다음, “region” = **“SUM”** 및 “category” = **“SUM”** 의 시계열이 트리 내 루트 노드로 분류됩니다. **“SUM”** 차원에 캡처된 변칙이 있을 때마다 드릴다운하고 분석하여 부모 노드 변칙에 가장 많이 기여한 특정 차원 값을 찾을 수 있습니다. 각 노드를 클릭하여 자세한 정보를 펼칩니다.

:::image type="content" source="../media/diagnostics/cross-dimension-diagnostic.png" alt-text="차원 간 인시던트 진단 보기" lightbox="../media/diagnostics/cross-dimension-diagnostic.png":::

### <a name="view-cross-metrics-diagnostic-insights-using-metrics-graph"></a>“메트릭 그래프”를 사용하여 메트릭 간 진단 인사이트 보기

단일 메트릭의 비정상 상태를 확인하여 문제를 분석하기 어려운 경우도 있으며, 여러 메트릭의 상관 관계를 파악해야 합니다. 고객은 메트릭 간 관계를 나타내는 “메트릭 그래프”를 구성할 수 있습니다. 위의 차원 간 진단 결과를 활용하면 근본 원인이 특정 차원 값으로 제한됩니다. 그런 다음, “메트릭 그래프”를 사용하고 분석된 근본 원인 차원으로 필터링하여 다른 메트릭의 변칙 상태를 확인합니다.
링크를 클릭하면 풍부한 진단 정보를 통해 해당 변칙을 분석하는 인시던트 분석 페이지로 피벗됩니다. 인시던트 세부 정보 페이지에는 인시던트 진단의 세 가지 주요 단계에 해당하는 세 가지 섹션이 있습니다. 

:::image type="content" source="../media/diagnostics/cross-metrics-analysis.png" alt-text="메트릭 간 인시던트 진단 분석" lightbox="../media/diagnostics/cross-metrics-analysis.png":::

하지만 차원별로 변칙을 드릴다운하고, 유사한 변칙을 보고, 메트릭을 비교하는 추가 기능을 활용하여 더 많은 진단 인사이트 간에 피벗할 수도 있습니다. 자세한 내용은 [방법: 인시던트 진단](../how-tos/diagnose-an-incident.md)을 참조하세요. 

## <a name="get-notified-when-new-anomalies-are-found"></a>새 변칙이 발견되면 알림 받기

데이터에서 변칙이 검색될 때 경고를 받으려면 하나 이상의 메트릭에 대한 구독을 만들면 됩니다. Metrics Advisor는 후크를 사용하여 경고를 보냅니다. 이메일 후크, 웹후크 및 Azure DevOps 같은 3가지 유형의 후크가 지원됩니다. 웹후크를 예로 들어 보겠습니다. 

### <a name="create-a-web-hook"></a>웹후크 만들기

웹후크는 발견된 변칙을 Metrics Advisor 서비스에서 프로그래매틱 방식으로 가져오기 위한 진입점으로, 경고가 트리거되면 사용자가 제공한 API를 호출합니다. 후크를 만드는 방법에 대한 자세한 내용은 [방법: 후크를 사용하여 경고를 구성하고 알림 가져오기](../how-tos/alerts.md#create-a-hook)의 **후크 만들기** 섹션을 참조하세요. 

### <a name="configure-alert-settings"></a>경고 설정 구성

후크를 만들면 경고 설정에 따라 어떤 경고 알림이 어떻게 전송되는지 결정됩니다. 각 메트릭에 여러 경고 설정을 지정할 수 있습니다. 두 가지 중요한 설정은 포함할 변칙을 지정하는 **경고 대상** 과 경고에 포함할 변칙을 정의하는 **변칙 필터링 옵션** 입니다. 자세한 내용은 **경고 설정 추가 또는 편집** 섹션([방법: 후크를 사용하여 경고를 구성하고 알림 가져오기](../how-tos/alerts.md#add-or-edit-alert-settings))을 참조하세요.


## <a name="next-steps"></a>다음 단계

- [데이터 피드 온보딩](../how-tos/onboard-your-data.md)
    - [데이터 피드 관리](../how-tos/manage-data-feeds.md)
    - [여러 데이터 원본에 대한 구성](../data-feeds-from-different-sources.md)
- [REST API 또는 클라이언트 라이브러리 사용](./rest-api-and-client-library.md)
- [메트릭을 구성하고 검색 구성 미세 조정](../how-tos/configure-metrics.md)
