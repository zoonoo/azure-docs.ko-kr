---
title: Azure 모니터 통합 문서 리소스 매개 변수
description: 미리 빌드되고 사용자 지정 매개 변수가 있는 통합 문서를 사용하여 복잡한 보고 간소화
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: cc2cde7932f783f63ee2783f0589ce4f88f248a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658105"
---
# <a name="workbook-resource-parameters"></a>통합 문서 리소스 매개 변수

리소스 매개 변수를 사용하면 통합 문서의 리소스를 선택할 수 있습니다. 이는 데이터를 얻을 수 있는 범위를 설정하는 데 유용합니다. 예를 들어 사용자가 데이터를 표시할 때 차트에서 나중에 사용할 VM 집합을 선택할 수 있도록 허용합니다.

리소스 선택자의 값은 통합 문서 컨텍스트, 정적 목록 또는 Azure 리소스 그래프 쿼리에서 올 수 있습니다.

## <a name="creating-a-resource-parameter-workbook-resources"></a>리소스 매개 변수 만들기(통합 문서 리소스)
1. 편집 모드에서 빈 통합 문서로 시작합니다.
2. 통합 문서 내의 링크에서 _매개변수 추가를_ 선택합니다.
3. 파란색 _매개변수 추가_ 버튼을 클릭합니다.
4. 팝업 새 매개 변수 창에서 다음을 입력합니다.
    1. 매개 변수 이름:`Applications`
    2. 매개 변수 유형:`Resource picker`
    3. 필수:`checked`
    4. 여러 선택 허용:`checked`
5. 다음에서 데이터 가져옵니다.`Workbook Resources`
6. 리소스 유형만 포함합니다.`Application Insights`
7. 도구 모음에서 '저장'을 선택하여 매개변수를 만듭니다.

![통합 문서 리소스를 사용하여 리소스 매개 변수를 만드는 이미지](./media/workbooks-resources/resource-create.png)

## <a name="creating-a-resource-parameter-azure-resource-graph"></a>리소스 매개 변수 만들기(Azure 리소스 그래프)
1. 편집 모드에서 빈 통합 문서로 시작합니다.
2. 통합 문서 내의 링크에서 _매개변수 추가를_ 선택합니다.
3. 파란색 _매개변수 추가_ 버튼을 클릭합니다.
4. 팝업 새 매개 변수 창에서 다음을 입력합니다.
    1. 매개 변수 이름:`Applications`
    2. 매개 변수 유형:`Resource picker`
    3. 필수:`checked`
    4. 여러 선택 허용:`checked`
5. 다음에서 데이터 가져옵니다.`Query`
    1. 쿼리 유형:`Azure Resource Graph`
    2. 구독:`Use default subscriptions`
    3. 쿼리 컨트롤에서 이 스니펫을 추가합니다.
    ```kusto
    where type == 'microsoft.insights/components'
    | project value = id, label = name, selected = false, group = resourceGroup
    ```
7. 도구 모음에서 '저장'을 선택하여 매개변수를 만듭니다.

![Azure 리소스 그래프를 사용하여 리소스 매개 변수를 만드는 이미지](./media/workbooks-resources/resource-query.png)

> [!NOTE]
> Azure 리소스 그래프는 아직 모든 클라우드에서 사용할 수 없습니다. 이 방법을 선택하면 대상 클라우드에서 지원되도록 해야 합니다.

[Azure 리소스 그래프 설명서](https://docs.microsoft.com/azure/governance/resource-graph/overview)

## <a name="creating-a-resource-parameter--json-list"></a>리소스 매개 변수 만들기(JSON 목록)
1. 편집 모드에서 빈 통합 문서로 시작합니다.
2. 통합 문서 내의 링크에서 _매개변수 추가를_ 선택합니다.
3. 파란색 _매개변수 추가_ 버튼을 클릭합니다.
4. 팝업 새 매개 변수 창에서 다음을 입력합니다.
    1. 매개 변수 이름:`Applications`
    2. 매개 변수 유형:`Resource picker`
    3. 필수:`checked`
    4. 여러 선택 허용:`checked`
5. 다음에서 데이터 가져옵니다.`JSON`
    1. 콘텐츠 컨트롤에서 이 json 스니펫을 추가합니다.
    ```json
    [
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeauthentication", "label": "acmeauthentication", "selected":true, "group":"Acme Backend" },
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeweb", "label": "acmeweb", "selected":false, "group":"Acme Frontend" }
    ]
    ```
    2. 파란색 _업데이트_ 버튼을 누를 수 있습니다.
6. 선택적으로 응용 `Include only resource types` _프로그램 인사이트로_ 설정
7. 도구 모음에서 '저장'을 선택하여 매개변수를 만듭니다.

## <a name="referencing-a-resource-parameter"></a>리소스 매개 변수 참조
1. 통합 문서에 쿼리 컨트롤을 추가하고 응용 프로그램 인사이트 리소스를 선택합니다.
2. 응용 _프로그램 인사이트_ 드롭다운을 사용하여 매개 변수를 컨트롤에 바인딩합니다. 이렇게 하면 런타임에 매개 변수에서 반환되는 리소스에 대한 쿼리 범위가 설정됩니다.
4. KQL 컨트롤에서 이 스니펫을 추가합니다.
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | order by Requests desc
    ```
5. 쿼리를 실행하여 결과를 확인합니다. 

![쿼리 컨트롤에서 참조된 리소스 매개 변수를 보여 주면 이미지](./media/workbooks-resources/resource-reference.png)

> 이 방법을 사용하면 메트릭과 같은 다른 컨트롤에 리소스를 바인딩할 수 있습니다.

## <a name="resource-parameter-options"></a>리소스 매개 변수 옵션
| 매개 변수 | 설명 | 예제 |
| ------------- |:-------------|:-------------|
| `{Applications}` | 선택한 리소스 ID | _/구독/<하위 id>/리소스그룹/<리소스 그룹>/공급자/<리소스 유형>/acmeauthentication_ |
| `{Applications:label}` | 선택한 리소스의 레이블 | `acmefrontend` |
| `{Applications:value}` | 선택한 리소스의 값 | _'/구독/<하위 id>/리소스그룹/<리소스 그룹>/공급자/<리소스 유형>/인증'_ |
| `{Applications:name}` | 선택한 리소스의 이름 | `acmefrontend` |
| `{Applications:resourceGroup}` | 선택한 리소스의 리소스 그룹 | `acmegroup` |
| `{Applications:resourceType}` | 선택한 리소스의 형식 | _마이크로 소프트.인사이트 / 구성 요소_ |
| `{Applications:subscription}` | 선택한 리소스의 구독 |  |
| `{Applications:grid}` | 리소스 속성을 보여 는 그리드입니다. 디버깅하는 동안 텍스트 블록에서 렌더링하는 데 유용합니다.  |  |

## <a name="next-steps"></a>다음 단계

* 통합 문서에 대해 자세히 알아보고 다양한 다양한 시각화 옵션을 [알아보세요.](workbooks-visualizations.md)
* 통합 문서 리소스에 대한 액세스를 [제어하고](workbooks-access-control.md) 공유합니다.
