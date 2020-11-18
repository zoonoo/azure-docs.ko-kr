---
title: Log Analytics 자습서
description: 이 자습서에서 Azure Monitor의 Log Analytics 기능을 사용하여 로그 쿼리를 작성 및 실행하고 Azure Portal에서 결과를 분석하는 방법을 알아봅니다.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 10/07/2020
ms.openlocfilehash: bd6ba091b3715741af929613600a59511e7cdb0b
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94495426"
---
# <a name="log-analytics-tutorial"></a>Log Analytics 자습서
Log Analytics는 Azure Monitor Logs에서 수집된 데이터에서 로그 쿼리를 편집 및 실행하고 그 결과를 대화형으로 분석하는 Azure Portal의 도구입니다. Log Analytics 쿼리를 사용하여 특정 조건과 일치하는 레코드를 검색하고, 추세를 식별하고, 패턴을 분석하고, 데이터에 대한 다양한 인사이트를 제공할 수 있습니다. 

이 자습서에서는 Log Analytics 인터페이스를 안내하고 몇 가지 기본 쿼리를 시작하며 결과를 사용하는 방법을 보여줍니다. 다음을 학습합니다.

> [!div class="checklist"]
> * 로그 데이터 스키마 이해
> * 간단한 쿼리 작성 및 실행, 쿼리 시간 범위 수정
> * 쿼리 결과 필터링, 정렬 및 그룹화
> * 쿼리 결과의 시각적 개체 보기, 수정 및 공유
> * 쿼리와 결과의 저장, 로드, 내보내기 및 복사

> [!IMPORTANT]
> 이 자습서에서는 쿼리 자체로 작업하는 대신 Log Analytics의 기능을 사용하여 쿼리를 작성하고 실행합니다. Log Analytics 기능을 활용하여 쿼리를 하나 작성하고 또 다른 예제 쿼리를 사용합니다. 쿼리 구문을 알아보고 쿼리 자체를 직접 편집할 준비가 되면 [Kusto 쿼리 언어 자습서](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor)를 참조하세요. 이 자습서는 Log Analytics에서 편집하고 실행할 수 있는 몇 가지 예제 쿼리를 이 자습서에서 알아볼 몇 가지 기능을 활용하여 안내합니다.


## <a name="prerequisites"></a>필수 구성 요소
이 자습서에서는 샘플 쿼리를 지원하는 샘플 데이터가 많이 포함된 [Log Analytics 데모 환경](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade)을 사용합니다. 자체 Azure 구독을 사용할 수도 있지만 동일한 테이블에 데이터가 없을 수 있습니다.

## <a name="open-log-analytics"></a>Log Analytics 열기
[Log Analytics 데모 환경](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade)을 열거나 구독의 Azure Monitor 메뉴에서 **로그** 를 선택합니다. 이렇게 하면 초기 범위가 Log Analytics 작업 영역으로 설정됩니다. 즉, 쿼리가 이 작업 영역의 모든 데이터에서 선택됩니다. Azure 리소스의 메뉴에서 **로그** 를 선택하면 범위가 해당 리소스의 레코드로만 설정됩니다. 범위에 대한 자세한 내용은 [로그 쿼리 범위](scope.md)를 참조하세요.

범위는 화면의 왼쪽 위 모서리에서 볼 수 있습니다. 자체 환경을 사용하는 경우에는 다른 범위를 선택하는 옵션이 표시되지만 데모 환경에서는 이 옵션을 사용할 수 없습니다.

