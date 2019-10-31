---
title: Azure Monitor 통합 문서 시각화를 사용 하 여 대화형 보고서 만들기 | Microsoft 문서
description: 텍스트, 차트, 표, 트리 및 그래프를 포함 한 모든 Azure Monitor 통합 문서 시각화 요소에 대해 알아봅니다.
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: d878e8b5dd49ba517f85ebb74332bc6a245c33ca
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73165057"
---
# <a name="azure-monitor-workbook-visualizations"></a>Azure Monitor 통합 문서 시각화

Azure Monitor 통합 문서는 보고 요구 사항에 맞게 다양 한 시각화 스타일을 지원 합니다. 이 문서에서는 각 유형의 시각화에 대 한 예제를 제공 합니다.

## <a name="text"></a>텍스트

통합 문서를 사용 하면 작성자가 해당 통합 문서에 텍스트 블록을 포함할 수 있습니다. 텍스트는 원격 분석에 대 한 사람이 분석할 수 있으며, 사용자가 데이터를 해석 하는 데 도움이 되는 정보, 섹션 제목 등입니다.

![Apdex 텍스트 표 스크린샷](./media/workbooks-visualizations/apdex.png)

텍스트는 모든 서식 지정 컨트롤을 제공 하는 Markdown 컨트롤을 통해 추가 됩니다.

![렌더링 된 테이블을 작성 하는 원시 markdown의 스크린샷](./media/workbooks-visualizations/markdown.png)

### <a name="add-a-text-control"></a>텍스트 컨트롤 추가

1. **편집** 도구 모음 항목을 클릭 하 여 통합 문서를 편집 모드로 전환 합니다.
2. 텍스트 **추가** 링크를 사용 하 여 통합 문서에 텍스트 컨트롤을 추가 합니다.
3. 컨트롤에 Markdown를 추가 합니다.
4. **편집 완료** 단추를 클릭 하 여 서식이 지정 된 텍스트를 확인 합니다.

> [!TIP]
> 이 [Markdown 참고 자료 시트](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) 를 사용 하 여 다양 한 서식 옵션에 대해 알아보세요.

## <a name="charts"></a>차트

통합 문서를 사용 하면 모니터링 데이터를 차트로 표시할 수 있습니다. 지원 되는 차트 종류에는 꺾은선형, 가로 막대형, 가로 막대형 범주, 영역형, 산 점도, 원형 및 시간이 포함 됩니다. 작성자는 차트의 높이, 너비, 색상표, 범례, 제목, 데이터 없음 메시지 등을 사용자 지정 하도록 선택할 수 있습니다.

통합 문서는 로그 및 메트릭 데이터 원본 모두에 대 한 차트를 지원 합니다. 

### <a name="adding-a-log-chart"></a>로그 차트 추가

