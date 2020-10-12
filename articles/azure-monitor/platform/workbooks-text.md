---
title: Azure Monitor 통합 문서 텍스트 매개 변수
description: 미리 빌드된 사용자 지정 매개 변수가 있는 통합 문서로 복잡 한 보고를 간소화 합니다. 통합 문서 텍스트 매개 변수에 대해 자세히 알아보세요.
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: c804cc8942a40e2f30c980636194daa82e0fb0e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "81687338"
---
# <a name="workbook-text-parameters"></a>통합 문서 텍스트 매개 변수

텍스트 상자 매개 변수는 통합 문서 사용자 로부터 텍스트 입력을 수집 하는 간단한 방법을 제공 합니다. 드롭다운을 사용 하 여 입력을 수집 하는 것이 실용적이 지 않을 때 (예: 임의의 임계값 또는 일반 필터) 사용 됩니다. 통합 문서를 사용 하면 작성자가 쿼리에서 textbox의 기본값을 가져올 수 있습니다. 이를 통해 메트릭의 p95을 기반으로 기본 임계값을 설정 하는 것과 같은 흥미로운 시나리오를 사용할 수 있습니다.

텍스트 상자의 일반적인 용도는 다른 통합 문서 컨트롤에서 사용 하는 내부 변수입니다. 이 작업을 수행 하려면 기본값에 대해 쿼리를 활용 하 고 입력 컨트롤을 읽기 모드로 표시 하지 않도록 설정 합니다. 예를 들어 사용자가 사용자가 아닌 수식에서 임계값을 가져온 다음 후속 쿼리에서 임계값을 사용 하도록 할 수 있습니다.

## <a name="creating-a-text-parameter"></a>텍스트 매개 변수 만들기
1. 편집 모드에서 빈 통합 문서를 시작 합니다.
2. 통합 문서 내의 링크에서 _매개 변수 추가_ 를 선택 합니다.
3. 파란색 _매개 변수 추가_ 단추를 클릭 합니다.
4. 새 매개 변수 창에서 enter 키를 누르십시오.
    1. 매개 변수 이름: `SlowRequestThreshold`
    2. 매개 변수 유형: `Text`
    3. 필수: `checked`
    4. 쿼리에서 기본값 가져오기: `unchecked`
5. 도구 모음에서 ' 저장 '을 선택 하 여 매개 변수를 만듭니다.

    ![텍스트 매개 변수 생성을 보여 주는 이미지](./media/workbooks-text/text-create.png)

읽기 모드에서 통합 문서를 표시 하는 방법입니다.

![읽기 모드에서 텍스트 매개 변수를 보여 주는 이미지](./media/workbooks-text/text-readmode.png)

## <a name="referencing-a-text-parameter"></a>텍스트 매개 변수 참조
1. 파란색 링크를 선택 하 고 Application Insights 리소스를 선택 하 여 통합 문서에 쿼리 컨트롤을 추가 `Add query` 합니다.
2. KQL 상자에 다음 코드 조각을 추가 합니다.
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= {SlowRequestThreshold}) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
3. 아래 쿼리를 효과적으로 실행 하는 쿼리 컨트롤과 결합 된 500 값을 사용 하 여 텍스트 매개 변수를 사용 합니다.
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= 500) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
4. 쿼리를 실행 하 여 결과를 확인 합니다.

    ![KQL에서 참조 되는 텍스트 매개 변수를 보여 주는 이미지](./media/workbooks-text/text-reference.png)

> [!NOTE]
> 위의 예제에서는 `{SlowRequestThreshold}` 정수 값을 나타냅니다. 과 같은 문자열을 쿼리 하는 경우 `{ComputerName}` `"{ComputerName}"` 매개 변수 필드를 따옴표 없이 accept 입력에 추가 하기 위해 Kusto 쿼리를 수정 해야 합니다.

## <a name="setting-default-values"></a>기본값 설정
1. 편집 모드에서 빈 통합 문서를 시작 합니다.
2. 통합 문서 내의 링크에서 _매개 변수 추가_ 를 선택 합니다.
3. 파란색 _매개 변수 추가_ 단추를 클릭 합니다.
4. 새 매개 변수 창에서 enter 키를 누르십시오.
    1. 매개 변수 이름: `SlowRequestThreshold`
    2. 매개 변수 유형: `Text`
    3. 필수: `checked`
    4. 쿼리에서 기본값 가져오기: `checked`
5. KQL 상자에 다음 코드 조각을 추가 합니다.
    ```kusto
    requests
    | summarize round(percentile(duration, 95), 2)
    ```
    이 쿼리는 앱의 모든 요청에 대해 텍스트 상자의 기본값을 95 번째 백분위 수로 설정 합니다.
6. 쿼리를 실행 하 여 결과를 확인 합니다.
7. 도구 모음에서 ' 저장 '을 선택 하 여 매개 변수를 만듭니다.

    ![KQL의 기본값을 포함 하는 텍스트 매개 변수를 보여 주는 이미지](./media/workbooks-text/text-default-value.png)

> [!NOTE]
> 이 예제에서는 데이터 Application Insights 쿼리 하는 동안 모든 로그 기반 데이터 원본 (Log Analytics, Azure 리소스 그래프 등)에 대해이 방법을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 통합 문서에 대 한 자세한 내용은 다양 한 기능을 갖춘 시각화 옵션을 [시작](workbooks-visualizations.md) 하세요.
* 통합 문서 리소스에 대 한 액세스를 [제어](workbooks-access-control.md) 하 고 공유 합니다.
