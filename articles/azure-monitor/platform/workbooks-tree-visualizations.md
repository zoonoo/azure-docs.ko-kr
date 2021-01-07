---
title: Azure Monitor 통합 문서 트리 시각화
description: 모든 Azure Monitor 통합 문서 트리 시각화에 대해 알아봅니다.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: a815f4dcf7c76d4c11b4f2e227802b15f9c92a54
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89664249"
---
# <a name="tree-visualizations"></a>트리 시각화

통합 문서는 트리-그리드를 통해 계층 뷰를 지원 합니다. 트리를 사용 하면 드릴 다운 환경에서 일부 행을 다음 수준으로 확장할 수 있습니다.

아래 예제에서는 트리 그리드로 시각화 된 컨테이너 상태 메트릭 (작업 집합 크기)을 보여 줍니다. 여기서 최상위 노드는 AKS (Azure Kubernetes Service) 노드이 고, 다음 수준은 pod 이며, 최종 수준은 컨테이너입니다. 표 형태 (열 지도, 아이콘, 링크)와 같이 열의 서식을 지정할 수 있습니다. 이 경우 기본 데이터 원본은 AKS 로그가 있는 Log Analytics 작업 영역입니다.

[![타일 요약 보기의 스크린샷](./media/workbooks-tree-visualizations/trees.png)](./media/workbooks-tree-visualizations/trees.png#lightbox)

## <a name="adding-a-tree-grid"></a>트리 그리드 추가
1. _편집_ 도구 모음 항목을 클릭 하 여 통합 문서를 편집 모드로 전환 합니다.
2. **추가** 를 선택 하 고 *쿼리 추가* 를 선택 하 여 통합 문서에 로그 쿼리 컨트롤을 추가 합니다.
3. **로그**, 리소스 유형 (예: Application Insights) 및 대상으로 할 리소스를 선택 합니다.
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
6. **열 설정** 단추를 선택 하 여 설정 창을 엽니다.
7. 아래쪽의 **트리/그룹화 방법 설정** 섹션에서 다음을 설정 합니다.
    * 트리 형식: `Parent/Child`
    * Id 필드: `Id`
    * 부모 Id 필드: `ParentId`
    * 확장기 표시 위치: `Name`
    * *트리의 최상위 수준을 확장* 합니다. 확인란을 선택 합니다.
8. 위쪽의 _열_ 섹션에서 다음을 설정 합니다.
    * _Id_ 열 렌더러: `Hidden`
    * _부모 Id_ -열 렌더러: `Hidden`
    * _요청_ -열 렌더러: `Bar` , 색: `Blue` , 최소값: `0`
9. 창 맨 아래에 있는 **저장 및 닫기** 단추를 선택 합니다.

[![위의 쿼리 및 설정이 포함 된 타일 요약 뷰의 스크린샷](./media/workbooks-tree-visualizations/tree-settings.png)](./media/workbooks-tree-visualizations/tree-settings.png#lightbox)

## <a name="tree-settings"></a>트리 설정

| 설정 | 설명 |
|:------------- |:-------------|
| `Id Field` | 표의 모든 행에 대 한 고유 ID입니다. |
| `Parent Id Field` | 현재 행의 부모 ID입니다. |
| `Show the expander on` | 트리 확장기를 표시할 열입니다. 트리 그리드는 읽을 수 없기 때문에 해당 ID 및 부모 ID 필드를 숨기는 것이 일반적입니다. 대신, 확장은 엔터티의 이름과 같이 보다 읽기 쉬운 값이 있는 필드에 표시 됩니다. |
| `Expand the top level of the tree` | 이 확인란을 선택 하면 최상위 수준에서 트리 눈금이 확장 됩니다. 기본적으로 추가 정보를 표시 하려는 경우에 유용 합니다. |

## <a name="grouping-in-a-grid"></a>표 형태의 그룹화

그룹화를 사용 하면 훨씬 간단한 쿼리를 사용 하 여 위와 유사한 계층 뷰를 작성할 수 있습니다. 트리의 내부 노드에서 집계가 손실 되지만 일부 시나리오에서는이를 사용할 수 있습니다. 기본 결과 집합을 적절 한 무료 형식으로 변환할 수 없는 경우 *Group By* 를 사용 하 여 트리 뷰를 작성 합니다 (예: 경고, 상태 및 메트릭 데이터).

## <a name="adding-a-tree-using-grouping"></a>그룹화를 사용 하 여 트리 추가

1. _편집_ 도구 모음 항목을 클릭 하 여 통합 문서를 편집 모드로 전환 합니다.
2. **추가** 를 선택 하 고 *쿼리 추가* 를 선택 하 여 통합 문서에 로그 쿼리 컨트롤을 추가 합니다.
3. **로그**, 리소스 유형 (예: Application Insights) 및 대상으로 사용할 리소스를 선택 합니다.
4. 쿼리 편집기를 사용 하 여 분석을 위한 KQL 입력
    ```kusto
    requests
    | summarize Requests = count() by App = appName, RequestName = name
    | order by Requests desc
    ```
1. 시각화를 *그리드*로 설정 합니다.
2. **열 설정 단추** 를 선택 하 여 설정 창을 엽니다.
3. 아래쪽의 *트리/그룹화 방법 설정* 섹션에서 다음을 설정 합니다.
    * 트리 형식: `Group By`
    * 그룹화 기준: `App`
    * 다음 기준: `None`
    * *트리의 최상위 수준을 확장* 합니다. 확인란을 선택 합니다.
4. 위쪽의 *열* 섹션에서 다음을 설정 합니다.
    * *앱* 열 렌더러: `Hidden`
    * *요청* -열 렌더러: `Bar` , 색: `Blue` , 최소값: `0`
5. 창 맨 아래에 있는 **저장 및 닫기** 단추를 선택 합니다.

[![통합 문서에서 트리 시각화 만들기를 보여 주는 스크린샷](./media/workbooks-tree-visualizations/tree-group-create.png)](./media/workbooks-tree-visualizations/tree-group-create.png#lightbox)

## <a name="next-steps"></a>다음 단계

* [통합 문서에서 그래프](workbooks-graph-visualizations.md)를 만드는 방법에 대해 알아봅니다.
* [통합 문서에서 타일](workbooks-tile-visualizations.md)을 만드는 방법에 대해 알아봅니다.
