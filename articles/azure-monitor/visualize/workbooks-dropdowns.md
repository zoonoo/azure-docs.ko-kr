---
title: Azure Monitor 통합 문서 드롭다운 매개 변수
description: 드롭다운 매개 변수가 포함되고 미리 빌드되었으며 사용자 지정 매개 변수가 있는 통합 문서를 이용해 복잡한 보고를 간소화
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: d6fb4954ff616bccd9b237aedb3001b0a8d592dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101717199"
---
# <a name="workbook-drop-down-parameters"></a>통합 문서 드롭다운 매개 변수

드롭다운을 통해 사용자는 알려진 집합에서 하나 이상의 입력 값을 수집할 수 있습니다(예: 앱의 요청 중 하나를 선택). 드롭다운은 임의의 사용자 입력을 수집하는 사용자 친화적인 방식을 제공합니다. 드롭다운은 대화형 보고서에서 필터링을 사용하는 경우에 특히 유용합니다. 

드롭다운을 지정하는 가장 쉬운 방법은 매개 변수 설정에서 정적 목록을 제공하는 것입니다. 더 흥미로운 방법은 KQL 쿼리를 통해 목록을 동적으로 가져오는 것입니다. 매개 변수 설정을 통해 단일 선택인지 다중 선택인지를 지정할 수 있으며, 다중 선택인 경우 결과 집합의 형식을 지정하는 방법(구분 기호, 따옴표 등)을 지정할 수 있습니다.

## <a name="creating-a-static-drop-down-parameter"></a>정적 드롭다운 매개 변수 만들기

1. 편집 모드에서 빈 통합 문서를 시작합니다.
2. 통합 문서 내의 링크에서 _매개 변수 추가_ 를 선택합니다.
3. 파란색 _매개 변수 추가_ 단추를 클릭합니다.
4. 새 매개 변수 창이 나타나면 다음과 같이 입력합니다.
    1. 매개 변수 이름: `Environment`
    2. 매개 변수 유형: `Drop down`
    3. 필수: `checked`
    4. `multiple selection` 허용: `unchecked`
    5. 데이터를 가져오는 출처: `JSON`
5. JSON 입력 텍스트 블록에서 다음 json 코드 조각을 삽입합니다.
    ```json
    [
        { "value":"dev", "label":"Development" },
        { "value":"ppe", "label":"Pre-production" },
        { "value":"prod", "label":"Production", "selected":true }
    ]
    ```
6. 파란색 `Update` 단추를 누릅니다.
7. 도구 모음에서 ‘저장’을 선택하여 매개 변수를 만듭니다.
8. 환경 매개 변수는 세 개의 값이 있는 드롭다운입니다.

    ![정적 드롭다운의 생성을 보여 주는 이미지](./media/workbooks-dropdowns/dropdown-create.png)

## <a name="creating-a-static-dropdown-with-groups-of-items"></a>항목 그룹을 사용하여 정적 드롭다운 만들기

쿼리 결과/json에 “그룹” 필드가 있는 경우 드롭다운에 값 그룹이 표시됩니다. 위의 샘플을 따르되, 대신 다음 json을 사용합니다.

```json
[
    { "value":"dev", "label":"Development", "group":"Development" },
    { "value":"dev-cloud", "label":"Development (Cloud)", "group":"Development" },
    { "value":"ppe", "label":"Pre-production", "group":"Test" },
    { "value":"ppe-test", "label":"Pre-production (Test)", "group":"Test" },
    { "value":"prod1", "label":"Prod 1", "selected":true, "group":"Production" },
    { "value":"prod2", "label":"Prod 2", "group":"Production" }
]
```

![그룹화된 드롭다운의 예제를 보여 주는 이미지](./media/workbooks-dropdowns/grouped-dropDown.png)


## <a name="creating-a-dynamic-drop-down-parameter"></a>동적 드롭다운 매개 변수 만들기
1. 편집 모드에서 빈 통합 문서를 시작합니다.
2. 통합 문서 내의 링크에서 _매개 변수 추가_ 를 선택합니다.
3. 파란색 _매개 변수 추가_ 단추를 클릭합니다.
4. 새 매개 변수 창이 나타나면 다음과 같이 입력합니다.
    1. 매개 변수 이름: `RequestName`
    2. 매개 변수 유형: `Drop down`
    3. 필수: `checked`
    4. `multiple selection` 허용: `unchecked`
    5. 데이터를 가져오는 출처: `Query`
5. JSON 입력 텍스트 블록에서 다음 json 코드 조각을 삽입합니다.

    ```kusto
        requests
        | summarize by name
        | order by name asc
    ```
