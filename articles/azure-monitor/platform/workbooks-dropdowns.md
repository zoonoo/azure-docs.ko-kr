---
title: Azure 모니터 통합 문서 드롭다운 매개 변수
description: 드롭다운 매개 변수가 포함된 미리 빌드되고 사용자 지정 매개 변수화된 통합 문서로 복잡한 보고 간소화
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: f3220a363025d80fd7636dbfc3af3d2d9d7bc040
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658287"
---
# <a name="workbook-drop-down-parameters"></a>통합 문서 드롭다운 매개변수

드롭다운을 사용하면 알려진 집합에서 하나 이상의 입력 값을 수집할 수 있습니다(예: 앱의 요청 중 하나를 선택). 드롭다운은 사용자로부터 임의의 입력을 수집하는 사용자 친화적인 방법을 제공합니다. 드롭다운은 대화형 보고서에서 필터링을 활성화하는 데 특히 유용합니다. 

드롭다운을 지정하는 가장 쉬운 방법은 매개 변수 설정에서 정적 목록을 제공하는 것입니다. 더 흥미로운 방법은 KQL 쿼리를 통해 동적으로 목록을 얻는 것입니다. 매개 변수 설정을 사용하면 단일 또는 다중 선택인지 를 지정할 수 있으며 다중 선택인 경우 결과 집합의 서식을 지정하는 방법(구분 기호, 따옴표 등)을 지정할 수 있습니다.

## <a name="creating-a-static-drop-down-parameter"></a>정적 드롭다운 매개변수 만들기

1. 편집 모드에서 빈 통합 문서로 시작합니다.
2. 통합 문서 내의 링크에서 _매개변수 추가를_ 선택합니다.
3. 파란색 _매개변수 추가_ 버튼을 클릭합니다.
4. 팝업 새 매개 변수 창에서 다음을 입력합니다.
    1. 매개 변수 이름:`Environment`
    2. 매개 변수 유형:`Drop down`
    3. 필수:`checked`
    4. 허용: `multiple selection``unchecked`
    5. 다음에서 데이터 가져옵니다.`JSON`
5. JSON 입력 텍스트 블록에서 이 json 스니펫을 삽입합니다.
    ```json
    [
        { "value":"dev", "label":"Development" },
        { "value":"ppe", "label":"Pre-production" },
        { "value":"prod", "label":"Production", "selected":true }
    ]
    ```
6. 파란색 버튼을 `Update` 누를 수 있습니다.
7. 도구 모음에서 '저장'을 선택하여 매개변수를 만듭니다.
8. 환경 매개 변수는 세 가지 값을 가진 드롭다운이 됩니다.

    ![정적 익사의 생성을 보여주는 이미지](./media/workbook-dropdowns/dropdown-create.png)

## <a name="creating-a-static-dropdown-with-groups-of-items"></a>항목 그룹으로 정적 드롭다운 만들기
쿼리 결과/json에 "그룹" 필드가 포함 된 경우 드롭다운값 그룹이 표시 됩니다. 위의 샘플을 따르되 대신 다음 json을 사용하십시오.
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
    ![Image showing an example of a grouped dropdown](./media/workbook-dropdowns/grouped-dropDown.png)


## <a name="creating-a-dynamic-drop-down-parameter"></a>동적 드롭다운 매개 변수 만들기
1. 편집 모드에서 빈 통합 문서로 시작합니다.
2. 통합 문서 내의 링크에서 _매개변수 추가를_ 선택합니다.
3. 파란색 _매개변수 추가_ 버튼을 클릭합니다.
4. 팝업 새 매개 변수 창에서 다음을 입력합니다.
    1. 매개 변수 이름:`RequestName`
    2. 매개 변수 유형:`Drop down`
    3. 필수:`checked`
    4. 허용: `multiple selection``unchecked`
    5. 다음에서 데이터 가져옵니다.`Query`
5. JSON 입력 텍스트 블록에서 이 json 스니펫을 삽입합니다.

    ```kusto
        requests
        | summarize by name
        | order by name asc
    ```
1. 파란색 버튼을 `Run Query` 누를 수 있습니다.
2. 도구 모음에서 '저장'을 선택하여 매개변수를 만듭니다.
3. RequestName 매개 변수는 앱의 모든 요청 이름을 드롭다운합니다.

    ![동적 드롭다운 생성을 보여주는 이미지](./media/workbook-dropdowns/dropdown-dynamic.png)

