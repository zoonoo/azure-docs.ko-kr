---
title: 로그 데이터 수집
titleSuffix: Azure Cognitive Search
description: 진단 설정을 사용하도록 설정하여 로그 데이터를 수집하고 분석한 다음 Kusto 쿼리 언어를 사용하여 결과를 탐색합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 86e869bc08552ea11728c508486a4784eccf4042
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462367"
---
# <a name="collect-and-analyze-log-data-for-azure-cognitive-search"></a>Azure 인지 검색에 대한 로그 데이터 수집 및 분석

진단 또는 운영 로그는 Azure Cognitive Search의 자세한 작업에 대한 통찰력을 제공하며 서비스 및 워크로드 프로세스를 모니터링하는 데 유용합니다. 내부적으로 로그는 지원 티켓을 제출하는 경우 조사 및 분석에 충분한 정도의 짧은 시간 동안 백 엔드에 있습니다. 그러나 운영 데이터에 대한 자체 방향을 원하는 경우 로깅 정보가 수집되는 위치를 지정하도록 진단 설정을 구성해야 합니다.

로그 설정은 진단 및 운영 기록 보존에 유용합니다. 로깅을 사용하도록 설정하면 쿼리를 실행하거나 구조화된 분석을 위한 보고서를 작성할 수 있습니다.

다음 표는 데이터 수집 및 유지 옵션을 등록합니다.

