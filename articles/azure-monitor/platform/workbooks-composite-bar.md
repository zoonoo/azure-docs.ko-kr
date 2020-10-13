---
title: Azure Monitor 통합 문서 복합 막대 렌더러
description: 모든 Azure Monitor 통합 문서 복합 가로 막대형 렌더러 시각화에 대해 알아봅니다.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 9/04/2020
ms.author: lagayhar
ms.openlocfilehash: 80846ecb1ad48d9f8ba49f0025772b4e131c23e2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776360"
---
# <a name="composite-bar-renderer"></a>복합 막대 렌더러

통합 문서를 사용 하면 여러 막대로 구성 된 가로 막대를 사용 하 여 데이터를 렌더링할 수 있습니다.

아래 이미지는 온라인 상태, 오프 라인 상태, 복구 중 상태에 있는 서버 수를 나타내는 데이터베이스 상태의 복합 막대를 보여줍니다.

![데이터베이스 상태에 대 한 복합 막대의 스크린샷](./media/workbooks-composite-bar/database-status.png)

복합 막대 렌더러는 그리드, 타일 및 그래프 시각화에 대해 지원 됩니다.

## <a name="adding-composite-bar-renderer"></a>복합 막대 렌더러 추가

1. 도구 모음 항목 *편집* 을 선택 하 여 통합 문서를 편집 모드로 전환 합니다.
2. *추가* 를 선택 하 고 *쿼리 추가*를 선택 합니다.
3. *데이터 소스* 를 "JSON"으로 설정 하 고 *시각화* 를 "그리드"로 설정 합니다.
4. 다음 JSON 데이터를 추가 합니다.

```json
[
    {"sub":"X", "server": "A", "online": 20, "recovering": 3, "offline": 4, "total": 27},
    {"sub":"X", "server": "B", "online": 15, "recovering": 8, "offline": 5, "total": 28},
    {"sub":"Y", "server": "C", "online": 25, "recovering": 4, "offline": 5, "total": 34},
    {"sub":"Y", "server": "D", "online": 18, "recovering": 6, "offline": 9, "total": 33}
]
```

5. 쿼리를 실행 합니다.
6. **열 설정을** 선택 하 여 설정을 엽니다.
7. *열* 에서 "합계"를 선택 하 고 *열 렌더러*의 경우 "복합 막대"를 선택 합니다.
8. *복합 막대 설정*에서 다음 설정을 설정 합니다.

| 열 이름 | 색        |
|-------------|--------------|
| 온라인      | 녹색        |
| 복구  | 노란색       |
| 오프라인     | 빨강 (밝음) |

9. 레이블 추가:`["online"] of ["total"] are healthy`
10. 온라인, 오프 라인 및 복구를 위한 열 설정에서 열 렌더러를 "Hidden" (선택 사항)으로 설정할 수 있습니다.
11. 위쪽에 있는 *레이블을* 선택 하 고 전체 열에 대 한 레이블을 "데이터베이스 상태" (선택 사항)로 업데이트 합니다.
12. **적용** 시 선택

복합 막대 설정은 아래 스크린샷 처럼 표시 됩니다.

![위에서 설명한 설정을 사용 하 여 복합 가로 막대형 열 설정의 스크린샷](./media/workbooks-composite-bar/composite-bar-settings.png)

위의 설정이 있는 복합 막대:

![복합 막대의 스크린샷](./media/workbooks-composite-bar/composite-bar.png)

## <a name="composite-bar-settings"></a>복합 막대 설정

열 이름 및 해당 색을 선택 하 여 해당 색의 열을 복합 막대의 일부분으로 렌더링 합니다. 행을 삽입, 삭제 및 이동 할 수 있습니다.

### <a name="label"></a>레이블

복합 막대 레이블이 복합 막대의 위쪽에 표시 됩니다. 정적 텍스트, 열 및 매개 변수를 함께 사용할 수 있습니다.  Label이 비어 있으면 현재 열의 값이 레이블로 표시 됩니다. 이전 예제에서 레이블 필드를 왼쪽으로 남겨 둔 경우 합계 열의 값이 표시 됩니다.

열을 참조 `["columnName"]` 하세요.

매개 변수를 참조 하십시오 `{paramName}` .

열 이름과 매개 변수 이름은 대/소문자를 구분 합니다. "이 항목을 링크로 설정"을 선택 하 고 링크 설정을 추가 하 여 레이블을 링크로 만들 수도 있습니다.

### <a name="aggregation"></a>집계

집계는 시각화 별 트리/그룹에 유용 합니다. 그룹 행의 열에 대 한 데이터는 해당 열에 대 한 집계 집합으로 결정 됩니다. 복합 막대에 적용할 수 있는 집계에는 None, Sum 및 Inherit의 세 가지 유형이 있습니다.

설정 별 그룹화를 추가 하려면:

1. 열 설정에서 설정을 추가 하려는 열로 이동 합니다.
2. 트리 *유형*아래 트리 */그룹 설정* 에서 **그룹화 방법** 을 선택 합니다.
3. 그룹화 할 필드를 선택 합니다.

![설정 별 그룹화의 스크린샷](./media/workbooks-composite-bar/group-by-settings.png)

#### <a name="none"></a>없음

None 집계는 그룹 행에 대해 해당 열에 대 한 결과를 표시 하지 않음을 의미 합니다.

![집계가 없음 인 복합 막대의 스크린샷](./media/workbooks-composite-bar/none.png)

#### <a name="sum"></a>합계

집계가 합계로 설정 된 경우 그룹 행의 열에는 렌더링에 사용 되는 열의 합계를 사용 하 여 복합 막대가 표시 됩니다. 또한 레이블은 참조 된 열의 합계를 사용 합니다.