## <a name="referencing-drop-down-parameter"></a>드롭다운 매개 변수 참조
### <a name="in-kql"></a>KQL에
1. 통합 문서에 쿼리 컨트롤을 추가하고 응용 프로그램 인사이트 리소스를 선택합니다.
2. KQL 편집기에서 이 스니펫을 입력합니다.

    ```kusto
        requests
        | where name == '{RequestName}'
        | summarize Requests = count() by bin(timestamp, 1h)

    ```
3. 이렇게 하면 쿼리 평가 시간이 다음과 같은 것으로 확장됩니다.

    ```kusto
        requests
        | where name == 'GET Home/Index'
        | summarize Requests = count() by bin(timestamp, 1h)
    ```

4. 쿼리를 실행하여 결과를 확인합니다. 선택적으로 차트로 렌더링합니다.

    ![KQL에서 참조된 드롭다운을 보여주는 이미지](./media/workbook-dropdowns/dropdown-reference.png)


## <a name="parameter-value-label-selection-and-group"></a>매개 변수 값, 레이블, 선택 및 그룹
위의 동적 드롭다운 매개 변수에 사용된 쿼리는 드롭다운에서 충실하게 렌더링되는 값 목록을 반환합니다. 그러나 다른 디스플레이 이름을 선택하거나 이 중 하나를 선택하려면 어떻게해야합니까? 드롭다운 매개변수는 값, 레이블, 선택 및 그룹 열을 통해 이를 허용합니다.

아래 샘플에서는 디스플레이 이름이 이모티콘으로 스타일이 지정되고 첫 번째 표시 이름이 선택된 응용 프로그램 인사이트 종속성 목록을 작업 이름으로 그룹화하는 방법을 보여 주었습니다.

```kusto
dependencies
| summarize by operation_Name, name
| where name !contains ('.')
| order by name asc
| serialize Rank = row_number()
| project value = name, label = strcat('🌐 ', name), selected = iff(Rank == 1, true, false), group = operation_Name
```
    ![Image showing a drop-down parameter using value, label, selection and group options](./media/workbook-dropdowns/dropdown-more-options.png)


## <a name="drop-down-parameter-options"></a>드롭다운 매개 변수 옵션
| 매개 변수 | 설명 | 예제 |
| ------------- |:-------------|:-------------|
| `{DependencyName}` | 선택한 값 | 파브리캄계정 받기 |
| `{DependencyName:label}` | 선택한 레이블 | 🌐 파브리캄계정 GET |
| `{DependencyName:value}` | 선택한 값 | 파브리캄계정 받기 |

## <a name="multiple-selection"></a>여러 선택
지금까지의 예제에서는 드롭다운에서 하나의 값만 선택하도록 매개 변수를 명시적으로 설정했습니다. 드롭다운 매개 변수도 지원하므로 `multiple selection` `Allow multiple selection` 옵션을 확인하는 것만큼 간단합니다. 

사용자는 `delimiter` 또한 및 설정을 통해 설정된 결과 의 형식을 `quote with` 지정할 수 있습니다. 기본값은 'a', 'b', 'c'와 같은 값만 컬렉션으로 반환합니다. 또한 선택 횟수를 제한할 수도 있습니다.

매개 변수를 참조하는 KQL은 결과의 형식으로 작동하도록 변경해야 합니다. 이를 가능하게 하는 가장 일반적인 `in` 방법은 연산자입니다.

```kusto
dependencies
| where name in ({DependencyName})
| summarize Requests = count() by bin(timestamp, 1h), name
```

다음은 직장에서 다중 선택 드롭다운에 대한 예입니다.

![다중 선택 드롭다운 매개변수를 보여주는 이미지](./media/workbook-dropdowns/dropdown-multiselect.png)

## <a name="next-steps"></a>다음 단계

* 통합 문서에 대해 자세히 알아보고 다양한 다양한 시각화 옵션을 [알아보세요.](workbooks-visualizations.md)
* 통합 문서 리소스에 대한 액세스를 [제어하고](workbooks-access-control.md) 공유합니다.
