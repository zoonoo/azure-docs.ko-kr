<properties
	pageTitle="Azure PowerShell을 사용하여 RBAC(역할 기반 액세스 제어) 관리 | Microsoft Azure"
	description="Azure PowerShell에서 역할을 나열하고, 역할을 할당하고, 역할 할당을 제거하는 등 RBAC를 관리하는 방법입니다."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="07/22/2016"
	ms.author="kgremban"/>

# Azure PowerShell을 사용하여 역할 기반 액세스 제어 관리

> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
- [REST API](role-based-access-control-manage-access-rest.md)


Azure 포털의 RBAC(역할 기반 액세스 제어) 및 Azure 리소스 관리 API를 사용하여 세밀한 수준에서 구독에 대한 액세스를 관리할 수 있습니다. 이 기능을 통해 특정 범위에서 Active Directory 사용자, 그룹 또는 서비스 사용자에게 일부 역할을 할당하여 액세스 권한을 부여할 수 있습니다.

PowerShell을 사용하여 RBAC를 관리하려면 다음 항목이 필요합니다.

- Azure PowerShell 버전 0.8.8 이상. 최신 버전을 설치하고 Azure 구독에 연결하려면 [Azure PowerShell 설치 및 구성하는 방법](../powershell-install-configure.md)을 참조하세요.