[![쿼리 범위](media/log-analytics-tutorial/scope.png)](media/log-analytics-tutorial/scope.png#lightbox)

## <a name="table-schema"></a>테이블 스키마
화면 왼쪽에는 **테이블** 탭이 있으며, 현재 범위에서 사용할 수 있는 테이블을 검사할 수 있습니다. 이러한 항목은 기본적으로 **솔루션** 별로 그룹화되지만 그룹화를 변경하거나 필터링할 수 있습니다. 

**로그 관리** 솔루션을 펼쳐서 **AzureActivity** 테이블을 찾습니다. 테이블을 펼쳐서 해당 스키마를 보거나 이름을 마우스로 가리켜서 추가 정보를 표시할 수 있습니다. 

[![테이블 뷰](media/log-analytics-tutorial/table-details.png)](media/log-analytics-tutorial/table-details.png#lightbox)

**자세한 정보** 를 클릭하면 각 테이블과 해당 열을 문서화하는 테이블 참조로 이동합니다. **데이터 미리 보기** 를 클릭하면 테이블에서 최근 레코드 몇 개를 빠르게 살펴볼 수 있습니다. 이 기능은 실제로 쿼리를 실행하기 전에 예상했던 데이터가 맞는지 확인하는 데 유용할 수 있습니다.

[![샘플 데이터](media/log-analytics-tutorial/sample-data.png)](media/log-analytics-tutorial/sample-data.png#lightbox)

## <a name="write-a-query"></a>쿼리 작성
이제 **AzureActivity** 테이블을 사용하여 쿼리를 작성하겠습니다. 이름을 두 번 클릭하여 쿼리 창에 추가합니다. 창에 직접 입력할 수도 있고 현재 범위 및 KQL 명령에서 테이블 이름을 완성하는 데 유용한 IntelliSense를 얻을 수도 있습니다.

작성할 수 있는 가장 간단한 쿼리입니다. 테이블의 모든 레코드를 반환합니다. **실행** 단추를 클릭하거나 쿼리 텍스트의 아무 곳에나 커서를 두고 Shift + Enter를 눌러서 실행합니다.

[![쿼리 결과](media/log-analytics-tutorial/query-results.png)](media/log-analytics-tutorial/query-results.png#lightbox)

결과가 있는 것이 보입니다. 쿼리에서 반환된 레코드 수가 오른쪽 아래 모서리에 표시됩니다. 

## <a name="filter"></a>필터

쿼리에 필터를 추가하여 반환되는 레코드 수를 줄이겠습니다. 왼쪽 창에서 **필터** 탭을 선택합니다. 쿼리 결과에 여러 열이 표시되어 결과를 필터링하는 데 사용할 수 있습니다. 이러한 열의 상위 값은 해당 값이 있는 레코드 수와 함께 표시됩니다. **CategoryValue** 에서 **관리** 를 클릭한 다음, **적용 및 실행** 을 클릭합니다. 

[![쿼리 창](media/log-analytics-tutorial/query-pane.png)](media/log-analytics-tutorial/query-pane.png#lightbox)

**where** 문이 선택한 값과 함께 쿼리에 추가됩니다. 이제 해당 값이 있는 레코드만 결과에 포함되기 때문에 레코드 수가 줄어드는 것을 볼 수 있습니다.

[![필터링된 쿼리 결과](media/log-analytics-tutorial/query-results-filter-01.png)](media/log-analytics-tutorial/query-results-filter-01.png#lightbox)


## <a name="time-range"></a>시간 범위
Log Analytics 작업 영역의 모든 테이블에는 **TimeGenerated** 라는 열이 있으며, 이것은 레코드가 생성된 시간입니다. 모든 쿼리에는 결과를 해당 범위 내의 **TimeGenerated** 값이 있는 레코드로 제한하는 시간 범위가 있습니다. 시간 범위는 쿼리에서 설정하거나 화면 맨 위에 있는 선택기로 설정할 수 있습니다.

기본적으로 쿼리는 지난 24시간 동안의 레코드를 반환합니다. **시간 범위** 드롭다운을 선택하고 **7일** 로 변경합니다. **실행** 을 다시 클릭하여 결과를 반환합니다. 결과가 반환되는 것을 볼 수 있지만, 모든 결과가 표시되는 것은 아니라는 메시지가 있습니다. Log Analytics는 최대 10,000개의 레코드를 반환할 수 있지만 쿼리에서 그보다 많은 레코드를 반환했기 때문입니다. 

[![시간 범위](media/log-analytics-tutorial/query-results-max.png)](media/log-analytics-tutorial/query-results-max.png#lightbox)


## <a name="multiple-query-conditions"></a>여러 쿼리 조건
다른 필터 조건을 추가하여 결과를 더 줄여 보겠습니다. 쿼리에는 원하는 만큼 필터를 포함하여 원하는 레코드 집합을 정확하게 목표할 수 있습니다. **ActivityStatusValue** 에서 **성공** 을 선택하고 **적용 및 실행** 을 클릭합니다. 

[![쿼리 결과 여러 필터](media/log-analytics-tutorial/query-results-filter-02.png)](media/log-analytics-tutorial/query-results-filter-02.png#lightbox)


## <a name="analyze-results"></a>결과 분석
Log Analytics는 쿼리 작성 및 실행을 돕는 것 외에도 결과로 작업할 수 있는 기능을 제공합니다. 먼저 레코드를 펼쳐서 모든 열의 값을 볼 수 있습니다.

[![레코드 펼치기](media/log-analytics-tutorial/expand-record.png)](media/log-analytics-tutorial/expand-record.png#lightbox)

열 이름을 클릭하면 해당 열을 기준으로 결과가 정렬됩니다. 옆에 있는 필터 아이콘을 클릭하면 필터 조건을 제공할 수 있습니다. 쿼리를 다시 실행하면 필터가 지워진다는 점을 제외하면, 쿼리 자체에 필터 조건을 추가하는 것과 유사합니다. 대화형 분석의 일환으로 레코드 세트를 빠르게 분석하려는 경우 이 방법을 사용합니다.

예를 들어 **CallerIpAddress** 열에 필터를 설정하여 레코드를 단일 호출자로 제한합니다. 

[![쿼리 결과 필터](media/log-analytics-tutorial/query-results-filter.png)](media/log-analytics-tutorial/query-results-filter.png#lightbox)

결과를 필터링하는 대신 특정 열을 기준으로 레코드를 그룹화할 수 있습니다. 방금 만든 필터를 지운 다음, **Group columns**(열 그룹화) 슬라이더를 켭니다. 

[![열 그룹화](media/log-analytics-tutorial/query-results-group-columns.png)](media/log-analytics-tutorial/query-results-group-columns.png#lightbox)

이제 **CallerIpAddress** 열을 그룹화 행으로 끌어옵니다. 이 열을 기준으로 결과가 구성되며, 각 그룹을 접으면 분석하는 데 유용할 수 있습니다.

[![그룹화된 쿼리 결과](media/log-analytics-tutorial/query-results-grouped.png)](media/log-analytics-tutorial/query-results-grouped.png#lightbox)

## <a name="work-with-charts"></a>차트 작업
차트에서 볼 수 있는 숫자 데이터를 사용하는 쿼리를 살펴보겠습니다. 쿼리를 작성하는 대신 예제 쿼리를 선택합니다.

왼쪽 창에서 **쿼리** 를 클릭합니다. 이 창에는 쿼리 창에 추가할 수 있는 예제 쿼리가 포함되어 있습니다. 자체 작업 영역을 사용하는 경우에는 여러 범주의 다양한 쿼리가 있을 수 있지만, 데모 환경을 사용하는 경우에는 단일 **Log Analytics 작업 영역** 범주만 보일 수 있습니다. 이 범주를 펼치면 범주의 쿼리가 보입니다.

**Request Count by ResponseCode**(ResponseCode별 요청 수)라는 쿼리를 클릭합니다. 그러면 쿼리 창에 쿼리가 추가됩니다. 새 쿼리는 빈 줄로 다른 쿼리와 분리됩니다. KQL의 쿼리는 빈 줄이 있으면 종료되므로 별도의 쿼리로 표시됩니다. 

[![새 쿼리](media/log-analytics-tutorial/example-query.png)](media/log-analytics-tutorial/example-query.png#lightbox)

현재 쿼리는 커서가 있는 쿼리입니다. 첫 번째 쿼리가 현재 쿼리라고 강조 표시된 것을 볼 수 있습니다. 새 쿼리의 아무 곳이나 클릭하여 선택한 다음, **실행** 단추를 클릭하여 실행합니다.

[![쿼리 결과 차트](media/log-analytics-tutorial/example-query-output-chart.png)](media/log-analytics-tutorial/example-query-output-chart.png#lightbox)

이번 출력은 마지막 쿼리와 같은 테이블이 아닌 차트입니다. 예제 쿼리가 끝에 [render](/azure/data-explorer/kusto/query/renderoperator?pivots=azuremonitor) 명령을 사용하기 때문입니다. 차트 작업에는 다양한 옵션(예: 차트를 다른 형식으로 변경)이 있습니다.

**결과** 를 선택하면 쿼리 출력을 테이블로 볼 수 있습니다. 

[![쿼리 결과 테이블](media/log-analytics-tutorial/example-query-output-table.png)](media/log-analytics-tutorial/example-query-output-table.png#lightbox)



## <a name="next-steps"></a>다음 단계

Log Analytics를 사용하는 방법을 파악했으면 이제 로그 쿼리를 사용하는 방법에 대한 자습서를 살펴보세요.
> [!div class="nextstepaction"]
> [Azure Monitor 로그 쿼리 작성](get-started-queries.md)
