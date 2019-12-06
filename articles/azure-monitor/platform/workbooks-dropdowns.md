---
title: Azure Monitor 통합 문서 드롭다운 매개 변수
description: 드롭다운 매개 변수를 포함 하는 미리 작성 된 사용자 지정 매개 변수가 있는 통합 문서로 복잡 한 보고 간소화
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: df30b71e6ec9e982988ed75b187db08255e24b61
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872879"
---
# <a name="workbook-drop-down-parameters"></a>통합 문서 드롭다운 매개 변수

Drop 드롭다운에서 사용자가 알려진 집합에서 하나 이상의 입력 값을 수집할 수 있도록 허용 합니다 (예: 앱 요청 중 하나를 선택). Drop 드롭다운은 사용자에 게 임의의 입력을 수집 하는 사용자에 게 친숙 한 방법을 제공 합니다. 드롭다운은 대화형 보고서에서 필터링을 사용 하도록 설정 하는 데 특히 유용 합니다. 

드롭다운을 지정 하는 가장 쉬운 방법은 매개 변수 설정에서 정적 목록을 제공 하는 것입니다. 더 흥미로운 방법은 KQL 쿼리를 통해 목록을 동적으로 가져오는 것입니다. 매개 변수 설정을 사용 하 여 단일 또는 다중 선택 인지 여부를 지정할 수 있으며, 다중 선택 인 경우 결과 집합의 형식을 지정 하는 방법 (구분 기호, 따옴표 등)을 지정할 수 있습니다.

## <a name="creating-a-static-drop-down-parameter"></a>정적 드롭다운 매개 변수 만들기

1. 편집 모드에서 빈 통합 문서를 시작 합니다.
2. 통합 문서 내의 링크에서 _매개 변수 추가_ 를 선택 합니다.
3. 파란색 _매개 변수 추가_ 단추를 클릭 합니다.
4. 새 매개 변수 창에서 enter 키를 누르십시오.
    1. 매개 변수 이름: `Environment`
    2. 매개 변수 유형: `Drop down`
    3. 필수: `checked`
    4. 허용 `multiple selection`: `unchecked`
    5. 데이터 가져오기: `JSON`
5. JSON 입력 텍스트 블록에서 다음 json 코드 조각을 삽입 합니다.
    ```json
    [
        { "value":"dev", "label":"Development" },
        { "value":"ppe", "label":"Pre-production" },
        { "value":"prod", "label":"Production", "selected":true }
    ]
    ```
6. 파란색 `Update` 단추를 누릅니다.
7. 도구 모음에서 ' 저장 '을 선택 하 여 매개 변수를 만듭니다.
8. 환경 매개 변수는 세 개의 값이 있는 드롭다운입니다.

    ![정적 drown의 생성을 보여 주는 이미지](./media/workbook-dropdowns/dropdown-create.png)

## <a name="creating-a-static-dropdown-with-groups-of-items"></a>항목 그룹을 사용 하 여 정적 드롭다운 만들기
쿼리 결과/json에 "그룹" 필드가 포함 되어 있는 경우 드롭다운에 값 그룹이 표시 됩니다. 위의 샘플을 따르고 대신 다음 json을 사용 합니다.
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
1. 편집 모드에서 빈 통합 문서를 시작 합니다.
2. 통합 문서 내의 링크에서 _매개 변수 추가_ 를 선택 합니다.
3. 파란색 _매개 변수 추가_ 단추를 클릭 합니다.
4. 새 매개 변수 창에서 enter 키를 누르십시오.
    1. 매개 변수 이름: `RequestName`
    2. 매개 변수 유형: `Drop down`
    3. 필수: `checked`
    4. 허용 `multiple selection`: `unchecked`
    5. 데이터 가져오기: `Query`
5. JSON 입력 텍스트 블록에서 다음 json 코드 조각을 삽입 합니다.

    ```kusto
        requests
        | summarize by name
        | order by name asc
    ```