1. 파란색 `Run Query` 단추를 누릅니다.
2. 도구 모음에서 ‘저장’을 선택하여 매개 변수를 만듭니다.
3. RequestName 매개 변수는 앱에서 이루어진 모든 요청의 이름을 드롭다운에 포함시킵니다.

    ![동적 드롭다운의 생성을 보여 주는 이미지](./media/workbooks-dropdowns/dropdown-dynamic.png)

## <a name="referencing-drop-down-parameter"></a>드롭다운 매개 변수 참조

### <a name="in-kql"></a>KQL의 경우
1. 통합 문서에 쿼리 컨트롤을 추가하고 Application Insights 리소스를 선택합니다.
2. KQL 편집기에서 다음 코드 조각을 입력합니다.

    ```kusto
        requests
        | where name == '{RequestName}'
        | summarize Requests = count() by bin(timestamp, 1h)

    ```
3. 이렇게 하면 쿼리 평가 시간이 다음과 같이 늘어납니다.

    ```kusto
        requests
        | where name == 'GET Home/Index'
        | summarize Requests = count() by bin(timestamp, 1h)
    ```

4. 쿼리를 실행하여 결과를 확인합니다. 필요에 따라 차트를 렌더링합니다.

    ![KQL에서 참조되는 드롭다운을 보여 주는 이미지](./media/workbooks-dropdowns/dropdown-reference.png)


## <a name="parameter-value-label-selection-and-group"></a>매개 변수 값, 레이블, 선택 및 그룹
위의 동적 드롭다운 매개 변수에 사용되는 쿼리는 드롭다운 목록에서 정확하게 렌더링되는 값 목록을 반환합니다. 다른 표시 이름을 선택하거나 이 중 하나를 선택하려면 어떻게 해야 할까요? 드롭다운 매개 변수는 값, 레이블, 선택 및 그룹 열을 통해 이를 허용합니다.

아래 샘플은 표시 이름이 이모지 스타일로 지정되어 있고, 첫 번째 항목이 선택되어 있으며, 작업 이름별로 그룹화된 Application Insights 종속성 목록을 가져오는 방법을 보여 줍니다.

```kusto
dependencies
| summarize by operation_Name, name
| where name !contains ('.')
| order by name asc
| serialize Rank = row_number()
| project value = name, label = strcat('🌐 ', name), selected = iff(Rank == 1, true, false), group = operation_Name
```

![값, 레이블, 선택 및 그룹 옵션을 사용하는 드롭다운 매개 변수를 보여 주는 이미지](./media/workbooks-dropdowns/dropdown-more-options.png)


## <a name="drop-down-parameter-options"></a>드롭다운 매개 변수 옵션
| 매개 변수 | 설명 | 예제 |
| ------------- |:-------------|:-------------|
| `{DependencyName}` | 선택한 값 | GET fabrikamaccount |
| `{DependencyName:label}` | 선택한 레이블 | 🌐 GET fabrikamaccount |
| `{DependencyName:value}` | 선택한 값 | GET fabrikamaccount |

## <a name="multiple-selection"></a>다중 선택
지금까지 예제에서는 드롭다운에서 값을 하나만 선택하도록 매개 변수를 명시적으로 설정했습니다. 드롭다운 매개 변수는 `multiple selection`도 지원하며, `Allow multiple selection` 옵션을 선택하는 것처럼 간단히 설정할 수 있습니다. 

또한 사용자는 `delimiter` 및 `quote with` 설정을 통해 결과 집합의 형식을 지정할 수 있습니다. 기본값은 단순히 ‘a’, ‘b’, ‘c’ 형태의 컬렉션으로 값을 반환합니다. 선택 항목의 수를 제한하는 옵션도 있습니다.

결과 형식을 사용할 수 있도록 매개 변수를 참조하는 KQL을 변경해야 합니다. 가장 일반적인 방법은 `in` 연산자를 통해 설정하는 것입니다.

```kusto
dependencies
| where name in ({DependencyName})
| summarize Requests = count() by bin(timestamp, 1h), name
```

다음은 작동하는 다중 선택 드롭다운의 예제입니다.

![다중 선택 드롭다운 매개 변수를 보여 주는 이미지](./media/workbooks-dropdowns/dropdown-multiselect.png)

## <a name="next-steps"></a>다음 단계

* 다양한 시각화 옵션을 갖춘 통합 문서에 대해 [자세히 알아보세요](./workbooks-overview.md#visualizations).
* 통합 문서 리소스에 대한 액세스를 [제어](./workbooks-access-control.md)하고 공유하세요.