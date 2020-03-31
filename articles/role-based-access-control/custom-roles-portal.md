---
title: Azure 포털(미리 보기) - Azure RBAC를 사용하여 Azure 사용자 지정 역할 만들기 또는 업데이트
description: Azure 포털을 사용하여 Azure 역할 기반 액세스 제어(Azure RBAC)에 대한 Azure 사용자 지정 역할을 만드는 방법에 대해 알아봅니다. 여기에는 사용자 지정 역할을 나열, 생성, 업데이트 및 삭제하는 방법이 포함됩니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/26/2020
ms.author: rolyon
ms.openlocfilehash: 3204cdf51f3f37588f684f801a811f569b337d13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77674871"
---
# <a name="create-or-update-azure-custom-roles-using-the-azure-portal-preview"></a>Azure 포털을 사용하여 Azure 사용자 지정 역할 만들기 또는 업데이트(미리 보기)

> [!IMPORTANT]
> Azure 포털을 사용하는 Azure 사용자 지정 역할은 현재 공개 미리 보기상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure [기본 제공 역할이](built-in-roles.md) 조직의 특정 요구 사항을 충족하지 않는 경우 고유한 Azure 사용자 지정 역할을 만들 수 있습니다. 기본 제공 역할과 마찬가지로 구독 및 리소스 그룹 범위에서 사용자, 그룹 및 서비스 주체에 사용자 지정 역할을 할당할 수 있습니다. 사용자 지정 역할은 Azure AD(Azure Active Directory) 디렉터리에 저장되며 구독에서 공유할 수 있습니다. 각 디렉터리에는 최대 5,000개의 사용자 지정 역할이 있을 수 있습니다. 사용자 지정 역할은 Azure 포털, Azure PowerShell, Azure CLI 또는 REST API를 사용하여 만들 수 있습니다. 이 문서에서는 Azure 포털(현재 미리 보기)을 사용하여 사용자 지정 역할을 만드는 방법을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

사용자 지정 역할을 만들려면 다음이 필요합니다.

