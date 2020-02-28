---
title: Azure Portal (미리 보기)를 사용 하 여 Azure 사용자 지정 역할 만들기 또는 업데이트-Azure RBAC
description: Azure Portal를 사용 하 여 azure RBAC (역할 기반 액세스 제어)에 대 한 Azure 사용자 지정 역할을 만드는 방법에 대해 알아봅니다. 여기에는 사용자 지정 역할을 나열, 생성, 업데이트 및 삭제하는 방법이 포함됩니다.
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
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77674871"
---
# <a name="create-or-update-azure-custom-roles-using-the-azure-portal-preview"></a>Azure Portal (미리 보기)를 사용 하 여 Azure 사용자 지정 역할 만들기 또는 업데이트

> [!IMPORTANT]
> Azure Portal를 사용 하는 Azure 사용자 지정 역할은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[Azure 기본 제공 역할이](built-in-roles.md) 조직의 특정 요구를 충족 하지 않는 경우 고유한 azure 사용자 지정 역할을 만들 수 있습니다. 기본 제공 역할과 마찬가지로, 구독 및 리소스 그룹 범위에서 사용자, 그룹 및 서비스 사용자에 게 사용자 지정 역할을 할당할 수 있습니다. 사용자 지정 역할은 Azure AD(Azure Active Directory) 디렉터리에 저장되며 구독에서 공유할 수 있습니다. 각 디렉터리에는 최대 5000 개의 사용자 지정 역할이 있을 수 있습니다. Azure Portal, Azure PowerShell, Azure CLI 또는 REST API를 사용 하 여 사용자 지정 역할을 만들 수 있습니다. 이 문서에서는 Azure Portal (현재 미리 보기 상태)를 사용 하 여 사용자 지정 역할을 만드는 방법을 설명 합니다.

## <a name="prerequisites"></a>사전 요구 사항

사용자 지정 역할을 만들려면 다음이 필요합니다.