- Azure Resource Manager cmdlet. PowerShell에서 [Azure Resource Manager cmdlet](https://msdn.microsoft.com/library/mt125356.aspx)을 설치합니다.

## 역할 나열

### 사용 가능한 모든 역할 나열
할당할 수 있는 RBAC 역할을 나열하고 액세스 권한을 부여하는 작업을 검사하려면 `Get-AzureRmRoleDefinition`을 사용합니다.

```
Get-AzureRmRoleDefinition | FT Name, Description
```

![RBAC PowerShell - Get-AzureRmRoleDefinition - 스크린샷](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### 역할의 작업 나열
특정 역할을 작업을 나열하려면 `Get-AzureRmRoleDefinition <role name>`을 사용합니다.

```
Get-AzureRmRoleDefinition Contributor | FL Actions, NotActions

(Get-AzureRmRoleDefinition "Virtual Machine Contributor").Actions
```

![RBAC PowerShell - 특정 역할에 대한 Get-AzureRmRoleDefinition - 스크린샷](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## 액세스 권한이 있는 사용자 확인
RBAC 액세스 할당을 나열하려면 `Get-AzureRmRoleAssignment`를 사용합니다.

###	특정 범위의 역할 할당 나열
지정된 구독, 리소스 그룹 또는 리소스에 대한 모든 액세스 할당을 확인할 수 있습니다. 예를 들어 리소스 그룹에 대한 모든 활성 할당을 확인하려면 `Get-AzureRmRoleAssignment -ResourceGroupName <resource group name>`을 사용합니다.

```
Get-AzureRmRoleAssignment -ResourceGroupName Pharma-Sales-ProjectForcast | FL DisplayName, RoleDefinitionName, Scope
```

![RBAC PowerShell - 리소스 그룹에 대한 Get-AzureRmRoleAssignment - 스크린샷](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### 사용자에게 할당된 역할 나열
지정된 사용자에게 할당된 역할 및 사용자가 속한 그룹에 할당된 역할을 모두 나열하려면 `Get-AzureRmRoleAssignment -SignInName <User email> -ExpandPrincipalGroups`를 사용합니다.

```
Get-AzureRmRoleAssignment -SignInName sameert@aaddemo.com | FL DisplayName, RoleDefinitionName, Scope

Get-AzureRmRoleAssignment -SignInName sameert@aaddemo.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

![RBAC PowerShell - 사용자에 대한 Get-AzureRmRoleAssignment - 스크린샷](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### 클래식 서비스 관리자 및 공동 관리자 역할 할당 나열
클래식 구독 관리자 및 공동 관리자에 대한 액세스 할당을 나열하려면 다음을 사용합니다.

    Get-AzureRmRoleAssignment -IncludeClassicAdministrators

## 액세스 권한 부여
### 개체 ID 검색
역할을 할당하려면 개체(사용자, 그룹 또는 응용 프로그램)와 범위 둘 다를 식별해야 합니다.

구독 ID를 모르는 경우 Azure 포털의 **구독** 블레이드에서 확인할 수 있습니다. 구독 ID를 쿼리하는 방법을 알아보려면 MSDN에서 [Get-AzureSubscription](https://msdn.microsoft.com/library/dn495302.aspx)을 참조하세요.

Azure AD 그룹에 대한 개체 ID를 가져오려면 다음을 사용합니다.

    Get-AzureRmADGroup -SearchString <group name in quotes>

Azure AD 서비스 사용자 또는 응용 프로그램에 대한 개체 ID를 찾으려면 다음을 사용합니다.

    Get-AzureRmADServicePrincipal -SearchString <service name in quotes>

### 구독 범위에서 응용 프로그램에 역할 할당
구독 범위에서 응용 프로그램에 액세스 권한을 부여하려면 다음을 사용합니다.

    New-AzureRmRoleAssignment -ObjectId <application id> -RoleDefinitionName <role name> -Scope <subscription id>

![RBAC PowerShell - New-AzureRmRoleAssignment - 스크린샷](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment2.png)

### 리소스 그룹 범위에서 사용자에 역할 할당
리소스 그룹 범위에서 사용자에 액세스 권한을 부여하려면 다음을 사용합니다.

    New-AzureRmRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>

![RBAC PowerShell - New-AzureRmRoleAssignment - 스크린샷](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment3.png)

### 리소스 범위에서 그룹에 역할 할당
리소스 범위에서 그룹에 액세스 권한을 부여하려면 다음을 사용합니다.

    New-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>

![RBAC PowerShell - New-AzureRmRoleAssignment - 스크린샷](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment4.png)

## 액세스 권한 제거
사용자, 그룹 및 응용 프로그램의 액세스 권한을 제거하려면 다음을 사용합니다.:

    Remove-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription id>

![RBAC PowerShell - Remove-AzureRmRoleAssignment - 스크린샷](./media/role-based-access-control-manage-access-powershell/3-remove-azure-rm-role-assignment.png)

## 사용자 지정 역할 만들기
사용자 지정 역할을 만들려면 `New-AzureRmRoleDefinition` 명령을 사용합니다.

PowerShell을 사용하여 사용자 지정 역할을 만드는 경우 [기본 제공 역할](role-based-access-built-in-roles.md) 중 하나로 시작해야 합니다. 속성을 편집하여 원하는 *Actions*, *notActions* 또는 *scopes*를 추가한 다음 변경 내용을 새 역할로 저장합니다.

다음 예제에서는 *Virtual Machine Contributor* 역할로 시작한 후 이 역할을 사용하여 *Virtual Machine Operator*라는 사용자 지정 역할을 만듭니다. 새 역할은 *Microsoft.Compute*, *Microsoft.Storage* 및 *Microsoft.Network* 리소스 공급자의 모든 읽기 작업에 대한 액세스 권한을 부여하고 가상 컴퓨터를 시작, 다시 시작 및 모니터링할 수 있는 권한을 부여합니다. 두 구독 모두에서 사용자 지정 역할을 사용할 수 있습니다.

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

## 사용자 지정 역할 수정
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
$role.AssignableScopes.Add("/subscriptions/34370e90-ac4a-4bf9-821f-85eeedead1a2"
Set-AzureRmRoleDefinition -Role $role)
```

![RBAC PowerShell - Set-AzureRmRoleDefinition - 스크린샷](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-2.png)

## 사용자 지정 역할 삭제

사용자 지정 역할을 삭제하려면 `Remove-AzureRmRoleDefinition` 명령을 사용합니다.

다음 예제에서는 *Virtual Machine Operator* 사용자 지정 역할을 제거합니다.

```
Get-AzureRmRoleDefinition "Virtual Machine Operator"

Get-AzureRmRoleDefinition "Virtual Machine Operator" | Remove-AzureRmRoleDefinition
```

![RBAC PowerShell - Remove-AzureRmRoleDefinition - 스크린샷](./media/role-based-access-control-manage-access-powershell/4-remove-azurermroledefinition.png)

## 사용자 지정 역할 나열
범위에서 할당할 수 있는 역할을 나열하려면 `Get-AzureRmRoleDefinition` 명령을 사용합니다.

다음 예제에서는 선택한 구독에 할당할 수 있는 모든 역할을 나열합니다.

```
Get-AzureRmRoleDefinition | FT Name, IsCustom
```

![RBAC PowerShell - Get-AzureRmRoleDefinition - 스크린샷](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition-1.png)

다음 예제에서는 *Virtual Machine Operator* 사용자 지정 역할을 *Production4* 구독에서 사용할 수 없습니다. 이 구독이 해당 역할의 **AssignableScopes**에 없기 때문입니다.

![RBAC PowerShell - Get-AzureRmRoleDefinition - 스크린샷](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition2.png)

## 참고 항목
- [Azure 리소스 관리자로 Azure PowerShell 사용](../powershell-azure-resource-manager.md) [AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=AcomDC_0810_2016-->