1. **편집** 도구 모음 항목을 클릭 하 여 통합 문서를 편집 모드로 전환 합니다.
2. **쿼리 추가** 링크를 사용 하 여 통합 문서에 로그 쿼리 컨트롤을 추가 합니다.
3. **로그**, 리소스 유형 (예: Application Insights) 및 대상으로 사용할 리소스를 선택 합니다.
4. 쿼리 편집기를 사용 하 여 분석에 대 한 [KQL](https://docs.microsoft.com/azure/kusto/query/) (예: 요청 추세)를 입력할 수 있습니다.
5. 시각화를 **영역**, **가로**막대형, **가로 막대형 (범주)** , **꺾은선형**, **원형**, **분산형**또는 **시간**중 하나로 설정 합니다.
6. 필요한 경우 시간 범위, 시각화, 크기, 색상표 및 범례와 같은 다른 매개 변수를 설정 합니다.

![편집 모드의 로그 차트 스크린샷](./media/workbooks-visualizations/log-chart.png)

#### <a name="log-chart-parameters"></a>로그 차트 매개 변수

| 매개 변수를 포함해야 합니다. | 설명 | 예제 |
| ------------- |:-------------|:-------------|
| `Query Type` | 사용할 쿼리 유형 | 로그, Azure 리소스 그래프 등 |
| `Resource Type` | 대상으로 할 리소스 형식입니다. | Application Insights, Log Analytics 또는 Azure-first |
| `Resources` | 메트릭 값을 가져올 리소스 집합입니다. | MyApp1 |
| `Time Range` | 로그 차트를 볼 시간 창 | 지난 시간, 지난 24 시간 등 |
| `Visualization` | 사용할 시각화 | 영역형, 가로 막대형, 꺾은선형, 원형, 분산형, 시간, 막대 범주 |
| `Size` | 컨트롤의 세로 크기입니다. | Small, medium, large 또는 full |
| `Color palette` | 차트에 사용할 색상표입니다. 다중 메트릭 또는 분할 모드에서 무시 됩니다. | 파랑, 녹색, 빨강 등 |
| `Legend` | 범례에 사용할 집계 함수입니다. | 값의 합계 또는 평균, Min, First, Last value |
| `Query` | 차트 시각화에 필요한 형식으로 데이터를 반환 하는 모든 KQL 쿼리 | _시리즈 요청 \| 요청 = count () 기본값 = 0 타임 스탬프 (1d)에서 now () 단계 1 시간_ |

### <a name="adding-a-metric-chart"></a>메트릭 차트 추가

1. **편집** 도구 모음 항목을 클릭 하 여 통합 문서를 편집 모드로 전환 합니다.
2. 메트릭 **추가** 링크를 사용 하 여 통합 문서에 메트릭 컨트롤을 추가 합니다.
3. 리소스 유형 (예: 저장소 계정), 대상 리소스, 메트릭 네임 스페이스 및 이름, 사용할 집계를 선택 합니다.
4. 필요한 경우 시간 범위, 분할 기준, 시각화, 크기 및 색상표와 같은 다른 매개 변수를 설정 합니다.

![편집 모드의 메트릭 차트 스크린샷](./media/workbooks-visualizations/metric-chart.png)

#### <a name="metric-chart-parameters"></a>메트릭 차트 매개 변수

| 매개 변수를 포함해야 합니다. | 설명 | 예제 |
| ------------- |:-------------|:-------------|
| `Resource Type` | 대상으로 할 리소스 형식입니다. | 저장소 또는 가상 머신. |
| `Resources` | 메트릭 값을 가져올 리소스 집합입니다. | MyStorage1 |
| `Namespace` | 메트릭이 포함 된 네임 스페이스입니다. | Storage > Blob |
| `Metric` | 시각화할 메트릭입니다. | 저장소 > Blob > 트랜잭션 |
| `Aggregation` | 메트릭에 적용할 집계 함수입니다. | 합계, 개수, 평균 등 |
| `Time Range` | 메트릭을 볼 시간 창 | 지난 시간, 지난 24 시간 등 |
| `Visualization` | 사용할 시각화 | 영역형, 가로 막대형, 꺾은선형, 분산형, 그리드 |
| `Split By` | 필요에 따라 차원의 메트릭을 분할 합니다. | 지역 유형별 트랜잭션 |
| `Size` | 컨트롤의 세로 크기입니다. | 소형, 중형 또는 큼 |
| `Color palette` | 차트에 사용할 색상표입니다. `Split by` 매개 변수를 사용 하는 경우 무시 됩니다. | 파랑, 녹색, 빨강 등 |

## <a name="grids"></a>배경

표 또는 테이블은 사용자에 게 데이터를 제공 하는 일반적인 방법입니다. 통합 문서를 사용 하면 사용자가 표의 열에 대해 개별적으로 스타일을 지정 하 여 보고서에 대 한 풍부한 UI를 제공할 수 있습니다.

아래 예제에서는 아이콘, 열 지도 및 spark 막대를 결합 하 여 복잡 한 정보를 제공 하는 표를 보여 줍니다. 통합 문서에는 정렬, 검색 상자 및 분석 이동 단추도 제공 됩니다.

![로그 기반 그리드 스크린샷](./media/workbooks-visualizations/grid.png)

### <a name="adding-a-log-based-grid"></a>로그 기반 그리드 추가

1. **편집** 도구 모음 항목을 클릭 하 여 통합 문서를 편집 모드로 전환 합니다.
2. **쿼리 추가** 링크를 사용 하 여 통합 문서에 로그 쿼리 컨트롤을 추가 합니다.
3. **로그**, 리소스 유형 (예: Application Insights) 및 대상으로 사용할 리소스를 선택 합니다.
4. 쿼리 편집기를 사용 하 여 분석에 대 한 KQL (예: 메모리가 임계값 미만인 Vm)를 입력할 수 있습니다.
5. 시각화를 **그리드** 로 설정
6. 필요한 경우 시간 범위, 크기, 색상표 및 범례와 같은 다른 매개 변수를 설정 합니다.

![로그 기반 그리드 쿼리 스크린샷](./media/workbooks-visualizations/grid-query.png)

## <a name="tiles"></a>타일

타일은 통합 문서에 요약 데이터를 표시 하는 매우 유용한 방법입니다. 아래 이미지는 상세 그리드 위에 타일-앱 수준 요약의 일반적인 사용 사례를 보여 줍니다.

![타일 요약 보기의 스크린샷](./media/workbooks-visualizations/tiles-summary.png)

통합 문서 타일은 제목, 부제목, 큰 텍스트, 아이콘, 메트릭 기반 그라데이션, spark 선/가로 막대형, 바닥글 등을 표시 하는 기능을 지원 합니다.

### <a name="adding-a-tile"></a>타일 추가

1. _편집_ 도구 모음 항목을 클릭 하 여 통합 문서를 편집 모드로 전환 합니다.
2. **쿼리 추가** 링크를 사용 하 여 통합 문서에 로그 쿼리 컨트롤을 추가 합니다. 
3. **로그**, 리소스 유형 (예: Application Insights) 및 대상으로 사용할 리소스를 선택 합니다.
4. 쿼리 편집기를 사용 하 여 분석을 위한 KQL 입력
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc
    ```
5. 크기를 **전체** 로 설정
6. 시각화를 **타일로** 설정
7. **타일 설정** 단추를 클릭 하 여 설정 창을 엽니다.
8. **타일 필드**에서 다음을 설정 합니다.
    * 제목: `name`
    * 왼쪽: `Requests`, 렌더러: `Big Number`, 색상표: `Green to Red`, Min Value: `0`
    * 아래쪽: `appName`
9. 창 맨 아래에 있는 **저장 및 닫기** 단추를 클릭 합니다.

![타일 요약 보기의 스크린샷](./media/workbooks-visualizations/tile-settings.png)

타일은 읽기 모드에서 다음과 같이 표시 됩니다.

![타일 요약 보기의 스크린샷](./media/workbooks-visualizations/tiles-read-mode.png)

## <a name="trees"></a>트리나

통합 문서는 트리-그리드를 통해 계층 뷰를 지원 합니다. 트리를 사용 하면 드릴 다운 환경에서 일부 행을 다음 수준으로 확장할 수 있습니다.

아래 예제에서는 트리 그리드로 시각화 된 컨테이너 상태 메트릭 (작업 집합 크기)을 보여 줍니다. 여기서 최상위 노드는 AKS (Azure Kubernetes Service) 노드이 고, 다음 수준은 pod 이며, 최종 수준은 컨테이너입니다. 표 형태 (열 지도, 아이콘, 링크)와 같이 열의 서식을 지정할 수 있습니다. 이 경우 기본 데이터 원본은 AKS 로그가 있는 Log Analytics 작업 영역입니다.

![타일 요약 보기의 스크린샷](./media/workbooks-visualizations/trees.png)

### <a name="adding-a-tree-grid"></a>트리 그리드 추가
1. _편집_ 도구 모음 항목을 클릭 하 여 통합 문서를 편집 모드로 전환 합니다.
2. **쿼리 추가** 링크를 사용 하 여 통합 문서에 로그 쿼리 컨트롤을 추가 합니다. 
3. **로그**, 리소스 유형 (예: Application Insights) 및 대상으로 사용할 리소스를 선택 합니다.
4. 쿼리 편집기를 사용 하 여 분석을 위한 KQL 입력
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
5. 시각화를 **그리드** 로 설정
6. 설정 창을 열려면 **열 설정** 단추를 클릭 합니다.
7. 아래쪽의 **트리/그룹화 방법 설정** 섹션에서 다음을 설정 합니다.
    * 트리 형식: `Parent/Child`
    * Id 필드: `Id`
    * 부모 Id 필드: `ParentId`
    * 확장기 표시 위치: `Name`
    * 트리의 최상위 수준을 확장 합니다. `checked`
8. 위쪽의 _열_ 섹션에서 다음을 설정 합니다.
    * _Id_ 열 렌더러: `Hidden`
    * _부모 Id_ -열 렌더러: `Hidden`
    * _요청_ -열 렌더러: `Bar`, 색: `Blue`, 최소 값: `0`
9. 창 맨 아래에 있는 _저장 및 닫기_ 단추를 클릭 합니다.    

![타일 요약 보기의 스크린샷](./media/workbooks-visualizations/tree-settings.png)

### <a name="tree-settings"></a>트리 설정

| 설정 | 설명 |
|:------------- |:-------------|
| `Id Field` | 표의 모든 행에 대 한 고유 Id |
| `Parent Id Field` | 현재 행의 부모 id입니다. |
| `Show the expander on` | 트리 확장기를 표시할 열입니다. 트리 그리드는 읽을 수 없기 때문에 해당 id 및 부모 id 필드를 숨기는 것이 일반적입니다. 대신, 확장은 엔터티의 이름과 같이 보다 읽기 쉬운 값이 있는 필드에 표시 됩니다. |
| `Expand the top level of the tree` | 이 확인란을 선택 하면 최상위 수준에서 트리 눈금이 확장 됩니다. 기본적으로 추가 정보를 표시 하려는 경우에 유용 합니다. |

## <a name="graphs"></a>그래프

통합 문서는 모니터링 엔터티 간의 관계를 표시 하기 위해 로그의 데이터를 기반으로 임의의 그래프를 시각화 하도록 지원 합니다.

아래 그래프는 외부 컴퓨터 간 다양 한 포트를 통해 컴퓨터에서 들어오고 나가는 데이터를 보여줍니다. 유형 (컴퓨터와 포트 및 외부 IP)에 따라 색이 지정 되 고에 지 크기는 사이에서 흐르는 데이터의 양에 해당 합니다. 기본 데이터는 VM 연결을 대상으로 하는 KQL query에서 제공 됩니다.

![타일 요약 보기의 스크린샷](./media/workbooks-visualizations/graph.png)

### <a name="adding-a-graph"></a>그래프 추가
1. _편집_ 도구 모음 항목을 클릭 하 여 통합 문서를 편집 모드로 전환 합니다.
2. **쿼리 추가** 링크를 사용 하 여 통합 문서에 로그 쿼리 컨트롤을 추가 합니다. 
3. **로그**, 리소스 유형 (예: Application Insights) 및 대상으로 사용할 리소스를 선택 합니다.
4. 쿼리 편집기를 사용 하 여 분석을 위한 KQL 입력
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
8. **그래프 설정** 단추를 클릭 하 여 설정 창을 엽니다.
9. 아래쪽의 _레이아웃 필드_ 에서 다음을 설정 합니다.
    * `Node Id`: `Id`
    * `Source Id`: `SourceId`
    * `Target Id`: `TargetId`
    * `Edge Label`: `None`
    * `Edge Size`: `Calls`
    * `Node Size`: `None`
    * `Coloring Type`: `Categorical`
    * `Node Color Field`: `Kind`
    * `Color palette`: `Pastel`
10. 위쪽의 _노드 형식 설정_ 에서 다음을 설정 합니다.
    * _상위 콘텐츠_-열 사용: `Name`, 열 렌더러: `Text`
    * _가운데 내용_-열 사용: `Calls`, 열 렌더러: `Big Number`, 색상표: `None`
    * _하위 콘텐츠_-열 사용: `Kind`, 열 렌더러: `Text`
10. 창 맨 아래에 있는 _저장 및 닫기_ 단추를 클릭 합니다.

![타일 요약 보기의 스크린샷](./media/workbooks-visualizations/graph-settings.png)

## <a name="next-steps"></a>다음 단계

* Azure Resource Manager를 사용 하 여 통합 문서를 [배포](workbooks-automate.md) 합니다.
* 통합 문서 리소스에 대 한 액세스를 [제어](workbooks-access-control.md) 하 고 공유 합니다.