<properties
	pageTitle="Azure Active Directory AD 역할 기반 액세스 제어 | Microsoft Azure"
	description="이 문서에서는 Azure 역할 기반 액세스 제어에 대해 설명합니다."
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

# Azure Active Directory 역할 기반 액세스 제어

## 역할 기반 액세스 제어
Azure 역할 기반 액세스 제어(RBAC)를 통해 Azure에 대한 세밀한 액세스 관리가 가능합니다. RBAC를 사용하여 devops 팀 내에서 업무를 분리하고 사용자에게 해당 작업을 수행하는 데 필요한 만큼의 권한만 부여할 수 있습니다.

### Azure에서 액세스 관리의 기본 사항
각 Azure 구독은 Azure Active Directory를 기반으로 합니다. Azure 관리 포털, Azure 명령줄 도구 및 Azure 관리 API를 사용하여 Azure 구독에서 리소스를 관리할 권한을 사용자, 그룹 및 해당 디렉터리의 응용 프로그램에만 부여할 수 있습니다.

올바른 범위에서 사용자, 그룹 및 응용 프로그램에 적절한 RBAC 역할을 할당하여 액세스 권한을 부여합니다. 전체 구독에 대한 액세스 권한을 부여하려면 구독 범위에서 역할을 할당합니다. 구독 내의 특정 리소스 그룹에 대한 액세스 권한을 부여하려면 리소스 그룹 범위에서 역할을 할당합니다. 웹 사이트, 가상 컴퓨터 및 서브넷과 같은 특정 리소스에도 역할을 할당하여 리소스에 대한 액세스 권한만 부여할 수 있습니다.

![](./media/role-based-access-control-configure/overview.png)

사용자, 그룹 및 응용 프로그램에 할당하는 RBAC 역할에 따라 해당 범위 내에서 사용자(또는 응용 프로그램)이 관리할 수 있는 리소스가 결정됩니다.

### Azure RBAC 기본 제공 역할
Azure RBAC에는 모든 리소스 유형에 적용되는 3가지 기본 역할(소유자, 참여자 및 읽기 권한자)이 있습니다. 소유자는 액세스 권한을 다른 사용자에게 위임할 수 있는 권한을 포함하여 모든 리소스에 대한 전체 액세스 권한을 보유합니다. 참여자는 모든 유형의 Azure 리소스를 만들고 관리할 수 있으나 다른 사용자에게 액세스 권한을 부여할 수 없습니다. 읽기 권한자는 기존 Azure 리소스만 볼 수 있습니다. Azure의 나머지 RBAC 역할은 특정 Azure 리소스의 관리를 허용합니다. 예를 들어, 가상 컴퓨터 참여자 역할은 가상 컴퓨터 만들기 및 관리를 허용하지만 가상 컴퓨터가 연결되는 가상 네트워크 또는 서브넷의 관리는 허용하지 않습니다.

[RBAC 기본 제공 역할](role-based-access-built-in-roles.md)은 Azure에서 사용할 수 있는 기본 제공 RBAC 역할을 나열합니다. 각 역할에 대해 기본 제공 역할로 액세스 권한이 부여되는 작업을 지정합니다.

### Azure 리소스 계층 구조 및 액세스 상속
Azure에서 각 구독은 단 하나의 디렉터리에 속하며 각 리소스 그룹은 단 하나의 구독에만 속합니다. 그리고 각 리소스는 단 하나의 리소스 그룹에만 속합니다. 부모 범위에서 부여되는 액세스 권한은 자식 범위에서 상속됩니다. 구독 범위에서 Azure AD 그룹에 읽기 권한자 역할을 부여하는 경우 해당 그룹의 멤버는 구독에서 모든 리소스 그룹 및 모든 리소스를 볼 수 있게 됩니다. 리소스 그룹 범위에서 응용 프로그램에 참여자 역할을 부여하는 경우 해당 리소스 그룹에 있는 모든 유형의 리소스를 관리할 수 있지만 구독에 있는 다른 리소스 그룹은 관리할 수 없습니다.

