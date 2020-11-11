---
title: Azure Monitor의 Log Analytics 개요
description: Azure Monitor 로그의 데이터를 분석 하기 위해 로그 쿼리를 편집 및 실행 하는 데 사용 되는 Azure Portal의 도구인 Log Analytics 설명 합니다.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/04/2020
ms.openlocfilehash: 26d6bcb52099b15aeeb73a36a7144c14bdf481d6
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94497342"
---
# <a name="overview-of-log-analytics-in-azure-monitor"></a>Azure Monitor의 Log Analytics 개요
Log Analytics는 Azure Monitor 로그의 데이터로 로그 쿼리를 편집 및 실행 하는 데 사용 되는 Azure Portal 도구입니다. 레코드 집합을 반환 하는 간단한 쿼리를 작성 한 다음 Log Analytics 기능을 사용 하 여 정렬, 필터링 및 분석할 수 있습니다. 또는 통계 분석을 수행 하 고 차트에서 결과를 시각화 하 여 특정 추세를 식별 하는 고급 쿼리를 작성할 수 있습니다. 쿼리 결과를 대화형으로 사용 하거나 쿼리 경고 또는 통합 문서 로그와 같은 다른 Azure Monitor 기능과 함께 사용 하는 경우에는 쓰기 및 테스트를 사용 하는 도구 Log Analytics. 


> [!TIP]
> 이 문서에서는 Log Analytics 및 각 기능에 대 한 설명을 제공 합니다. 자습서로 바로 이동 하려면 [Log Analytics 자습서](get-started-portal.md)를 참조 하세요.



## <a name="starting-log-analytics"></a>시작 Log Analytics
Azure Portal의 **Azure Monitor** 메뉴에 있는 **로그** 에서 Log Analytics를 시작 합니다. 또한 대부분의 Azure 리소스에 대 한 메뉴에서이 옵션을 볼 수 있습니다. 에서 시작 하는 위치에 관계 없이이 도구는 동일한 Log Analytics 도구입니다. Log Analytics를 시작 하는 데 사용 하는 메뉴에는 사용할 수 있는 데이터가 결정 됩니다. **Azure Monitor** 메뉴 또는 **Log Analytics 작업 영역** 메뉴에서 시작 하는 경우 작업 영역에 있는 모든 레코드에 액세스할 수 있습니다. 다른 유형의 리소스에서 **로그** 를 선택 하면 해당 리소스에 대 한 로그 데이터로 데이터가 제한 됩니다. 자세한 내용은 [Azure Monitor Log Analytics의 로그 쿼리 범위 및 시간 범위](scope.md)를 참조하세요.

