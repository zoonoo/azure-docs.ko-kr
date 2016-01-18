<properties
	pageTitle="Azure PowerShell을 사용하여 RBAC(역할 기반 액세스 제어) 관리 | Microsoft Azure"
	description="Azure PowerShell에서 역할을 나열하고, 역할을 할당하고, 역할 할당을 제거하는 등 RBAC를 관리하는 방법입니다."
	services="active-directory"
	documentationCenter=""
	authors="IHenkel"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="01/04/2016"
	ms.author="inhenk"/>

# Azure PowerShell을 사용하여 RBAC(역할 기반 액세스 제어) 관리
> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
- [REST API](role-based-access-control-manage-access-rest.md)

## RBAC 역할 나열
### 사용 가능한 모든 역할 나열
할당할 수 있는 RBAC 역할을 나열하고 액세스 권한을 부여하는 작업을 검사하려면 다음을 사용합니다.

		Get-AzureRmRoleDefinition

![](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### 역할의 작업 나열
특정 역할을 작업을 나열하려면 다음을 사용합니다.

    Get-AzureRmRoleDefinition <role name>

![](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## 액세스 권한 나열
### 선택한 구독에서 모든 역할 할당 나열
지정된 구독, 리소스 또는 리소스 그룹에서 적용되는 RBAC 액세스 할당을 나열하려면 다음을 사용합니다.

    Get-AzureRmRoleAssignment

###	리소스 그룹에 적용되는 역할 할당 나열
리소스 그룹에 대한 액세스 할당을 나열하려면 다음을 사용합니다.

    Get-AzureRmRoleAssignment -ResourceGroupName <resource group name>

![](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### 사용자 그룹에 할당된 역할을 비롯해 사용자에 대한 역할 할당 나열
지정된 사용자에 대한 액세스 할당 및 사용자가 구성원인 그룹에 대한 액세스 할당을 나열하려면 다음을 사용합니다.

    Get-AzureRmRoleAssignment -ExpandPrincipalGroups

![](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### 클래식 서비스 관리자 및 공동 관리자 역할 할당 나열
클래식 구독 관리자 및 공동 관리자에 대한 액세스 할당을 나열하려면 다음을 사용합니다.

    Get-AzureRmRoleAssignment -IncludeClassicAdministrators

## 액세스 권한 부여
### 개체 ID 검색
다음 명령 시퀀스를 사용하려면 먼저 개체 ID를 찾아야 합니다. 사용하는 구독 ID를 이미 알고 있다고 가정하지만, 그렇지 않은 경우 MSDN에서 [Get-AzureSubscription](https://msdn.microsoft.com/library/dn495302.aspx)을 참조하세요.

#### Azure AD 그룹에 대한 개체 ID 찾기
Azure AD 그룹에 대한 개체 ID를 가져오려면 다음을 사용합니다.

    Get-AzureRmADGroup -SearchString <group name in quotes>

#### Azure AD 서비스 보안 주체에 대한 개체 ID 찾기
Azure AD 서비스 주체에 대한 개체 ID를 가져오려면 Get-AzureRmADServicePrincipal -SearchString을 사용합니다. <service name in quotes>

### 구독 범위에서 그룹에 역할 할당
구독 범위에서 그룹에 액세스 권한을 부여하려면 다음을 사용합니다.

    New-AzureRmRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -Scope <scope such as subscription/subscription id>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment1.png)

### 구독 범위에서 응용 프로그램에 역할 할당
구독 범위에서 응용 프로그램에 액세스 권한을 부여하려면 다음을 사용합니다.

    New-AzureRmRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -Scope <scope such as subscription/subscription id>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment2.png)

### 리소스 그룹 범위에서 사용자에 역할 할당
리소스 그룹 범위에서 사용자에 액세스 권한을 부여하려면 다음을 사용합니다.

    New-AzureRmRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment3.png)

### 리소스 범위에서 그룹에 역할 할당
리소스 범위에서 그룹에 액세스 권한을 부여하려면 다음을 사용합니다.

    New-AzureRmRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment4.png)

## 액세스 권한 제거
사용자, 그룹 및 응용 프로그램의 액세스 권한을 제거하려면 다음을 사용합니다.:

    Remove-AzureRmRoleAssignment -ObjId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription/subscription id>

![](./media/role-based-access-control-manage-access-powershell/3-remove-azure-rm-role-assignment.png)

## 사용자 지정 역할 만들기
사용자 지정 역할을 만들려면 `New-AzureRmRoleDefinition` 명령을 사용합니다.

다음 예제에서는 *Microsoft.Compute*, *Microsoft.Storage* 및 *Microsoft.Network* 리소스 공급자의 모든 읽기 작업에 대한 액세스 권한을 부여하고, 가상 컴퓨터를 시작, 다시 시작 및 모니터링할 수 있는 권한을 부여하는 *Virtual Machine Operator*라는 사용자 지정 역할을 만듭니다. 두 구독 모두에서 사용자 지정 역할을 사용할 수 있습니다.

![](./media/role-based-access-control-manage-access-powershell/2-new-azurermroledefinition.png)

## 사용자 지정 역할 수정
사용자 지정 역할을 수정하려면 먼저 `Get-AzureRmRoleDefinition` 명령을 사용하여 역할 정의를 검색합니다. 그런 다음 역할 정의를 원하는 대로 변경합니다. 마지막으로 `Set-AzureRmRoleDefinition` 명령을 사용하여 수정한 역할 정의를 저장합니다.

다음 예제에서는 *Virtual Machine Operator* 사용자 지정 역할에 `Microsoft.Insights/diagnosticSettings/*` 작업을 추가합니다.

![](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-1.png)

다음 예제에서는 Virtual Machine Operator 사용자 지정 역할의 할당 가능한 범위에 Azure 구독을 추가합니다.

![](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-2.png)

## 사용자 지정 역할 삭제

사용자 지정 역할을 삭제하려면 `Remove-AzureRmRoleDefinition` 명령을 사용합니다.

다음 예제에서는 *Virtual Machine Operator* 사용자 지정 역할을 제거합니다.

![](./media/role-based-access-control-manage-access-powershell/4-remove-azurermroledefinition.png)

## 사용자 지정 역할 나열
범위에서 할당할 수 있는 역할을 나열하려면 `Get-AzureRmRoleDefinition` 명령을 사용합니다.

다음 예제에서는 선택한 구독에 할당할 수 있는 모든 역할을 나열합니다.

![](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition-1.png)

다음 예제에서는 *Virtual Machine Operator* 사용자 지정 역할을 *Production4* 구독에서 사용할 수 없습니다. 이 구독이 해당 역할의 **AssignableScopes**에 없기 때문입니다.

![](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition2.png)

## RBAC 항목
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=AcomDC_0107_2016-->