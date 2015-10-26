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
	ms.date="10/12/2015"
	ms.author="inhenk"/>

# Azure PowerShell을 사용하여 RBAC(역할 기반 액세스 제어) 관리
<!-- Azure Selector -->
> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)

## RBAC 역할 나열
### 사용 가능한 모든 역할 나열
할당할 수 있는 RBAC 역할을 나열하고 액세스 권한을 부여하는 작업을 검사하려면 다음을 사용합니다.

		Get-AzureRoleDefinition

![](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### 역할의 작업 나열
특정 역할을 작업을 나열하려면 다음을 사용합니다.

    Get-AzureRoleDefinition <role name>

![](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## 액세스 권한 나열
### 선택한 구독에서 모든 역할 할당 나열
지정된 구독, 리소스 또는 리소스 그룹에서 적용되는 RBAC 액세스 할당을 나열하려면 다음을 사용합니다.

    Get-AzureRoleAssignment

###	리소스 그룹에 적용되는 역할 할당 나열
리소스 그룹에 대한 액세스 할당을 나열하려면 다음을 사용합니다.

    Get-AzureRoleAssignment -ResourceGroupName <resource group name>

![](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### 사용자 그룹에 할당된 역할을 비롯해 사용자에 대한 역할 할당 나열
지정된 사용자에 대한 액세스 할당 및 사용자가 구성원인 그룹에 대한 액세스 할당을 나열하려면 다음을 사용합니다.

    Get-AzureRoleAssignment -ExpandPrincipalGroups

![](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### 클래식 서비스 관리자 및 공동 관리자 역할 할당 나열
클래식 구독 관리자 및 공동 관리자에 대한 액세스 할당을 나열하려면 다음을 사용합니다.

    Get-AzureRoleAssignment -IncludeClassicAdministrators

## 액세스 권한 부여
### 개체 ID 검색
다음 명령 시퀀스를 사용하려면 먼저 개체 ID를 찾아야 합니다. 사용하는 구독 ID를 이미 알고 있다고 가정하지만, 그렇지 않은 경우 MSDN에서 [Get-AzureSubscription](https://msdn.microsoft.com/library/dn495302.aspx)을 참조하세요.

#### Azure AD 그룹에 대한 개체 ID 찾기
Azure AD 그룹에 대한 개체 ID를 가져오려면 다음을 사용합니다.

    Get-AzureADGroup -SearchString <group name in quotes>

#### Azure AD 서비스 보안 주체에 대한 개체 ID 찾기
Azure AD 서비스 보안 주체에 대한 개체 ID를 가져오려면 다음을 사용합니다. Get AzureADServicePrincipal SearchString <service name in quotes>

### 구독 범위에서 그룹에 역할 할당
구독 범위에서 그룹에 액세스 권한을 부여하려면 다음을 사용합니다.

    New-AzureRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -Scope <scope such as subscription/subscription id>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment1.png)

### 구독 범위에서 응용 프로그램에 역할 할당
구독 범위에서 응용 프로그램에 액세스 권한을 부여하려면 다음을 사용합니다.

    New-AzureRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -Scope <scope such as subscription/subscription id>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment2.png)

### 리소스 그룹 범위에서 사용자에 역할 할당
리소스 그룹 범위에서 사용자에 액세스 권한을 부여하려면 다음을 사용합니다.

    New-AzureRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment3.png)

### 리소스 범위에서 그룹에 역할 할당
리소스 범위에서 그룹에 액세스 권한을 부여하려면 다음을 사용합니다.

    New-AzureRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment4.png)

## 액세스 권한 제거
사용자, 그룹 및 응용 프로그램의 액세스 권한을 제거하려면 다음을 사용합니다.:

    Remove-AzureRoleAssignment -ObjId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription/subscription id>

![](./media/role-based-access-control-manage-access-powershell/3-remove-azure-rm-role-assignment.png)

## RBAC 항목
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=Oct15_HO3-->