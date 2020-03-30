---
title: Azure 모니터 통합 문서 시각화
description: 텍스트, 차트, 그리드, 트리 및 그래프를 포함한 모든 Azure Monitor 통합 문서 시각화 구성 요소에 대해 알아봅니다.
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: d05902f47dff3dd2f8a63ae240c0b8825a5c441f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658033"
---
# <a name="azure-monitor-workbook-visualizations"></a>Azure 모니터 통합 문서 시각화

Azure Monitor 통합 문서는 보고 요구 사항을 수용하기 위해 다양한 유형의 시각화를 지원합니다. 이 문서에서는 각 시각화 유형에 대한 예제를 제공합니다.

## <a name="text"></a>텍스트

통합 문서를 사용하면 작성자가 통합 문서에 텍스트 블록을 포함할 수 있습니다. 텍스트는 원격 분석의 인간 분석, 사용자가 데이터를 해석하는 데 도움이되는 정보, 섹션 제목 등이 될 수 있습니다.

![텍스트의 Apdex 테이블의 스크린 샷](./media/workbooks-visualizations/apdex.png)

텍스트는 전체 서식 지정 컨트롤을 제공하는 마크다운 컨트롤을 통해 추가됩니다.

![렌더링된 테이블을 빌드하는 원시 마크다운 스크린샷](./media/workbooks-visualizations/markdown.png)

### <a name="add-a-text-control"></a>텍스트 컨트롤 추가

1. 도구 모음 항목 **편집을** 클릭하여 통합 문서를 편집 모드로 전환합니다.
2. 텍스트 **추가** 링크를 사용하여 통합 문서에 텍스트 컨트롤을 추가합니다.
3. 컨트롤에 마크다운을 추가합니다.
4. 편집 **완료** 단추를 클릭하여 서식이 지정된 텍스트를 확인합니다.

> [!TIP]
> 이 [마크다운 치트 시트를](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) 사용하여 다양한 서식 옵션에 대해 알아봅니다.

## <a name="charts"></a>차트

통합 문서를 사용하면 모니터링 데이터를 차트로 표시할 수 있습니다. 지원되는 차트 유형에는 선, 막대, 막대 범주형, 영역, 분산형 플롯, 원형 및 시간이 포함됩니다. 작성자는 차트의 높이, 너비, 색상 표기, 범례, 제목, 데이터 없음 메시지 등을 사용자 지정하도록 선택할 수 있습니다.

통합 문서는 로그 및 메트릭 데이터 원본모두에 대한 차트를 지원합니다. 

### <a name="adding-a-log-chart"></a>로그 차트 추가

