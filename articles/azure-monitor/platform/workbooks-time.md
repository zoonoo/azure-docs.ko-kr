---
title: Azure Monitor 통합 문서 시간 매개 변수
description: 미리 빌드되고 사용자 지정 매개 변수가 있는 통합 문서를 사용하여 복잡한 보고 간소화
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 380b8a7ce286ab06b6935bf63bf3a0e82f371c2f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "77658016"
---
# <a name="workbook-time-parameters"></a>통합 문서 시간 매개 변수

사용자는 시간 매개 변수를 사용 하 여 분석의 시간 컨텍스트를 설정 하 고 거의 모든 보고서에 사용 됩니다. 사용자 지정 시간 범위에 대 한 옵션을 포함 하 여 작성자가 드롭다운에서 표시할 시간 범위를 지정할 수 있도록 비교적 간단 하 게 설정 하 고 사용할 수 있습니다. 

## <a name="creating-a-time-parameter"></a>시간 매개 변수 만들기
1. 편집 모드에서 빈 통합 문서를 시작 합니다.
2. 통합 문서 내의 링크에서 _매개 변수 추가_ 를 선택 합니다.
3. 파란색 _매개 변수 추가_ 단추를 클릭 합니다.
4. 새 매개 변수 창에서 enter 키를 누르십시오.
    1. 매개 변수 이름:`TimeRange`
    2. 매개 변수 유형:`Time range picker`
    3. 필수:`checked`
    4. 사용 가능한 시간 범위: 지난 1 시간, 지난 12 시간, 지난 24 시간, 최근 48 시간, 지난 3 일, 지난 7 일 및 사용자 지정 시간 범위 선택 허용
5. 도구 모음에서 ' 저장 '을 선택 하 여 매개 변수를 만듭니다.

    ![시간 범위 매개 변수 생성을 보여 주는 이미지](./media/workbooks-time/time-settings.png)

읽기 모드에서 통합 문서를 표시 하는 방법입니다.

![읽기 모드의 시간 범위 매개 변수를 보여 주는 이미지](./media/workbooks-time/parameters-time.png)

## <a name="referencing-a-time-parameter"></a>Time 매개 변수 참조
### <a name="via-bindings"></a>Via 바인딩
1. 통합 문서에 쿼리 컨트롤을 추가 하 고 Application Insights 리소스를 선택 합니다.
2. 대부분의 통합 문서 컨트롤은 _시간 범위_ 범위 선택을 지원 합니다. _시간 범위_ 드롭다운을 열고 `{TimeRange}` 아래쪽의 time 결심 매개 변수 그룹에서를 선택 합니다.
3. 이는 시간 범위 매개 변수를 차트의 시간 범위에 바인딩합니다. 샘플 쿼리의 시간 범위는 이제 최근 24 시간입니다.
4. 쿼리를 실행 하 여 결과를 확인 합니다.

    ![바인딩을 통해 참조 되는 시간 범위 매개 변수를 보여 주는 이미지](./media/workbooks-time/time-binding.png)

### <a name="in-kql"></a>KQL에서
1. 통합 문서에 쿼리 컨트롤을 추가 하 고 Application Insights 리소스를 선택 합니다.
2. KQL에서 매개 변수를 사용 하 여 시간 범위 필터를 입력 합니다.`| where timestamp {TimeRange}`
3. 이렇게 하면 쿼리 평가 시간이 `| where timestamp > ago(1d)` 매개 변수의 시간 범위 값인로 확장 됩니다.
4. 쿼리를 실행 하 여 결과를 확인 합니다.

    ![KQL에서 참조 되는 시간 범위를 보여 주는 이미지](./media/workbooks-time/time-in-code.png)

### <a name="in-text"></a>텍스트 
1. 통합 문서에 텍스트 컨트롤을 추가 합니다.
2. Markdown에 다음을 입력 합니다.`The chosen time range is {TimeRange:label}`
3. _편집 완료_ 를 선택 합니다.
4. 텍스트 컨트롤에 텍스트가 표시 됩니다. _선택한 시간 범위는 최근 24 시간입니다_ .

## <a name="time-parameter-options"></a>시간 매개 변수 옵션
| 매개 변수 | 설명 | 예제 |
| ------------- |:-------------|:-------------|
| `{TimeRange}` | 시간 범위 레이블 | 최근 24시간 |
| `{TimeRange:label}` | 시간 범위 레이블 | 최근 24시간 |
| `{TimeRange:value}` | 시간 범위 값 | > 전 (1d) |
| `{TimeRange:query}` | 시간 범위 쿼리 | > 전 (1d) |
| `{TimeRange:start}` | 시간 범위 시작 시간 | 오후 3/20/2019 4:18 |
| `{TimeRange:end}` | 시간 범위 종료 시간 | 오후 3/21/2019 4:18 |
| `{TimeRange:grain}` | 시간 범위 수준 | 30 m |


### <a name="using-parameter-options-in-a-query"></a>쿼리에서 매개 변수 옵션 사용
```kusto
requests
| make-series Requests = count() default = 0 on timestamp from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain}
```

## <a name="next-steps"></a>다음 단계

* 통합 문서에 대 한 자세한 내용은 다양 한 기능을 갖춘 시각화 옵션을 [시작](workbooks-visualizations.md) 하세요.
* 통합 문서 리소스에 대 한 액세스를 [제어](workbooks-access-control.md) 하 고 공유 합니다.
