---
title: "Azure CLI를 사용하여 RBAC(역할 기반 액세스 제어) 관리 | Microsoft Docs"
description: "Azure 명령줄 인터페이스에서 역할 및 역할 작업을 나열하고, 구독 및 응용 프로그램 범위에 역할을 할당하여 RBAC(역할 기반 액세스 제어)를 관리하는 방법을 알아봅니다."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: d9ef14d1d83d98de5c1d95b237a2e06ea2506766
ms.lasthandoff: 03/04/2017


---
# <a name="manage-role-based-access-control-with-the-azure-command-line-interface"></a>Azure 명령줄 인터페이스를 사용하여 역할 기반 액세스 제어 관리
> [!div class="op_single_selector"]
> * [PowerShell](role-based-access-control-manage-access-powershell.md)
> * [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
> * [REST API](role-based-access-control-manage-access-rest.md)


Azure 포털의 RBAC(역할 기반 액세스 제어) 및 Azure Resource Manager API를 사용하여 세밀한 수준에서 구독과 리소스에 대한 액세스를 관리할 수 있습니다. 이 기능을 통해 특정 범위에서 Active Directory 사용자, 그룹 또는 서비스 사용자에게 일부 역할을 할당하여 액세스 권한을 부여할 수 있습니다.

Azure CLI(명령줄 인터페이스)를 사용하여 RBAC를 관리하려면 다음 항목이 필요합니다.

* Azure CLI 버전 0.8.8 이상을 사용하세요. 최신 버전을 설치하고 Azure 구독에 연결하려면 [Azure CLI 설치 및 구성 방법](../xplat-cli-install.md)을 참조하세요.
* Azure CLI에서 Azure Resource Manager입니다. 자세한 내용을 보려면 [리소스 관리자에서 Azure CLI 사용](../xplat-cli-azure-resource-manager.md) 으로 이동합니다.

## <a name="list-roles"></a>역할 나열
### <a name="list-all-available-roles"></a>사용 가능한 모든 역할 나열
사용 가능한 역할을 모두 사용하려면 다음을 사용합니다.

        azure role list

다음 예제에서는 *사용 가능한 모든 역할*의 목록을 보여 줍니다.

```
azure role list --json | jq '.[] | {"roleName":.properties.roleName, "description":.properties.description}'
```

![RBAC Azure 명령줄 - azure role list - 스크린샷](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-list.png)

### <a name="list-actions-of-a-role"></a>역할의 작업 나열
역할의 작업을 나열하려면 다음을 사용합니다.

    azure role show "<role name>"

다음 예제에서는 *참가자* 및 *가상 컴퓨터 참가자* 역할의 작업을 보여줍니다.

```
azure role show "contributor" --json | jq '.[] | {"Actions":.properties.permissions[0].actions,"NotActions":properties.permissions[0].notActions}'

azure role show "virtual machine contributor" --json | jq '.[] | .properties.permissions[0].actions'
```

![RBAC Azure 명령줄 - azure role show - 스크린샷](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-show.png)

## <a name="list-access"></a>액세스 권한 나열
### <a name="list-role-assignments-effective-on-a-resource-group"></a>리소스 그룹에 적용되는 역할 할당 나열
리소스 그룹에 존재하는 역할 할당을 나열하려면 다음을 사용합니다.

    azure role assignment list --resource-group <resource group name>

다음 예제에서는 *pharma-sales-projecforcast* 그룹에 있는 역할 할당을 보여 줍니다.

```
azure role assignment list --resource-group pharma-sales-projecforcast --json | jq '.[] | {"DisplayName":.properties.aADObject.displayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'
```

![RBAC Azure 명령줄 - 그룹별 azure role assignment list - 스크린샷](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-1.png)

### <a name="list-role-assignments-for-a-user"></a>사용자에 대한 역할 할당 목록
특정 사용자에 대한 역할 할당 및 사용자 그룹에 할당된 할당을 나열하려면 다음을 사용합니다.

    azure role assignment list --signInName <user email>

또한 명령을 수정하여 그룹에서 상속하는 역할 할당을 확인할 수도 있습니다.

    azure role assignment list --expandPrincipalGroups --signInName <user email>

다음 예제에서는 사용자 *sameert@aaddemo.com* 에 부여된 역할 할당을 보여 줍니다. 여기에는 사용자에게 직접 할당된 역할 및 그룹에서 상속된 역할이 포함됩니다.

```
azure role assignment list --signInName sameert@aaddemo.com --json | jq '.[] | {"DisplayName":.properties.aADObject.DisplayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'

azure role assignment list --expandPrincipalGroups --signInName sameert@aaddemo.com --json | jq '.[] | {"DisplayName":.properties.aADObject.DisplayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'
```

![RBAC Azure 명령줄 - 사용자별 azure role assignment list - 스크린샷](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-2.png)

## <a name="grant-access"></a>액세스 권한 부여
할당할 역할을 식별한 후 액세스 권한을 부여하려면 다음을 사용합니다.

    azure role assignment create

### <a name="assign-a-role-to-group-at-the-subscription-scope"></a>구독 범위에서 그룹에 역할 할당
구독 범위에서 그룹에 역할을 할당하려면 다음을 사용합니다.

    azure role assignment create --objectId  <group object id> --roleName <name of role> --subscription <subscription> --scope <subscription/subscription id>

다음 예제에서는 *구독* 범위에서 *Christine Koch 팀*에 *독자* 역할을 할당합니다.

![RBAC Azure 명령줄 - 그룹별 azure role assignment create - 스크린샷](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-1.png)

### <a name="assign-a-role-to-an-application-at-the-subscription-scope"></a>구독 범위에서 응용 프로그램에 역할 할당
구독 범위에서 응용 프로그램에 역할을 할당하려면 다음을 사용합니다.

    azure role assignment create --objectId  <applications object id> --roleName <name of role> --subscription <subscription> --scope <subscription/subscription id>

다음 예제에서는 선택한 구독에서 *Azure AD* 응용 프로그램에 *참가자* 역할을 부여합니다.

 ![RBAC Azure 명령줄 - 응용 프로그램별 azure role assignment create - 스크린샷](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-2.png)

### <a name="assign-a-role-to-a-user-at-the-resource-group-scope"></a>리소스 그룹 범위에서 사용자에 역할 할당
리소스 그룹 범위에서 사용자에 역할을 할당하려면 다음을 사용합니다.

    azure role assignment create --signInName  <user email address> --roleName "<name of role>" --resourceGroup <resource group name>

다음 예제에서는 *Pharma-Sales-ProjectForcast* 리소스 그룹 범위에서 사용자 *samert@aaddemo.com*에 *가상 컴퓨터 참가자* 역할을 부여합니다.

![RBAC Azure 명령줄 - 사용자별 azure role assignment create - 스크린샷](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-3.png)

### <a name="assign-a-role-to-a-group-at-the-resource-scope"></a>리소스 범위에서 그룹에 역할 할당
리소스 범위에서 그룹에 역할을 할당하려면 다음을 사용합니다.

    azure role assignment create --objectId <group id> --role "<name of role>" --resource-name <resource group name> --resource-type <resource group type> --parent <resource group parent> --resource-group <resource group>

다음 예제에서는 *서브넷*에서 *Azure AD* 그룹에 *가상 컴퓨터 참가자* 역할을 부여합니다.

![RBAC Azure 명령줄 - 그룹별 azure role assignment create - 스크린샷](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-4.png)

## <a name="remove-access"></a>액세스 권한 제거
역할 할당을 제거하려면 다음을 사용합니다.

    azure role assignment delete --objectId <object id to from which to remove role> --roleName "<role name>"

다음 예제에서는 *Pharma-Sales-ProjectForcast* 리소스 그룹의 사용자 *sammert@aaddemo.com*에서 *가상 컴퓨터 참가자* 역할 할당을 제거합니다.
그런 다음 구독의 그룹에서 역할 할당을 제거합니다.

![RBAC Azure 명령줄 - azure role assignment delete - 스크린샷](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-assignment-delete.png)

## <a name="create-a-custom-role"></a>사용자 지정 역할 만들기
사용자 지정 역할을 만들려면 다음을 사용합니다.

    azure role create --inputfile <file path>

다음 예제에서는 *Virtual Machine Operator*라는 사용자 지정 역할을 만듭니다. 사용자 지정 역할은 *Microsoft.Compute*, *Microsoft.Storage* 및 *Microsoft.Network* 리소스 공급자의 모든 읽기 작업에 대한 액세스 권한을 부여하고 가상 컴퓨터를 시작, 다시 시작 및 모니터링할 수 있는 권한을 부여합니다. 두 구독 모두에서 사용자 지정 역할을 사용할 수 있습니다. 이 예제에서는 입력으로 JSON 파일을 사용합니다.

![JSON - 사용자 지정 역할 정의 - 스크린샷](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-1.png)

![RBAC Azure 명령줄 - azure role create - 스크린샷](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-2.png)

## <a name="modify-a-custom-role"></a>사용자 지정 역할 수정
사용자 지정 역할을 수정하려면 먼저 `azure role show` 명령을 사용하여 역할 정의를 검색합니다. 그런 다음 역할 정의 파일을 원하는 대로 변경합니다. 마지막으로 `azure role set` 을 사용하여 수정한 역할 정의를 저장합니다.

    azure role set --inputfile <file path>

다음 예제에서는 **작업**에 *Microsoft.Insights/diagnosticSettings/* 작업을 추가하고 Virtual Machine Operator 사용자 지정 역할의 **AssignableScopes**에 Azure 구독을 추가합니다.

![JSON - 사용자 지정 역할 수정 정의 - 스크린샷](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set-1.png)

![RBAC Azure 명령줄 - azure role set - 스크린샷](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set2.png)

## <a name="delete-a-custom-role"></a>사용자 지정 역할 삭제
사용자 지정 역할을 삭제하려면 먼저 `azure role show` 명령을 사용하여 역할의 **ID** 를 확인합니다. 그런 다음 `azure role delete` 명령을 사용하여 **ID**를 지정하여 역할을 삭제합니다.

다음 예제에서는 *Virtual Machine Operator* 사용자 지정 역할을 제거합니다.

![RBAC Azure 명령줄 - azure role delete - 스크린샷](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-delete.png)

## <a name="list-custom-roles"></a>사용자 지정 역할 나열
범위에서 할당할 수 있는 역할을 나열하려면 `azure role list` 명령을 사용합니다.

다음 명령에서는 선택한 구독에 할당할 수 있는 모든 역할을 나열합니다.

```
azure role list --json | jq '.[] | {"name":.properties.roleName, type:.properties.type}'
```

![RBAC Azure 명령줄 - azure role list - 스크린샷](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list1.png)

다음 예제에서는 *Virtual Machine Operator* 사용자 지정 역할을 *Production4* 구독에서 사용할 수 없습니다. 이 구독이 해당 역할의 **AssignableScopes**에 없기 때문입니다.

```
azure role list --json | jq '.[] | if .properties.type == "CustomRole" then .properties.roleName else empty end'
```

![RBAC Azure 명령줄 - 사용자 지정 역할에 대한 azure role list - 스크린샷](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list2.png)

## <a name="rbac-topics"></a>RBAC 항목
[!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]