1. 도구 모음 항목 **편집을** 클릭하여 통합 문서를 편집 모드로 전환합니다.
2. 쿼리 **추가** 링크를 사용하여 통합 문서에 로그 쿼리 컨트롤을 추가합니다.
3. 쿼리 유형을 **로그,** 리소스 유형(예: 응용 프로그램 인사이트)으로 선택하고 대상 리소스를 선택합니다.
4. 쿼리 편집기를 사용하여 분석에 [대한 KQL을](https://docs.microsoft.com/azure/kusto/query/) 입력합니다(예: 요청 추세).
5. 시각화를 중 하나로 설정합니다: **영역,** **막대,** **막대(범주형),** **선,** **파이,** **분산,** 또는 **시간.**
6. 시간 범위, 시각화, 크기, 색상 팔레트 및 범례와 같은 필요한 경우 다른 매개 변수를 설정합니다.

![편집 모드에서 로그 차트의 스크린 샷](./media/workbooks-visualizations/log-chart.png)

#### <a name="log-chart-parameters"></a>로그 차트 매개 변수

| 매개 변수 | 설명 | 예제 |
| ------------- |:-------------|:-------------|
| `Query Type` | 사용할 쿼리 유형 | 로그, Azure 리소스 그래프 등 |
| `Resource Type` | 대상에 대한 리소스 유형 | 애플리케이션 인사이트, 로그 분석 또는 Azure 우선 |
| `Resources` | 메트릭 값을 가져오기 위한 리소스 집합 | 마이앱1 |
| `Time Range` | 로그 차트를 볼 시간 창 | 지난 시간, 마지막 24 시간 등 |
| `Visualization` | 사용할 시각화 | 영역, 바, 라인, 파이, 산란, 시간, 바 범주형 |
| `Size` | 컨트롤의 수직 크기 | 소형, 중형, 대형 또는 전체 |
| `Color palette` | 차트에 사용할 색상 표입니다. 다중 메트릭 또는 분할 모드에서 무시됩니다. | 블루, 그린, 레드 등 |
| `Legend` | 범례에 사용할 집계 함수 | 값 또는 최대, 최소, 첫 번째, 마지막 값의 합계 또는 평균 |
| `Query` | 차트 시각화에서 예상하는 형식으로 데이터를 반환하는 모든 KQL 쿼리 | _요청 \| 메이크 시리즈 요청 = count() 기본 = 0 전(1d)에서 지금() 단계 1h_ |

### <a name="adding-a-metric-chart"></a>측정항목 차트 추가

1. 도구 모음 항목 **편집을** 클릭하여 통합 문서를 편집 모드로 전환합니다.
2. 메트릭 **추가** 링크를 사용하여 통합 문서에 메트릭 컨트롤을 추가합니다.
3. 리소스 유형(예: 저장소 계정), 대상 리소스, 메트릭 네임스페이스 및 이름 및 사용할 집계를 선택합니다.
4. 시간 범위, 분할별, 시각화, 크기 및 색상 팔레트와 같이 필요한 경우 다른 매개변수를 설정합니다.

![편집 모드에서 메트릭 차트의 스크린샷](./media/workbooks-visualizations/metric-chart.png)

#### <a name="metric-chart-parameters"></a>메트릭 차트 매개 변수

| 매개 변수 | 설명 | 예제 |
| ------------- |:-------------|:-------------|
| `Resource Type` | 대상에 대한 리소스 유형 | 스토리지 또는 가상 머신. |
| `Resources` | 메트릭 값을 가져오기 위한 리소스 집합 | 마이스토리지1 |
| `Namespace` | 메트릭이 있는 네임스페이스 | 저장 > Blob |
| `Metric` | 시각화할 메트릭 | 저장소 > Blob > 트랜잭션 |
| `Aggregation` | 메트릭에 적용할 집계 함수 | 합계, 개수, 평균 등 |
| `Time Range` | 메트릭을 볼 수 있는 시간 창 | 지난 시간, 마지막 24 시간 등 |
| `Visualization` | 사용할 시각화 | 면적, 바, 선, 흩어질, 그리드 |
| `Split By` | 선택적으로 측정기준에서 측정항목을 분할 | 지리적 유형별 트랜잭션 |
| `Size` | 컨트롤의 수직 크기 | 소형, 중형 또는 대형 |
| `Color palette` | 차트에 사용할 색상 표입니다. 매개 변수가 `Split by` 사용되는 경우 무시됨 | 블루, 그린, 레드 등 |

## <a name="grids"></a>표

그리드 또는 테이블은 사용자에게 데이터를 표시하는 일반적인 방법입니다. 통합 문서를 사용하면 사용자가 그리드의 열에 개별적으로 스타일을 지정하여 보고서에 풍부한 UI를 제공할 수 있습니다.

아래 예제에서는 아이콘, 히트맵 및 스파크 바를 결합하여 복잡한 정보를 표시하는 그리드를 보여 주습니다. 통합 문서에는 정렬, 검색 상자 및 분석 버튼도 제공됩니다.

![로그 기반 그리드의 스크린샷](./media/workbooks-visualizations/grid.png)

### <a name="adding-a-log-based-grid"></a>로그 기반 그리드 추가

1. 도구 모음 항목 **편집을** 클릭하여 통합 문서를 편집 모드로 전환합니다.
2. 쿼리 **추가** 링크를 사용하여 통합 문서에 로그 쿼리 컨트롤을 추가합니다.
3. 쿼리 유형을 **로그,** 리소스 유형(예: 응용 프로그램 인사이트)으로 선택하고 대상 리소스를 선택합니다.
4. 쿼리 편집기를 사용하여 분석에 대한 KQL을 입력합니다(예: 메모리가 임계값 미만인 VM).
5. 시각화를 **그리드로** 설정
6. 시간 범위, 크기, 색상 팔레트 및 범례와 같은 필요한 경우 다른 매개변수를 설정합니다.

![로그 기반 그리드 쿼리의 스크린샷](./media/workbooks-visualizations/grid-query.png)

## <a name="tiles"></a>타일

타일은 통합 문서에서 요약 데이터를 표시하는 매우 유용한 방법입니다. 아래 이미지는 타일의 일반적인 사용 사례를 보여줍니다 - 자세한 그리드 위에 응용 프로그램 수준 요약.

![타일 요약 보기의 스크린샷](./media/workbooks-visualizations/tiles-summary.png)

통합 문서 타일은 제목, 자막, 큰 텍스트, 아이콘, 메트릭 기반 그라데이션, 스파크 라인 / 막대, 바닥글 등을 표시하는 것을 지원합니다.

### <a name="adding-a-tile"></a>타일 추가

1. 도구 모음 항목 _편집을_ 클릭하여 통합 문서를 편집 모드로 전환합니다.
2. 쿼리 **추가** 링크를 사용하여 통합 문서에 로그 쿼리 컨트롤을 추가합니다. 
3. 쿼리 유형을 **로그,** 리소스 유형(예: 응용 프로그램 인사이트)으로 선택하고 대상 리소스를 선택합니다.
4. 쿼리 편집기를 사용하여 분석에 KQL 입력
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc
    ```
5. 크기를 **최대로** 설정
6. 시각화를 **타일로** 설정
7. 타일 **설정** 버튼을 클릭하여 설정 창을 엽니다.
8. **타일 필드**집합에서:
    * 제목: `name`
    * 왼쪽: `Requests`렌더러: `Big Number`, 색상 `Green to Red`팔레트: , 최소 값:`0`
    * 하단:`appName`
9. 창 하단의 **저장 및 닫기** 버튼을 클릭합니다.

![타일 요약 보기의 스크린샷](./media/workbooks-visualizations/tile-settings.png)

타일이 읽기 모드에서 보이는 방법은 다음과 같은 것입니다.

![타일 요약 보기의 스크린샷](./media/workbooks-visualizations/tiles-read-mode.png)

## <a name="trees"></a>Trees

통합 문서는 트리 그리드를 통한 계층적 보기를 지원합니다. 트리를 사용하면 드릴다운 환경을 위해 일부 행을 다음 단계로 확장할 수 있습니다.

아래 예제에서는 트리 그리드로 시각화된 컨테이너 상태 메트릭(작업 집합 크기)을 보여 주며 있습니다. 최상위 노드는 AZURE Kubernetes 서비스(AKS) 노드이며, 다음 수준은 포드이고 최종 수준은 컨테이너입니다. 그리드(히트맵, 아이콘, 링크)와 같이 열을 포맷할 수 있습니다. 이 경우 기본 데이터 원본은 AKS 로그가 있는 로그 분석 작업 영역입니다.

![타일 요약 보기의 스크린샷](./media/workbooks-visualizations/trees.png)

### <a name="adding-a-tree-grid"></a>트리 그리드 추가
1. 도구 모음 항목 _편집을_ 클릭하여 통합 문서를 편집 모드로 전환합니다.
2. 쿼리 **추가** 링크를 사용하여 통합 문서에 로그 쿼리 컨트롤을 추가합니다. 
3. 쿼리 유형을 **로그,** 리소스 유형(예: 응용 프로그램 인사이트)으로 선택하고 대상 리소스를 선택합니다.
4. 쿼리 편집기를 사용하여 분석에 KQL 입력
    ```kusto
    requests
    | summarize Requests = count() by ParentId = appName, Id = name
    | extend Kind = 'Request', Name = strcat('🌐 ', Id)
    | union (requests
    | summarize Requests = count() by Id = appName
    | extend Kind = 'Request', ParentId = '', Name = strcat('📱 ', Id))
    | project Name, Kind, Requests, Id, ParentId
    | order by Requests desc
    ```
5. 시각화를 **그리드로** 설정
6. 열 **설정** 버튼을 클릭하여 설정 창을 엽니다.
7. 아래쪽의 **트리/그룹별 설정** 섹션에서 다음을 설정합니다.
    * 트리 유형:`Parent/Child`
    * ID 필드:`Id`
    * 상위 ID 필드:`ParentId`
    * 익스팬더 표시:`Name`
    * 트리의 최상위 수준을 확장합니다.`checked`
8. 상단의 _열_ 섹션에서 다음을 설정합니다.
    * _ID_ - 열 렌더러:`Hidden`
    * _부모 ID_ - 열 렌더러:`Hidden`
    * _요청_ - 열 `Bar`렌더러: 색상: `Blue`최소 값:`0`
9. 창 하단의 _저장 및 닫기_ 버튼을 클릭합니다.    

![타일 요약 보기의 스크린샷](./media/workbooks-visualizations/tree-settings.png)

### <a name="tree-settings"></a>트리 설정

| 설정 | 설명 |
|:------------- |:-------------|
| `Id Field` | 그리드의 모든 행의 고유 ID |
| `Parent Id Field` | 현재 행의 상위 ID |
| `Show the expander on` | 트리 확장기를 표시할 열입니다. 트리 그리드는 읽을 수 없기 때문에 ID 및 상위 ID 필드를 숨기는 것이 일반적입니다. 대신 확장기는 엔터티 이름과 같이 읽기 쉬운 값을 가진 필드에 나타납니다. |
| `Expand the top level of the tree` | 이 옵션을 선택하면 트리 그리드가 최상위 수준에서 확장됩니다. 기본적으로 더 많은 정보를 표시하려는 경우 유용합니다. |

## <a name="graphs"></a>그래프

통합 문서는 로그의 데이터를 기반으로 임의의 그래프를 시각화하여 모니터링 엔터티 간의 관계를 표시합니다.

아래 그래프는 외부 컴퓨터와 의 다양한 포트를 통해 컴퓨터의 출입을 통해 흐르는 데이터를 보여줍니다. 형식(컴퓨터 대 포트 대 외부 IP)으로 색상이 지정되며 가장자리 크기는 그 사이에 흐르는 데이터의 양에 해당합니다. 기본 데이터는 VM 연결을 대상으로 하는 KQL 쿼리에서 제공됩니다.

![타일 요약 보기의 스크린샷](./media/workbooks-visualizations/graph.png)

### <a name="adding-a-graph"></a>그래프 추가
1. 도구 모음 항목 _편집을_ 클릭하여 통합 문서를 편집 모드로 전환합니다.
2. 쿼리 **추가** 링크를 사용하여 통합 문서에 로그 쿼리 컨트롤을 추가합니다. 
3. 쿼리 유형을 **로그,** 리소스 유형(예: 응용 프로그램 인사이트)으로 선택하고 대상 리소스를 선택합니다.
4. 쿼리 편집기를 사용하여 분석에 KQL 입력
    ```kusto
    let data = dependencies
    | summarize Calls = count() by App = appName, Request = operation_Name, Dependency = name
    | extend RequestId = strcat(App, '::', Request);
    let links = data
    | summarize Calls = sum(Calls) by App, RequestId
    | project SourceId = App, TargetId = RequestId, Calls, Kind = 'App -> Request'
    | union (data
        | project SourceId = RequestId, TargetId = Dependency, Calls, Kind = 'Request -> Dependency');
    let nodes = data
    | summarize Calls = sum(Calls) by App
    | project Id = App, Name = App, Calls, Kind = 'App'
    | union (data
        | summarize Calls = sum(Calls) by RequestId, Request
        | project Id = RequestId, Name = Request, Calls, Kind = 'Request')
    | union (data
        | summarize Calls = sum(Calls) by Dependency
        | project Id = Dependency, Name = Dependency, Calls, Kind = 'Dependency');
    nodes
    | union (links)
    ```
7. 시각화를 **그래프로** 설정
8. 그래프 **설정** 버튼을 클릭하여 설정 창을 엽니다.
9. 하단의 _레이아웃 필드에서_ 다음을 설정합니다.
    * `Node Id`: `Id`
    * `Source Id`: `SourceId`
    * `Target Id`: `TargetId`
    * `Edge Label`: `None`
    * `Edge Size`: `Calls`
    * `Node Size`: `None`
    * `Coloring Type`: `Categorical`
    * `Node Color Field`: `Kind`
    * `Color palette`: `Pastel`
10. 상단의 _노드 형식 설정에서_ 다음을 설정합니다.
    * _상위 콘텐츠_- `Name`열 사용: , 열 렌더러:`Text`
    * _중심 내용_- `Calls`열 사용: `Big Number`, 열 렌더러: [ 색상 팔레트]`None`
    * _하단 콘텐츠_- `Kind`열 사용: , 열 렌더러:`Text`
10. 창 하단의 _저장 및 닫기_ 버튼을 클릭합니다.

![타일 요약 보기의 스크린샷](./media/workbooks-visualizations/graph-settings.png)

## <a name="next-steps"></a>다음 단계

* Azure 리소스 관리자를 사용 하 고 통합 문서를 [배포](workbooks-automate.md) 합니다.
* 통합 문서 리소스에 대한 액세스를 [제어하고](workbooks-access-control.md) 공유합니다.
