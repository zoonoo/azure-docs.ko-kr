---
title: '사용자 지정 역할: 온라인 SQL 서버에서 SQL 관리 인스턴스 마이그레이션'
titleSuffix: Azure Database Migration Service
description: SQL Server에서 Azure SQL Database관리 인스턴스 온라인 마이그레이션에 대한 사용자 지정 역할을 사용하는 방법을 알아봅니다.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 10/25/2019
ms.openlocfilehash: e9a1024ca3ab68841474ab051c029042df4915b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254936"
---
# <a name="custom-roles-for-sql-server-to-sql-database-managed-instance-online-migrations"></a>SQL Server에서 SQL Database에 대한 사용자 지정 역할 관리 인스턴스 온라인 마이그레이션

Azure 데이터베이스 마이그레이션 서비스는 APP ID를 사용하여 Azure 서비스와 상호 작용합니다. APP ID에는 많은 회사 보안 부서에서 허용하지 않는 구독 수준에서 기여자 역할이 필요하거나 Azure 데이터베이스 마이그레이션 서비스에서 요구하는 특정 권한을 부여하는 사용자 지정 역할 만들기가 필요합니다. Azure Active Directory에는 2,000개의 사용자 지정 역할이 제한되므로 APP ID에서 특별히 요구하는 모든 권한을 하나 또는 두 개의 사용자 지정 역할로 결합한 다음 특정 개체 또는 리소스 그룹에 대한 사용자 지정 역할을 APP ID에 부여할 수 있습니다. 구독 수준)을 참조하십시오. 사용자 지정 역할 수가 중요하지 않은 경우 사용자 지정 역할을 리소스 유형별로 분할하여 아래에 설명된 대로 총 세 개의 사용자 지정 역할을 만들 수 있습니다.

역할 정의 json 문자열의 할당 가능한 Scopes 섹션을 사용하면 포털의 **역할 할당 UI에** 사용 권한이 표시되는 위치를 제어할 수 있습니다. 추가 역할로 UI를 어지럽히지 않도록 리소스 그룹 또는 리소스 수준에서 역할을 정의할 수 있습니다. 실제 역할 할당은 수행되지 않습니다.

## <a name="minimum-number-of-roles"></a>최소 역할 수

현재 APP ID에 대해 최소 두 개의 사용자 지정 역할을 만드는 것이 좋습니다.

> [!NOTE]
> 새 SQL Database 관리 인스턴스 코드가 Azure에 배포되면 마지막 사용자 지정 역할 요구 사항이 결국 제거될 수 있습니다.

**APP ID에 대한 사용자 지정 역할.** 이 역할은 *리소스* 또는 *리소스 그룹* 수준에서 Azure Database 마이그레이션 서비스 마이그레이션에 필요합니다(APP ID에 대한 자세한 내용은 포털 사용 문서에서 [리소스에 액세스할 수 있는 Azure AD 응용 프로그램 및 서비스 주체를 만듭니다).](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)

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

**APP ID에 대한 사용자 지정 역할 - 구독**. 이 역할은 *구독* 수준에서 Azure 데이터베이스 마이그레이션 서비스 마이그레이션에 필요합니다.

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

위의 json은 세 개의 텍스트 파일에 저장되어야 하며 AzureRM, AZ PowerShell cmdlet 또는 Azure CLI를 사용하여 **새 AzureRmRoleDefinition(AzureRM)** 또는 **새 AzRoleDefinition(AZ)를**사용하여 역할을 만들 수 있습니다.

자세한 내용은 [Azure 리소스에 대한 사용자 지정 역할](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)문서를 참조하십시오.

이러한 사용자 지정 역할을 만든 후 사용자 및 APP ID에 역할 할당을 해당 리소스 또는 리소스 그룹에 추가해야 합니다.

* "DMS 역할 - 앱 ID" 역할은 마이그레이션에 사용되는 APP ID와 저장소 계정, Azure 데이터베이스 마이그레이션 서비스 인스턴스 및 SQL Database 관리 인스턴스 리소스 수준에서 부여되어야 합니다.
* "DMS 역할 - 앱 ID - Sub" 역할은 구독 수준에서 APP ID에 부여되어야 합니다(리소스 또는 리소스 그룹에서 부여가 실패함). 이 요구 사항은 코드 업데이트가 배포될 때까지 일시적입니다.

## <a name="expanded-number-of-roles"></a>역할 의 확장 된 수

Azure Active Directory의 사용자 지정 역할 수가 중요하지 않은 경우 총 세 가지 역할을 만드는 것이 좋습니다. "DMS 역할 - 앱 ID - 하위" 역할은 여전히 필요하지만 위의 "DMS 역할 - 앱 ID" 역할은 리소스 유형별로 두 가지 역할로 분할됩니다.

**SQL 데이터베이스 관리 인스턴스에 대한 APP ID에 대한 사용자 지정 역할**

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

**스토리지용 APP ID에 대한 사용자 지정 역할**

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

사용자/APP ID에 역할을 할당하려면 Azure 포털을 열고 다음 단계를 수행합니다.

1. 리소스 그룹 또는 리소스(구독에서 부여해야 하는 역할 제외)로 이동한 다음 **액세스 제어**로 이동한 다음 스크롤하여 방금 만든 사용자 지정 역할을 찾습니다.

2. 적절한 역할을 선택하고 APP ID를 선택한 다음 변경 내용을 저장합니다.

  이제 APP ID가 **역할 할당** 탭에 나열됩니다.

## <a name="next-steps"></a>다음 단계

* Microsoft 데이터베이스 마이그레이션 가이드에서 시나리오에 대한 마이그레이션 [지침을](https://datamigration.microsoft.com/)검토합니다.
