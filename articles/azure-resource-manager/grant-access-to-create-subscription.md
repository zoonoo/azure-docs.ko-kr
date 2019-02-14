---
title: Azure 엔터프라이즈 구독 만들기에 대한 액세스 권한 부여 | Microsoft Docs
description: Azure 엔터프라이즈 구독을 프로그래밍 방식으로 만드는 기능을 사용자 또는 서비스 주체에 부여하는 방법에 대해 알아봅니다.
services: azure-resource-manager
author: adpick
manager: adpick
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/05/2018
ms.author: adpick
ms.openlocfilehash: 7a2397328f715dbf63246e8d4aaa789b5986b3b4
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56112566"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Azure 엔터프라이즈 구독 만들기에 대한 액세스 권한 부여(미리 보기)

[EA(기업계약)](https://azure.microsoft.com/pricing/enterprise-agreement/)의 Azure 고객은 고객의 계정으로 청구되는 구독을 만들 수 있는 권한을 다른 사용자 또는 서비스 주체에게 부여할 수 있습니다. 이 문서에서는 [RBAC(역할 기반 액세스 제어)](../active-directory/role-based-access-control-configure.md)를 사용하여 구독을 만드는 기능을 공유하고 구독 생성을 감사하는 방법을 배웁니다. 공유하려는 계정에 대해 소유자 역할이 있어야 합니다.

구독을 만들려면 [프로그래밍 방식으로 Azure 엔터프라이즈 구독 만들기(미리 보기)](programmatically-create-subscription.md)를 참조하세요.

## <a name="delegate-access-to-an-enrollment-account-using-rbac"></a>RBAC를 사용하여 등록 계정에 대한 액세스 위임

다른 사용자 또는 서비스 사용자에게 특정 계정에 대한 구독을 만들 수 있는 기능을 부여하려면 [등록 계정의 범위에서 RBAC 소유자 역할을 제공합니다](../active-directory/role-based-access-control-manage-access-rest.md). 다음 예제에서는 테넌트의 사용자에게 `<userObjectId>`의 `principalId`로(SignUpEngineering@contoso.com에 대한) 등록 계정의 소유자 역할을 제공합니다. 등록 계정 ID 및 보안 주체 ID를 찾으려면 [프로그래밍 방식으로 Azure 엔터프라이즈 구독 만들기(미리 보기)](programmatically-create-subscription.md)를 참조하세요.

# <a name="resttabrest"></a>[REST (영문)](#tab/rest)

```json
PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

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

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[New-AzRoleAssignment](../active-directory/role-based-access-control-manage-access-powershell.md)를 사용하여 다른 사용자 소유자에게 등록 계정에 대한 액세스를 제공합니다.

```azurepowershell-interactive
New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

[az role assignment create](../active-directory/role-based-access-control-manage-access-azure-cli.md)를 사용하여 다른 사용자 소유자에게 등록 계정에 대한 액세스를 제공합니다.

```azurecli-interactive 
az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

----

사용자가 등록 계정에 대한 RBAC 소유자가 되면 그 아래에서 프로그래밍 방식으로 구독을 만들 수 있습니다. 위임된 사용자가 만든 구독에는 여전히 서비스 관리자로서의 원래 계정 소유자가 있지만 기본적으로 소유자로서 위임된 사용자가 있습니다. 

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>활동 로그를 사용하여 구독을 만든 사람 감사

이 API를 통해 만든 구독을 추적하려면 [테넌트 활동 로그 API](/rest/api/monitor/tenantactivitylogs)를 사용합니다. 현재 PowerShell, CLI 또는 Azure Portal을 사용하여 구독 만들기를 추적할 수 없습니다.

1. Azure AD 테넌트의 테넌트 관리자로 [액세스의 권한을 상승한](../active-directory/role-based-access-control-tenant-admin-access.md) 다음, `/providers/microsoft.insights/eventtypes/management` 범위에 대해 감사 사용자에게 읽기 역할을 할당합니다.
1. 감사 사용자로 [테넌트 활동 로그 API](/rest/api/monitor/tenantactivitylogs)를 호출하여 구독 생성 작업을 봅니다. 예제:

```
GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'" 
```

> [!NOTE]
> 명령줄에서 이 API를 편리하게 호출하려면 [ARMClient](https://github.com/projectkudu/ARMClient)를 시도합니다.

## <a name="next-steps"></a>다음 단계

* 사용자 또는 서비스 주체가 구독을 만들 권한을 가지므로 해당 ID를 사용하여 [프로그래밍 방식으로 Azure 엔터프라이즈 구독을 만들 수 있습니다](programmatically-create-subscription.md).
* .NET을 사용하여 구독 만들기에 대한 예제는 [GitHub의 샘플 코드](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core)를 참조하세요.
* Azure Resource Manager 및 해당 API에 대한 자세한 내용은 [Azure Resource Manager 개요](resource-group-overview.md)를 참조하세요.
* 관리 그룹을 사용하여 많은 수의 구독 관리에 대해 자세히 알아보려면 [Azure 관리 그룹으로 리소스 구성](management-groups-overview.md)을 참조하세요.
* 구독 거버넌스에서 대규모 조직에 대한 포괄적인 모범 사례 지침을 보려면 [Azure 엔터프라이즈 스캐폴드 - 규범적 구독 거버넌스](/azure/architecture/cloud-adoption-guide/subscription-governance)를 참조하세요.
