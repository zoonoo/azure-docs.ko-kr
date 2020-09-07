---
title: Azure 엔터프라이즈 구독을 만들기 위한 액세스 권한 부여
description: Azure 엔터프라이즈 구독을 프로그래밍 방식으로 만드는 기능을 사용자 또는 서비스 주체에 부여하는 방법에 대해 알아봅니다.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.reviewer: amberb
ms.topic: conceptual
ms.date: 08/26/2020
ms.author: banders
ms.openlocfilehash: b154d723e82d02ea864459ef65eb5c05c14ae336
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88943184"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Azure 엔터프라이즈 구독 만들기에 대한 액세스 권한 부여(미리 보기)

[EA(기업계약)](https://azure.microsoft.com/pricing/enterprise-agreement/)의 Azure 고객은 고객의 계정으로 청구되는 구독을 만들 수 있는 권한을 다른 사용자 또는 서비스 주체에게 부여할 수 있습니다. 이 문서에서는 [Azure RBAC(Azure 역할 기반 액세스 제어)](../../role-based-access-control/role-assignments-portal.md)를 사용하여 구독을 만드는 기능을 공유하는 방법 및 구독 만들기를 감사하는 방법에 대해 알아봅니다. 공유하려는 계정에 대해 소유자 역할이 있어야 합니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="grant-access"></a>액세스 권한 부여

[등록 계정에서 구독을 만들려면](programmatically-create-subscription.md) 사용자에게 해당 계정에 대한 [RBAC 소유자 역할](../../role-based-access-control/built-in-roles.md#owner)이 있어야 합니다. 다음 단계를 수행하여 등록 계정에 대한 RBAC 소유자 역할을 사용자 또는 사용자 그룹에 부여할 수 있습니다.

1. 액세스 권한을 부여하려는 등록 계정의 개체 ID를 가져옵니다.

    다른 사용자에게 등록 계정에 대한 RBAC 소유자 역할을 부여하려면 계정 소유자 또는 계정의 RBAC 소유자여야 합니다.

    # <a name="rest"></a>[REST (영문)](#tab/rest)

    액세스 권한이 있는 모든 등록 계정을 나열하도록 요청합니다.

    ```json
    GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
    ```

    Azure는 액세스할 수 있는 모든 등록 계정의 목록으로 응답합니다.

    ```json
    {
      "value": [
        {
          "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "Microsoft.Billing/enrollmentAccounts",
          "properties": {
            "principalName": "SignUpEngineering@contoso.com"
          }
        },
        {
          "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "Microsoft.Billing/enrollmentAccounts",
          "properties": {
            "principalName": "BillingPlatformTeam@contoso.com"
          }
        }
      ]
    }
    ```

    `principalName` 속성을 사용하여 RBAC 소유자에게 액세스 권한을 부여하려는 계정을 식별합니다. 해당 계정의 `name`을 복사합니다. 예를 들어 RBAC 소유자에게 SignUpEngineering@contoso.com 등록 계정에 대한 액세스 권한을 부여하려면 ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```를 복사합니다. 이는 등록 계정의 개체 ID입니다. 다음 단계에서 `enrollmentAccountObjectId`로 사용할 수 있도록 이 값을 어딘가에 붙여넣습니다.

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) cmdlet을 사용하여 액세스 권한이 있는 모든 등록 계정을 나열합니다. **사용해 보세요**를 선택하여 [Azure Cloud Shell](https://shell.azure.com/)을 엽니다. 코드를 붙여넣으려면 마우스 오른쪽 단추로 셸 창을 클릭하고 **붙여넣기**를 선택합니다.

    ```azurepowershell-interactive
    Get-AzEnrollmentAccount
    ```

    Azure에서 액세스 권한이 있는 등록 계정 목록으로 응답합니다.

    ```azurepowershell
    ObjectId                               | PrincipalName
    747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
    4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
    ```

    `principalName` 속성을 사용하여 RBAC 소유자에게 액세스 권한을 부여하려는 계정을 식별합니다. 해당 계정의 `ObjectId`를 복사합니다. 예를 들어 RBAC 소유자에게 SignUpEngineering@contoso.com 등록 계정에 대한 액세스 권한을 부여하려면 ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```를 복사합니다. 다음 단계에서 `enrollmentAccountObjectId`로 사용할 수 있도록 이 개체 ID를 어딘가에 붙여넣습니다.

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    [az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) 명령을 사용하여 액세스할 수 있는 모든 등록 계정을 나열합니다. **사용해 보세요**를 선택하여 [Azure Cloud Shell](https://shell.azure.com/)을 엽니다. 코드를 붙여넣으려면 마우스 오른쪽 단추로 셸 창을 클릭하고 **붙여넣기**를 선택합니다.

    ```azurecli-interactive
    az billing enrollment-account list
    ```

    Azure에서 액세스 권한이 있는 등록 계정 목록으로 응답합니다.

    ```json
    [
      {
        "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "principalName": "SignUpEngineering@contoso.com",
        "type": "Microsoft.Billing/enrollmentAccounts",
      },
      {
        "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "principalName": "BillingPlatformTeam@contoso.com",
        "type": "Microsoft.Billing/enrollmentAccounts",
      }
    ]
    ```

    ---

    `principalName` 속성을 사용하여 RBAC 소유자에게 액세스 권한을 부여하려는 계정을 식별합니다. 해당 계정의 `name`을 복사합니다. 예를 들어 RBAC 소유자에게 SignUpEngineering@contoso.com 등록 계정에 대한 액세스 권한을 부여하려면 ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```를 복사합니다. 이는 등록 계정의 개체 ID입니다. 다음 단계에서 `enrollmentAccountObjectId`로 사용할 수 있도록 이 값을 어딘가에 붙여넣습니다.

1. <a id="userObjectId"></a>RBAC 소유자 역할을 부여하려는 사용자 또는 그룹의 개체 ID를 가져옵니다.

    1. Azure Portal에서 **Azure Active Directory**를 검색합니다.
    1. 사용자에게 액세스 권한을 부여하려면 왼쪽 메뉴에서 **사용자**를 선택합니다. 그룹에 대한 액세스 권한을 부여하려면 **그룹**을 선택합니다.
    1. RBAC 소유자 역할을 부여하려는 [사용자] 또는 [그룹]을 선택합니다.
    1. [사용자]를 선택한 경우 [프로필] 페이지에서 개체 ID를 찾을 수 있습니다. [그룹]을 선택한 경우 개체 ID는 [개요] 페이지에 있습니다. 텍스트 상자 오른쪽에 있는 아이콘을 선택하여 **ObjectID**를 복사합니다. 다음 단계에서 `userObjectId`로 사용할 수 있도록 이를 어딘가에 붙여넣습니다.

1. 등록 계정에 대한 RBAC 소유자 역할을 사용자 또는 그룹에 부여합니다.

    처음 두 단계에서 수집한 값을 사용하여 등록 계정에 대한 RBAC 소유자 역할을 사용자 또는 그룹에 부여합니다.

    # <a name="rest"></a>[REST (영문)](#tab/rest-2)

    ```<enrollmentAccountObjectId>```를 첫 번째 단계에서 복사한 `name`(```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```)으로 바꿔서 다음 명령을 실행합니다. ```<userObjectId>```를 두 번째 단계에서 복사한 개체 ID로 바꿉니다.

    ```json
    PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>"
      }
    }
    ```

    등록 계정 범위에서 소유자 역할을 성공적으로 할당하면 Azure는 역할 할당의 정보로 응답합니다.

    ```json
    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>",
        "scope": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "createdOn": "2018-03-05T08:36:26.4014813Z",
        "updatedOn": "2018-03-05T08:36:26.4014813Z",
        "createdBy": "<assignerObjectId>",
        "updatedBy": "<assignerObjectId>"
      },
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "<roleAssignmentGuid>"
    }
    ```

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell-2)

    ```<enrollmentAccountObjectId>```를 첫 번째 단계에서 수집한 `ObjectId`(```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```)로 바꿔서 다음 [New-AzRoleAssignment](../../role-based-access-control/role-assignments-powershell.md) 명령을 실행합니다. ```<userObjectId>```를 두 번째 단계에서 수집한 개체 ID로 바꿉니다.

    ```azurepowershell-interactive
    New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli-2)

    ```<enrollmentAccountObjectId>```를 첫 번째 단계에서 복사한 `name`(```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```)으로 바꿔서 다음 [az role assignment create](../../role-based-access-control/role-assignments-cli.md) 명령을 실행합니다. ```<userObjectId>```를 두 번째 단계에서 수집한 개체 ID로 바꿉니다.

    ```azurecli-interactive
    az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    사용자가 등록 계정에 대한 RBAC 소유자가 되면 [구독을 프로그래밍 방식으로 만들](programmatically-create-subscription.md) 수 있습니다. 위임된 사용자가 만든 구독에는 여전히 원래 계정 소유자가 서비스 관리자로 있지만, 기본적으로 위임된 사용자도 RBAC 소유자로 있습니다.

    ---

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>활동 로그를 사용하여 구독을 만든 사람 감사

이 API를 통해 만든 구독을 추적하려면 [테넌트 활동 로그 API](/rest/api/monitor/tenantactivitylogs)를 사용합니다. 현재 PowerShell, CLI 또는 Azure Portal을 사용하여 구독 만들기를 추적할 수 없습니다.

1. Azure AD 테넌트의 테넌트 관리자로 [액세스의 권한을 상승한](../../role-based-access-control/elevate-access-global-admin.md) 다음, `/providers/microsoft.insights/eventtypes/management` 범위에 대해 감사 사용자에게 읽기 역할을 할당합니다. 이 액세스 권한은 [읽기 권한자](../../role-based-access-control/built-in-roles.md#reader) 역할, [모니터링 기여자](../../role-based-access-control/built-in-roles.md#monitoring-contributor) 역할 또는 [ 사용자 지정 역할](../../role-based-access-control/custom-roles.md)에서 사용할 수 있습니다.
1. 감사 사용자로 [테넌트 활동 로그 API](/rest/api/monitor/tenantactivitylogs)를 호출하여 구독 생성 작업을 봅니다. 예:

    ```
    GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'"
    ```

명령줄에서 이 API를 편리하게 호출하려면 [ARMClient](https://github.com/projectkudu/ARMClient)를 시도합니다.

## <a name="next-steps"></a>다음 단계

* 사용자 또는 서비스 주체가 구독을 만들 권한을 가지므로 해당 ID를 사용하여 [프로그래밍 방식으로 Azure 엔터프라이즈 구독을 만들 수 있습니다](programmatically-create-subscription.md).
* .NET을 사용하여 구독 만들기에 대한 예제는 [GitHub의 샘플 코드](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core)를 참조하세요.
* Azure Resource Manager 및 해당 API에 대한 자세한 내용은 [Azure Resource Manager 개요](../../azure-resource-manager/management/overview.md)를 참조하세요.
* 관리 그룹을 사용하여 많은 수의 구독 관리에 대해 자세히 알아보려면 [Azure 관리 그룹으로 리소스 구성](../../governance/management-groups/overview.md)을 참조하세요.
* 구독 거버넌스에서 대규모 조직에 대한 포괄적인 모범 사례 지침을 보려면 [Azure 엔터프라이즈 스캐폴드 - 규범적 구독 거버넌스](/azure/architecture/cloud-adoption-guide/subscription-governance)를 참조하세요.
