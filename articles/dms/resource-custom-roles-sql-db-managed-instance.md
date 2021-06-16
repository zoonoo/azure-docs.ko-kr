---
title: '사용자 지정 역할: 온라인 SQL Server에서 SQL Managed Instance로의 마이그레이션'
titleSuffix: Azure Database Migration Service
description: SQL Server에서 Azure SQL Managed Instance로의 온라인 마이그레이션에 대한 사용자 지정 역할을 사용하는 방법을 알아봅니다.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 6979f2968cde3a584658375ded505002ebcc703e
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110695912"
---
# <a name="custom-roles-for-sql-server-to-azure-sql-managed-instance-online-migrations"></a>SQL Server에서 Azure SQL Managed Instance로의 온라인 마이그레이션에 대한 사용자 지정 역할

Azure Database Migration Service는 앱 ID를 사용하여 Azure 서비스와 상호 작용합니다. 앱 ID를 사용하려면 구독 수준의 기여자 역할(많은 회사 보안 부서에서 허용하지 않음) 또는 Azure Database Migration Service에서 필요로 하는 특정 사용 권한을 부여하는 사용자 지정 역할을 만들어야 합니다. Azure Active Directory에는 2,000개의 사용자 지정 역할 제한이 있으므로 앱 ID에 필요한 모든 사용 권한을 1~2개의 사용자 지정 역할로 결합한 다음, 특정 개체 또는 리소스 그룹(또는 구독 수준에서)에 대한 사용자 지정 역할을 앱 ID에 부여해야 할 수 있습니다. 사용자 지정 역할의 수가 문제되지 않는 경우 리소스 종류별로 사용자 지정 역할을 분할하여 아래와 같이 총 3개의 사용자 지정 역할을 만들 수 있습니다.

역할 정의 json 문자열의 AssignableScopes 섹션에서는 포털의 **역할 할당 추가** UI에서 사용 권한이 표시되는 위치를 제어할 수 있습니다. UI가 추가 역할로 인해 복잡해지지 않도록 리소스 그룹 또는 리소스 수준에서 역할을 정의하는 것이 좋습니다. 실제 역할 할당을 수행하지는 않습니다.

## <a name="minimum-number-of-roles"></a>최소 역할 수

현재 리소스 수준에서, 그리고 구독 수준에서, 앱 ID에 대해 둘 이상의 사용자 지정 역할을 만드는 것이 좋습니다.

> [!NOTE]
> 새 SQL Managed Instance 코드가 Azure에 배포되면 최종적으로 마지막 사용자 지정 역할 요구 사항이 제거될 수 있습니다.

**앱 ID에 대한 사용자 지정 역할**. 이 역할은 Azure Database Migration Service를 호스트하는 *리소스* 또는 *리소스 그룹* 수준에서 Azure Database Migration Service 마이그레이션에 필요 합니다(앱 ID에 대한 자세한 내용은 [포털을 사용하여 리소스에 액세스할 수 있는 Azure AD 애플리케이션 및 서비스 주체 만들기](../active-directory/develop/howto-create-service-principal-portal.md) 문서를 참조).

```json
{
  "Name": "DMS Role - App ID",
  "IsCustom": true,
  "Description": "DMS App ID access to complete MI migrations",
  "Actions": [
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageaccounts/blobservices/read",
        "Microsoft.Storage/storageaccounts/blobservices/write",
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/write",
        "Microsoft.Sql/managedInstances/databases/read",
        "Microsoft.Sql/managedInstances/databases/write",
        "Microsoft.Sql/managedInstances/databases/delete",
        "Microsoft.Sql/managedInstances/metrics/read",
        "Microsoft.DataMigration/locations/*",
        "Microsoft.DataMigration/services/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/ResourceGroups/<StorageAccount_rg_name>",
    "/subscriptions/<subscription_id>/ResourceGroups/<ManagedInstance_rg_name>",
    "/subscriptions/<subscription_id>/ResourceGroups/<DMS_rg_name>",
  ]
}
```