아래 예제에서 온라인, 오프 라인 및 복구는 모두 max 집계를 설정 하 고 total 열의 집계는 sum입니다.

![합계 집계가 있는 복합 막대의 스크린샷](./media/workbooks-composite-bar/sum.png)

#### <a name="inherit"></a>상속

집계가 상속으로 설정 된 경우 그룹 행의 열에는 해당 열을 렌더링 하는 데 사용 되는 열에 대해 사용자가 설정한 집계를 사용 하 여 복합 막대가 표시 됩니다. 레이블에 사용 되는 열에는 사용자가 설정한 집계가 사용 됩니다. 현재 열 렌더러가 복합 가로이 고 레이블 (위 예제의 경우 "total" 처럼 refereed) 이면 sum이 해당 열의 집계로 사용 됩니다.

아래 예제에서 온라인, 오프 라인 및 복구는 모두 최대 집계를 설정 하 고 total 열의 집계가 상속 됩니다.

![집계가 상속 된 복합 막대의 스크린샷](./media/workbooks-composite-bar/inherit.png)

## <a name="sorting"></a>정렬

그리드 시각화의 경우 복합 막대 렌더러를 사용 하는 열에 대 한 행 정렬은 복합 막대 컴퓨터를 동적으로 렌더링 하는 데 사용 되는 열의 합계인 값을 기준으로 작동 합니다. 앞의 예제에서 정렬에 사용 되는 값은 온라인, 복구 및 해당 특정 행에 대 한 오프 라인 열의 합계입니다.

## <a name="tiles-visualization"></a>타일 시각화

1. **추가** 및 *쿼리 추가*를 선택 합니다.
2. 데이터 원본을 JSON으로 변경 하 여 [이전 예제의](#adding-composite-bar-renderer)데이터를 입력 합니다.
3. 시각화를 *타일로*변경 합니다.
4. 쿼리를 실행 합니다.
5. **타일 설정**을 선택 합니다.
6. 타일 필드에서 *왼쪽* 을 선택 합니다.
7. *필드 설정*아래에 있는 설정을 입력 합니다.
    1. 열 "server"를 사용 합니다.
    2. 열 렌더러: "Text"
8. 타일 필드에서 *아래쪽* 을 선택 합니다.
9. *필드 설정*아래에 있는 설정을 입력 합니다.
    1. 열 "total"을 사용 합니다.
    2. 열 렌더러: "복합 막대".
    3. "복합 막대 설정"에서 다음 설정으로 설정을 입력 합니다.

    | 열 이름 | 색        |
    |-------------|--------------|
    | 온라인      | 녹색        |
    | 복구  | 노란색       |
    | 오프라인     | 빨강 (밝음) |

    4. 레이블 `["online"] of ["total"] are healthy` 추가:
10. **적용**을 선택합니다.

타일에 대 한 복합 표시줄 설정:

![위에서 설명한 설정을 사용 하 여 복합 막대 타일 설정의 스크린샷](./media/workbooks-composite-bar/tiles-settings.png)

위의 설정이 있는 타일의 복합 막대 보기는 다음과 같습니다.

![복합 가로 막대 타일의 스크린샷](./media/workbooks-composite-bar/composite-bar-tiles.png)

## <a name="graphs-visualization"></a>그래프 시각화

그래프 시각화를 위한 복합 막대 렌더러를 만들려면 (Hive 클러스터 입력) 아래 지침을 따르세요.

1. **추가** 및 *쿼리 추가*를 선택 합니다.
2. 데이터 원본을 JSON으로 변경 하 여 [이전 예제의](#adding-composite-bar-renderer)데이터를 입력 합니다.
3. 시각화를 *그래프로*변경 합니다.
4. 쿼리를 실행 합니다.
5. **그래프 설정**을 선택 합니다.
6. 노드 형식 설정에서 *가운데 콘텐츠* 를 선택 합니다.
7. *필드 설정*아래에 있는 설정을 입력 합니다.
    1. 열 "total"을 사용 합니다.
    2. 열 렌더러: "복합 막대".
    3. *복합 표시줄 설정*에서 다음 설정을 입력 합니다.

    |열 이름  |     색    |
    |-------------|--------------|
    | 온라인      | 녹색        |
    | 복구  | 노란색       |
    | 오프라인     | 빨강 (밝음) |

   4. 레이블 `["online"] of ["total"] are healthy` 추가:
9. *레이아웃 설정*아래에 있는 설정을 입력 합니다.
    1. 그래프 유형: **Hive 클러스터**.
    2. 노드 ID select: "server".
    3. 필드별 그룹화: "None"
    4. 노드 크기: 100.
    5. 육각형: 5 사이의 여백입니다.
    6. 형식 색 지정: **없음**
1. **적용**을 선택합니다.
    
그래프에 대 한 복합 막대 설정:

![위에서 설명한 설정이 있는 복합 막대 그래프 설정의 스크린샷](./media/workbooks-composite-bar/graphs-settings.png)

위의 설정이 있는 그래프의 복합 막대 보기는 다음과 같습니다.

![Hive 클러스터가 포함 된 복합 막대 그래프의 스크린샷](./media/workbooks-composite-bar/composite-bar-graphs.png)

## <a name="next-steps"></a>다음 단계

* Azure Resource Manager를 사용 하 여 통합 문서를 [배포](workbooks-automate.md) 합니다.
* 통합 문서 리소스에 대 한 액세스를 [제어](workbooks-access-control.md) 하 고 공유 합니다.
