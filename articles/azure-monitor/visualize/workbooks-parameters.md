---
title: Azure Monitor 통합 문서 생성 매개 변수
description: 매개 변수가 통합 문서 작성자로 하여금 소비자의 입력을 수집하여 이를 통합 문서 내 다른 부분에서 참조하게 하는 방법을 알아봅니다.
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 95e810373ac5a591c530004e8d1175c3300656f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101700561"
---
# <a name="workbook-parameters"></a>통합 문서 매개 변수

매개 변수를 사용하면 통합 문서 작성자가 소비자 입력을 수집하고 이를 통합 문서 내 다른 부분(일반적으로는 결과 집합 범위 설정이나 올바른 시각적 개체 설정)에 참조할 수 있습니다. 이것은 작성자로 하여금 대화형 보고서 및 환경을 조성하도록 하는 핵심 기능입니다. 

통합 문서를 사용하면 소비자에게 매개 변수 컨트롤을 텍스트 상자와 드롭다운 중에서, 단일 선택과 다중 선택 중에서, 텍스트나 JSON 또는 KQL의 값과 Azure Resource Graph 중에서 어떤 식으로 표시할지를 제어할 수 있습니다.  

다음 매개 변수 유형을 지원합니다.
* [시간](workbooks-time.md) - 사용자가 미리 채워진 시간 범위에서 선택하거나 사용자 지정 범위를 선택하도록 합니다.
* [드롭다운](workbooks-dropdowns.md) - 사용자가 단일 값이나 값 집합을 선택할 수 있도록 합니다.
* [텍스트](workbooks-text.md) - 사용자가 임의의 텍스트를 입력할 수 있도록 합니다.
* [리소스](workbooks-resources.md) - 사용자가 Azure 리소스를 하나 또는 여러 개를 선택할 수 있도록 합니다.
* [구독](workbooks-resources.md) - 사용자가 Azure 구독 리소스를 하나 또는 여러 개를 선택할 수 있도록 합니다.
* 리소스 유형 - 사용자가 Azure 리소스 유형 값을 하나 또는 여러 개를 선택할 수 있도록 합니다.
* 위치 - 사용자가 Azure 위칫값을 하나 또는 여러 개를 선택할 수 있도록 합니다.

이러한 매개 변숫값은 바인딩이나 값 확장을 통하여 통합 문서의 다른 부분에서 참조할 수 있습니다.

## <a name="creating-a-parameter"></a>매개 변수 만들기
1. 편집 모드에서 빈 통합 문서로 시작합니다.
2. 통합 문서 내의 링크에서 _매개 변수 추가_ 를 선택합니다.
3. 파란색의 _매개 변수 추가_ 단추를 클릭합니다.
4. 새 매개 변수 팝업 창에서 다음을 입력합니다.
    1. 매개 변수 이름: `TimeRange` *(매개 변수 __이름은__ **절대** 공백이나 특수 문자를 포함할 수 없습니다)*
    2. 표시 이름: `Time Range`  *(그러나, __표시 이름에는__ 공백, 특수 문자, 이모지 등을 넣을 수 있습니다)*
    2. 매개 변수 형식: `Time range picker`
    3. 필수: `checked`
    4. 사용 가능한 시간 범위: 지난 1시간, 지난 12시간, 지난 24시간, 지난 48시간, 지난 3일, 지난 7일 및 사용자 지정 시간 범위 선택 허용
5. 도구 모음에서 ‘저장’을 선택하여 매개 변수를 만듭니다.

   ![시간 범위 매개 변수 생성을 보여 주는 이미지](./media/workbooks-parameters/time-settings.png)

읽기 모드에서 통합 문서는 이와 같이 필 스타일로 표시됩니다.

   ![읽기 모드의 시간 범위 매개 변수를 보여 주는 이미지](./media/workbooks-parameters/parameters-time.png)

## <a name="referencing-a-parameter"></a>매개 변수 참조하기
### <a name="via-bindings"></a>바인딩 매개
1. 통합 문서에 쿼리 컨트롤을 추가하고 Application Insights 리소스를 선택합니다.
2. _시간 범위_ 드롭다운을 열고 아랫쪽 매개 변수 섹션에서 `Time Range` 옵션을 선택합니다.
3. 이는 시간 범위 매개 변수를 차트의 시간 범위에 바인딩합니다. 샘플 쿼리의 시간 범위는 이제 지난 24시간입니다.
4. 쿼리를 실행하여 결과 확인

    ![바인딩을 통하여 참조되는 시간 범위 매개 변수를 보여 주는 이미지](./media/workbooks-parameters/time-binding.png)

### <a name="in-kql"></a>KQL의 경우
1. 통합 문서에 쿼리 컨트롤을 추가하고 Application Insights 리소스를 선택합니다.
2. KQL에서 매개 변수를 사용하여 시간 범위 필터를 입력합니다. `| where timestamp {TimeRange}`
3. 이렇게 하면 매개 변수의 시간 범위 값에 `| where timestamp > ago(1d)` 쿼리 평가 시간에 대한 정보를 제공합니다.
4. 쿼리를 실행하여 결과 확인

    ![KQL에서 참조되는 시간 범위를 보여 주는 이미지](./media/workbooks-parameters/time-in-code.png)

### <a name="in-text"></a>텍스트의 경우 
1. 통합 문서에 텍스트 컨트롤을 추가합니다.
2. Markdown에서 `The chosen time range is {TimeRange:label}`을 입력합니다.
3. _편집 완료_ 를 선택합니다.
4. 텍스트 컨트롤에 다음 텍스트가 표시됩니다. _선택한 시간 범위는 최근 24시간입니다_

## <a name="parameter-options"></a>매개 변수 옵션
_텍스트의 경우_ 섹션은 값 대신 매개 변수의 `label`을 사용하였습니다. 매개 변수는 유형에 따라 이러한 옵션을 다양하게 노출합니다. 예를 들어, 시간 범위 선택기에는 값, 레이블, 쿼리, 시작, 종료, 그레인을 사용할 수 있습니다.

_매개 변수 편집_ 창의 `Previews` 섹션을 사용하여 자신의 매개 변수에 대한 확장 옵션을 확인합니다.

![시간 범위 매개 변수 옵션을 보여 주는 이미지](./media/workbooks-parameters/time-previews.png)

## <a name="next-steps"></a>다음 단계

* [시작하여](./workbooks-overview.md#visualizations) 다양한 시각화 옵션을 갖춘 통합 문서에 대하여 알아봅니다.
* 통합 문서 리소스에 대한 액세스를 [제어](./workbooks-access-control.md)하고 공유합니다.