| 리소스 | 사용 대상 |
|----------|----------|
| [로그 분석 작업 공간으로 보내기](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-resource-logs) | 이벤트 및 메트릭은 Log Analytics 작업 영역으로 전송되며, 자세한 정보를 반환하기 위해 포털에서 쿼리할 수 있습니다. 소개는 Azure [모니터 로그 시작 을 참조하세요.](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) |
| [Blob 스토리지가 있는 아카이브](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | 이벤트 및 메트릭은 Blob 컨테이너에 보관되고 JSON 파일에 저장됩니다. 로그는 시간/분단위로 매우 세분화될 수 있으며, 특정 인시던트를 조사하는 데 유용하지만 서술형 조사에는 유용하지 않습니다. JSON 편집기를 사용하여 원시 로그 파일 또는 Power BI를 보고 로그 데이터를 집계하고 시각화합니다.|
| [이벤트 허브로 스트리밍](https://docs.microsoft.com/azure/event-hubs/) | 이벤트 및 메트릭은 Azure 이벤트 허브 서비스로 스트리밍됩니다. 이 서비스는 매우 큰 로그에 대한 대체 데이터 수집 서비스로 선택합니다. |

Azure 모니터 로그와 Blob 저장소는 모두 무료 서비스로 사용할 수 있으므로 Azure 구독의 수명 동안 무료로 사용해 볼 수 있습니다. 애플리케이션 데이터 크기가 특정 제한을 초과하지 않으면 Application Insights 평가판을 등록하여 사용할 수 있습니다(자세한 내용은 [가격 페이지](https://azure.microsoft.com/pricing/details/monitor/) 참조).

## <a name="prerequisites"></a>사전 요구 사항

로그 분석 또는 Azure 저장소를 사용하는 경우 사전에 리소스를 만들 수 있습니다.

+ [로그 분석 작업 영역 만들기](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

+ [저장소 계정 만들기](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)

## <a name="enable-data-collection"></a>데이터 컬렉션 활성화

진단 설정은 기록된 이벤트 및 메트릭을 수집하는 방법을 지정합니다.

1. **모니터링** 아래에서 **진단 설정**을 선택합니다.

   ![진단 설정](./media/search-monitor-usage/diagnostic-settings.png "진단 설정")

1. +진단 **설정 추가** 선택

1. **로그 분석을**확인하고 작업 영역을 선택하고 **작업 로그** 및 **AllMetrics를 선택합니다.**

   ![데이터 수집 구성](./media/search-monitor-usage/configure-storage.png "데이터 수집 구성")

1. 설정을 저장합니다.

1. 로깅을 사용하도록 설정한 후 검색 서비스를 사용하여 로그 및 메트릭 생성을 시작합니다. 기록된 이벤트 및 메트릭을 사용할 수 있게 되기까지 시간이 걸릴 수 있습니다.

로그 분석의 경우 데이터를 사용할 수 있게 되기까지 몇 분이 지나고 Kusto 쿼리를 실행하여 데이터를 반환할 수 있습니다. 자세한 내용은 [쿼리 요청 모니터링을](search-monitor-logs.md)참조하십시오.

Blob 저장소의 경우 컨테이너가 Blob 저장소에 나타나기까지 1시간이 걸립니다. 시간 및 컨테이너당 하나의 Blob가 있습니다. 기록하거나 측정하는 활동이 있는 경우에만 컨테이너가 만들어집니다. 데이터가 스토리지 계정에 복사되면 해당 데이터가 JSON 형식으로 다음 두 컨테이너에 배치됩니다.

+ insights-logs-operationlogs: 검색 트래픽 로그인 경우
+ insights-metrics-pt1m: 메트릭인 경우

## <a name="query-log-information"></a>로그 정보 쿼리

진단 로그에서 두 테이블에는 Azure 인지 **검색에** 대한 로그 **및**메트릭이 포함되어 있습니다.

1. **모니터링에서** **로그를 선택합니다.**

1. 쿼리 창에 **AzureMetrics를 입력합니다.** 이 간단한 쿼리를 실행하여 이 테이블에서 수집된 데이터에 대해 잘 알고 있습니다. 테이블을 스크롤하여 메트릭 및 값을 봅니다. 상단에 레코드 수를 표시하고 서비스가 잠시 동안 메트릭을 수집한 경우 시간 간격을 조정하여 관리 가능한 데이터 집합을 얻을 수 있습니다.

   ![AzureMetrics 테이블](./media/search-monitor-usage/azuremetrics-table.png "AzureMetrics 테이블")

1. 다음 쿼리를 입력하여 테이블 형식 결과 집합을 반환합니다.

   ```
   AzureMetrics
    | project MetricName, Total, Count, Maximum, Minimum, Average
   ```

1. 이전 단계를 반복하여 **AzureDiagnostics로** 시작하여 정보를 위해 모든 열을 반환한 다음 더 흥미로운 정보를 추출하는 보다 선택적인 쿼리를 반복합니다.

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search" 
   ```

   ![Azure진단 테이블](./media/search-monitor-usage/azurediagnostics-table.png "Azure진단 테이블")

## <a name="log-schema"></a>로그 스키마

Azure 인지 검색 로그 데이터를 포함하는 데이터 구조는 아래 스키마를 준수합니다. 

Blob 저장소의 경우 각 Blob에는 로그 개체 배열을 포함하는 **레코드라는** 하나의 루트 개체가 있습니다. 각 Blob에는 동일한 시간 동안 발생한 모든 작업에 대한 레코드가 포함됩니다.

다음 표는 진단 로깅에 공통적인 필드의 일부 목록입니다.

| 이름 | Type | 예제 | 메모 |
| --- | --- | --- | --- |
| 시간 생성 |Datetime |"2018-12-07T00:00:43.6872559Z" |작업 타임스탬프 |
| resourceId |문자열 |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>  MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |ResourceId |
| operationName |문자열 |"Query.Search" |작업 이름 |
| operationVersion |문자열 |"2019-05-06" |사용된 api-version |
| category |문자열 |"OperationLogs" |constant |
| resultType |문자열 |"Success" |가능한 값: Success 또는 Failure |
| resultSignature |int |200 |HTTP 결과 코드 |
| durationMS |int |50 |밀리초 단위의 작업 기간 |
| properties |object |다음 테이블 참조 |데이터별 작업을 포함하는 개체 |

### <a name="properties-schema"></a>속성 스키마

아래 속성은 Azure 인지 검색에 만 해당합니다.

| 이름 | Type | 예제 | 메모 |
| --- | --- | --- | --- |
| Description_s |문자열 |"GET /indexes('content')/docs" |작업의 엔드포인트 |
| Documents_d |int |42 |처리된 문서 수 |
| IndexName_s |문자열 |"테스트 인덱스" |작업과 연결된 인덱스의 이름 |
| Query_s |문자열 |"?search=AzureSearch&$count=true&api 버전=2019-05-06" |쿼리 매개 변수 |

## <a name="metrics-schema"></a>메트릭 스키마

메트릭은 쿼리 요청에 대해 캡처되고 1분 간격으로 측정됩니다. 각 메트릭은 분당 최소, 최대 및 평균 값을 표시합니다. 자세한 내용은 [쿼리 요청 모니터링을](search-monitor-queries.md)참조하십시오.

| 이름 | Type | 예제 | 메모 |
| --- | --- | --- | --- |
| resourceId |문자열 |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |리소스 ID |
| metricName |문자열 |"Latency" |메트릭 이름 |
| time |Datetime |"2018-12-07T00:00:43.6872559Z" |작업의 타임스탬프 |
| average |int |64 |메트릭 시간 간격의 원시 샘플의 평균 값(메트릭에 따라 단위 단위) 또는 백분율입니다. |
| minimum |int |37 |메트릭 시간 간격의 원시 샘플의 최소 값(단위 단위)입니다. |
| maximum |int |78 |메트릭 시간 간격의 원시 샘플의 최대 값(단위 단위)입니다.  |
| total |int |258 |메트릭 시간 간격의 원시 샘플의 총 값(단위 단위)입니다.  |
| count |int |4 |노드에서 로그로 1분 간격 내에 로그로 내보내는 메트릭 수입니다.  |
| timegrain |문자열 |"PT1M" |ISO 8601에서 메트릭의 시간 그레인입니다. |

쿼리가 밀리초 단위로 실행되는 것이 일반적이므로 초를 측정하는 쿼리만 QPS와 같은 메트릭에 표시됩니다.

초당 **검색 쿼리** 메트릭의 경우 최소 값은 해당 분 동안 등록된 초당 검색 쿼리의 가장 낮은 값입니다. 최대값도 마찬가지입니다. 평균은 전체 분에 대한 집계입니다. 예를 들어 1분 이내에 SearchQuerysPerSecond의 최대값인 고부하의 1초, 평균 로드의 58초, 마지막으로 최소 쿼리가 하나인 쿼리 가 하나만 있는 1초의 패턴이 있을 수 있습니다.

**제한 검색 쿼리 비율의**경우 최소, 최대, 평균 및 합계모두 1분 동안의 총 검색 쿼리 수에서 제한된 검색 쿼리의 백분율과 같은 값을 가집니다.

## <a name="view-raw-log-files"></a>원시 로그 파일 보기

Blob 저장소는 로그 파일을 보관하는 데 사용됩니다. JSON 편집기를 사용하여 로그 파일을 볼 수 있습니다. 없는 경우 [Visual Studio Code](https://code.visualstudio.com/download)를 사용하는 것이 좋습니다.

1. Azure Portal에서 Storage 계정을 엽니다. 

2. 왼쪽 탐색 창에서 **Blob**을 클릭합니다. **sights-logs-operationlogs** 및 **inights-metrics-pt1m**이 표시됩니다. 이러한 컨테이너는 로그 데이터를 Blob 저장소로 내보낼 때 Azure Cognitive Search에 의해 만들어집니다.

3. .json 파일에 도달할 때까지 폴더 계층 구조를 따라 아래로 클릭합니다.  상황에 맞는 메뉴를 사용하여 파일을 다운로드합니다.

파일이 다운로드되면 JSON 편집기에서 파일을 열어 내용을 봅니다.

## <a name="next-steps"></a>다음 단계

아직 수행하지 않은 경우 검색 서비스 모니터링의 기본 을 검토하여 전체 범위의 감독 기능에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [Azure 인지 검색에서 작업 및 활동 모니터링](search-monitor-usage.md)