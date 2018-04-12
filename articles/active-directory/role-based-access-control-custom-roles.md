---
title: Azure RBAC에 대한 사용자 지정 역할 만들기 | Microsoft Docs
description: Azure 구독에 보다 정확한 ID 관리를 위해 Azure 역할 기반 Access Control로 사용자 지정 역할을 정의하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/11/2017
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2871ff5eea8fb99040dfab2593d1640d79f51092
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/11/2018
---
# <a name="create-custom-roles-for-azure-role-based-access-control"></a>Azure 역할 기반 Access Control의 사용자 지정 역할 만들기
특정 액세스 요구를 충족하는 기본 제공 역할이 없는 경우 Azure 역할 기반 Access Control(RBAC)에서 사용자 지정 역할을 만듭니다. [Azure PowerShell](role-based-access-control-manage-access-powershell.md), [Azure 명령줄 인터페이스](role-based-access-control-manage-access-azure-cli.md)(CLI) 및 [REST API](role-based-access-control-manage-access-rest.md)를 사용하여 사용자 지정 역할을 만들 수 있습니다. 기본 제공 역할과 마찬가지로 사용자 지정 역할을 사용자, 그룹 및 응용 프로그램에 구독, 리소스 그룹 및 리소스 범위에서 할당할 수 있습니다. 사용자 지정 역할은 Azure AD 테넌트에 저장되고 구독에서 공유할 수 있습니다.

각 테넌트는 최대 2000개의 사용자 지정 역할을 만들 수 있습니다. 

다음은 가상 머신의 모니터링 및 재시작을 위한 사용자 지정 역할에 관한 예제입니다.

```json
{
  "Name": "Virtual Machine Operator",
  "Id": "cadb4a5a-4e7a-47be-84db-05cad13b6769",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624",
    "/subscriptions/34370e90-ac4a-4bf9-821f-85eeedeae1a2"
  ]
}
```
## <a name="actions"></a>작업
사용자 지정 역할의 **Actions** 속성은 해당 역할에 액세스 권한이 부여되는 Azure 작업을 지정합니다. Azure 리소스 공급자의 보안 개체 작업을 식별하는 작업 문자열 모음입니다. 작업 문자열은 `Microsoft.<ProviderName>/<ChildResourceType>/<action>` 형식을 따릅니다. 와일드카드(\*)를 포함하는 작업 문자열은 작업 문자열과 일치하는 모든 작업에 대한 액세스 권한을 부여합니다. 예:

* `*/read` 는 모든 Azure 리소스 공급자의 모든 리소스 종류에 대한 읽기 작업의 액세스 권한을 부여합니다.
* `Microsoft.Compute/*`는 Microsoft.Compute 리소스 공급자에 있는 모든 리소스 종류의 모든 작업에 대한 액세스 권한을 부여합니다.
* `Microsoft.Network/*/read` 는 Azure의 Microsoft.Network 리소스 공급자에서 모든 리소스 종류에 대한 읽기 작업의 액세스 권한을 부여합니다.
* `Microsoft.Compute/virtualMachines/*` 는 가상 머신 및 해당 하위 리소스 종류의 모든 작업에 대한 액세스 권한을 부여합니다.
* `Microsoft.Web/sites/restart/Action`은 웹 사이트를 다시 시작하기 위한 액세스 권한을 부여합니다.

`Get-AzureRmProviderOperation`(PowerShell) 또는 `azure provider operations show`(Azure CLI)를 사용하여 Azure 리소스 공급자에 대한 작업을 나열합니다. 이러한 명령을 사용하여 작업 문자열이 올바른지 확인하고 와일드카드 작업 문자열을 확장할 수 있습니다.

```powershell
Get-AzureRMProviderOperation Microsoft.Compute/virtualMachines/*/action | FT Operation, OperationName

Get-AzureRMProviderOperation Microsoft.Network/*
```

![PowerShell 스크린샷 - Get-AzureRMProviderOperation](./media/role-based-access-control-configure/1-get-azurermprovideroperation-1.png)

```azurecli
azure provider operations show "Microsoft.Compute/virtualMachines/*/action" --js on | jq '.[] | .operation'

azure provider operations show "Microsoft.Network/*"
```

