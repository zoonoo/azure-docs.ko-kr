---
title: Azure Monitor 통합 문서 리소스 매개 변수
description: 미리 빌드되고 사용자 지정 매개 변수가 있는 통합 문서를 사용하여 복잡한 보고 간소화
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: b5822c1af4f3bc5d9519af93054a01c3cdc82001
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86504994"
---
# <a name="workbook-resource-parameters"></a>통합 문서 리소스 매개 변수

리소스 매개 변수를 사용 하 여 통합 문서에서 리소스를 선택할 수 있습니다. 이는 데이터를 가져올 범위를 설정 하는 데 유용 합니다. 사용자가 나중에 데이터를 표시할 때 차트에서 사용할 Vm 집합을 선택할 수 있도록 허용 하는 경우를 예로 들 수 있습니다.

리소스 선택기의 값은 통합 문서 컨텍스트, 정적 목록 또는 Azure 리소스 그래프 쿼리에서 가져올 수 있습니다.

## <a name="creating-a-resource-parameter-workbook-resources"></a>리소스 매개 변수 만들기 (통합 문서 리소스)
1. 편집 모드에서 빈 통합 문서를 시작 합니다.
2. 통합 문서 내의 링크에서 _매개 변수 추가_ 를 선택 합니다.
3. 파란색 _매개 변수 추가_ 단추를 클릭 합니다.
4. 새 매개 변수 창에서 enter 키를 누르십시오.
    1. 매개 변수 이름:`Applications`
    2. 매개 변수 유형:`Resource picker`
    3. 필수:`checked`
    4. 여러 항목을 선택할 수 있습니다.`checked`
5. 데이터 가져오기:`Workbook Resources`
6. 리소스 유형만 포함:`Application Insights`
7. 도구 모음에서 ' 저장 '을 선택 하 여 매개 변수를 만듭니다.

![통합 문서 리소스를 사용 하 여 리소스 매개 변수의 생성을 보여 주는 이미지](./media/workbooks-resources/resource-create.png)

## <a name="creating-a-resource-parameter-azure-resource-graph"></a>리소스 매개 변수 만들기 (Azure 리소스 그래프)
1. 편집 모드에서 빈 통합 문서를 시작 합니다.
2. 통합 문서 내의 링크에서 _매개 변수 추가_ 를 선택 합니다.
3. 파란색 _매개 변수 추가_ 단추를 클릭 합니다.
4. 새 매개 변수 창에서 enter 키를 누르십시오.
    1. 매개 변수 이름:`Applications`
    2. 매개 변수 유형:`Resource picker`
    3. 필수:`checked`
    4. 여러 항목을 선택할 수 있습니다.`checked`
5. 데이터 가져오기:`Query`
    1. 쿼리 유형:`Azure Resource Graph`
    2. 등에`Use default subscriptions`
    3. 쿼리 컨트롤에서이 코드 조각을 추가 합니다.
    ```kusto
    where type == 'microsoft.insights/components'
    | project value = id, label = name, selected = false, group = resourceGroup
    ```
7. 도구 모음에서 ' 저장 '을 선택 하 여 매개 변수를 만듭니다.

![Azure 리소스 그래프를 사용 하 여 리소스 매개 변수의 생성을 보여 주는 이미지](./media/workbooks-resources/resource-query.png)

> [!NOTE]
> 모든 클라우드에서 Azure 리소스 그래프를 아직 사용할 수 없습니다. 이 방법을 선택 하는 경우 대상 클라우드에서 지원 되는지 확인 합니다.

[Azure Resource Graph 설명서](../../governance/resource-graph/overview.md)

## <a name="creating-a-resource-parameter--json-list"></a>리소스 매개 변수 만들기 (JSON 목록)
1. 편집 모드에서 빈 통합 문서를 시작 합니다.
2. 통합 문서 내의 링크에서 _매개 변수 추가_ 를 선택 합니다.
3. 파란색 _매개 변수 추가_ 단추를 클릭 합니다.
4. 새 매개 변수 창에서 enter 키를 누르십시오.
    1. 매개 변수 이름:`Applications`
    2. 매개 변수 유형:`Resource picker`
    3. 필수:`checked`
    4. 여러 항목을 선택할 수 있습니다.`checked`
5. 데이터 가져오기:`JSON`
    1. 콘텐츠 컨트롤에서이 json 코드 조각을 추가 합니다.
    ```json
    [
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeauthentication", "label": "acmeauthentication", "selected":true, "group":"Acme Backend" },
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeweb", "label": "acmeweb", "selected":false, "group":"Acme Frontend" }
    ]
    ```
    2. 파란색 _업데이트_ 단추를 누릅니다.
6. 필요에 따라 `Include only resource types` _Application Insights_ 설정
7. 도구 모음에서 ' 저장 '을 선택 하 여 매개 변수를 만듭니다.

## <a name="referencing-a-resource-parameter"></a>리소스 매개 변수 참조
1. 통합 문서에 쿼리 컨트롤을 추가 하 고 Application Insights 리소스를 선택 합니다.
2. _Application Insights_ 드롭다운을 사용 하 여 매개 변수를 컨트롤에 바인딩합니다. 이렇게 하면 런타임에 매개 변수에 의해 반환 되는 리소스로 쿼리 범위가 설정 됩니다.
4. KQL 컨트롤에서이 코드 조각을 추가 합니다.
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | order by Requests desc
    ```
5. 쿼리를 실행 하 여 결과를 확인 합니다. 

![쿼리 컨트롤에서 참조 되는 리소스 매개 변수를 보여 주는 이미지](./media/workbooks-resources/resource-reference.png)

> 이 접근 방식을 사용 하면 리소스를 메트릭과 같은 다른 컨트롤에 바인딩할 수 있습니다.

## <a name="resource-parameter-options"></a>리소스 매개 변수 옵션
| 매개 변수 | 설명 | 예제 |
| ------------- |:-------------|:-------------|
| `{Applications}` | 선택한 리소스 ID | _/subscriptions/<하위 id>/Stgg/<리소스 그룹>/providers/<>/acmeauthentication_ |
| `{Applications:label}` | 선택한 리소스의 레이블 | `acmefrontend` |
| `{Applications:value}` | 선택한 리소스의 값입니다. | _'/subscriptions/<하위 id>/Wsourceg/<resource-group>/providers/<>/acmeauthentication '_ |
| `{Applications:name}` | 선택한 리소스의 이름입니다. | `acmefrontend` |
| `{Applications:resourceGroup}` | 선택한 리소스의 리소스 그룹 | `acmegroup` |
| `{Applications:resourceType}` | 선택한 리소스의 형식입니다. | _microsoft 인 사이트/구성 요소_ |
| `{Applications:subscription}` | 선택한 리소스의 구독 |  |
| `{Applications:grid}` | 리소스 속성을 보여 주는 표입니다. 디버깅 하는 동안 텍스트 블록에서 렌더링 하는 데 유용 합니다.  |  |

## <a name="next-steps"></a>다음 단계

* 통합 문서에 대 한 자세한 내용은 다양 한 기능을 갖춘 시각화 옵션을 [시작](workbooks-visualizations.md) 하세요.
* 통합 문서 리소스에 대 한 액세스를 [제어](workbooks-access-control.md) 하 고 공유 합니다.
