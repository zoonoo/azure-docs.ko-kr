---
title: Azure 모니터 통합 문서 텍스트 매개 변수
description: 미리 빌드되고 사용자 지정 매개 변수화된 통합 문서로 복잡한 보고를 간소화합니다. 통합 문서 텍스트 매개 변수에 대해 자세히 알아보세요.
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: c804cc8942a40e2f30c980636194daa82e0fb0e8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687338"
---
# <a name="workbook-text-parameters"></a>통합 문서 텍스트 매개 변수

Textbox 매개 변수는 통합 문서 사용자로부터 텍스트 입력을 수집하는 간단한 방법을 제공합니다. 드롭다운을 사용하여 입력을 수집하는 것이 실용적이지 않은 경우(예: 임의 임계값 또는 제네릭 필터) 사용됩니다. 통합 문서를 사용하면 작성자가 쿼리에서 텍스트 상자의 기본값을 얻을 수 있습니다. 이렇게 하면 메트릭의 p95를 기반으로 기본 임계값을 설정하는 것과 같은 흥미로운 시나리오를 사용할 수 있습니다.

텍스트 상자를 일반적으로 사용하는 것은 다른 통합 문서 컨트롤에서 사용하는 내부 변수입니다. 이 작업은 기본값에 대한 쿼리를 활용하고 입력 컨트롤을 읽기 모드에서 보이지 않게 함으로써 수행됩니다. 예를 들어 사용자는 수식(사용자가 아님)에서 임계값을 입력한 다음 후속 쿼리에서 임계값을 사용하도록 할 수 있습니다.

## <a name="creating-a-text-parameter"></a>텍스트 매개 변수 만들기
1. 편집 모드에서 빈 통합 문서로 시작합니다.
2. 통합 문서 내의 링크에서 _매개변수 추가를_ 선택합니다.
3. 파란색 _매개변수 추가_ 버튼을 클릭합니다.
4. 팝업 새 매개 변수 창에서 다음을 입력합니다.
    1. 매개 변수 이름:`SlowRequestThreshold`
    2. 매개 변수 유형:`Text`
    3. 필수:`checked`
    4. 쿼리에서 기본값을 가져옵니다.`unchecked`
5. 도구 모음에서 '저장'을 선택하여 매개변수를 만듭니다.

    ![텍스트 매개 변수 생성을 보여주는 이미지](./media/workbooks-text/text-create.png)

통합 문서의 읽기 모드가 어떻게 보이는지 알 수 있습니다.

![읽기 모드에서 텍스트 매개 변수를 표시하는 이미지](./media/workbooks-text/text-readmode.png)

## <a name="referencing-a-text-parameter"></a>텍스트 매개 변수 참조
1. 파란색 `Add query` 링크를 선택하고 응용 프로그램 인사이트 리소스를 선택하여 통합 문서에 쿼리 컨트롤을 추가합니다.
2. KQL 상자에 이 스니펫을 추가합니다.
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= {SlowRequestThreshold}) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
3. 500의 값으로 텍스트 매개 변수를 쿼리 컨트롤과 결합하면 아래 쿼리를 효과적으로 실행합니다.
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= 500) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
4. 쿼리를 실행하여 결과를 확인합니다.

    ![KQL에서 참조된 텍스트 매개 변수를 보여 보이는 이미지](./media/workbooks-text/text-reference.png)

> [!NOTE]
> 위의 예에서 `{SlowRequestThreshold}` 정수 값을 나타냅니다. 문자열을 쿼리하는 경우 와 `{ComputerName}` 같은 Kusto 쿼리를 수정하여 `"{ComputerName}"` 매개 변수 필드가 따옴표없이 입력을 accept에 표시하기 위해 따옴표를 추가해야합니다.

## <a name="setting-default-values"></a>기본값 설정
1. 편집 모드에서 빈 통합 문서로 시작합니다.
2. 통합 문서 내의 링크에서 _매개변수 추가를_ 선택합니다.
3. 파란색 _매개변수 추가_ 버튼을 클릭합니다.
4. 팝업 새 매개 변수 창에서 다음을 입력합니다.
    1. 매개 변수 이름:`SlowRequestThreshold`
    2. 매개 변수 유형:`Text`
    3. 필수:`checked`
    4. 쿼리에서 기본값을 가져옵니다.`checked`
5. KQL 상자에 이 스니펫을 추가합니다.
    ```kusto
    requests
    | summarize round(percentile(duration, 95), 2)
    ```
    이 쿼리는 앱의 모든 요청에 대해 텍스트 상자의 기본값을 95번째 백분위수 기간으로 설정합니다.
6. 쿼리를 실행하여 결과를 확인합니다.
7. 도구 모음에서 '저장'을 선택하여 매개변수를 만듭니다.

    ![KQL의 기본값이 있는 텍스트 매개변수를 표시하는 이미지](./media/workbooks-text/text-default-value.png)

> [!NOTE]
> 이 예제에서는 Application Insights 데이터를 쿼리하지만 로그 분석, Azure 리소스 그래프 등 모든 로그 기반 데이터 원본에 대해 이 방법을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 통합 문서에 대해 자세히 알아보고 다양한 다양한 시각화 옵션을 [알아보세요.](workbooks-visualizations.md)
* 통합 문서 리소스에 대한 액세스를 [제어하고](workbooks-access-control.md) 공유합니다.
