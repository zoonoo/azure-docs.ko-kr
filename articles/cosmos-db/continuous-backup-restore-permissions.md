---
title: Azure Cosmos DB 계정을 복원 하는 권한을 구성 합니다.
description: 연속 백업 계정에 대 한 복원 권한을 특정 역할 또는 보안 주체로 분리 하 고 제한 하는 방법을 알아봅니다. Azure Portal, CLI를 사용 하 여 기본 제공 역할을 할당 하거나 사용자 지정 역할을 정의 하는 방법을 보여 줍니다.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 82af70547d20509c48f1e07bbc7610fc666a6da1
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393057"
---
# <a name="manage-permissions-to-restore-an-azure-cosmos-db-account"></a>Azure Cosmos DB 계정을 복원 하는 권한 관리
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB에 대 한 특정 시점 복원 기능 (연속 백업 모드)은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure Cosmos DB를 사용 하면 연속 백업 (미리 보기) 계정에 대 한 복원 권한을 특정 역할 또는 보안 주체로 분리 하 고 제한할 수 있습니다. 계정의 소유자는 복원을 트리거하고 다른 보안 주체에 역할을 할당 하 여 복원 작업을 수행할 수 있습니다. 이러한 권한은 다음 이미지에 표시 된 것 처럼 원본 계정 범위에서 구독 범위 또는 추가 세부적으로에 적용할 수 있습니다.

:::image type="content" source="./media/continuous-backup-restore-permissions/restore-roles-permissions.png" alt-text="복원 작업을 수행 하는 데 필요한 역할의 목록입니다." lightbox="./media/continuous-backup-restore-permissions/restore-roles-permissions.png" border="false":::

범위는 액세스 권한이 있는 리소스 집합으로, 범위에 대해 자세히 알아보려면 [AZURE RBAC](../role-based-access-control/scope-overview.md) 설명서를 참조 하세요. Azure Cosmos DB에서 적용 가능한 범위는 대부분의 사용 사례에 대 한 원본 구독 및 데이터베이스 계정입니다. 복원 작업을 수행 하는 보안 주체에는 대상 리소스 그룹에 대 한 쓰기 권한이 있어야 합니다.

## <a name="assign-roles-for-restore-using-the-azure-portal"></a>Azure Portal를 사용 하 여 복원에 대 한 역할 할당

복원을 수행 하려면 사용자 또는 보안 주체에 게 복원 권한 (즉, *복원/작업* 권한)과 새 계정 ( *쓰기* 권한)을 프로 비전 할 수 있는 권한이 필요 합니다.  이러한 권한을 부여 하기 위해 소유자는 `CosmosRestoreOperator` 및 `Cosmos DB Operator` 기본 제공 역할을 보안 주체에 할당할 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 구독으로 이동 하 여 **액세스 제어 (IAM)** 탭으로 이동 하 고 **추가**  >  **역할 할당** 추가를 선택 합니다.

1. **역할 할당 추가** 창에서 **역할** 필드에 대해 **CosmosRestoreOperator** role을 선택 합니다. 다음 그림에 표시 된 것 처럼에 대 한 **액세스 할당** 필드의 **사용자, 그룹 또는 서비스 주체** 를 선택 하 고 사용자의 이름 또는 전자 메일 ID를 검색 합니다.

   :::image type="content" source="./media/continuous-backup-restore-permissions/assign-restore-operator-roles.png" alt-text="CosmosRestoreOperator 및 Cosmos DB 운영자 역할을 할당 합니다." border="true":::

1. **저장** 을 선택 하 여 *복원/작업* 권한을 부여 합니다.

1. **Cosmos DB Operator** 역할을 사용 하 여 3 단계를 반복 하 여 쓰기 권한을 부여 합니다. Azure Portal에서이 역할을 할당할 때 전체 구독에 대 한 복원 권한을 부여 합니다.

## <a name="permission-scopes"></a>사용 권한 범위

