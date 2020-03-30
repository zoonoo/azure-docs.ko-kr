---
title: 매개 변수를 만드는 Azure 모니터 통합 문서
description: 미리 빌드되고 사용자 지정 매개 변수가 있는 통합 문서를 사용하여 복잡한 보고 간소화
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 3e7dda85f1f890d5ae0eb4722c3e028b373fdcab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658220"
---
# <a name="workbook-parameters"></a>통합 문서 매개 변수

매개 변수를 사용하면 통합 문서 작성자가 소비자의 입력을 수집하고 통합 문서의 다른 부분에서 참조하여 일반적으로 결과 집합의 범위를 조정하거나 올바른 시각적 개체를 설정할 수 있습니다. 저자가 대화형 보고서와 경험을 작성할 수 있는 핵심 기능입니다. 

통합 문서를 사용하면 텍스트 상자와 드롭다운, 단일 및 다중 선택, 텍스트의 값, JSON, KQL 또는 Azure 리소스 그래프 등 매개 변수 컨트롤이 소비자에게 표시되는 방식을 제어할 수 있습니다.  

지원되는 매개 변수 유형은 다음과 같습니다.
* [시간](workbooks-time.md) - 사용자가 미리 채워진 시간 범위에서 선택하거나 사용자 지정 범위를 선택할 수 있습니다.
* [드롭다운](workbooks-dropdowns.md) - 사용자가 값 또는 값 집합에서 선택할 수 있습니다.
* [텍스트](workbooks-text.md) - 사용자가 임의의 텍스트를 입력 할 수 있습니다
* [리소스](workbooks-resources.md) - 사용자가 하나 이상의 Azure 리소스를 선택할 수 있습니다.
* [구독](workbooks-resources.md) - 사용자가 하나 이상의 Azure 구독 리소스를 선택할 수 있습니다.
* 리소스 유형 - 사용자가 하나 이상의 Azure 리소스 유형 값을 선택할 수 있습니다.
* 위치 - 사용자가 하나 이상의 Azure 위치 값을 선택할 수 있습니다.

이러한 매개 변수 값은 바인딩 또는 값 확장을 통해 통합 문서의 다른 부분에서 참조할 수 있습니다.

## <a name="creating-a-parameter"></a>매개 변수 만들기
1. 편집 모드에서 빈 통합 문서로 시작합니다.
2. 통합 문서 내의 링크에서 _매개변수 추가를_ 선택합니다.
3. 파란색 _매개변수 추가_ 버튼을 클릭합니다.
4. 팝업 새 매개 변수 창에서 다음을 입력합니다.
    1. 매개 변수 `TimeRange` 이름: *(매개 변수 __이름은__ 공백 이나 특수 문자를 **포함할 수 없습니다)** *
    2. 표시 이름: `Time Range` *(그러나 표시 __이름에는__ 공백, 특수 문자, 그림 이모티콘 등이 포함될 수 있음)*  
    2. 매개 변수 유형:`Time range picker`
    3. 필수:`checked`
    4. 사용 가능한 시간 범위: 지난 시간, 마지막 12시간, 마지막 24시간, 마지막 48시간, 마지막 3일, 마지막 7일 및 사용자 지정 시간 범위 선택 허용
5. 도구 모음에서 '저장'을 선택하여 매개변수를 만듭니다.

   ![시간 범위 매개 변수 생성을 보여주는 이미지](./media/workbooks-parameters/time-settings.png)

이것은 통합 문서읽기 모드에서 "알 약" 스타일처럼 보이는 방법입니다.

   ![읽기 모드에서 시간 범위 매개 변수를 보여주는 이미지](./media/workbooks-parameters/parameters-time.png)

## <a name="referencing-a-parameter"></a>매개 변수 참조
### <a name="via-bindings"></a>비아 바인딩
1. 통합 문서에 쿼리 컨트롤을 추가하고 응용 프로그램 인사이트 리소스를 선택합니다.
2. 시간 _범위_ 드롭다운을 열고 `Time Range` 아래쪽의 매개변수 섹션에서 옵션을 선택합니다.
3. 이렇게 하면 시간 범위 매개 변수가 차트의 시간 범위에 바인딩됩니다. 샘플 쿼리의 시간 범위가 이제 24시간 입니다.
4. 쿼리를 실행하여 결과를 확인합니다.

    ![바인딩을 통해 참조된 시간 범위 매개 변수를 보여 주면 이미지](./media/workbooks-parameters/time-binding.png)

### <a name="in-kql"></a>KQL에
1. 통합 문서에 쿼리 컨트롤을 추가하고 응용 프로그램 인사이트 리소스를 선택합니다.
2. KQL에서 매개 변수를 사용하여 시간 범위 필터를 입력합니다.`| where timestamp {TimeRange}`
3. 이렇게 하면 쿼리 평가 `| where timestamp > ago(1d)`시간이 매개 변수의 시간 범위 값인 로 확장됩니다.
4. 쿼리를 실행하여 결과를 확인합니다.

    ![KQL에서 참조된 시간 범위를 보여주는 이미지](./media/workbooks-parameters/time-in-code.png)

### <a name="in-text"></a>텍스트에서 
1. 통합 문서에 텍스트 컨트롤을 추가합니다.
2. 마크다운에서`The chosen time range is {TimeRange:label}`
3. _편집 완료_ 선택
4. 텍스트 컨트롤에 텍스트가 표시됩니다: _선택한 시간 범위는 마지막 24시간입니다._

## <a name="parameter-options"></a>매개 변수 옵션
_텍스트_ 에서 섹션에서는 `label` 해당 값 대신 매개변수의 매개변수를 사용했습니다. 매개 변수는 해당 유형에 따라 다양한 옵션을 노출합니다(예: 시간 범위 선택기는 값, 레이블, 쿼리, 시작, 끝 및 그레인을 허용합니다).

`Previews` _매개변수 편집_ 창의 섹션을 사용하여 매개 변수에 대한 확장 옵션을 확인합니다.

![시간 범위 매개 변수 옵션을 보여주는 이미지](./media/workbooks-parameters/time-previews.png)

## <a name="next-steps"></a>다음 단계

* 통합 문서에 대해 자세히 알아보고 다양한 다양한 시각화 옵션을 [알아보세요.](workbooks-visualizations.md)
* 통합 문서 리소스에 대한 액세스를 [제어하고](workbooks-access-control.md) 공유합니다.