[![시작 Log Analytics](media/log-analytics-overview/start-log-analytics.png)](media/log-analytics-overview/start-log-analytics.png#lightbox)

Log Analytics를 시작할 때 가장 먼저 표시 되는 항목은 [예제 쿼리](example-queries.md)를 포함 하는 대화 상자입니다. 이러한 항목은 솔루션 별로 분류 되며 특정 요구 사항과 일치 하는 쿼리를 찾아보거나 검색할 수 있습니다. 필요한 작업을 정확히 수행 하는를 찾거나 편집기에 로드 하 고 필요에 따라 수정할 수 있습니다. 예제 쿼리를 통해 검색 하는 것은 실제로 쿼리를 작성 하는 방법을 배울 수 있는 좋은 방법입니다. 물론 빈 스크립트로 시작 하 여 직접 작성 하려는 경우 예제 쿼리를 닫을 수 있습니다. 화면 위쪽에서 **쿼리** 를 다시 가져오려면 클릭 하면 됩니다.

## <a name="log-analytics-interface"></a>Log Analytics 인터페이스
다음 이미지는 Log Analytics의 다양 한 구성 요소를 식별 합니다.

[![Log Analytics](media/log-analytics-overview/log-analytics.png)](media/log-analytics-overview/log-analytics.png#lightbox)

### <a name="1-top-action-bar"></a>1. 위쪽 작업 모음
쿼리 창에서 쿼리를 사용 하기 위한 컨트롤입니다.

| 옵션 | 설명 |
|:---|:---|
| Scope | 쿼리에 사용 되는 데이터의 범위를 지정 합니다. 이는 Log Analytics 작업 영역에 있는 모든 데이터 이거나 여러 작업 영역에서 특정 리소스에 대 한 데이터 일 수 있습니다. [쿼리 범위](scope.md)를 참조 하세요. |
| 실행 단추 | 쿼리 창에서 선택한 쿼리를 실행 하려면 클릭 합니다. Shift + enter를 눌러 쿼리를 실행할 수도 있습니다. |
| 시간 선택기 | 쿼리에 사용할 수 있는 데이터의 시간 범위를 선택 합니다. 쿼리에 시간 필터를 포함 하는 경우이는 무시 됩니다. [Azure Monitor Log Analytics의 로그 쿼리 범위 및 시간 범위](scope.md)를 참조 하세요. |
| 저장 단추 | 작업 영역에 대 한 쿼리 탐색기에 쿼리를 저장 합니다. |
 복사 단추 | 쿼리, 쿼리 텍스트 또는 쿼리 결과에 대 한 링크를 클립보드에 복사 합니다. |
| 새 경고 규칙 단추 | 빈 쿼리를 사용 하 여 새 탭을 만듭니다. |
| 내보내기 단추 | 쿼리 결과를 CSV 파일 또는 쿼리로 내보내 Power Bi에서 사용할 수 있도록 수식 언어 형식 파워 쿼리 합니다. |
| 대시보드에 고정 단추 | Azure 대시보드에 쿼리 결과를 추가 합니다. |
| 쿼리 단추 서식 지정 | 가독성을 위해 선택한 텍스트를 정렬 합니다. |
| 예제 쿼리 단추 | Log Analytics를 처음 열 때 표시 되는 쿼리 예제 대화 상자를 엽니다. |
| 쿼리 탐색기 단추 | 작업 영역에서 저장 된 쿼리에 대 한 액세스를 제공 하는 **쿼리 탐색기** 를 엽니다. |


### <a name="2-sidebar"></a>2. 사이드바
작업 영역에 있는 테이블 목록, 예제 쿼리 및 현재 쿼리에 대 한 필터 옵션입니다.

| 탭 | Description |
|:---|:---|
| 테이블 | 선택한 범위에 속하는 테이블을 나열 합니다. **그룹화** 방법을 선택 하 여 테이블 그룹화를 변경 합니다. 테이블 이름 위로 마우스를 이동 하 여 테이블에 대 한 설명이 포함 된 대화 상자를 표시 하 고 해당 설명서를 보고 데이터를 미리 볼 수 있습니다. 테이블을 확장 하 여 해당 열을 표시 합니다. 테이블 또는 열 이름을 두 번 클릭 하 여 쿼리에 추가 합니다. |
| 쿼리 | 쿼리 창에서 열 수 있는 예제 쿼리 목록입니다. Log Analytics를 열 때 표시 되는 것과 동일한 목록입니다. **그룹화** 방법을 선택 하 여 쿼리 그룹화를 변경 합니다. 쿼리를 두 번 클릭 하 여 쿼리 창에 추가 하거나 다른 옵션에 대해 마우스로 가리킵니다. |
| Assert | 쿼리 결과에 따라 필터 옵션을 만듭니다. 쿼리를 실행 한 후 결과와 다른 값이 포함 된 열이 표시 됩니다. 하나 이상의 값을 선택 하 고 **적용 & 실행** 을 클릭 하 여 쿼리에 **where** 명령을 추가 하 고 다시 실행 합니다. |

### <a name="3-query-window"></a>3. 쿼리 창
쿼리 창에서 쿼리를 편집 합니다. 여기에는 가독성을 높이기 위해 KQL 명령 및 색 구분을 위한 intellisense가 포함 됩니다. **+** 창 맨 위에 있는를 클릭 하 여 다른 탭을 엽니다.

단일 창에는 여러 개의 쿼리가 포함 될 수 있습니다. 쿼리에는 빈 줄이 포함 될 수 없으므로 창의 여러 쿼리를 하나 이상의 빈 줄로 구분할 수 있습니다. 현재 쿼리는 커서가 있는 위치에 있는 커서를 포함 하는 쿼리입니다.

현재 쿼리를 실행 하려면 **실행** 단추를 클릭 하거나 Shift + enter를 누릅니다.

### <a name="4-results-window"></a>4. 결과 창
쿼리 결과가 결과 창에 표시 됩니다. 기본적으로 결과는 테이블로 표시 됩니다. 차트로 표시 하려면 결과 창에서 **차트** 를 선택 하거나 쿼리에 **렌더링** 명령을 추가 합니다.

#### <a name="results-view"></a>Results view(결과 보기)
열 및 행으로 구성 된 테이블에 쿼리 결과를 표시 합니다. 행의 왼쪽을 클릭 하 여 해당 값을 확장 합니다. **열 드롭다운을** 클릭 하 여 열 목록을 변경 합니다. 열 이름을 클릭 하 여 결과를 정렬 합니다. 열 이름 옆의 깔때기를 클릭 하 여 결과를 필터링 합니다. 필터를 지우고 쿼리를 다시 실행 하 여 정렬을 다시 설정 합니다.

**그룹 열** 을 선택 하 여 쿼리 결과 위에 그룹화 막대를 표시 합니다. 결과를 막대로 끌어 열을 그룹화 합니다. 열을 더 추가 하 여 결과에 중첩 된 그룹을 만듭니다. 

#### <a name="chart-view"></a>차트 보기
사용 가능한 여러 차트 종류 중 하나로 결과를 표시 합니다. 쿼리의 **render** 명령에 차트 종류를 지정 하거나 **시각화 유형** 드롭다운에서 선택할 수 있습니다.

| 옵션 | 설명 |
|:---|:---|
| **시각화 유형** | 표시할 차트의 유형입니다. |
| **X 축** | X 축에 사용할 결과의 열입니다. 
| **Y 축** | Y 축에 사용할 결과의 열입니다. 이는 일반적으로 숫자 열입니다. |
| **분할 기준** | 차트에서 계열을 정의 하는 결과의 열입니다. 열의 각 값에 대해 계열이 생성 됩니다. |
| **집계** | Y 축의 숫자 값에 대해 수행할 집계의 유형입니다. |

## <a name="relationship-to-azure-data-explorer"></a>Azure 데이터 탐색기에 대 한 관계
Azure 데이터 탐색기 웹 UI를 이미 잘 알고 있는 경우에는 Log Analytics에 대해 잘 알고 있어야 합니다. Azure 데이터 탐색기을 기반으로 구축 되 고 동일한 Kusto 쿼리 언어 (KQL)를 사용 하기 때문입니다. Log Analytics 시간 범위를 기준으로 필터링, 쿼리에서 경고 규칙을 만드는 기능 등 Azure Monitor 관련 된 기능을 추가 합니다. 두 도구 모두에는 사용 가능한 테이블의 구조를 검색할 수 있는 탐색기가 포함 되어 있지만 Azure 데이터 탐색기 웹 UI는 기본적으로 Azure 데이터 탐색기 데이터베이스의 테이블을 사용 하 고 Log Analytics는 Log Analytics 작업 영역의 테이블에 대해 작동 합니다. 

## <a name="next-steps"></a>다음 단계
- [Azure Portal에서 Log Analytics를 사용 하는 방법에 대 한 자습서](get-started-portal.md)를 안내 합니다.
- [쿼리 작성에 대 한 자습서](get-started-queries.md)를 안내 합니다.