### Azure RBAC 및 클래식 구독 관리자와 공동 관리자
클래식 구독 관리자와 공동 관리자는 Azure 구독에 대한 모든 권한을 보유합니다. 이들은 클래식 포털(https://manage.windowsazure.com), Azure 서비스 관리자 API, 새로운 관리 포털(https://portal.azure.com), 새로운 Azure 리소스 관리자 API를 사용하여 리소스를 관리할 수 있습니다. RBAC 모델에서는 클래식 관리자에게 구독 범위에서 소유자 역할이 할당됩니다.

세부적인 권한 부여 모델(Azure RBAC)은 새로운 관리 포털(https://portal.azure.com) 및 Azure 리소스 관리자 API에서만 지원됩니다. RBAC 역할(구독/리소스 그룹/리소스 범위)이 할당되는 사용자 및 응용 프로그램에서는 클래식 관리 포털(http://manage.windowsazure.com) 및 Azure 서비스 관리 API를 사용할 수 없습니다.

### 관리를 위한 권한 부여와 데이터 작업 비교
세부적인 권한 부여 모델(Azure RBAC)은 Azure 포털 및 Azure 리소스 관리자 API에서 수행하는 Azure 리소스 관리 작업에 대해서만 지원됩니다. RBAC를 통해 Azure 리소스의 모든 데이터 수준 작업에 대한 권한을 부여할 수 있는 것은 아닙니다. 예를 들어 저장소 계정 만들기/읽기/업데이트/삭제는 RBAC를 통해 제어할 수 있지만 저장소 계정 내 Blob 또는 테이블 만들기/읽기/업데이트/삭제는 아직은 RBAC를 통해 제어할 수 없습니다. 마찬가지로 SQL DB 만들기/읽기/업데이트/삭제는 RBAC를 통해 제어할 수 있지만 DB 내 SQL 테이블 만들기/읽기/업데이트/삭제는 아직은 RBAC를 통해 제어할 수 없습니다.

## Azure 관리 포털을 사용하여 액세스 관리
### 액세스 보기
리소스 그룹 블레이드의 필수 섹션에서 액세스 설정을 선택합니다. **사용자** 블레이드에 해당 리소스 그룹에 대한 액세스가 부여된 모든 사용자, 그룹 및 응용 프로그램이 나열됩니다. 액세스는 리소스 그룹에 할당되거나 부모 구독에 할당된 내용에서 상속됩니다.

![](./media/role-based-access-control-configure/view-access.png)

> [AZURE.NOTE]클래식 구독 관리자와 공동 관리자는 새 RBAC 모델에서 구독의 유효한 소유자입니다.

### 액세스 추가
1. **사용자** 블레이드에서 **추가** 아이콘을 클릭합니다. ![](./media/role-based-access-control-configure/grant-access1.png)
2. 할당하고자 하는 역할을 선택합니다.
3. 액세스 권한을 부여할 사용자, 그룹 또는 응용 프로그램을 검색 및 선택합니다.
4. 표시 이름, 전자 메일 주소 및 개체 식별자를 사용하여 디렉터리에서 사용자, 그룹 및 응용 프로그램을 검색합니다.![](./media/role-based-access-control-configure/grant-access2.png)

### 액세스 제거
1. **사용자** 블레이드에서 제거할 역할 할당을 선택합니다.
2. 할당 세부 정보 블레이드에서 **제거** 아이콘을 클릭합니다.
3. **예**를 클릭하여 제거를 확인합니다.

![](./media/role-based-access-control-configure/remove-access1.png)


> [AZURE.NOTE]자식 범위에서 상속된 할당을 제거할 수 있습니다. 부모 범위로 이동하여 이러한 할당을 제거합니다.


![](./media/role-based-access-control-configure/remove-access2.png)

## Azure PowerShell을 사용하여 액세스 관리
Azure PowerShell 도구에서 Azure RBAC 명령을 사용하여 액세스를 관리할 수 있습니다.

-	할당할 수 있는 RBAC 역할을 나열하고 액세스 권한을 부여하는 작업을 검사하려면 `Get-AzureRoleDefinition`을 사용합니다.

-	지정된 구독, 리소스 그룹 또는 리소스에서 적용되는 RBAC 액세스 할당을 나열하려면 `Get-AzureRoleAssignment`를 사용합니다. 지정된 사용자에 대한 액세스 할당 및 사용자가 멤버인 그룹에 대한 액세스 할당을 나열하려면 `ExpandPrincipalGroups` 매개 변수를 사용합니다. 클래식 구독 관리자와 공동 관리자를 나열하려면 `IncludeClassicAdministrators` 매개 변수를 사용합니다.

-	사용자, 그룹 및 응용 프로그램에 액세스 권한을 부여하려면 `New-AzureRoleAssignment`를 사용합니다.

-	액세스 권한을 제거하려면 `Remove-AzureRoleAssignment`를 사용합니다.

Azure PowerShell을 사용하여 액세스를 관리하는 자세한 예를 보려면 [Azure PowerShell을 사용하여 액세스 관리](role-based-access-control-manage-access-powershell.md)를 참조하세요.

## Azure 명령줄 인터페이스를 사용하여 액세스 관리
Azure 명령줄 인터페이스에서 Azure RBAC 명령을 사용하여 액세스를 관리할 수 있습니다.

-	할당에 사용할 수 있는 RBAC 역할을 나열하려면 `azure role list`를 사용합니다. azure 역할 보기를 사용하여 액세스 권한을 부여하는 작업을 검사합니다.

-	지정된 구독, 리소스 그룹 또는 리소스에서 적용되는 RBAC 액세스 할당을 나열하려면 `azure role assignment list`를 사용합니다. 지정된 사용자에 대한 액세스 할당 및 사용자가 멤버인 그룹에 대한 액세스 할당을 나열하려면 `expandPrincipalGroups` 옵션을 사용합니다. 클래식 구독 관리자와 공동 관리자를 나열하려면 `includeClassicAdministrators` 매개 변수를 사용합니다.

-	사용자, 그룹 및 응용 프로그램에 액세스 권한을 부여하려면 `azure role assignment create`를 사용합니다.

-	액세스 권한을 제거하려면 `azure role assignment delete`를 사용합니다.

Azure CLI를 사용하여 액세스를 관리하는 자세한 예를 보려면 [Azure CLI를 사용하여 액세스 관리](role-based-access-control-manage-access-azure-cli.md)를 참조하세요.

## 액세스 변경 기록 보고서 사용
Azure 구독에서 발생하는 모든 액세스 변경 내용은 Azure 이벤트에 기록됩니다.

### Azure PowerShell에서 보고서 만들기
Azure 구독 내의 어떤 범위에서 어떤 액세스 종류가 부여/해지되었는지에 대한 보고서를 만들려면 다음 PowerShell 명령을 사용합니다.

    Get-AzureAuthorizationChangeLog

### Azure CLI에서 보고서 만들기
Azure 구독 내의 어떤 범위에서 어떤 액세스 종류가 부여/해지되었는지에 대한 보고서를 만들려면 Azure 명령줄 인터페이스(CLI) 명령을 사용합니다.

    azure authorization changelog

> [AZURE.NOTE]지난 90일(15일 단위) 동안 액세스 변경 내용을 쿼리할 수 있습니다.

다음 예에서는 지난 7일 동안 구독에서 발생한 모든 액세스 변경 내용을 나열합니다.

![](./media/role-based-access-control-configure/access-change-history.png)

### 스프레드시트로 액세스 변경 내용 내보내기
액세스 변경 내용을 스프레드시트로 내보내면 편리하게 검토할 수 있습니다.

![](./media/role-based-access-control-configure/change-history-spreadsheet.png)

<!---HONumber=Oct15_HO3-->