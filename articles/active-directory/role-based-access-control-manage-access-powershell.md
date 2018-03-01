---
title: "Azure PowerShell을 사용하여 RBAC(역할 기반 Access Control) 관리 | Microsoft Docs"
description: "Azure PowerShell에서 역할을 나열하고, 역할을 할당하고, 역할 할당을 제거하는 등 RBAC를 관리하는 방법입니다."
services: active-directory
documentationcenter: 
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: rolyon
ms.reviewer: rqureshi
ms.openlocfilehash: 5e7e01502a173cb93216c77dd43f61094fd88148
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/22/2018
---
# <a name="manage-role-based-access-control-with-azure-powershell"></a>Azure PowerShell을 사용하여 역할 기반 Access Control 관리
> [!div class="op_single_selector"]
> * [PowerShell](role-based-access-control-manage-access-powershell.md)
> * [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
> * [REST API](role-based-access-control-manage-access-rest.md)

RBAC(역할 기반 액세스 제어)를 통해 특정 범위에서 역할을 할당하여 사용자, 그룹, 서비스 주체에 대한 액세스를 정의합니다. 이 문서에서는 Azure PowerShell을 사용하여 액세스를 관리하는 방법을 설명합니다.

PowerShell을 사용하여 RBAC를 관리하려면 다음 항목이 필요합니다.

* Azure PowerShell 버전 0.8.8 이상. 최신 버전을 설치하고 Azure 구독에 연결하려면 [Azure PowerShell 설치 및 구성하는 방법](/powershell/azure/overview)을 참조하세요.
* Azure Resource Manager cmdlet. PowerShell에서 [Azure Resource Manager cmdlet](/powershell/azure/overview) 을 설치합니다.

## <a name="list-roles"></a>역할 나열
### <a name="list-all-available-roles"></a>사용 가능한 모든 역할 나열
할당할 수 있는 RBAC 역할을 나열하고 액세스 권한을 부여하는 작업을 검사하려면 `Get-AzureRmRoleDefinition`을 사용합니다.

```
Get-AzureRmRoleDefinition | FT Name, Description
```

![RBAC PowerShell - Get-AzureRmRoleDefinition - 스크린샷](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### <a name="list-actions-of-a-role"></a>역할의 작업 나열
특정 역할을 작업을 나열하려면 `Get-AzureRmRoleDefinition <role name>`을 사용합니다.

```
Get-AzureRmRoleDefinition Contributor | FL Actions, NotActions

(Get-AzureRmRoleDefinition "Virtual Machine Contributor").Actions
```

![RBAC PowerShell - 특정 역할에 대한 Get-AzureRmRoleDefinition - 스크린샷](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## <a name="see-who-has-access"></a>액세스 권한이 있는 사용자 확인
RBAC 액세스 할당을 나열하려면 `Get-AzureRmRoleAssignment`를 사용합니다.

### <a name="list-role-assignments-at-a-specific-scope"></a>특정 범위의 역할 할당 나열
지정된 구독, 리소스 그룹 또는 리소스에 대한 모든 액세스 할당을 확인할 수 있습니다. 예를 들어 리소스 그룹에 대한 모든 활성 할당을 확인하려면 `Get-AzureRmRoleAssignment -ResourceGroupName <resource group name>`을 사용합니다.

```
Get-AzureRmRoleAssignment -ResourceGroupName Pharma-Sales-ProjectForcast | FL DisplayName, RoleDefinitionName, Scope
```

![RBAC PowerShell - 리소스 그룹에 대한 Get-AzureRmRoleAssignment - 스크린샷](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### <a name="list-roles-assigned-to-a-user"></a>사용자에게 할당된 역할 나열
지정된 사용자에게 할당된 역할 및 사용자가 속한 그룹에 할당된 역할을 모두 나열하려면 `Get-AzureRmRoleAssignment -SignInName <User email> -ExpandPrincipalGroups`를 사용합니다.

```
Get-AzureRmRoleAssignment -SignInName sameert@aaddemo.com | FL DisplayName, RoleDefinitionName, Scope

Get-AzureRmRoleAssignment -SignInName sameert@aaddemo.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

![RBAC PowerShell - 사용자에 대한 Get-AzureRmRoleAssignment - 스크린샷](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### <a name="list-classic-service-administrator-and-coadmin-role-assignments"></a>클래식 서비스 관리자 및 공동 관리자 역할 할당 나열
클래식 구독 관리자 및 공동 관리자에 대한 액세스 할당을 나열하려면 다음을 사용합니다.

    Get-AzureRmRoleAssignment -IncludeClassicAdministrators

## <a name="grant-access"></a>액세스 권한 부여
### <a name="search-for-object-ids"></a>개체 ID 검색
역할을 할당하려면 개체(사용자, 그룹 또는 응용 프로그램)와 범위 둘 다를 식별해야 합니다.

구독 ID를 모르는 경우 Azure 포털의 **구독** 블레이드에서 확인할 수 있습니다. 구독 ID를 쿼리하는 방법을 알아보려면 MSDN에서 [Get-AzureSubscription](/powershell/module/azure/get-azuresubscription?view=azuresmps-3.7.0) 을 참조하세요.

Azure AD 그룹에 대한 개체 ID를 가져오려면 다음을 사용합니다.

    Get-AzureRmADGroup -SearchString <group name in quotes>

Azure AD 서비스 사용자 또는 응용 프로그램에 대한 개체 ID를 찾으려면 다음을 사용합니다.

    Get-AzureRmADServicePrincipal -SearchString <service name in quotes>

### <a name="assign-a-role-to-an-application-at-the-subscription-scope"></a>구독 범위에서 응용 프로그램에 역할 할당
구독 범위에서 응용 프로그램에 액세스 권한을 부여하려면 다음을 사용합니다.

    New-AzureRmRoleAssignment -ObjectId <application id> -RoleDefinitionName <role name> -Scope <subscription id>

![RBAC PowerShell - New-AzureRmRoleAssignment - 스크린샷](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment2.png)

### <a name="assign-a-role-to-a-user-at-the-resource-group-scope"></a>리소스 그룹 범위에서 사용자에 역할 할당
리소스 그룹 범위에서 사용자에 액세스 권한을 부여하려면 다음을 사용합니다.

    New-AzureRmRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>

![RBAC PowerShell - New-AzureRmRoleAssignment - 스크린샷](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment3.png)

### <a name="assign-a-role-to-a-group-at-the-resource-scope"></a>리소스 범위에서 그룹에 역할 할당
리소스 범위에서 그룹에 액세스 권한을 부여하려면 다음을 사용합니다.

    New-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>

![RBAC PowerShell - New-AzureRmRoleAssignment - 스크린샷](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment4.png)

## <a name="remove-access"></a>액세스 권한 제거
사용자, 그룹 및 응용 프로그램의 액세스 권한을 제거하려면 다음을 사용합니다.:

    Remove-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription id>

![RBAC PowerShell - Remove-AzureRmRoleAssignment - 스크린샷](./media/role-based-access-control-manage-access-powershell/3-remove-azure-rm-role-assignment.png)

## <a name="create-a-custom-role"></a>사용자 지정 역할 만들기
사용자 지정 역할을 만들려면 ```New-AzureRmRoleDefinition``` 명령을 사용합니다. 역할을 구조화하는 방법에는 PSRoleDefinitionObject를 사용하거나 JSON 템플릿을 사용하는 두 가지 방법이 있습니다. 

## <a name="get-actions-for-a-resource-provider"></a>리소스 공급자에 대한 작업 가져오기
처음부터 사용자 지정 역할을 만드는 경우 리소스 공급자에서 가능한 모든 작업을 알고 있어야 합니다.
```Get-AzureRMProviderOperation``` 명령을 사용하여 이 정보를 가져옵니다.
예를 들어, 가상 머신에 사용 가능한 모든 작업을 확인하려는 경우 다음 명령을 사용합니다.

```
Get-AzureRMProviderOperation "Microsoft.Compute/virtualMachines/*" | FT OperationName, Operation , Description -AutoSize
```

### <a name="create-role-with-psroledefinitionobject"></a>PSRoleDefinitionObject를 사용하여 역할 만들기
PowerShell을 사용하여 사용자 지정 역할을 만들 때는 처음부터 시작하거나 [기본 제공 역할](role-based-access-built-in-roles.md) 중 하나를 출발점으로 사용할 수 있습니다. 이 섹션의 예제에서는 기본 제공 역할로 시작한 다음 추가 권한으로 사용자 지정합니다. 속성을 편집하여 원하는 *Actions*, *notActions* 또는 *scopes*를 추가한 다음 변경 내용을 새 역할로 저장합니다.

다음 예제에서는 *Virtual Machine Contributor* 역할로 시작한 후 이 역할을 사용하여 *Virtual Machine Operator*라는 사용자 지정 역할을 만듭니다. 새 역할은 *Microsoft.Compute*, *Microsoft.Storage* 및 *Microsoft.Network* 리소스 공급자의 모든 읽기 작업에 대한 액세스 권한을 부여하고 가상 머신을 시작, 다시 시작 및 모니터링할 수 있는 권한을 부여합니다. 두 구독 모두에서 사용자 지정 역할을 사용할 수 있습니다.

```
$role = Get-AzureRmRoleDefinition "Virtual Machine Contributor"
$role.Id = $null
$role.Name = "Virtual Machine Operator"
$role.Description = "Can monitor and restart virtual machines."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/*/read")
$role.Actions.Add("Microsoft.Network/*/read")
$role.Actions.Add("Microsoft.Compute/*/read")
$role.Actions.Add("Microsoft.Compute/virtualMachines/start/action")
$role.Actions.Add("Microsoft.Compute/virtualMachines/restart/action")
$role.Actions.Add("Microsoft.Authorization/*/read")
$role.Actions.Add("Microsoft.Resources/subscriptions/resourceGroups/read")
$role.Actions.Add("Microsoft.Insights/alertRules/*")
$role.Actions.Add("Microsoft.Support/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e")
$role.AssignableScopes.Add("/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624")
New-AzureRmRoleDefinition -Role $role
```

![RBAC PowerShell - Get-AzureRmRoleDefinition - 스크린샷](./media/role-based-access-control-manage-access-powershell/2-new-azurermroledefinition.png)

### <a name="create-role-with-json-template"></a>JSON 템플릿을 사용하여 역할 만들기
JSON 템플릿을 사용자 지정 역할의 원본 정의로 사용할 수 있습니다. 다음 예제에서는 저장소 및 계산 리소스에 대한 읽기 액세스, 지원 액세스를 허용하고 해당 역할을 두 개의 구독에 추가하는 사용자 지정 역할을 만듭니다. 다음 예제가 포함된 새 파일 `C:\CustomRoles\customrole1.json`을 만듭니다. 초기 역할 생성 시 새 ID가 자동 생성되므로 Id를 `null`로 설정해야 합니다. 

```
{
  "Name": "Custom Role 1",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```
구독에 역할을 추가하려면 다음 PowerShell 명령을 실행합니다.
```
New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="modify-a-custom-role"></a>사용자 지정 역할 수정
사용자 지정 역할을 만들 때와 유사하게 PSRoleDefinitionObject 또는 JSON 템플릿을 사용하여 기존 사용자 지정 역할을 수정할 수 있습니다.

### <a name="modify-role-with-psroledefinitionobject"></a>PSRoleDefinitionObject를 사용하여 역할 수정
사용자 지정 역할을 수정하려면 먼저 `Get-AzureRmRoleDefinition` 명령을 사용하여 역할 정의를 검색합니다. 그런 다음 역할 정의를 원하는 대로 변경합니다. 마지막으로 `Set-AzureRmRoleDefinition` 명령을 사용하여 수정한 역할 정의를 저장합니다.

다음 예제에서는 *Virtual Machine Operator* 사용자 지정 역할에 `Microsoft.Insights/diagnosticSettings/*` 작업을 추가합니다.

```
$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzureRmRoleDefinition -Role $role
```

![RBAC PowerShell - Set-AzureRmRoleDefinition - 스크린샷](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-1.png)

다음 예제에서는 *Virtual Machine Operator* 사용자 지정 역할의 할당 가능한 범위에 Azure 구독을 추가합니다.

```
Get-AzureRmSubscription - SubscriptionName Production3

$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/34370e90-ac4a-4bf9-821f-85eeedead1a2")
Set-AzureRmRoleDefinition -Role $role
```

![RBAC PowerShell - Set-AzureRmRoleDefinition - 스크린샷](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-2.png)

### <a name="modify-role-with-json-template"></a>JSON 템플릿을 사용하여 역할 수정
이전 JSON 템플릿을 통해 기존 사용자 지정 역할을 손쉽게 수정하여 작업을 추가 또는 제거할 수 있습니다. 다음 예에 표시된 것처럼 JSON 템플릿을 업데이트하고 네트워킹에 대한 읽기 작업을 추가합니다. 템플릿에 나열된 정의는 기존 정의에 점증적으로 적용되지 않습니다. 즉, 템플릿에 지정한 것과 똑같이 역할이 표시됩니다. 또한 Id 필드를 역할의 ID로 업데이트해야 합니다. 이 값을 잘 모르는 경우 `Get-AzureRmRoleDefinition` cmdlet을 사용하여 이 정보를 가져올 수 있습니다.

```
{
  "Name": "Custom Role 1",
  "Id": "acce7ded-2559-449d-bcd5-e9604e50bad1",
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

기존 역할을 업데이트하려면 다음 PowerShell 명령을 실행합니다.
```
Set-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="delete-a-custom-role"></a>사용자 지정 역할 삭제
사용자 지정 역할을 삭제하려면 `Remove-AzureRmRoleDefinition` 명령을 사용합니다.

다음 예제에서는 *Virtual Machine Operator* 사용자 지정 역할을 제거합니다.

```
Get-AzureRmRoleDefinition "Virtual Machine Operator"

Get-AzureRmRoleDefinition "Virtual Machine Operator" | Remove-AzureRmRoleDefinition
```

![RBAC PowerShell - Remove-AzureRmRoleDefinition - 스크린샷](./media/role-based-access-control-manage-access-powershell/4-remove-azurermroledefinition.png)

## <a name="list-custom-roles"></a>사용자 지정 역할 나열
범위에서 할당할 수 있는 역할을 나열하려면 `Get-AzureRmRoleDefinition` 명령을 사용합니다.

다음 예제에서는 선택한 구독에 할당할 수 있는 모든 역할을 나열합니다.

```
Get-AzureRmRoleDefinition | FT Name, IsCustom
```

![RBAC PowerShell - Get-AzureRmRoleDefinition - 스크린샷](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition-1.png)

다음 예제에서는 *Virtual Machine Operator* 사용자 지정 역할을 *Production4* 구독에서 사용할 수 없습니다. 이 구독이 해당 역할의 **AssignableScopes**에 없기 때문입니다.

![RBAC PowerShell - Get-AzureRmRoleDefinition - 스크린샷](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition2.png)

## <a name="see-also"></a>참고 항목
* [Azure Resource Manager로 Azure PowerShell 사용](../powershell-azure-resource-manager.md)
  [!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

