---
title: Azure Monitor 통합 문서 시간 매개 변수
description: 사용자가 분석의 시간 컨텍스트를 설정할 수 있도록 시간 매개 변수를 설정하는 방법에 대해 알아봅니다. 시간 매개 변수는 거의 모든 보고서에서 사용됩니다.
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 9f4f5f82423f0ecb4e810eab2d675e95b8b66763
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101717165"
---
# <a name="workbook-time-parameters"></a>통합 문서 시간 매개 변수

사용자는 시간 매개 변수를 사용하여 분석의 시간 컨텍스트를 설정하고 시간 매개 변수는 거의 모든 보고서에 사용됩니다. 사용자 지정 시간 범위에 대한 옵션을 포함하여 작성자가 드롭다운에서 표시할 시간 범위를 지정할 수 있도록 비교적 간단하게 설정 및 사용할 수 있습니다. 

## <a name="creating-a-time-parameter"></a>시간 매개 변수 만들기
1. 편집 모드에서 빈 통합 문서를 시작합니다.
2. 통합 문서 내의 링크에서 _매개 변수 추가_ 를 선택합니다.
3. 파란색 _매개 변수 추가_ 단추를 클릭합니다.
4. 새 매개 변수 창이 나타나면 다음과 같이 입력합니다.
    1. 매개 변수 이름: `TimeRange`
    2. 매개 변수 유형: `Time range picker`
    3. 필수: `checked`
    4. 사용 가능한 시간 범위: 지난 1시간, 지난 12시간, 지난 24시간, 지난 48시간, 지난 3일, 지난 7일 및 사용자 지정 시간 범위 선택 허용
5. 도구 모음에서 ‘저장’을 선택하여 매개 변수를 만듭니다.

    ![시간 범위 매개 변수 생성을 보여주는 이미지](./media/workbooks-time/time-settings.png)

읽기 모드에서 통합 문서는 이와 같이 표시됩니다.

![읽기 모드의 시간 범위 매개 변수를 보여주는 이미지](./media/workbooks-time/parameters-time.png)

## <a name="referencing-a-time-parameter"></a>시간 매개 변수 참조
### <a name="via-bindings"></a>바인딩 매개
1. 통합 문서에 쿼리 컨트롤을 추가하고 Application Insights 리소스를 선택합니다.
2. 대부분의 통합 문서 컨트롤은 ‘시간 범위’ 범위 선택기를 지원합니다. ‘시간 범위’ 드롭다운을 열고 하단 시간 매개 변수 그룹에서 다음을 `{TimeRange}` 선택합니다.
3. 이는 시간 범위 매개 변수를 차트의 시간 범위에 바인딩합니다. 샘플 쿼리의 시간 범위는 이제 지난 24시간입니다.
4. 쿼리를 실행하여 결과 확인

    ![바인딩을 통해 참조되는 시간 범위 매개 변수를 보여주는 이미지](./media/workbooks-time/time-binding.png)

### <a name="in-kql"></a>KQL의 경우
1. 통합 문서에 쿼리 컨트롤을 추가하고 Application Insights 리소스를 선택합니다.
2. KQL에서 매개 변수를 사용하여 시간 범위 필터를 입력합니다. `| where timestamp {TimeRange}`
3. 이렇게 하면 매개 변수의 시간 범위 값에 `| where timestamp > ago(1d)` 쿼리 평가 시간에 대한 정보를 제공합니다.
4. 쿼리를 실행하여 결과 확인

    ![KQL에서 참조되는 시간 범위를 보여주는 이미지](./media/workbooks-time/time-in-code.png)

### <a name="in-text"></a>텍스트의 경우 
1. 통합 문서에 텍스트 컨트롤을 추가합니다.
2. Markdown에서 다음을 입력합니다. `The chosen time range is {TimeRange:label}`
3. ‘편집 완료’를 선택합니다.
4. 텍스트 컨트롤에 다음 텍스트가 표시됩니다. ‘선택한 시간 범위는 최근 24시간입니다’

## <a name="time-parameter-options"></a>시간 매개 변수 옵션
| 매개 변수 | 설명 | 예제 |
| ------------- |:-------------|:-------------|
| `{TimeRange}` | 시간 범위 레이블 | 최근 24시간 |
| `{TimeRange:label}` | 시간 범위 레이블 | 최근 24시간 |
| `{TimeRange:value}` | 시간 범위 값 | > 전(1일) |
| `{TimeRange:query}` | 시간 범위 쿼리 | > 전(1일) |
| `{TimeRange:start}` | 시간 범위 시작 시간 | 2019-03-20 오후 4:18 |
| `{TimeRange:end}` | 시간 범위 종료 시간 | 2019-03-21 오후 4:18 |
| `{TimeRange:grain}` | 시간 범위 조직 | 30분 |


### <a name="using-parameter-options-in-a-query"></a>쿼리에서 매개 변수 옵션 사용
```kusto
requests
| make-series Requests = count() default = 0 on timestamp from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain}
```

## <a name="next-steps"></a>다음 단계

* 다양한 시각화 옵션을 갖춘 통합 문서에 대해 [자세히 알아보세요](./workbooks-overview.md#visualizations).
* 통합 문서 리소스에 대한 액세스를 [제어](./workbooks-access-control.md)하고 공유하세요.