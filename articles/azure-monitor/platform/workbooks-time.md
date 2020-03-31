---
title: Azure 모니터 통합 문서 시간 매개 변수
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658016"
---
# <a name="workbook-time-parameters"></a>통합 문서 시간 매개 변수

시간 매개 변수를 사용하면 사용자가 분석 시간 컨텍스트를 설정할 수 있으며 거의 모든 보고서에서 사용됩니다. 설치 및 사용이 비교적 간단하므로 작성자가 사용자 지정 시간 범위에 대한 옵션을 포함하여 드롭다운에 표시할 시간 범위를 지정할 수 있습니다. 

## <a name="creating-a-time-parameter"></a>시간 매개변수 만들기
1. 편집 모드에서 빈 통합 문서로 시작합니다.
2. 통합 문서 내의 링크에서 _매개변수 추가를_ 선택합니다.
3. 파란색 _매개변수 추가_ 버튼을 클릭합니다.
4. 팝업 새 매개 변수 창에서 다음을 입력합니다.
    1. 매개 변수 이름:`TimeRange`
    2. 매개 변수 유형:`Time range picker`
    3. 필수:`checked`
    4. 사용 가능한 시간 범위: 지난 시간, 마지막 12시간, 마지막 24시간, 마지막 48시간, 마지막 3일, 마지막 7일 및 사용자 지정 시간 범위 선택 허용
5. 도구 모음에서 '저장'을 선택하여 매개변수를 만듭니다.

    ![시간 범위 매개 변수 생성을 보여주는 이미지](./media/workbooks-time/time-settings.png)

통합 문서의 읽기 모드가 어떻게 보이는지 알 수 있습니다.

![읽기 모드에서 시간 범위 매개 변수를 보여주는 이미지](./media/workbooks-time/parameters-time.png)

## <a name="referencing-a-time-parameter"></a>시간 매개 변수 참조
### <a name="via-bindings"></a>비아 바인딩
1. 통합 문서에 쿼리 컨트롤을 추가하고 응용 프로그램 인사이트 리소스를 선택합니다.
2. 대부분의 통합 문서 컨트롤은 _시간 범위_ 범위 선택기를 지원합니다. 시간 _범위_ 드롭다운을 열고 `{TimeRange}` 하단의 시간 울림 매개변수 그룹을 선택합니다.
3. 이렇게 하면 시간 범위 매개 변수가 차트의 시간 범위에 바인딩됩니다. 샘플 쿼리의 시간 범위가 이제 24시간 입니다.
4. 쿼리를 실행하여 결과를 확인합니다.

    ![바인딩을 통해 참조된 시간 범위 매개 변수를 보여 주면 이미지](./media/workbooks-time/time-binding.png)

### <a name="in-kql"></a>KQL에
1. 통합 문서에 쿼리 컨트롤을 추가하고 응용 프로그램 인사이트 리소스를 선택합니다.
2. KQL에서 매개 변수를 사용하여 시간 범위 필터를 입력합니다.`| where timestamp {TimeRange}`
3. 이렇게 하면 쿼리 평가 `| where timestamp > ago(1d)`시간이 매개 변수의 시간 범위 값인 로 확장됩니다.
4. 쿼리를 실행하여 결과를 확인합니다.

    ![KQL에서 참조된 시간 범위를 보여주는 이미지](./media/workbooks-time/time-in-code.png)

### <a name="in-text"></a>텍스트에서 
1. 통합 문서에 텍스트 컨트롤을 추가합니다.
2. 마크다운에서`The chosen time range is {TimeRange:label}`
3. _편집 완료_ 선택
4. 텍스트 컨트롤에 텍스트가 표시됩니다: _선택한 시간 범위는 마지막 24시간입니다._

## <a name="time-parameter-options"></a>시간 매개 변수 옵션
| 매개 변수 | 설명 | 예제 |
| ------------- |:-------------|:-------------|
| `{TimeRange}` | 시간 범위 레이블 | 최근 24시간 |
| `{TimeRange:label}` | 시간 범위 레이블 | 최근 24시간 |
| `{TimeRange:value}` | 시간 범위 값 | > 전(1d) |
| `{TimeRange:query}` | 시간 범위 쿼리 | > 전(1d) |
| `{TimeRange:start}` | 시간 범위 시작 시간 | 2019년 3월 20일 오후 4:18 |
| `{TimeRange:end}` | 시간 범위 종료 시간 | 2019년 3월 21일 오후 4:18 |
| `{TimeRange:grain}` | 시간 범위 곡물 | 30 m |


### <a name="using-parameter-options-in-a-query"></a>쿼리에서 매개 변수 옵션 사용
```kusto
requests
| make-series Requests = count() default = 0 on timestamp from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain}
```

## <a name="next-steps"></a>다음 단계

* 통합 문서에 대해 자세히 알아보고 다양한 다양한 시각화 옵션을 [알아보세요.](workbooks-visualizations.md)
* 통합 문서 리소스에 대한 액세스를 [제어하고](workbooks-access-control.md) 공유합니다.