**앱 ID - 구독에 대한 사용자 지정 역할**. 이 역할은 SQL Managed Instance를 호스트하는 *구독* 수준에서 Azure Database Migration Service 마이그레이션을 수행하는 데 필요합니다.

```json
{
  "Name": "DMS Role - App ID - Sub",
  "IsCustom": true,
  "Description": "DMS App ID access at subscription level to complete MI migrations",
  "Actions": [
        "Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>"
  ]
}
```

위의 json은 3개의 텍스트 파일에 저장되어야 하며, AzureRM, AZ PowerShell cmdlet 또는 Azure CLI를 사용하여 **New-AzureRmRoleDefinition (AzureRM)** 또는 **New-AzRoleDefinition (AZ)** 을 사용하는 역할을 만들 수 있습니다.

자세한 내용은 [Azure 사용자 지정 역할](../role-based-access-control/custom-roles.md) 문서를 참조하세요.

이러한 사용자 지정 역할을 만든 후에는 사용자 및 앱 ID에 대한 역할 할당을 적절한 리소스 또는 리소스 그룹에 추가해야 합니다.

* "DMS 역할 - 앱 ID" 역할은 마이그레이션에 사용되는 앱 ID에 부여되어야 하고, 스토리지 계정, Azure Database Migration Service 인스턴스 및 SQL Managed Instance 리소스 수준에서도 부여되어야 합니다. Azure Database Migration Service를 호스트하는 리소스 또는 리소스 그룹 수준에서 부여됩니다.
* SQL Managed Instance를 호스트하는 구독 수준에서 앱 ID에 "DMS 역할 - 앱 ID - 하위" 역할을 부여해야 합니다(리소스 또는 리소스 그룹에 대한 부여는 실패). 이 요구 사항은 코드 업데이트가 배포될 때까지 일시적입니다.

## <a name="expanded-number-of-roles"></a>확장된 역할 수

Azure Active Directory의 사용자 지정 역할 수가 문제되지 않는 경우 총 3개의 역할을 만드는 것이 좋습니다. 여전히 "DMS 역할 - 앱 ID – 하위" 역할이 필요하지만 위의 "DMS 역할 - 앱 ID" 역할은 리소스 종류별로 두 개의 다른 역할로 분할됩니다.

**SQL Managed Instance 앱 ID에 대한 사용자 지정 역할**

```json
{
  "Name": "DMS Role - App ID - SQL MI",
  "IsCustom": true,
  "Description": "DMS App ID access to complete MI migrations",
  "Actions": [
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/write",
        "Microsoft.Sql/managedInstances/databases/read",
        "Microsoft.Sql/managedInstances/databases/write",
        "Microsoft.Sql/managedInstances/databases/delete",
        "Microsoft.Sql/managedInstances/metrics/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/resourceGroups/<ManagedInstance_rg_name>"
  ]
}
```

**스토리지에 대한 앱 ID의 사용자 지정 역할**

```json
{
  "Name": "DMS Role - App ID - Storage",
  "IsCustom": true,
  "Description": "DMS App ID storage access to complete MI migrations",
  "Actions": [
"Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageaccounts/blobservices/read",
        "Microsoft.Storage/storageaccounts/blobservices/write"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/resourceGroups/<StorageAccount_rg_name>"
  ]
}
```

## <a name="role-assignment"></a>역할 할당

사용자/앱 ID에 역할을 할당하려면 Azure Portal을 열고 다음 단계를 수행합니다.

1. 리소스 그룹 또는 리소스(구독에 부여되어야 하는 역할 제외)로 이동하고 **Access Control** 로 이동한 다음, 스크롤하여 방금 만든 사용자 지정 역할을 찾습니다.

2. 적절한 역할을 선택하고, 앱 ID를 선택한 다음, 변경 내용을 저장합니다.

  이제 앱 ID가 **역할 할당** 탭에 표시됩니다.

## <a name="next-steps"></a>다음 단계

* Microsoft [데이터베이스 마이그레이션 가이드](https://datamigration.microsoft.com/)의 시나리오에 대한 마이그레이션 지침을 검토합니다.
