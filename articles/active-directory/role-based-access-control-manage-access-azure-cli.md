<properties
	pageTitle="Azure CLI를 사용하여 RBAC(역할 기반 액세스 제어) 관리 | Microsoft Azure"
	description="Azure 명령줄 인터페이스에서 역할 및 역할 작업을 나열하고, 구독 및 응용 프로그램 범위에 역할을 할당하여 RBAC(역할 기반 액세스)를 관리하는 방법을 알아봅니다."
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

# Azure CLI(명령줄 인터페이스)를 사용하여 RBAC(역할 기반 액세스 제어) 관리
<!-- Azure Selector -->
> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)

## RBAC 역할 나열
###	사용 가능한 모든 역할 나열
사용 가능한 역할을 모두 사용하려면 다음을 사용합니다.

		azure role list

다음 예제에서는 *사용 가능한 모든 역할*의 목록을 보여 줍니다.

![](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-list.png)

###	역할의 작업 나열
역할의 작업을 나열하려면 다음을 사용합니다.

    azure role show <role in quotes>

다음 예제에서는 *참가자* 및 *가상 컴퓨터 참가자* 역할의 작업을 보여 줍니다.

![](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-show.png)

##	액세스 권한 나열
###	리소스 그룹에 적용되는 역할 할당 나열
리소스 그룹에 적용되는 역할 할당을 나열하려면 다음을 사용합니다.

    azure role assignment list --resource-group <resource group name>

다음 예제에서는 *pharma-sales-projecforcast* 그룹에 적용되는 역할 할당을 보여 줍니다.

![](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-1.png)

###	사용자 그룹에 할당된 역할을 비롯해 사용자에 대한 역할 할당 나열

다음 예제에서는 *pharma-sales-projecforcast* 그룹에 적용되는 역할 할당을 보여 줍니다.

![](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-2.png)

##	액세스 권한 부여
할당할 역할을 식별한 후 액세스 권한을 부여하려면 다음을 사용합니다.

    azure role assignment create

###	구독 범위에서 그룹에 역할 할당
구독 범위에서 그룹에 역할을 할당하려면 다음을 사용합니다.

   azure role assignment create --objId <그룹의 개체 ID> --role <name of role> --scope <구독/구독 ID>

다음 예제에서는 *구독* 범위에서 *Christine Koch 팀*에 *읽기* 역할을 할당합니다.

![](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-1.png)

###	구독 범위에서 응용 프로그램에 역할 할당
구독 범위에서 응용 프로그램에 역할을 할당하려면 다음을 사용합니다.

    azure role assignment create --objId  <applications's object id> --role <name of role> --scope <subscription/subscription id>

다음 예제에서는 선택한 구독에서 *Azure AD* 응용 프로그램에 *참가자* 역할을 부여합니다.

 ![](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-2.png)

###	리소스 그룹 범위에서 사용자에 역할 할당
리소스 그룹 범위에서 사용자에 역할을 할당하려면 다음을 사용합니다.

    azure role assignment create --signInName  <user's email address> --roleName <name of role in quotes> --resourceGroup <resource group name>

다음 예제에서는 *Pharma-Sales-ProjectForcast* 리소스 그룹 범위에서 사용자 **samert@aaddemo.com*에 *가상 컴퓨터 참가자* 역할을 부여합니다.

![](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-3.png)

###	리소스 범위에서 그룹에 역할 할당
리소스 범위에서 그룹에 역할을 할당하려면 다음을 사용합니다.

    azure role assignment create --objId  <group id> --roleName <name of role in quotes> --resource-name <resource group name> --resource-type <resource group type> --parent <resource group parent> --resource-group <resource group>

다음 예제에서는 *서브넷*에서 *Azure AD* 그룹에 *가상 컴퓨터 참가자* 역할을 부여합니다.

![](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-4.png)

##	액세스 권한 제거
역할 할당을 제거하려면 다음을 사용합니다.

    azure role assignment delete --objId <object id to from which to remove role> --roleName <role name>

다음 예제에서는 *Pharma-Sales-ProjectForcast* 리소스 그룹의 **sammert@aaddemo.com*에서 *가상 컴퓨터 참가자* 역할 할당을 제거합니다. 그런 다음 구독의 그룹에서 역할 할당을 제거합니다.

![](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-assignment-delete.png)

## RBAC 항목
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=Oct15_HO3-->