- 사용자 지정 역할을 만들 수 있는 권한(예: [소유자](built-in-roles.md#owner) 또는 [사용자 액세스 관리자](built-in-roles.md#user-access-administrator))

## <a name="step-1-determine-the-permissions-you-need"></a>1 단계: 필요한 권한 확인

Azure에는 잠재적으로 사용자 지정 역할에 포함할 수 있는 수천 개의 권한이 있습니다. 다음 네 가지 방법으로 사용자 지정 역할에 추가 하려는 권한을 결정할 수 있습니다.

| 방법 | Description |
| --- | --- |
| 기존 역할 살펴보기 | 기존 역할을 확인 하 여 사용 되는 사용 권한을 확인할 수 있습니다. 자세한 내용은 [Azure 기본 제공 역할](built-in-roles.md)을 참조 하세요. |
| 키워드별 권한 검색 | Azure Portal를 사용 하 여 사용자 지정 역할을 만드는 경우 키워드를 사용 하 여 사용 권한을 검색할 수 있습니다. 예를 들어 *가상 컴퓨터* 또는 *청구* 권한을 검색할 수 있습니다. 이 검색 기능에 대 한 자세한 내용은 [4 단계: 사용 권한](#step-4-permissions)을 참조 하세요. |
| 모든 권한 다운로드 | Azure Portal를 사용 하 여 사용자 지정 역할을 만드는 경우 모든 권한을 CSV 파일로 다운로드 한 다음이 파일을 검색할 수 있습니다. **권한 추가** 창에서 **모든 권한 다운로드** 단추를 클릭 하 여 모든 사용 권한을 다운로드 합니다. 권한 추가 창에 대 한 자세한 내용은 [4 단계: 사용 권한](#step-4-permissions)을 참조 하세요. |
| 문서에서 사용 권한 보기 | [리소스 공급자 작업 Azure Resource Manager](resource-provider-operations.md)에서 사용 가능한 권한을 볼 수 있습니다. |

## <a name="step-2-choose-how-to-start"></a>2 단계: 시작 방법 선택

세 가지 방법으로 사용자 지정 역할을 만들 수 있습니다. 기존 역할을 복제 하거나, 처음부터 시작 하거나, JSON 파일을 사용 하 여 시작할 수 있습니다. 가장 쉬운 방법은 필요한 권한 대부분을 포함 하는 기존 역할을 찾은 다음 시나리오에 맞게 복제 및 수정 하는 것입니다. 

### <a name="clone-a-role"></a>역할 복제

기존 역할에 필요한 권한이 없는 경우 해당 역할을 복제 한 다음 사용 권한을 수정할 수 있습니다. 역할 복제를 시작 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 사용자 지정 역할을 할당할 수 있도록 하려는 구독 또는 리소스 그룹을 연 다음 **Access control (IAM)** 을 엽니다.

    다음 스크린샷은 구독에 대해 열린 액세스 제어 (IAM) 페이지를 보여 줍니다.

    ![구독의 액세스 제어 (IAM) 페이지](./media/custom-roles-portal/access-control-subscription.png)

1. **역할** 탭을 클릭하여 모든 기본 제공 역할 및 사용자 지정 역할 목록을 봅니다.

1. 청구 읽기 권한자 역할과 같이 복제할 역할을 검색 합니다.

1. 행의 끝에서 줄임표 ( **...** )를 클릭 한 다음 **복제**를 클릭 합니다.

    ![복제 상황에 맞는 메뉴](./media/custom-roles-portal/clone-menu.png)

    그러면 **역할 복제** 옵션을 선택한 상태에서 사용자 지정 역할 편집기가 열립니다.

1. [3 단계: 기본 사항](#step-3-basics)으로 이동 합니다.

### <a name="start-from-scratch"></a>처음부터 시작

원하는 경우 다음 단계를 수행 하 여 처음부터 사용자 지정 역할을 시작할 수 있습니다.

1. Azure Portal에서 사용자 지정 역할을 할당할 수 있도록 하려는 구독 또는 리소스 그룹을 연 다음 **Access control (IAM)** 을 엽니다.

1. **추가** 를 클릭 한 다음 **사용자 지정 역할 추가 (미리 보기)** 를 클릭 합니다.

    ![사용자 지정 역할 추가 메뉴](./media/custom-roles-portal/add-custom-role-menu.png)

    그러면 처음 **부터 시작** 하는 옵션을 선택 하 여 사용자 지정 역할 편집기가 열립니다.

1. [3 단계: 기본 사항](#step-3-basics)으로 이동 합니다.

### <a name="start-from-json"></a>JSON에서 시작

원하는 경우 JSON 파일에서 대부분의 사용자 지정 역할 값을 지정할 수 있습니다. 사용자 지정 역할 편집기에서 파일을 열고 추가로 변경한 다음 사용자 지정 역할을 만들 수 있습니다. JSON 파일을 시작 하려면 다음 단계를 수행 합니다.

1. 다음 형식의 JSON 파일을 만듭니다.

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

1. JSON 파일에서 다양 한 속성에 대 한 값을 지정 합니다. 일부 값이 추가 된 예제는 다음과 같습니다. 다른 속성에 대 한 자세한 내용은 [역할 정의 이해](role-definitions.md)를 참조 하세요.

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
    
1. Azure Portal에서 **Access control (IAM)** 페이지를 엽니다.

1. **추가** 를 클릭 한 다음 **사용자 지정 역할 추가 (미리 보기)** 를 클릭 합니다.

    ![사용자 지정 역할 추가 메뉴](./media/custom-roles-portal/add-custom-role-menu.png)

    그러면 사용자 지정 역할 편집기가 열립니다.

1. 기본 정보 탭의 **기준 권한**에서 **JSON에서 시작**을 선택 합니다.

1. 파일 선택 상자 옆에 있는 폴더 단추를 클릭 하 여 열기 대화 상자를 엽니다.

1. JSON 파일을 선택 하 고 **열기**를 클릭 합니다.

1. [3 단계: 기본 사항](#step-3-basics)으로 이동 합니다.

## <a name="step-3-basics"></a>3 단계: 기본 사항

**기본 사항** 탭에서 사용자 지정 역할에 대 한 이름, 설명 및 기준 사용 권한을 지정 합니다.

1. **사용자 지정 역할 이름** 상자에 사용자 지정 역할의 이름을 지정 합니다. 이름은 Azure AD 디렉터리에 대해 고유 해야 합니다. 이름에는 문자, 숫자, 공백 및 특수 문자가 포함 될 수 있습니다.

1. **설명** 상자에 사용자 지정 역할에 대 한 선택적 설명을 지정 합니다. 사용자 지정 역할에 대 한 도구 설명이 됩니다.

    이전 단계를 기반으로 **기준 사용 권한** 옵션을 이미 설정 해야 하지만 변경할 수 있습니다.

    ![값이 지정 된 기본 탭](./media/custom-roles-portal/basics-values.png)

## <a name="step-4-permissions"></a>4 단계: 사용 권한

**사용 권한** 탭에서 사용자 지정 역할에 대 한 사용 권한을 지정 합니다. 역할을 복제 했는지에 따라 또는 JSON으로 시작한 경우 사용 권한 탭에는 이미 일부 사용 권한이 나열 될 수 있습니다.

![사용자 지정 역할 만들기의 사용 권한 탭](./media/custom-roles-portal/permissions.png)

### <a name="add-or-remove-permissions"></a>권한 추가 또는 제거

사용자 지정 역할에 대 한 사용 권한을 추가 하거나 제거 하려면 다음 단계를 따르세요.

1. 사용 권한을 추가 하려면 권한 **추가** 를 클릭 하 여 권한 추가 창을 엽니다.

    이 창에는 카드 형식의 다른 범주로 그룹화 된 사용 가능한 모든 권한이 나열 됩니다. 각 범주는 Azure 리소스를 제공 하는 서비스인 *리소스 공급자*를 나타냅니다.

1. **권한 검색** 상자에 사용 권한을 검색할 문자열을 입력 합니다. 예를 *들어 청구서를 검색 하 여 청구서* 와 관련 된 사용 권한을 찾습니다.

    리소스 공급자 카드의 목록이 검색 문자열에 따라 표시 됩니다. 리소스 공급자가 Azure 서비스에 매핑되는 방법에 대 한 목록은 [azure 서비스에 대 한 리소스 공급자](../azure-resource-manager/management/azure-services-resource-providers.md)를 참조 하세요.

    ![리소스 공급자를 사용 하 여 권한 창 추가](./media/custom-roles-portal/add-permissions-provider.png)

1. **Microsoft 청구**와 같이 사용자 지정 역할에 추가 하려는 사용 권한을 가질 수 있는 리소스 공급자 카드를 클릭 합니다.

    해당 리소스 공급자에 대 한 관리 권한 목록이 검색 문자열에 따라 표시 됩니다.

    ![권한 목록 추가](./media/custom-roles-portal/add-permissions-list.png)

1. 데이터 평면에 적용 되는 사용 권한을 찾고 있는 경우 **데이터 작업**을 클릭 합니다. 그렇지 않으면 관리 평면에 적용 되는 사용 권한을 나열 하려면 작업 설정/ **해제 작업을** 그대로 둡니다. 관리 평면과 데이터 평면 간의 차이점에 대 한 자세한 내용은 [관리 및 데이터 작업](role-definitions.md#management-and-data-operations)을 참조 하세요.

1. 필요한 경우 검색 문자열을 업데이트 하 여 검색을 구체화 합니다.

1. 사용자 지정 역할에 추가 하려는 사용 권한을 하나 이상 찾았으면 권한 옆에 확인 표시를 추가 합니다. 예를 들어 **기타: 청구서** 다운로드 옆에 확인 표시를 추가 하 여 청구서 다운로드 권한을 추가 합니다.

1. **추가** 를 클릭 하 여 사용 권한 목록에 사용 권한을 추가 합니다.

    사용 권한은 `Actions` 또는 `DataActions`로 추가 됩니다.

    ![권한이 추가 됨](./media/custom-roles-portal/permissions-list-add.png)

1. 사용 권한을 제거 하려면 행의 끝에 있는 삭제 아이콘을 클릭 합니다. 이 예에서는 사용자에 게 지원 티켓을 만들 수 있는 기능이 필요 하지 않으므로 `Microsoft.Support/*` 권한을 삭제할 수 있습니다.

### <a name="add-wildcard-permissions"></a>와일드 카드 권한 추가

시작을 선택한 방법에 따라 사용 권한 목록에서 와일드 카드 (\*)를 사용할 수 있습니다. 와일드 카드 (\*)는 제공 된 문자열과 일치 하는 모든 항목에 대 한 사용 권한을 확장 합니다. 예를 들어 Azure Cost Management 및 내보내기와 관련 된 모든 권한을 추가 하려고 한다고 가정 합니다. 이러한 사용 권한을 모두 추가할 수 있습니다.

```
Microsoft.CostManagement/exports/action
Microsoft.CostManagement/exports/read
Microsoft.CostManagement/exports/write
Microsoft.CostManagement/exports/delete
Microsoft.CostManagement/exports/run/action
```

이러한 사용 권한을 모두 추가 하는 대신 와일드 카드 사용 권한만 추가할 수 있습니다. 예를 들어 다음 와일드 카드 권한은 이전의 5 개 사용 권한과 같습니다. 여기에는 추가 될 수 있는 이후의 내보내기 권한도 포함 됩니다.

```
Microsoft.CostManagement/exports/*
```

새 와일드 카드 권한을 추가 하려면 **권한 추가** 창을 사용 하 여 추가할 수 없습니다. 와일드 카드 권한을 추가 하려면 **JSON** 탭을 사용 하 여 수동으로 추가 해야 합니다. 자세한 내용은 [6 단계: JSON](#step-6-json)을 참조 하세요.

### <a name="exclude-permissions"></a>권한 제외

사용자의 역할에 와일드 카드 (\*) 권한이 있고 해당 와일드 카드 권한에서 특정 사용 권한을 제외 하거나 빼려는 경우 해당 사용 권한을 제외할 수 있습니다. 예를 들어 다음과 같은 와일드 카드 권한이 있다고 가정해 보겠습니다.

```
Microsoft.CostManagement/exports/*
```

내보내기 삭제를 허용 하지 않으려면 다음과 같은 삭제 권한을 제외 하면 됩니다.

```
Microsoft.CostManagement/exports/delete
```

사용 권한을 제외 하면 `NotActions` 또는 `NotDataActions`로 추가 됩니다. 모든 `Actions`을 추가한 다음 `NotActions`를 모두 빼서 유효 관리 권한을 계산 합니다. 유효 데이터 권한은 모든 `DataActions`을 추가한 다음 `NotDataActions`를 모두 빼서 계산 합니다.

> [!NOTE]
> 권한 제외는 거부와 동일 하지 않습니다. 권한 제외는 단순히 와일드 카드 사용 권한에서 사용 권한을 빼는 편리한 방법입니다.

1. 허용 되는 와일드 카드 사용 권한에 대 한 사용 권한을 제외 하거나 빼려면 권한 **제외** 를 클릭 하 여 권한 제외 창을 엽니다.

    이 창에서 제외 하거나 뺄 관리 또는 데이터 사용 권한을 지정 합니다.

1. 제외 하려는 하나 이상의 사용 권한을 찾았으면 권한 옆에 확인 표시를 추가 하 고 **추가** 단추를 클릭 합니다.

    ![권한 제외 창-사용 권한 선택](./media/custom-roles-portal/exclude-permissions-select.png)

    사용 권한은 `NotActions` 또는 `NotDataActions`로 추가 됩니다.

    ![권한 제외](./media/custom-roles-portal/exclude-permissions-list-add.png)

## <a name="step-5-assignable-scopes"></a>5 단계: 할당 가능한 범위

할당 가능한 **범위** 탭에서 사용자 지정 역할을 할당할 수 있는 위치 (예: 구독 또는 리소스 그룹)를 지정 합니다. 시작을 선택한 방법에 따라이 탭에는 액세스 제어 (IAM) 페이지를 연 범위가 나열 될 수 있습니다. 할당 가능한 범위를 루트 범위 ("/")로 설정 하는 것은 지원 되지 않습니다. 이 미리 보기에서는 관리 그룹을 할당 가능한 범위로 추가할 수 없습니다.

1. **할당 가능한 범위 추가** 를 클릭 하 여 할당 가능한 범위 추가 창을 엽니다.

    ![할당 가능한 범위 탭](./media/custom-roles-portal/assignable-scopes.png)

1. 사용 하려는 하나 이상의 범위 (일반적으로 구독)를 클릭 합니다.

    ![할당 가능한 범위 추가](./media/custom-roles-portal/add-assignable-scopes.png)

1. **추가** 단추를 클릭 하 여 할당 가능한 범위를 추가 합니다.

## <a name="step-6-json"></a>6 단계: JSON

**Json** 탭에서 json으로 형식이 지정 된 사용자 지정 역할을 확인 합니다. 원하는 경우 JSON을 직접 편집할 수 있습니다. 와일드 카드 (\*) 권한을 추가 하려면이 탭을 사용 해야 합니다.

1. JSON을 편집 하려면 **편집**을 클릭 합니다.

    ![사용자 지정 역할을 보여 주는 JSON 탭](./media/custom-roles-portal/json.png)

1. JSON을 변경 합니다.

    JSON의 형식이 잘못 된 경우 가로 여백에 빨강 선이나 표시기가 표시 됩니다.

1. 편집이 완료 되 면 **저장**을 클릭 합니다.

## <a name="step-7-review--create"></a>7 단계: 검토 + 만들기

**검토 + 만들기** 탭에서 사용자 지정 역할 설정을 검토할 수 있습니다.

1. 사용자 지정 역할 설정을 검토 합니다.

    ![리뷰 + 만들기 탭](./media/custom-roles-portal/review-create.png)

1. **만들기** 를 클릭 하 여 사용자 지정 역할을 만듭니다.

    잠시 후 사용자 지정 역할이 성공적으로 만들어졌는지 나타내는 메시지 상자가 나타납니다.

    ![사용자 지정 역할 메시지 만들기](./media/custom-roles-portal/custom-role-success.png)

    오류가 검색 되 면 메시지가 표시 됩니다.

    ![검토 + 만들기 오류](./media/custom-roles-portal/review-create-error.png)

1. **역할** 목록에서 새 사용자 지정 역할을 확인 합니다. 사용자 지정 역할이 표시 되지 않으면 **새로 고침**을 클릭 합니다.

     사용자 지정 역할이 어디에 나 표시 될 때까지 몇 분 정도 걸릴 수 있습니다.

## <a name="list-custom-roles"></a>사용자 지정 역할 나열

사용자 지정 역할을 보려면 다음 단계를 따르세요.

1. 구독 또는 리소스 그룹을 연 다음 **Access control (IAM)** 을 엽니다.

1. **역할** 탭을 클릭하여 모든 기본 제공 역할 및 사용자 지정 역할 목록을 봅니다.

1. **유형** 목록에서 **customrole** 을 선택 하 여 사용자 지정 역할만 표시 합니다.

    방금 사용자 지정 역할을 만든 경우 목록에 표시 되지 않으면 **새로 고침**을 클릭 합니다.

    ![사용자 지정 역할 목록](./media/custom-roles-portal/custom-role-list.png)

## <a name="update-a-custom-role"></a>사용자 지정 역할 업데이트

1. 이 문서의 앞부분에서 설명한 대로 사용자 지정 역할의 목록을 엽니다.

1. 업데이트 하려는 사용자 지정 역할에 대 한 줄임표 ( **...** )를 클릭 한 다음 **편집**을 클릭 합니다. 기본 제공 역할은 업데이트할 수 없습니다.

    편집기에서 사용자 지정 역할이 열립니다.

    ![사용자 지정 역할 메뉴](./media/custom-roles-portal/edit-menu.png)

1. 다른 탭을 사용 하 여 사용자 지정 역할을 업데이트 합니다.

1. 변경을 완료 한 후에는 **검토 + 만들기** 탭을 클릭 하 여 변경 내용을 검토 합니다.

1. **업데이트** 단추를 클릭 하 여 사용자 지정 역할을 업데이트 합니다.

## <a name="delete-a-custom-role"></a>사용자 지정 역할 삭제

1. 이 문서의 앞부분에서 설명한 대로 사용자 지정 역할의 목록을 엽니다.

1. 사용자 지정 역할을 사용 하는 역할 할당을 제거 합니다.

1. 삭제 하려는 사용자 지정 역할에 대 한 줄임표 ( **...** )를 클릭 한 다음 **삭제**를 클릭 합니다.

    ![사용자 지정 역할 메뉴](./media/custom-roles-portal/delete-menu.png)

    사용자 지정 역할을 완전히 삭제 하는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [자습서: Azure PowerShell을 사용하여 사용자 지정 역할 만들기](tutorial-custom-role-powershell.md)
- [Azure의 사용자 지정 역할](custom-roles.md)
- [Azure Resource Manager 리소스 공급자 작업](resource-provider-operations.md)