1. 파란색 `Run Query` 단추를 누릅니다.
2. 도구 모음에서 ' 저장 '을 선택 하 여 매개 변수를 만듭니다.
3. RequestName 매개 변수는 앱의 모든 요청 이름이 드롭다운 됩니다.

    ![동적 드롭다운 생성을 보여 주는 이미지](./media/workbook-dropdowns/dropdown-dynamic.png)

## <a name="referencing-drop-down-parameter"></a>드롭다운 매개 변수 참조
### <a name="in-kql"></a>KQL에서
1. 통합 문서에 쿼리 컨트롤을 추가 하 고 Application Insights 리소스를 선택 합니다.
2. KQL 편집기에서 다음 코드 조각을 입력 합니다.

    ```kusto
        requests
        | where name == '{RequestName}'
        | summarize Requests = count() by bin(timestamp, 1h)

    ```
3. 이렇게 하면 쿼리 평가 시간이 다음과 같이 확장 됩니다.

    ```kusto
        requests
        | where name == 'GET Home/Index'
        | summarize Requests = count() by bin(timestamp, 1h)
    ```

4. 쿼리를 실행 하 여 결과를 확인 합니다. 필요에 따라 차트를 렌더링 합니다.

    ![KQL에서 참조 되는 드롭다운을 보여 주는 이미지](./media/workbook-dropdowns/dropdown-reference.png)


## <a name="parameter-value-label-selection-and-group"></a>매개 변수 값, 레이블, 선택 및 그룹
위의 동적 드롭다운 매개 변수에 사용 되는 쿼리는 드롭다운 목록에서 정확 하 게 렌더링 되는 값 목록을 반환 합니다. 다른 표시 이름을 선택 하거나이 중 하나를 선택 하려면 어떻게 해야 하나요? 드롭다운 매개 변수는 값, 레이블, 선택 및 그룹 열을 통해이를 허용 합니다.

아래 샘플은 표시 이름이 해당 하는 것으로 스타일이 지정 되 고, 첫 번째 항목을 선택 하 고, 작업 이름별로 그룹화 된 Application Insights 종속성 목록을 가져오는 방법을 보여 줍니다.

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
| 매개 변수를 포함해야 합니다. | 설명 | 예제 |
| ------------- |:-------------|:-------------|
| `{DependencyName}` | 선택한 값 | Fabrikamaccount 가져오기 |
| `{DependencyName:label}` | 선택한 레이블 | fabrikamaccount 가져오기 🌐 |
| `{DependencyName:value}` | 선택한 값 | Fabrikamaccount 가져오기 |

## <a name="multiple-selection"></a>다중 선택
지금까지 예제에서는 매개 변수를 명시적으로 설정 하 여 드롭다운에서 값을 하나만 선택 합니다. 드롭다운 매개 변수는 `multiple selection` 지원 `Allow multiple selection` 옵션을 선택 하는 것 처럼 간단 합니다. 

또한 사용자는 `delimiter` 및 `quote with` 설정을 통해 결과 집합의 형식을 지정할 수 있습니다. 기본값은 단순히 ' a ', ' b ', ' c ' 형식의 컬렉션으로 값을 반환 합니다. 또한 선택 항목 수를 제한 하는 옵션도 있습니다.

매개 변수를 참조 하는 KQL는 결과 형식으로 작업 하기 위해 변경 해야 합니다. 이를 사용 하도록 설정 하는 가장 일반적인 방법은 `in` 연산자를 사용 하는 것입니다.

```kusto
dependencies
| where name in ({DependencyName})
| summarize Requests = count() by bin(timestamp, 1h), name
```

다음은 work에서 다중 선택 드롭 다운에 대 한 예제입니다.

![다중 선택 드롭다운 매개 변수를 보여 주는 이미지](./media/workbook-dropdowns/dropdown-multiselect.png)

## <a name="next-steps"></a>다음 단계

* 통합 문서에 대 한 자세한 내용은 다양 한 기능을 갖춘 시각화 옵션을 [시작](workbooks-visualizations.md) 하세요.
* 통합 문서 리소스에 대 한 액세스를 [제어](workbooks-access-control.md) 하 고 공유 합니다.
