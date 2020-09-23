---
title: 통합 문서 Azure Monitor 매개 변수 만들기
description: 매개 변수를 사용 하 여 통합 문서 작성자가 소비자의 입력을 수집 하 고 통합 문서의 다른 부분에서이를 참조 하도록 허용 하는 방법을 알아봅니다.
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 8be7dd170d5e4e3bddb09bc1b163fba7a841a6b7
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90984568"
---
# <a name="workbook-parameters"></a>통합 문서 매개 변수

통합 문서 작성자는 매개 변수를 사용 하 여 소비자의 입력을 수집 하 고, 일반적으로 결과 집합의 범위를 설정 하거나 오른쪽 시각적 개체를 설정 하는 통합 문서의 다른 부분을 참조할 수 있습니다. 이는 작성자가 대화형 보고서 및 환경을 작성할 수 있도록 하는 핵심 기능입니다. 

통합 문서를 사용 하면 매개 변수 컨트롤이 소비자에 게 표시 되는 방식, 텍스트 상자 및 드롭다운, 단일 vs 다중 선택, 텍스트, JSON, KQL 또는 Azure 리소스 그래프 등의 값을 제어할 수 있습니다.  

지원 되는 매개 변수 형식은 다음과 같습니다.
* [시간](workbooks-time.md) -사용자가 미리 채워진 시간 범위에서 선택 하거나 사용자 지정 범위를 선택할 수 있습니다.
* [드롭다운](workbooks-dropdowns.md) -사용자가 값 또는 값 집합을 선택할 수 있습니다.
* [텍스트](workbooks-text.md) -사용자가 임의의 텍스트를 입력할 수 있습니다.
* [리소스](workbooks-resources.md) -사용자가 하나 이상의 Azure 리소스를 선택할 수 있습니다.
* [구독](workbooks-resources.md) -사용자가 하나 이상의 Azure 구독 리소스를 선택할 수 있습니다.
* 리소스 종류-사용자가 하나 이상의 Azure 리소스 종류 값을 선택할 수 있습니다.
* 위치-사용자가 하나 이상의 Azure 위치 값을 선택할 수 있습니다.

이러한 매개 변수 값은 바인딩 또는 값 확장을 통해 통합 문서의 다른 부분에서 참조 될 수 있습니다.

## <a name="creating-a-parameter"></a>매개 변수 만들기
1. 편집 모드에서 빈 통합 문서를 시작 합니다.
2. 통합 문서 내의 링크에서 _매개 변수 추가_ 를 선택 합니다.
3. 파란색 _매개 변수 추가_ 단추를 클릭 합니다.
4. 새 매개 변수 창에서 enter 키를 누르십시오.
    1. 매개 변수 이름: `TimeRange` *(매개 변수 __이름__ 에 공백이 나 특수 문자를 포함할 **수 없음** )*
    2. 표시 이름: `Time Range` *(그러나 __표시 이름__ 에는 공백, 특수 문자,이 모 지 등이 포함 될 수 있습니다.)*  
    2. 매개 변수 유형: `Time range picker`
    3. 필수: `checked`
    4. 사용 가능한 시간 범위: 지난 1 시간, 지난 12 시간, 지난 24 시간, 최근 48 시간, 지난 3 일, 지난 7 일 및 사용자 지정 시간 범위 선택 허용
5. 도구 모음에서 ' 저장 '을 선택 하 여 매개 변수를 만듭니다.

   ![시간 범위 매개 변수 생성을 보여 주는 이미지](./media/workbooks-parameters/time-settings.png)

이는 통합 문서가 "알" 스타일의 읽기 모드와 같은 모양입니다.

   ![읽기 모드의 시간 범위 매개 변수를 보여 주는 이미지](./media/workbooks-parameters/parameters-time.png)

## <a name="referencing-a-parameter"></a>매개 변수 참조
### <a name="via-bindings"></a>Via 바인딩
1. 통합 문서에 쿼리 컨트롤을 추가 하 고 Application Insights 리소스를 선택 합니다.
2. _시간 범위_ 드롭다운을 열고 `Time Range` 아래쪽의 매개 변수 섹션에서 옵션을 선택 합니다.
3. 이는 시간 범위 매개 변수를 차트의 시간 범위에 바인딩합니다. 샘플 쿼리의 시간 범위는 이제 최근 24 시간입니다.
4. 쿼리를 실행 하 여 결과를 확인 합니다.

    ![바인딩을 통해 참조 되는 시간 범위 매개 변수를 보여 주는 이미지](./media/workbooks-parameters/time-binding.png)

### <a name="in-kql"></a>KQL에서
1. 통합 문서에 쿼리 컨트롤을 추가 하 고 Application Insights 리소스를 선택 합니다.
2. KQL에서 매개 변수를 사용 하 여 시간 범위 필터를 입력 합니다. `| where timestamp {TimeRange}`
3. 이렇게 하면 쿼리 평가 시간이 `| where timestamp > ago(1d)` 매개 변수의 시간 범위 값인로 확장 됩니다.
4. 쿼리를 실행 하 여 결과를 확인 합니다.

    ![KQL에서 참조 되는 시간 범위를 보여 주는 이미지](./media/workbooks-parameters/time-in-code.png)

### <a name="in-text"></a>텍스트 
1. 통합 문서에 텍스트 컨트롤을 추가 합니다.
2. Markdown에 다음을 입력 합니다. `The chosen time range is {TimeRange:label}`
3. _편집 완료_ 를 선택 합니다.
4. 텍스트 컨트롤에 텍스트가 표시 됩니다. _선택한 시간 범위는 최근 24 시간입니다_ .

## <a name="parameter-options"></a>매개 변수 옵션
_In Text_ 섹션은 `label` 값 대신 매개 변수의를 사용 했습니다. 매개 변수는 형식에 따라 다양 한 옵션을 노출 합니다. 예를 들어 시간 범위 선택에는 value, label, query, start, end 및 그레인을 사용할 수 있습니다.

매개 변수 `Previews` _편집_ 창의 섹션을 사용 하 여 매개 변수에 대 한 확장 옵션을 볼 수 있습니다.

![시간 범위 매개 변수 옵션을 보여 주는 이미지](./media/workbooks-parameters/time-previews.png)

## <a name="next-steps"></a>다음 단계

* 통합 문서에 대 한 자세한 내용은 다양 한 기능을 갖춘 시각화 옵션을 [시작](workbooks-visualizations.md) 하세요.
* 통합 문서 리소스에 대 한 액세스를 [제어](workbooks-access-control.md) 하 고 공유 합니다.