|Scope  |예  |
|---------|---------|
|구독 | /subscriptions/00000000-0000-0000-0000-000000000000 |
|Resource group | /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-cosmosdb-rg |
|CosmosDB 복원 가능한 계정 리소스 | /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/위치/미국 서 부/restorableDatabaseAccounts/23e99a35-cd36-4df4-9614-f767a03b9995|

복원 가능한 계정 리소스는 `az cosmosdb restorable-database-account list --name <accountname>` PowerShell의 CLI 또는 cmdlet에 있는 명령의 출력에서 추출할 수 있습니다 `Get-AzCosmosDBRestorableDatabaseAccount -DatabaseAccountName <accountname>` . 출력의 name 특성은 `instanceID` 복원 가능한 계정의를 나타냅니다. 자세히 알아보려면 [PowerShell](continuous-backup-restore-powershell.md) 또는 [CLI](continuous-backup-restore-command-line.md) 문서를 참조 하세요.

## <a name="permissions"></a>사용 권한

연속 백업 모드 계정을 복원 하는 것과 관련 된 여러 작업을 수행 하려면 다음 권한이 필요 합니다.

|사용 권한  |영향  |최소 범위  |최대 범위  |
|---------|---------|---------|---------|
|`Microsoft.Resources/deployments/validate/action`, `Microsoft.Resources/deployments/write` | 이러한 권한은 ARM 템플릿 배포에서 복원 된 계정을 만드는 데 필요 합니다. 이 역할을 설정 하는 방법은 아래의 sample permission [RestorableAction](#custom-restorable-action) 을 참조 하십시오. | 해당 없음 | 해당 없음  |
|Microsoft.DocumentDB/databaseAccounts/write | 계정을 리소스 그룹으로 복원 하려면이 권한이 필요 합니다. | 복원 된 계정이 만들어진 리소스 그룹입니다. | 복원 된 계정이 만들어진 구독 |
|`Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action` |이 권한은 원본 복원 가능한 데이터베이스 계정 범위에서 복원 동작을 수행할 수 있도록 하는 데 필요 합니다.  | 복원 중인 원본 계정에 속하는 *RestorableDatabaseAccount* 리소스입니다. 이 값은 `ID` 복원 가능한 데이터베이스 계정 리소스의 속성에도 제공 됩니다. 복원 가능한 계정의 예는 */subscriptions/subscriptionId/providers/Microsoft.DocumentDB/위치/지역 이름/restorableDatabaseAccounts/<guid-instanceid>* | 복원 가능한 데이터베이스 계정이 포함 된 구독입니다. 리소스 그룹을 범위로 선택할 수 없습니다.  |
|`Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read` |이 권한은 원본 복원 가능한 데이터베이스 계정 범위에서 복원할 수 있는 데이터베이스 계정을 나열 하는 데 필요 합니다.  | 복원 중인 원본 계정에 속하는 *RestorableDatabaseAccount* 리소스입니다. 이 값은 `ID` 복원 가능한 데이터베이스 계정 리소스의 속성에도 제공 됩니다. 복원 가능한 계정의 예는 */subscriptions/subscriptionId/providers/Microsoft.DocumentDB/위치/지역 이름/restorableDatabaseAccounts/<guid-instanceid>*| 복원 가능한 데이터베이스 계정이 포함 된 구독입니다. 리소스 그룹을 범위로 선택할 수 없습니다.  |
|`Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` | 이 권한은 원본 복원 가능한 계정 범위에서 복원 가능한 계정에 대 한 데이터베이스 및 컨테이너 목록과 같은 복원 가능한 리소스를 읽을 수 있도록 하는 데 필요 합니다.  | 복원 중인 원본 계정에 속하는 *RestorableDatabaseAccount* 리소스입니다. 이 값은 `ID` 복원 가능한 데이터베이스 계정 리소스의 속성에도 제공 됩니다. 복원 가능한 계정의 예는 */subscriptions/subscriptionId/providers/Microsoft.DocumentDB/위치/지역 이름/restorableDatabaseAccounts/<guid-instanceid>*| 복원 가능한 데이터베이스 계정이 포함 된 구독입니다. 리소스 그룹을 범위로 선택할 수 없습니다. |

## <a name="azure-cli-role-assignment-scenarios-to-restore-at-different-scopes"></a>다른 범위에서 복원 하는 역할 할당 시나리오 Azure CLI

구독 또는 지정 된 계정 내에서 복원 작업을 수행할 수 있는 사용자를 세부적으로 제어 하기 위해 사용 권한이 있는 역할을 다른 범위에 할당할 수 있습니다.

### <a name="assign-capability-to-restore-from-any-restorable-account-in-a-subscription"></a>구독의 모든 복원 가능한 계정에서 복원 하는 기능을 할당 합니다.

`CosmosRestoreOperator`구독 수준에서 기본 제공 역할 할당

```azurecli-interactive
az role assignment create --role "CosmosRestoreOperator" --assignee <email> –scope /subscriptions/<subscriptionId>
```

### <a name="assign-capability-to-restore-from-a-specific-account"></a>특정 계정에서 복원 하는 기능 할당

* 특정 리소스 그룹에 대 한 사용자 쓰기 작업을 할당 합니다. 이 작업은 리소스 그룹에 새 계정을 만드는 데 필요 합니다.

* 복원 해야 하는 특정 복원 가능한 데이터베이스 계정에 *CosmosRestoreOperator* 기본 제공 역할을 할당 합니다. 다음 명령에서 *RestorableDatabaseAccount* 의 범위는 `ID` 의 출력 `az cosmosdb restorable-database-account` (CLI를 사용 하는 경우) 또는  `Get-AzCosmosDBRestorableDatabaseAccount` (PowerShell을 사용 하는 경우)의 속성에서 검색 됩니다.

  ```azurecli-interactive
   az role assignment create --role "CosmosRestoreOperator" --assignee <email> –scope <RestorableDatabaseAccount>
  ```

### <a name="assign-capability-to-restore-from-any-source-account-in-a-resource-group"></a>리소스 그룹의 모든 원본 계정에서 복원 하는 기능을 할당 합니다.
이 작업은 현재 지원 되지 않습니다.

## <a name="custom-role-creation-for-restore-action-with-cli"></a><a id="custom-restorable-action"></a>CLI를 사용 하 여 복원 작업에 대 한 사용자 지정 역할 만들기

구독 소유자는 다른 Azure AD id로 복원할 수 있는 권한을 제공할 수 있습니다. 복원 권한은 동작을 기반으로 `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action` 하며 해당 복원 권한에 포함 되어야 합니다. 이 역할을 포함 하는 *CosmosRestoreOperator* 라는 기본 제공 역할이 있습니다. 이 기본 제공 역할을 사용 하 여 사용 권한을 할당 하거나 사용자 지정 역할을 만들 수 있습니다.

아래 RestorableAction 사용자 지정 역할을 나타냅니다. 이 역할은 명시적으로 만들어야 합니다. 다음 JSON 템플릿은 restore 권한이 있는 *RestorableAction* 사용자 지정 역할을 만듭니다.

```json
{
  "assignableScopes": [
    "/subscriptions/23587e98-b6ac-4328-a753-03bcd3c8e744"
  ],
  "description": "Can do a restore request for any Cosmos DB database account with continuous backup",
  "permissions": [
    {
      "actions": [
        "Microsoft.Resources/deployments/validate/action",
        "Microsoft.DocumentDB/databaseAccounts/write",
        "Microsoft.Resources/deployments/write",  
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "Name": "RestorableAction",
    "roleType": "CustomRole"
}
```

다음 템플릿 배포 명령을 사용 하 여 ARM 템플릿을 사용 하는 restore 권한이 있는 역할을 만듭니다.

```azurecli-interactive
az role definition create --role-definition <JSON_Role_Definition_Path>
```

## <a name="next-steps"></a>다음 단계

* [Azure Portal](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md)또는 [Azure Resource Manager](continuous-backup-restore-template.md)를 사용 하 여 연속 백업을 구성 하 고 관리 합니다.
* [연속 백업 모드의 리소스 모델](continuous-backup-restore-resource-model.md)