![Azure CLI 스크린샷 - azure 공급자 작업 표시 "Microsoft.Compute/virtualMachines/\*/action" ](./media/role-based-access-control-configure/1-azure-provider-operations-show.png)

## <a name="notactions"></a>NotActions
허용하려는 작업 집합을 제한된 작업을 제외하여 보다 쉽게 정의하는 경우 **NotActions** 속성을 사용합니다. 사용자 지정 역할로 부여되는 액세스 권한은 **Actions** 작업에서 **NotActions** 작업을 빼서 계산합니다.

> [!NOTE]
> 사용자에게 **NotActions**에서 작업을 제외하는 역할이 할당되고 동일한 작업에 대한 액세스 권한을 부여하는 두 번째 역할이 할당된 경우 사용자는 해당 작업을 수행할 수 있습니다. **NotActions** 는 거부 규칙이 아니며 특정 작업을 제외해야 할 경우 허용된 작업 집합을 만드는 편리한 방법일 뿐입니다.
>
>

## <a name="assignablescopes"></a>AssignableScopes
사용자 지정 역할의 **AssignableScopes** 속성은 사용자 지정 역할을 할당할 수 있는 범위(구독, 리소스 그룹 또는 리소스)를 지정합니다. 역할이 필요한 구독 또는 리소스 그룹에만 사용자 지정 역할을 할당할 수 있도록 하고 그 외의 구독 또는 리소스 그룹에 대해서는 사용자 환경을 보다 깔끔하게 유지할 수 있습니다.

유효한 할당 가능한 범위의 예는 다음과 같습니다.

* "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e", "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624" - 두 개의 구독에 역할을 할당할 수 있도록 합니다.
* "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e" - 단일 구독에 역할을 할당할 수 있도록 합니다.
* "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network" - 네트워크 리소스 그룹에만 역할을 할당할 수 있도록 합니다.

> [!NOTE]
> 구독, 리소스 그룹 또는 리소스 ID를 적어도 하나 사용해야 합니다.
>
>

## <a name="custom-roles-access-control"></a>사용자 지정 역할 액세스 제어
사용자 지정 역할의 **AssignableScopes** 속성으로 해당 역할을 보고 수정하며 삭제할 수 있는 사용자를 제어합니다.

* 사용자 지정 역할을 만들 수 있는 사람
    구독, 리소스 그룹 및 리소스의 소유자(및 사용자 액세스 관리자)는 해당 범위에 사용할 사용자 지정 역할을 만들 수 있습니다.
    역할을 만드는 사용자는 역할의 모든 **AssignableScopes**에서 `Microsoft.Authorization/roleDefinition/write` 작업을 수행할 수 있어야 합니다.
* 사용자 지정 역할을 수정할 수 있는 사람
    구독, 리소스 그룹 및 리소스의 소유자(및 사용자 액세스 관리자)는 해당 범위에 사용자 지정 역할을 수정할 수 있습니다. 사용자는 사용자 지정 역할의 모든 **AssignableScopes**에서 `Microsoft.Authorization/roleDefinition/write` 작업을 수행해야 할 수 있습니다.
* 사용자 지정 역할을 볼 수 있는 사용자는 누구인가요?
    Azure RBAC에서 모든 기본 제공 역할은 할당 가능한 역할을 볼 수 있습니다. 범위에서 `Microsoft.Authorization/roleDefinition/read` 작업을 수행할 수 있는 사용자는 해당 범위에서 할당 가능한 RBAC 역할을 볼 수 있습니다.

## <a name="see-also"></a>참고 항목
* [역할 기반 Access Control](role-based-access-control-configure.md): Azure Portal에서 RBAC를 통해 시작합니다.
* 사용 가능한 작업 목록은 [Azure Resource Manager 리소스 공급자 작업](role-based-access-control-resource-provider-operations.md)을 참조하세요.
* 다음을 사용하여 액세스를 관리하는 방법에 대해 알아봅니다.
  * [PowerShell](role-based-access-control-manage-access-powershell.md)
  * [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
  * [REST API](role-based-access-control-manage-access-rest.md)
* [기본 제공 역할](role-based-access-built-in-roles.md): RBAC에서 표준이 되는 역할에 대한 세부 정보를 봅니다.
