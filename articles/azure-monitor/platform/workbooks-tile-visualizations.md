---
title: Azure Monitor 통합 문서 타일 시각화
description: 모든 Azure Monitor 통합 문서 타일 시각화에 대해 알아봅니다.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: 07de9fcd01559e59a1bcd5d8928075aebcf79cc7
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89664393"
---
# <a name="tile-visualizations"></a>타일 시각화

타일은 통합 문서에 요약 데이터를 표시 하는 데 유용한 방법입니다. 아래 이미지는 상세 그리드 위에 앱 수준 요약이 있는 타일의 일반적인 사용 사례를 보여 줍니다.

[![타일 요약 보기의 스크린샷](./media/workbooks-tile-visualizations/tiles-summary.png)](./media/workbooks-tile-visualizations/tiles-summary.png#lightbox)

통합 문서 타일은 제목, 부제목, 큰 텍스트, 아이콘, 메트릭 기반 그라데이션, spark 선/가로 막대형, 바닥글 등을 표시 하는 기능을 지원 합니다.

## <a name="adding-a-tile"></a>타일 추가

1. _편집_ 도구 모음 항목을 클릭 하 여 통합 문서를 편집 모드로 전환 합니다.
2. **추가** 를 선택 하 고 *쿼리 추가* 를 선택 하 여 통합 문서에 로그 쿼리 컨트롤을 추가 합니다.
3. **로그**, 리소스 유형 (예: Application Insights) 및 대상으로 사용할 리소스를 선택 합니다.
4. 쿼리 편집기를 사용 하 여 분석을 위한 KQL를 입력 합니다.

    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc
    ```

5. 크기를 **Full**로 설정 합니다.
6. 시각화를 **타일로**설정 합니다.
7. **타일 설정** 단추를 선택 하 여 설정 창을 엽니다.
    1. *제목*에서 다음을 설정 합니다.
        * 열 `name` 사용:
    2. *왼쪽*에서 다음을 설정 합니다.
        * 열 `Requests` 사용:
        * 열 `Big Number` 렌더러:
        * 색상표: `Green to Red`
        * 최 솟 값은 `0` 입니다.
    3. *아래쪽*에서 다음을 설정 합니다.
        * 열 `appName` 사용:
8. 창 맨 아래에 있는 **저장 및 닫기** 단추를 선택 합니다.

[![위의 쿼리 및 타일 설정이 포함 된 타일 요약 뷰의 스크린샷](./media/workbooks-tile-visualizations/tile-settings.png)](./media/workbooks-tile-visualizations/tile-settings.png#lightbox)

읽기 모드의 타일:

[![읽기 모드의 타일 요약 보기 스크린샷](./media/workbooks-tile-visualizations/tiles-read-mode.png)](./media/workbooks-tile-visualizations/tiles-read-mode.png#lightbox)

## <a name="spark-lines-in-tiles"></a>타일의 Spark 선

1. _편집_ 도구 모음 항목을 클릭 하 여 통합 문서를 편집 모드로 전환 합니다.
2. 이라는 시간 범위 매개 변수를 추가 `TimeRange` 합니다.
    1. **추가** 를 선택한 다음 *매개 변수를 추가*합니다.
    2. 매개 변수 컨트롤에서 **매개 변수 추가**를 선택 합니다.
    3. `TimeRange` *매개 변수 이름* 필드에를 입력 하 `Time range picker` 고 *매개 변수 형식*으로를 선택 합니다.
    4. 창 위쪽에서 **저장** 을 선택한 다음 매개 변수 컨트롤에서 **편집 완료** 를 선택 합니다.
3. **추가** 를 선택 하 고 *쿼리 추가* 를 선택 하 여 매개 변수 컨트롤 아래에 로그 쿼리 컨트롤을 추가 합니다.
4. **로그**, 리소스 유형 (예: Application Insights) 및 대상으로 사용할 리소스를 선택 합니다.
5. 쿼리 편집기를 사용 하 여 분석을 위한 KQL를 입력 합니다.

    ```kusto
    let topRequests = requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc;
    let topRequestNames = topRequests | project name;
    requests
    | where name in (topRequestNames)
    | make-series Trend = count() default = 0 on timestamp from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by name
    | join (topRequests) on name
    | project-away name1, timestamp
    ```

6. **쿼리 실행**을 선택합니다. `TimeRange`쿼리를 실행 하기 전에를 선택한 값으로 설정 해야 합니다.
7. *시각화* 를 "타일"로 설정 합니다.
8. *크기* 를 "Full"로 설정 합니다.
9. **타일 설정**을 선택 합니다.
    1. *타일*에서 다음을 설정 합니다.
        * 열 `name` 사용:
    2. 하위 *타일*에서 다음을 설정 합니다.
        *  열 `appNAme` 사용:
    3. *왼쪽*에서 다음을 설정 합니다.
        *  열 `Requests` 사용:
        * 열 `Big Number` 렌더러:
        * 색상표: `Green to Red` .
        * 최 솟 값은 `0` 입니다.
    4. *아래쪽*에서 다음을 설정 합니다.
        * 열 `Trend` 사용:
        * 열 `Spark line` 렌더러:
        * 색상표: `Green to Red` .
        * 최 솟 값은 `0` 입니다.
10. **저장을 선택 하 고** 창 아래쪽에서 닫습니다.

![Spark 선을 사용 하 여 타일 시각화의 스크린샷](./media/workbooks-tile-visualizations/spark-line.png)

## <a name="tile-sizes"></a>타일 크기

작성자에 게는 타일 설정에서 타일 너비를 설정 하는 옵션이 있습니다.

* `fixed`(기본값)

    타일의 기본 동작은 너비가 160 픽셀이 고 타일 주위의 공간을 합한 크기와 동일 합니다.

    ![고정 폭 타일을 표시 하는 스크린샷](./media/workbooks-tile-visualizations/tiles-fixed.png)
* `auto`

    각 제목은 내용에 맞게 축소 또는 확대 되지만 타일은 타일 보기의 너비 (가로 스크롤 없음)로 제한 됩니다.

    ![자동 너비 타일을 표시 하는 스크린샷](./media/workbooks-tile-visualizations/tiles-auto.png)
* `full size`

    각 제목은 항상 타일 보기의 전체 너비가 되며, 한 줄에 한 제목으로 표시 됩니다.

     ![전체 크기의 가로 바둑판식 배열을 표시 하는 스크린샷](./media/workbooks-tile-visualizations/tiles-full.png)

## <a name="next-steps"></a>다음 단계

* 타일은 복합 막대 렌더러도 지원 합니다. 자세히 알아보려면 [복합 막대 설명서](workbooks-composite-bar.md)를 참조 하세요.
* 시간 매개 변수에 대 한 자세한 내용은 `TimeRange` [통합 문서 시간 매개 변수 설명서](workbooks-time.md)를 참조 하세요.