- 사용자 지정 역할을 만들 수 있는 권한(예: [소유자](built-in-roles.md#owner) 또는 [사용자 액세스 관리자](built-in-roles.md#user-access-administrator))

## <a name="step-1-determine-the-permissions-you-need"></a>1단계: 필요한 사용 권한 결정

Azure에는 사용자 지정 역할에 잠재적으로 포함할 수 있는 수천 개의 사용 권한이 있습니다. 다음은 사용자 지정 역할에 추가할 권한을 결정할 수 있는 네 가지 방법입니다.

| 방법 | 설명 |
| --- | --- |
| 기존 역할 보기 | 기존 역할을 확인하여 사용 중인 사용 권한을 확인할 수 있습니다. 자세한 내용은 [Azure 기본 제공 역할을](built-in-roles.md)참조하십시오. |
| 키워드로 사용 권한 검색 | Azure 포털을 사용하여 사용자 지정 역할을 만들 때 키워드별로 사용 권한을 검색할 수 있습니다. 예를 들어 *가상 컴퓨터* 또는 *청구* 권한을 검색할 수 있습니다. 이 검색 기능은 4단계 권한 의 나중에 자세히 [설명합니다.](#step-4-permissions) |
| 모든 권한 다운로드 | Azure 포털을 사용하여 사용자 지정 역할을 만들 때 모든 사용 권한을 CSV 파일로 다운로드한 다음 이 파일을 검색할 수 있습니다. 권한 **추가** 창에서 **모든 권한 다운로드 단추를** 클릭하여 모든 권한을 다운로드합니다. 사용 권한 추가 창에 대한 자세한 내용은 [4단계: 사용 권한](#step-4-permissions)을 참조하십시오. |
| 문서에서 사용 권한 보기 | Azure 리소스 관리자 리소스 [공급자 작업에서](resource-provider-operations.md)사용 가능한 권한을 볼 수 있습니다. |

## <a name="step-2-choose-how-to-start"></a>2단계: 시작 방법 선택

사용자 지정 역할을 만드는 방법에는 세 가지가 있습니다. 기존 역할을 복제하거나 처음부터 시작하거나 JSON 파일로 시작할 수 있습니다. 가장 쉬운 방법은 필요한 대부분의 사용 권한이 있는 기존 역할을 찾은 다음 시나리오에 맞게 복제하고 수정하는 것입니다. 

### <a name="clone-a-role"></a>역할 복제

기존 역할에 필요한 사용 권한이 없는 경우 해당 역할에 복제한 다음 사용 권한을 수정할 수 있습니다. 다음 단계에 따라 역할 복제를 시작합니다.

1. Azure 포털에서 사용자 지정 역할을 할당할 수 있는 구독 또는 리소스 그룹을 열고 **IAM(액세스 컨트롤)을**엽니다.

    다음 스크린샷은 구독에 대해 열린 IAM(액세스 제어) 페이지를 보여 주며 있습니다.

    ![구독에 대한 액세스 제어(IAM) 페이지](./media/custom-roles-portal/access-control-subscription.png)

1. **역할** 탭을 클릭하여 모든 기본 제공 역할 및 사용자 지정 역할 목록을 봅니다.

1. 청구 리더 역할과 같이 복제할 역할을 검색합니다.

1. 행의 끝에서 타원 **(...**)을 클릭한 다음 **복제를**클릭합니다.

    ![클론 컨텍스트 메뉴](./media/custom-roles-portal/clone-menu.png)

    그러면 역할 복제 옵션이 선택된 사용자 지정 역할 **편집기가** 열립니다.

1. [3단계: 기본 사항으로 진행.](#step-3-basics)

### <a name="start-from-scratch"></a>처음부터 시작

원하는 경우 다음 단계를 수행하여 사용자 지정 역할을 처음부터 시작할 수 있습니다.

1. Azure 포털에서 사용자 지정 역할을 할당할 수 있는 구독 또는 리소스 그룹을 열고 **IAM(액세스 컨트롤)을**엽니다.

1. **추가를** 클릭한 다음 **사용자 지정 역할 추가(미리 보기)를**클릭합니다.

    ![사용자 지정 역할 메뉴 추가](./media/custom-roles-portal/add-custom-role-menu.png)

    이렇게 하면 처음부터 시작 옵션을 선택한 사용자 지정 역할 **편집기가** 열립니다.

1. [3단계: 기본 사항으로 진행.](#step-3-basics)

### <a name="start-from-json"></a>JSON에서 시작

원하는 경우 JSON 파일에 대부분의 사용자 지정 역할 값을 지정할 수 있습니다. 사용자 지정 역할 편집기에서 파일을 열고 추가 변경한 다음 사용자 지정 역할을 만들 수 있습니다. 다음 단계에 따라 JSON 파일로 시작합니다.

1. 다음과 같은 형식의 JSON 파일을 만듭니다.

    ```json
    {
        "properties": {
            "roleName": "",
            "description": "",
            "assignableScopes": [],
            "permissions": [
                {
                    "actions": [],
                    "notActions": [],
                    "dataActions": [],
                    "notDataActions": []
                }
            ]
        }
    }
    ```

1. JSON 파일에서 다양한 속성에 대한 값을 지정합니다. 다음은 일부 값이 추가된 예제입니다. 다른 속성에 대한 자세한 내용은 [역할 정의 이해하기를](role-definitions.md)참조하십시오.

    ```json
    {
        "properties": {
            "roleName": "Billing Reader Plus",
            "description": "Read billing data and download invoices",
            "assignableScopes": [
                "/subscriptions/11111111-1111-1111-1111-111111111111"
            ],
            "permissions": [
                {
                    "actions": [
                        "Microsoft.Authorization/*/read",
                        "Microsoft.Billing/*/read",
                        "Microsoft.Commerce/*/read",
                        "Microsoft.Consumption/*/read",
                        "Microsoft.Management/managementGroups/read",
                        "Microsoft.CostManagement/*/read",
                        "Microsoft.Support/*"
                    ],
                    "notActions": [],
                    "dataActions": [],
                    "notDataActions": []
                }
            ]
        }
    }
    ```
    
1. Azure 포털에서 **IAM(액세스 컨트롤)** 페이지를 엽니다.

1. **추가를** 클릭한 다음 **사용자 지정 역할 추가(미리 보기)를**클릭합니다.

    ![사용자 지정 역할 메뉴 추가](./media/custom-roles-portal/add-custom-role-menu.png)

    그러면 사용자 지정 역할 편집기가 열립니다.

1. 기본 탭에서 기준선 사용 **권한에서 JSON에서 시작을** **선택합니다.**

1. 파일 선택 상자 옆의 폴더 단추를 클릭하여 대화 상자 열기를 엽니다.

1. JSON 파일을 선택한 다음 **열기를**클릭합니다.

1. [3단계: 기본 사항으로 진행.](#step-3-basics)

## <a name="step-3-basics"></a>3단계: 기본 사항

**기본** 탭에서 사용자 지정 역할에 대한 이름, 설명 및 기준 사용 권한을 지정합니다.

1. 사용자 **지정 역할 이름** 상자에서 사용자 지정 역할에 대 한 이름을 지정 합니다. 이름은 Azure AD 디렉터리에서 고유해야 합니다. 이름에는 문자, 숫자, 공백 및 특수 문자가 포함될 수 있습니다.

1. **설명** 상자에서 사용자 지정 역할에 대한 선택적 설명을 지정합니다. 사용자 지정 역할의 도구 설명이 됩니다.

    **기준선 사용 권한** 옵션은 이전 단계에 따라 이미 설정되어 있어야 하지만 변경할 수 있습니다.

    ![값이 지정된 기본 탭](./media/custom-roles-portal/basics-values.png)

## <a name="step-4-permissions"></a>4단계: 권한

사용 **권한** 탭에서 사용자 지정 역할에 대한 권한을 지정합니다. 역할을 복제했는지 또는 JSON을 시작했는지에 따라 사용 권한 탭에 이미 일부 사용 권한이 나열될 수 있습니다.

![사용자 지정 역할 만들기의 권한 탭](./media/custom-roles-portal/permissions.png)

### <a name="add-or-remove-permissions"></a>사용 권한 추가 또는 제거

다음 단계에 따라 사용자 지정 역할에 대한 권한을 추가하거나 제거합니다.

1. 권한을 추가하려면 **사용 권한 추가창을** 클릭하여 사용 권한 추가 창을 엽니다.

    이 창에는 카드 형식으로 서로 다른 범주로 그룹화된 사용 가능한 모든 사용 권한이 나열됩니다. 각 범주는 Azure 리소스를 제공하는 서비스인 *리소스 공급자를*나타냅니다.

1. 사용 **권한 검색** 상자에서 사용 권한을 검색할 문자열을 입력합니다. 예를 들어 *송장* 검색을 통해 송장과 관련된 권한을 찾을 수 있습니다.

    리소스 공급자 카드 목록이 검색 문자열에 따라 표시됩니다. 리소스 공급자가 Azure 서비스에 매핑하는 방법에 대한 목록은 [Azure 서비스에 대한 리소스 공급자를](../azure-resource-manager/management/azure-services-resource-providers.md)참조하십시오.

    ![리소스 공급자를 사용 하 고 사용 권한 창 추가](./media/custom-roles-portal/add-permissions-provider.png)

1. 사용자 지정 역할에 추가할 권한이 있을 수 있는 리소스 공급자 카드를 **클릭합니다(예: Microsoft 청구.**

    해당 리소스 공급자에 대 한 관리 권한 목록은 검색 문자열에 따라 표시 됩니다.

    ![사용 권한 목록 추가](./media/custom-roles-portal/add-permissions-list.png)

1. 데이터 평면에 적용되는 권한을 찾고 있는 경우 데이터 작업 을 **클릭합니다.** 그렇지 않으면 작업 토글을 **작업으로** 설정하여 관리 평면에 적용되는 권한을 나열합니다. 자세한 내용은 관리 평면과 데이터 평면 간의 차이점을 참조하여 [관리 및 데이터 작업을](role-definitions.md#management-and-data-operations)참조하십시오.

1. 필요한 경우 검색 문자열을 업데이트하여 검색을 더 구체화합니다.

1. 사용자 지정 역할에 추가할 권한을 하나 이상 찾은 후 사용 권한 옆에 확인 표시를 추가합니다. 예를 들어 **기타: 인보이스 다운로드** 옆에 확인 표시를 추가하여 송장을 다운로드할 수 있는 권한을 추가합니다.

1. 권한 목록에 권한을 추가하려면 **추가를** 클릭합니다.

    권한은 `Actions` 또는 로 `DataActions`추가됩니다.

    ![권한 추가](./media/custom-roles-portal/permissions-list-add.png)

1. 권한을 제거하려면 행 끝에 있는 삭제 아이콘을 클릭합니다. 이 예제에서는 사용자가 지원 티켓을 만들 필요가 없으므로 `Microsoft.Support/*` 권한을 삭제할 수 있습니다.

### <a name="add-wildcard-permissions"></a>와일드카드 권한 추가

시작 방법에 따라 사용 권한 목록에 와일드카드()가\*있는 사용 권한이 있을 수 있습니다. 와일드카드 ()\*는 제공한 문자열과 일치하는 모든 것에 대한 권한을 확장합니다. 예를 들어 Azure 비용 관리 및 내보내기와 관련된 모든 권한을 추가하려고 한다고 가정합니다. 다음 모든 권한을 추가할 수 있습니다.

```
Microsoft.CostManagement/exports/action
Microsoft.CostManagement/exports/read
Microsoft.CostManagement/exports/write
Microsoft.CostManagement/exports/delete
Microsoft.CostManagement/exports/run/action
```

이러한 모든 권한을 추가하는 대신 와일드카드 권한을 추가할 수 있습니다. 예를 들어 다음 와일드카드 권한은 이전 5개의 사용 권한과 동일합니다. 여기에는 추가될 수 있는 향후 내보내기 권한도 포함됩니다.

```
Microsoft.CostManagement/exports/*
```

새 와일드카드 권한을 추가하려면 **사용 권한 추가** 창을 사용하여 와일드카드 권한을 추가할 수 없습니다. 와일드카드 권한을 추가하려면 **JSON** 탭을 사용하여 수동으로 추가해야 합니다. 자세한 내용은 [6단계: JSON](#step-6-json)을 참조하십시오.

### <a name="exclude-permissions"></a>권한 제외

역할에 와일드카드()\*권한이 있고 해당 와일드카드 사용 권한에서 특정 권한을 제외하거나 빼려는 경우 제외할 수 있습니다. 예를 들어 다음과 같은 와일드카드 권한이 있다고 가정해 보겠습니다.

```
Microsoft.CostManagement/exports/*
```

내보내기를 삭제하지 않으려면 다음 삭제 권한을 제외할 수 있습니다.

```
Microsoft.CostManagement/exports/delete
```

권한을 제외하면 `NotActions` 또는 `NotDataActions`로 추가됩니다. 효과적인 관리 권한은 모든 `Actions` 을 추가한 다음 을 모두 빼서 계산됩니다. `NotActions` 유효 데이터 사용 권한은 모든 `DataActions` 을 추가한 다음 을 모두 `NotDataActions`빼서 계산됩니다.

> [!NOTE]
> 권한을 제외하는 것은 거부와 동일하지 않습니다. 사용 권한을 제외하는 것은 와일드카드 권한에서 권한을 빼는 편리한 방법입니다.

1. 허용된 와일드카드 권한에서 권한을 제외하거나 빼려면 **사용 권한 제외창을** 클릭하여 사용 권한 제외 창을 엽니다.

    이 창에서 제외되거나 차감되는 관리 또는 데이터 권한을 지정합니다.

1. 제외할 권한을 하나 이상 찾은 후 사용 권한 옆에 확인 표시를 추가한 다음 **추가** 단추를 클릭합니다.

    ![사용 권한 창 제외 - 사용 권한 선택](./media/custom-roles-portal/exclude-permissions-select.png)

    권한은 `NotActions` 또는 `NotDataActions`으로 추가됩니다.

    ![사용 권한 제외](./media/custom-roles-portal/exclude-permissions-list-add.png)

## <a name="step-5-assignable-scopes"></a>5단계: 할당 가능한 범위

할당 **가능한 범위** 탭에서 구독 또는 리소스 그룹과 같이 사용자 지정 역할을 할당할 수 있는 위치를 지정합니다. 시작을 선택한 방법에 따라 이 탭에 IAM(액세스 컨트롤) 페이지를 연 범위가 나열될 수 있습니다. 루트 범위("/")에 할당 가능한 범위를 설정하면 지원되지 않습니다. 이 미리 보기의 경우 관리 그룹을 할당 가능한 범위로 추가할 수 없습니다.

1. **할당 가능한 범위 추가를** 클릭하여 할당 가능한 범위 추가 창을 엽니다.

    ![할당 가능한 범위 탭](./media/custom-roles-portal/assignable-scopes.png)

1. 사용하려는 범위(일반적으로 구독)를 하나 이상 클릭합니다.

    ![할당 가능한 범위 추가](./media/custom-roles-portal/add-assignable-scopes.png)

1. 추가 **단추를** 클릭하여 할당 가능한 범위를 추가합니다.

## <a name="step-6-json"></a>6단계: JSON

**JSON** 탭에서 JSON에서 사용자 지정 역할 의 서식이 지정된 것을 볼 수 있습니다. 원하는 경우 JSON을 직접 편집할 수 있습니다. 와일드카드()\*권한을 추가하려면 이 탭을 사용해야 합니다.

1. JSON을 편집하려면 **편집을**클릭합니다.

    ![사용자 지정 역할을 표시하는 JSON 탭](./media/custom-roles-portal/json.png)

1. JSON을 변경합니다.

    JSON의 서식이 올바르게 지정되지 않으면 빨간색 들쭉날쭉한 선과 수직 홈통에 표시등이 표시됩니다.

1. 편집이 완료되면 **저장을**클릭합니다.

## <a name="step-7-review--create"></a>7단계: 검토 + 만들기

검토 **+ 만들기** 탭에서 사용자 지정 역할 설정을 검토할 수 있습니다.

1. 사용자 지정 역할 설정을 검토합니다.

    ![리뷰 + 만들기 탭](./media/custom-roles-portal/review-create.png)

1. **만들기를** 클릭하여 사용자 지정 역할을 만듭니다.

    잠시 후 사용자 지정 역할이 성공적으로 만들어졌음을 나타내는 메시지 상자가 나타납니다.

    ![사용자 지정 역할 메시지 만들기](./media/custom-roles-portal/custom-role-success.png)

    오류가 감지되면 메시지가 표시됩니다.

    ![검토 + 오류 만들기](./media/custom-roles-portal/review-create-error.png)

1. **역할** 목록에서 새 사용자 지정 역할을 봅니다. 사용자 지정 역할이 표시되지 않으면 **새로 고침을**클릭합니다.

     사용자 지정 역할이 모든 곳에 표시되는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="list-custom-roles"></a>사용자 지정 역할 나열

다음 단계에 따라 사용자 지정 역할을 봅니다.

1. 구독 또는 리소스 그룹을 연 다음 **IAM(액세스 제어)을**엽니다.

1. **역할** 탭을 클릭하여 모든 기본 제공 역할 및 사용자 지정 역할 목록을 봅니다.

1. **유형** 목록에서 사용자 **지정 롤을** 선택하여 사용자 지정 역할만 볼 수 있습니다.

    사용자 지정 역할을 방금 만들었는데 목록에 표시되지 않는 경우 **새로 고침을 클릭합니다.**

    ![사용자 지정 역할 목록](./media/custom-roles-portal/custom-role-list.png)

## <a name="update-a-custom-role"></a>사용자 지정 역할 업데이트

1. 이 문서의 앞에서 설명한 대로 사용자 지정 역할 목록을 엽니다.

1. 업데이트할 사용자 지정 역할에 대한 타원 **(...**) 을 클릭한 다음 **편집을**클릭합니다. 기본 제공 역할을 업데이트할 수 없습니다.

    편집기에서 사용자 지정 역할이 열립니다.

    ![사용자 지정 역할 메뉴](./media/custom-roles-portal/edit-menu.png)

1. 다른 탭을 사용하여 사용자 지정 역할을 업데이트합니다.

1. 변경 작업이 완료되면 검토 + **만들기** 탭을 클릭하여 변경 내용을 검토합니다.

1. **업데이트** 단추를 클릭하여 사용자 지정 역할을 업데이트합니다.

## <a name="delete-a-custom-role"></a>사용자 지정 역할 삭제

1. 이 문서의 앞에서 설명한 대로 사용자 지정 역할 목록을 엽니다.

1. 사용자 지정 역할을 사용하는 역할 할당을 제거합니다.

1. 삭제하려는 사용자 지정 역할에 대한 타원 **(...**) 을 클릭한 다음 **삭제를**클릭합니다.

    ![사용자 지정 역할 메뉴](./media/custom-roles-portal/delete-menu.png)

    사용자 지정 역할이 완전히 삭제되려면 몇 분 정도 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [자습서: Azure PowerShell을 사용하여 사용자 지정 역할 만들기](tutorial-custom-role-powershell.md)
- [Azure의 사용자 지정 역할](custom-roles.md)
- [Azure 리소스 관리자 리소스 공급자 작업](resource-provider-operations.md)
