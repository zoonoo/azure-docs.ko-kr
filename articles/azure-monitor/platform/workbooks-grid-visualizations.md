---
title: Azure Monitor 통합 문서 그리드 시각화
description: 모든 Azure Monitor 통합 문서 그리드 시각화에 대해 알아봅니다.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: 114245030ba2356861b83a580f103d3b84c0eae3
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89664280"
---
# <a name="grid-visualizations"></a>그리드 시각화

표 또는 테이블은 사용자에 게 데이터를 제공 하는 일반적인 방법입니다. 통합 문서를 사용 하면 사용자가 표의 열에 대해 개별적으로 스타일을 지정 하 여 보고서에 대 한 풍부한 UI를 제공할 수 있습니다.

아래 예제에서는 아이콘, 열 지도 및 spark 막대를 결합 하 여 복잡 한 정보를 제공 하는 표를 보여 줍니다. 통합 문서에는 정렬, 검색 상자 및 분석 이동 단추도 제공 됩니다.

[![로그 기반 그리드 스크린샷](./media/workbooks-grid-visualizations/grid.png)](./media/workbooks-grid-visualizations/grid.png#lightbox)

## <a name="adding-a-log-based-grid"></a>로그 기반 그리드 추가

1. **편집** 도구 모음 항목을 클릭 하 여 통합 문서를 편집 모드로 전환 합니다.
2. **쿼리 추가** 링크를 사용 하 여 통합 문서에 로그 쿼리 컨트롤을 추가 합니다.
3. **로그**, 리소스 유형 (예: Application Insights) 및 대상으로 사용할 리소스를 선택 합니다.
4. 쿼리 편집기를 사용 하 여 분석에 대 한 KQL (예: 메모리가 임계값 미만인 Vm)를 입력할 수 있습니다.
5. 시각화를 **그리드** 로 설정
6. 필요한 경우 시간 범위, 크기, 색상표 및 범례와 같은 다른 매개 변수를 설정 합니다.

[![로그 기반 그리드 쿼리 스크린샷](./media/workbooks-grid-visualizations/grid-query.png)](./media/workbooks-grid-visualizations/grid-query.png#lightbox)

## <a name="log-chart-parameters"></a>로그 차트 매개 변수

| 매개 변수 | 설명 | 예제 |
| ------------- |:-------------|:-------------|
| `Query Type` | 사용할 쿼리 유형입니다. | 로그, Azure 리소스 그래프 등 |
| `Resource Type` | 대상으로 할 리소스 형식입니다. | Application Insights, Log Analytics 또는 Azure-first |
| `Resources` | 메트릭 값을 가져올 리소스 집합입니다. | MyApp1 |
| `Time Range` | 로그 차트를 볼 시간 창입니다. | 지난 시간, 지난 24 시간 등 |
| `Visualization` | 사용할 시각화입니다. | 그리드 |
| `Size` | 컨트롤의 세로 크기입니다. | Small, medium, large 또는 full |
| `Query` | 차트 시각화에 필요한 형식으로 데이터를 반환 하는 모든 KQL 쿼리입니다. | _요청 \| 요약 요청 = 개수 () 이름_ |

## <a name="simple-grid"></a>간단한 그리드

통합 문서는 KQL 결과를 간단한 테이블로 렌더링할 수 있습니다. 아래 표에서는 앱에서 요청당 요청 수와 유형별 사용자 수를 보여 줍니다.

```kusto
requests
| where name !endswith('.eot')
| summarize Requests = count(), Users = dcount(user_Id) by name
| order by Requests desc
```

[![편집 모드의 로그 기반 그리드 스크린샷](./media/workbooks-grid-visualizations/log-chart-simple-grid.png)](./media/workbooks-grid-visualizations/log-chart-simple-grid.png#lightbox)

## <a name="grid-styling"></a>표 스타일 지정

일반 테이블은 데이터를 표시 하는 반면 읽기는 어려울 수 있으며 정보를 항상 명확 하 게 알 수는 없습니다. 표 스타일을 지정 하면 데이터를 보다 쉽게 읽고 해석할 수 있습니다.

다음은 열 지도으로 스타일이 지정 된 이전 섹션의 표와 동일 합니다.

[![열 지도으로 스타일이 지정 된 열이 있는 로그 기반 그리드 스크린샷](./media/workbooks-grid-visualizations/log-chart-grid-heatmap.png)](./media/workbooks-grid-visualizations/log-chart-grid-heatmap.png#lightbox)

막대 스타일로 지정 된 것과 동일한 그리드: 세로 [ ![ 막대로 스타일을 지정 하는 로그 기반 그리드 스크린샷](./media/workbooks-grid-visualizations/log-chart-grid-bar.png)](./media/workbooks-grid-visualizations/log-chart-grid-bar.png#lightbox)

### <a name="styling-a-grid-column"></a>표 형태 창의 열 스타일 지정

1. 쿼리 컨트롤 도구 모음에서 **열 설정** 단추를 선택 합니다.
2. *열 편집 설정*에서 스타일의 열을 선택 합니다.
3. 열을 스타일 설정 하려면 열 렌더러 (예: 열 지도, 가로 막대형, 가로 막대형 등)와 관련 설정을 선택 합니다.

다음은 *요청* 열을 막대로 스타일을 표시 하는 예제입니다.

[![막대형으로 스타일이 지정 된 요청 열이 있는 로그 기반 표의 스크린샷](./media/workbooks-grid-visualizations/log-chart-grid-column-settings-start.png)](./media/workbooks-grid-visualizations/log-chart-grid-column-settings-start.png#lightbox)

### <a name="column-renderers"></a>열 렌더러

| 열 렌더러 | 설명 | 추가 옵션 |
|:------------- |:-------------|:-------------|
| `Automatic` | 기본값은 열 유형을 기반으로 가장 적합 한 렌더러를 사용 합니다.  |  |
| `Text` | 열 값을 텍스트로 렌더링 합니다. | |
| `Right Aligned` | 오른쪽 맞춤 이라는 점을 제외 하 고 텍스트와 유사 합니다. | |
| `Date/Time` | 읽을 수 있는 날짜 시간 문자열을 렌더링 합니다. | |
| `Heatmap` | 셀의 값을 기준으로 표 셀의 색을 설정 합니다. | 크기 조정에 사용 되는 최소/최대 값입니다. |
| `Bar` | 셀의 값을 기준으로 셀 옆에 막대를 렌더링 합니다. | 크기 조정에 사용 되는 최소/최대 값입니다. |
| `Bar underneath` | 셀의 값을 기준으로 셀의 아래쪽 근처에 있는 막대를 렌더링 합니다. | 크기 조정에 사용 되는 최소/최대 값입니다. |
| `Composite bar` | 해당 행의 지정 된 열을 사용 하 여 복합 막대를 렌더링 합니다. 자세한 내용은 [복합 막대](workbooks-composite-bar.md) 를 참조 하세요. | 막대를 렌더링 하는 데 사용 되는 색이 있는 열 및 막대의 위쪽에 표시 되는 레이블 |
| `Spark bars` | 셀의 동적 배열 값을 기준으로 셀에 spark 막대를 렌더링 합니다. 예를 들어 위쪽의 스크린샷에서 추세 열이 있습니다. | 크기 조정에 사용 되는 최소/최대 값입니다. |
| `Spark lines` | 셀의 동적 배열 값을 기준으로 셀에서 spark 선을 렌더링 합니다. | 크기 조정에 사용 되는 최소/최대 값입니다. |
| `Icon` | 셀의 텍스트 값을 기준으로 아이콘을 렌더링 합니다. 지원 되는 값은,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,, 등 `cancelled` `critical` `disabled` `error` `failed` `info` `none` `pending` `stopped` `question` `success` `unknown` `warning` `uninitialized` `resource` `up` `down` `left` `right` `trendup` `trenddown` `4` `3` `2` `1` `Sev0` `Sev1` `Sev2` `Sev3` `Sev4` `Fired` `Resolved` `Available` `Unavailable` `Degraded` `Unknown` `Blank` 입니다.|  |
| `Link` | 구성 가능한 작업을 클릭 하거나 수행할 때 링크를 렌더링 합니다. 항목이 링크만 *되도록 하려면이* 를 사용 합니다.  다른 형식은 설정을 사용 하 여 링크 일 *수도* 있습니다 `Make this item a link` . 자세한 내용은 아래의 [링크 작업](#link-actions) 을 참조 하세요. |  |
| `Location` | 지역 id를 기반으로 친숙 한 Azure 지역 이름을 렌더링 합니다. |  |
| `Resource type` | 리소스 유형 id를 기반으로 하는 친숙 한 리소스 유형 문자열을 렌더링 합니다.  |  |
| `Resource` | 리소스 id를 기반으로 친숙 한 리소스 이름 및 링크를 렌더링 합니다.  | 리소스 유형 아이콘을 표시 하는 옵션  |
| `Resource group` | 리소스 그룹 id를 기반으로 하 여 친숙 한 리소스 그룹 이름 및 링크를 렌더링 합니다. 셀의 값이 리소스 그룹이 아닌 경우 1로 변환 됩니다.  | 리소스 그룹 아이콘을 표시 하는 옵션  |
| `Subscription` | 구독 id를 기반으로 하 여 친숙 한 구독 이름 및 링크를 렌더링 합니다.  셀의 값이 구독이 아닌 경우 1로 변환 됩니다.  | 구독 아이콘을 표시 하는 옵션입니다.  |
| `Hidden` | 표에서 열을 숨깁니다. 기본 쿼리가 필요한 것 보다 많은 열을 반환 하지만 프로젝트를 원하지 않는 경우에 유용 합니다. |  |

### <a name="link-actions"></a>링크 작업

렌더러를 `Link` 선택 하거나 *이 항목을 링크로 만들기* 확인란을 선택 하면 작성자가 셀을 선택할 때 발생 하는 링크 작업을 구성할 수 있습니다. 일반적으로 사용자는 컨텍스트를 사용 하 여 셀에서 오는 다른 뷰로 이동 하거나 url을 열 수 있습니다.

### <a name="custom-formatting"></a>사용자 지정 서식 지정

또한 통합 문서를 통해 사용자는 셀 값의 숫자 형식을 설정할 수 있습니다. 사용 가능한 경우 *사용자 지정 형식* 확인란을 클릭 하 여이 작업을 수행할 수 있습니다.

| 서식 지정 옵션 | 설명 |
|:------------- |:-------------|
| `Units` | 열에 대 한 단위-백분율, 개수, 시간, 바이트, 개수/시간, 바이트/시간 등에 대 한 다양 한 옵션입니다. 예를 들어 1234 값에 대 한 단위를 밀리초로 설정할 수 있으며, 1.234 s로 렌더링 됩니다. |
| `Style` | 10 진수, 통화, 백분율로 렌더링할 형식입니다. |
| `Show group separator` | 그룹 구분 기호를 표시 하려면 확인란을 선택 합니다. 미국에서 1234를 1234으로 렌더링 합니다. |
| `Minimum integer digits` | 사용할 최소 정수 자릿수 (기본값 1)입니다. |
| `Minimum fractional digits` | 사용할 최소 소수 자릿수 (기본값은 0)입니다.  |
| `Maximum fractional digits` | 사용할 최대 소수 자릿수입니다. |
| `Minimum significant digits` | 사용할 최소 유효 자릿수 (기본값 1)입니다. |
| `Maximum significant digits` | 사용할 최대 유효 자릿수입니다. |
| `Custom text for missing values` | 데이터 요소에 값이 없는 경우 공백 대신이 사용자 지정 텍스트를 표시 합니다. |

### <a name="custom-date-formatting"></a>사용자 지정 날짜 형식 지정

Author에서 열이 날짜/시간 렌더러에서 설정 된 것으로 지정 된 경우 작성자는 *사용자 지정 날짜 형식* 지정 확인란을 사용 하 여 사용자 지정 날짜 형식 지정 옵션을 지정할 수 있습니다.

| 서식 지정 옵션 | 설명 |
|:------------- |:-------------|
| `Style` | 날짜를 짧은 시간, 긴 형식 또는 짧은 시간 형식으로 렌더링 하는 형식입니다. |
| `Show time as` | 작성자가 현지 시간 (기본값) 또는 UTC (협정 세계시)를 표시 하는 방법을 결정할 수 있습니다. 선택한 날짜 형식 스타일에 따라 UTC/표준 시간대 정보가 표시 되지 않을 수 있습니다. |

## <a name="custom-column-width-setting"></a>사용자 지정 열 너비 설정

작성자는 *열 설정*의 *사용자 지정 열 너비* 필드를 사용 하 여 표의 열 너비를 사용자 지정할 수 있습니다.

![빨간색 상자에 표시 된 사용자 지정 열 너비 필드가 있는 열 설정의 스크린샷](./media/workbooks-grid-visualizations/custom-column-width-setting.png)

필드를 검은색으로 그대로 두면 열의 문자 수와 표시 되는 열 수를 기준으로 너비가 자동으로 결정 됩니다. 기본 단위는 "ch" (문자)입니다.

레이블에서 파란색 **(현재 너비)** 단추를 선택 하면 선택한 열의 현재 너비로 텍스트 필드가 채워집니다. 측정 단위가 없는 사용자 지정 너비 필드에 값이 있으면 기본값이 사용 됩니다.

사용 가능한 측정 단위는 다음과 같습니다.

| 측정 단위 | 정의           |
|:--------------------|:---------------------|
| ch                  | 문자 (기본값) |
| px                  | 픽셀               |
| fr                  | 분수 단위     |
| %                   | percentage           |

입력 유효성 검사-유효성 검사에 실패 하는 경우 빨간색 지침 메시지가 필드 아래에 팝업 되지만 사용자는 여전히 너비를 적용할 수 있습니다. 입력에 값이 있으면 해당 값은 구문 분석 됩니다. 유효한 측정 단위가 없으면 기본값이 사용 됩니다.

최소/최대 너비는 없으며,이는 작성자의 판단에 따라 좌우 됩니다. 숨겨진 열에 대해 사용자 지정 열 너비 필드를 사용할 수 없습니다.

## <a name="examples"></a>예제

### <a name="spark-lines-and-bar-underneath"></a>아래 Spark 선 및 막대

아래 예제에서는 요청 수와 요청 이름별 추세를 보여 줍니다.

```kusto
requests
| make-series Trend = count() default = 0 on timestamp from ago(1d) to now() step 1h by name
| project-away timestamp
| join kind = inner (requests
    | summarize Requests = count() by name
    ) on name
| project name, Requests, Trend
| order by Requests desc
```

[![및 spark 선 아래에 막대가 있는 로그 기반 그리드 스크린샷](./media/workbooks-grid-visualizations/log-chart-grid-spark-line.png)](./media/workbooks-grid-visualizations/log-chart-grid-spark-line.png#lightbox)

### <a name="heatmap-with-shared-scales-and-custom-formatting"></a>열 지도 공유 크기 조정 및 사용자 지정 서식 지정

이 예제에서는 다양 한 요청 기간 메트릭과 해당 개수를 보여 줍니다. 열 지도 렌더러는 설정에 설정 된 최소 값을 사용 하거나 열에 대 한 최소값과 최대값을 계산 하 고 열의 최소값과 최 댓 값을 기준으로 셀의 값을 기준으로 셀에 대해 선택한 색상표의 배경색을 할당 합니다.

```
requests
| summarize Mean = avg(duration), (Median, p80, p95, p99) = percentiles(duration, 50, 80, 95, 99), Requests = count() by name
| order by Requests desc
```

[![열 전체에 공유 된 눈금이 있는 열 지도가 있는 로그 기반 그리드 스크린샷](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale.png)](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale.png#lightbox)

위의 예제에서 공유 색상표 (녹색 또는 빨강)와 소수 자릿수를 사용 하 여 열 (평균, 중앙값, p80, p95 및 p 99)을 색으로 만듭니다. 요청 열에 사용 되는 별도의 색상표 (파란색)입니다.

#### <a name="shared-scale"></a>공유 크기 조정

공유 크기를 가져오려면:

1. 정규식을 사용 하 여 설정을 적용할 열을 두 개 이상 선택할 수 있습니다. 예를 들어 열 이름을로 설정 하 여 `Mean|Median|p80|p95|p99` 모두 선택 합니다.
2. 개별 열에 대 한 기본 설정을 삭제 합니다.

이렇게 하면 새 다중 열 설정이 해당 설정을 적용 하 여 공유 배율을 포함 합니다.

[![열에서 공유 크기 조정을 가져오기 위한 로그 기반 그리드 설정의 스크린샷](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale-settings.png)](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale-settings.png#lightbox)

### <a name="icons-to-represent-status"></a>상태를 나타내는 아이콘

아래 예제에서는 p95 기간을 기준으로 요청에 대 한 사용자 지정 상태를 보여 줍니다.

```
requests
| summarize p95 = percentile(duration, 95) by name
| order by p95 desc
| project Status = case(p95 > 5000, 'critical', p95 > 1000, 'error', 'success'), name, p95
```

[![위의 쿼리를 사용 하 여 열 전체에 공유 된 눈금이 있는 로그 기반 표의 스크린샷 열 지도.](./media/workbooks-grid-visualizations/log-chart-grid-icons.png)](./media/workbooks-grid-visualizations/log-chart-grid-icons.png#lightbox)

지원 되는 아이콘 이름 `cancelled` 에는,, `critical` `disabled` , `error` ,, `failed` `info` `none` `pending` `stopped` `question` `success` `unknown` `warning` `uninitialized` `resource` `up` `down` `left` `right` `trendup` `trenddown` `4` `3` `2` `1` `Sev0` `Sev1` `Sev2` `Sev3` `Sev4` `Fired` `Resolved` `Available` `Unavailable` `Degraded` `Unknown` `Blank` ,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,, 등이 있습니다.

### <a name="using-thresholds-with-links"></a>링크에 임계값 사용

아래 지침에서는 링크에 임계값을 사용 하 여 아이콘을 할당 하 고 다른 통합 문서를 여는 방법을 보여 줍니다. 표의 각 링크를 통해 해당 Application Insights 리소스에 대해 다른 통합 문서 템플릿이 열립니다.

1. 도구 모음 항목 *편집* 을 선택 하 여 통합 문서를 편집 모드로 전환 합니다.
2. **추가** 를 선택 하 고 *쿼리 추가*를 선택 합니다.
3. *데이터 소스* 를 "JSON"으로 변경 하 고 *시각화* 를 "그리드"로 변경 합니다.
4. 다음 쿼리를 입력합니다.

```json
[ 
    { "name": "warning", "link": "Community-Workbooks/Performance/Performance Counter Analysis" },
    { "name": "info", "link": "Community-Workbooks/Performance/Performance Insights" },
    { "name": "error", "link": "Community-Workbooks/Performance/Apdex" }
]
```

5. 쿼리를 실행 합니다.
6. **열 설정을** 선택 하 여 설정을 엽니다.
7. *열*에서 "이름"을 선택 합니다.
8. *열 렌더러*에서 "임계값"을 선택 합니다.
9.  을 입력 하 고 다음 *임계값 설정을*선택 합니다.
   
    | 연산자 | 값   | 아이콘   |
    |----------|---------|---------|
    | ==       | warning | 경고 |
    | ==       | error   | Failed  |

    ![위의 설정이 있는 열 설정 편집 탭의 스크린샷](./media/workbooks-grid-visualizations/column-settings.png)

    기본 행을 그대로 유지 합니다. 원하는 모든 텍스트를 입력할 수 있습니다. 텍스트 열은 입력으로 문자열 형식을 사용 하 고 지정 된 경우 열 값 및 단위를 사용 하 여 채웁니다. 예를 들어, warning이 열 값 이면 텍스트는 "링크!"가 될 수 있으며 {0} {1} "경고 링크!"로 표시 됩니다.
10. *이 항목을 링크로 만들기* 상자를 선택 합니다.
    1. *열려는 보기*에서 "통합 문서 (템플릿)"를 선택 합니다.
    2. 다음 *에서 연결 값 제공*에서 "링크"를 선택 합니다.
    3. *상황에 맞는 블레이드 상자에서 열기 링크* 를 선택 합니다.
    4. *통합 문서 링크 설정* 에서 다음 설정을 선택 합니다.
        1. *템플릿 Id의*출처에서 "열"을 선택 합니다.
        2. *열* 아래에서 "링크"를 선택 합니다.

    ![위의 설정이 포함 된 링크 설정의 스크린샷](./media/workbooks-grid-visualizations/make-this-item-a-link.png)

11. *열*에서 "연결"을 선택 합니다. *열 렌더러*옆의 설정에서 **(열 숨기기)** 를 선택 합니다.
1. "이름" 열의 표시 이름을 변경 하려면 **레이블** 탭을 선택 합니다. *열 ID*가 "name" 인 행에서 * 열 레이블 아래에 표시할 이름을 입력 합니다.
2. **적용** 선택

![위의 설정이 있는 표 형태의 임계값 스크린샷](./media/workbooks-grid-visualizations/thresholds-workbooks-links.png)

## <a name="fractional-units-percentages"></a>분수 단위 백분율

Fr-fr (분수 단위)은 다양 한 유형의 표에서 일반적으로 사용 되는 동적 측정 단위입니다. 창 크기/해상도가 변경 되 면 fr-fr 너비도 변경 됩니다.

아래 스크린샷에서는 각각 1fr 너비와 너비가 같은 8 개의 열이 있는 테이블을 보여 줍니다. 창 크기가 변경 되 면 각 열의 너비가 비례적으로 변경 됩니다.

[![열 너비 값이 각각 1fr 인 그리드 열의 스크린샷](./media/workbooks-grid-visualizations/custom-column-width-fr.png)](./media/workbooks-grid-visualizations/custom-column-width-fr.png#lightbox)

첫 번째 열이 50% width로 설정 된 경우를 제외 하 고 아래 이미지는 동일한 테이블을 보여 줍니다. 그러면 열이 전체 표 너비의 절반으로 동적으로 설정 됩니다. 창 크기를 조정 하면 창 크기가 너무 작은 경우를 제외 하 고는 50% 너비를 계속 유지 합니다. 이러한 동적 열의 내용에 따라 최소 너비가 사용 됩니다. 표의 나머지 50%는 총 소수 단위 8 개로 나뉩니다. 아래의 "kind" 열은 2fr로 설정 되어 있으므로 나머지 공간의 1/4이 사용 됩니다. 다른 열은 각각 1fr 이므로 각 열은 그리드 오른쪽 절반의 1-8을 사용 합니다.

[![1 열 너비 값이 50%이 고 나머지는 각각 1fr 인 표 형태의 열 스크린샷](./media/workbooks-grid-visualizations/custom-column-width-fr2.png)](./media/workbooks-grid-visualizations/custom-column-width-fr2.png#lightbox)

Fr,%, px 및 ch 너비를 결합 하는 것이 가능 하며 앞의 예제와 유사 하 게 작동 합니다. 고정 단위 (ch 및 px)로 설정 된 너비는 창/해상도가 변경 되는 경우에도 변경 되지 않는 하드 상수입니다. %로 설정 된 열은 전체 그리드 너비를 기준으로 백분율을 차지 합니다 (이전 최소 너비 때문에 정확 하지 않을 수 있음). Fr로 설정 된 열은 할당 된 소수 자릿수 단위를 기준으로 나머지 그리드 공간을 분할 합니다.

[![사용 되는 다양 한 너비 단위의 모음이 있는 그리드 열의 스크린샷](./media/workbooks-grid-visualizations/custom-column-width-fr3.png)](./media/workbooks-grid-visualizations/custom-column-width-fr3.png#lightbox)

## <a name="next-steps"></a>다음 단계

* [통합 문서에서 트리](workbooks-tree-visualizations.md)를 만드는 방법에 대해 알아봅니다.
* [통합 문서 텍스트 매개 변수](workbooks-text.md)를 만드는 방법에 대해 알아봅니다.