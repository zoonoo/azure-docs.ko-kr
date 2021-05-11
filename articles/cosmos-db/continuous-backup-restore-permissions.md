---
title: Azure Cosmos DB 계정을 복원하는 권한을 구성합니다.
description: 지속적인 백업 계정에 대한 복원 권한을 특정 역할 또는 보안 주체로 분리하고 제한하는 방법을 알아봅니다. Azure Portal, CLI를 사용하여 기본 제공 역할을 할당하거나 사용자 지정 역할을 정의하는 방법을 보여 줍니다.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 8b3ce2c195dc2fa3dd703306e731aa5b807b78b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100648606"
---
# <a name="manage-permissions-to-restore-an-azure-cosmos-db-account"></a>Azure Cosmos DB 계정을 복원하는 권한 관리
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB에 대한 특정 시점 복원 기능(지속적인 백업 모드)은 현재 퍼블릭 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure Cosmos DB를 사용하면 지속적인 백업(미리 보기) 계정에 대한 복원 권한을 특정 역할 또는 보안 주체로 분리하고 제한할 수 있습니다. 계정의 소유자는 복원을 트리거하고 다른 보안 주체에 역할을 할당하여 복원 작업을 수행할 수 있습니다. 이러한 권한은 다음 이미지에 표시된 것처럼 구독 범위나 더 세부적으로 원본 계정 범위에 적용될 수 있습니다.

:::image type="content" source="./media/continuous-backup-restore-permissions/restore-roles-permissions.png" alt-text="복원 작업을 수행하는 데 필요한 역할의 목록입니다." lightbox="./media/continuous-backup-restore-permissions/restore-roles-permissions.png" border="false":::

범위는 액세스 권한이 있는 리소스 집합입니다. 범위에 관해 자세히 알아보려면 [Azure RBAC](../role-based-access-control/scope-overview.md) 설명서를 참조하세요. Azure Cosmos DB에서 적용 가능한 범위는 대부분의 사용 사례에 대한 원본 구독 및 데이터베이스 계정입니다. 복원 작업을 수행하는 보안 주체에는 대상 리소스 그룹에 대한 쓰기 권한이 있어야 합니다.

## <a name="assign-roles-for-restore-using-the-azure-portal"></a>Azure Portal을 사용한 복원용 역할 할당

복원을 수행하려면 사용자 또는 보안 주체에 복원 권한(즉, *복원/작업* 권한)과 새 계정(즉, *쓰기* 권한)을 프로비저닝할 수 있는 권한이 필요합니다.  이러한 권한을 부여하려면 소유자는 `CosmosRestoreOperator`과 `Cosmos DB Operator` 기본 제공 역할을 보안 주체에 할당할 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 구독으로 이동하여 **IAM(액세스 제어)** 탭으로 이동하고 **추가** > **역할 할당 추가** 를 선택합니다

1. **역할 할당 추가** 창에서 **역할** 필드에 대해 **CosmosRestoreOperator** 역할을 선택합니다. 다음 이미지에 표시된 것처럼 **액세스 할당 대상** 필드에 대한 **사용자, 그룹 또는 서비스 주체** 를 선택하고 사용자의 이름 또는 메일 ID를 검색합니다.

   :::image type="content" source="./media/continuous-backup-restore-permissions/assign-restore-operator-roles.png" alt-text="CosmosRestoreOperator 및 Cosmos DB 운영자 역할을 할당합니다." border="true":::

1. **저장** 을 선택하여 *복원/작업* 권한을 부여합니다.

1. **Cosmos DB Operator** 역할을 사용해 3단계를 반복하여 쓰기 권한을 부여합니다. Azure Portal에서 이 역할을 할당할 때 전체 구독에 대한 복원 권한을 부여합니다.

## <a name="permission-scopes"></a>사용 권한 범위

|범위  |예제  |
|---------|---------|
|Subscription | /subscriptions/00000000-0000-0000-0000-000000000000 |
|Resource group | /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-cosmosdb-rg |
|CosmosDB 복원 가능한 계정 리소스 | /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/23e99a35-cd36-4df4-9614-f767a03b9995|

복원 가능한 계정 리소스는 `az cosmosdb restorable-database-account list --name <accountname>` CLI의 명령이나 PowerShell의 `Get-AzCosmosDBRestorableDatabaseAccount -DatabaseAccountName <accountname>` cmdlet의 출력에서 추출할 수 있습니다. 출력의 이름 특성은 복원 가능한 계정의 `instanceID`를 나타냅니다. 자세히 알아보려면 [PowerShell](continuous-backup-restore-powershell.md) 또는 [CLI](continuous-backup-restore-command-line.md) 문서를 참조하세요.

## <a name="permissions"></a>권한

지속적인 백업 모드 계정을 복원과 관련된 여러 작업을 수행하려면 다음 권한이 필요합니다.

|사용 권한  |영향  |최소 범위  |최대 범위  |
|---------|---------|---------|---------|
|`Microsoft.Resources/deployments/validate/action`, `Microsoft.Resources/deployments/write` | 이러한 권한은 ARM 템플릿 배포에서 복원된 계정을 만드는 데 필요합니다. 이 역할을 설정하는 방법은 아래의 샘플 권한 [RestorableAction](#custom-restorable-action)을 참조하세요. | 해당 없음 | 해당 없음  |
|`Microsoft.DocumentDB/databaseAccounts/write` | 계정을 리소스 그룹으로 복원하려면 이 권한이 필요합니다. | 복원된 계정이 만들어진 리소스 그룹입니다. | 복원된 계정이 만들어진 구독 |
|`Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action` |이 권한은 원본 복원이 가능한 데이터베이스 계정 범위에서 복원 동작을 수행할 수 있도록 하는 데 필요합니다.  | 복원 중인 원본 계정에 속하는 *RestorableDatabaseAccount* 리소스입니다. 이 값은 복원 가능한 데이터베이스 계정 리소스의 `ID` 속성에도 제공됩니다. 복원 가능한 계정의 예시 중 하나인 */subscriptions/subscriptionId/providers/Microsoft.DocumentDB/locations/regionName/restorableDatabaseAccounts/<guid-instanceid>* 입니다 | 복원 가능한 데이터베이스 계정이 포함된 구독입니다. 리소스 그룹을 범위로 선택할 수 없습니다.  |
|`Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read` |이 권한은 복원 가능한 원본 데이터베이스 계정 범위에서 복원할 수 있는 데이터베이스 계정을 나열하는 데 필요합니다.  | 복원 중인 원본 계정에 속하는 *RestorableDatabaseAccount* 리소스입니다. 이 값은 복원 가능한 데이터베이스 계정 리소스의 `ID` 속성에도 제공됩니다. 복원 가능한 계정의 예시 중 하나인 */subscriptions/subscriptionId/providers/Microsoft.DocumentDB/locations/regionName/restorableDatabaseAccounts/<guid-instanceid>* 입니다| 복원 가능한 데이터베이스 계정이 포함된 구독입니다. 리소스 그룹을 범위로 선택할 수 없습니다.  |
|`Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` | 이 권한은 원본 복원이 가능한 계정 범위에서 복원 가능한 계정에 대한 데이터베이스 및 컨테이너 목록과 같은 복원 가능한 리소스를 읽을 수 있도록 하는 데 필요합니다.  | 복원 중인 원본 계정에 속하는 *RestorableDatabaseAccount* 리소스입니다. 이 값은 복원 가능한 데이터베이스 계정 리소스의 `ID` 속성에도 제공됩니다. 복원 가능한 계정의 예시 중 하나인 */subscriptions/subscriptionId/providers/Microsoft.DocumentDB/locations/regionName/restorableDatabaseAccounts/<guid-instanceid>* 입니다| 복원 가능한 데이터베이스 계정이 포함된 구독입니다. 리소스 그룹을 범위로 선택할 수 없습니다. |

## <a name="azure-cli-role-assignment-scenarios-to-restore-at-different-scopes"></a>다른 범위에서 복원하는 Azure CLI 역할 할당 시나리오

구독 또는 지정된 계정 내에서 복원 작업을 수행할 수 있는 사용자를 세부적으로 제어하기 위해 사용 권한이 있는 역할을 다른 범위에 할당할 수 있습니다.

### <a name="assign-capability-to-restore-from-any-restorable-account-in-a-subscription"></a>구독 내의 모든 복원 가능한 계정에 복원 기능을 할당합니다

구독 수준에서 `CosmosRestoreOperator` 기본 제공 역할 할당

```azurecli-interactive
az role assignment create --role "CosmosRestoreOperator" --assignee <email> –scope /subscriptions/<subscriptionId>
```

### <a name="assign-capability-to-restore-from-a-specific-account"></a>특정 계정에서 복원 기능 할당

* 특정 리소스 그룹에 대한 사용자 쓰기 작업을 할당합니다. 이 작업은 리소스 그룹에 새 계정을 만드는 데 필요합니다.

* 복원해야 하는 특정 복원 가능한 데이터베이스 계정에 *CosmosRestoreOperator* 기본 제공 역할을 할당합니다. 다음 명령에서 *RestorableDatabaseAccount* 의 범위는 `az cosmosdb restorable-database-account`의 출력(CLI를 사용하는 경우) 또는 `Get-AzCosmosDBRestorableDatabaseAccount`(PowerShell을 사용하는 경우)의 `ID` 속성에서 검색됩니다.

  ```azurecli-interactive
   az role assignment create --role "CosmosRestoreOperator" --assignee <email> –scope <RestorableDatabaseAccount>
  ```

### <a name="assign-capability-to-restore-from-any-source-account-in-a-resource-group"></a>리소스 그룹의 모든 원본 계정에 복원 기능을 할당합니다.
이 작업은 현재 지원되지 않습니다.

## <a name="custom-role-creation-for-restore-action-with-cli"></a><a id="custom-restorable-action"></a>CLI를 사용한 복원 작업에 대한 사용자 지정 역할 만들기

구독 소유자는 다른 Azure AD ID로 복원할 수 있는 권한을 제공할 수 있습니다. 복원 권한은 `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action` 동작을 기반으로 하며, 이는 해당 복원 권한에 포함되어야 합니다. 이 역할을 포함하는 *CosmosRestoreOperator* 라는 기본 제공 역할이 있습니다. 이 기본 제공 역할을 사용하여 사용 권한을 할당하거나 사용자 지정 역할을 만들 수 있습니다.

아래의 RestorableAction는 사용자 지정 역할을 나타냅니다. 이 역할은 명시적으로 만들어야 합니다. 다음 JSON 템플릿은 복원 권한이 있는 *RestorableAction* 사용자 지정 역할을 만듭니다.

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

다음 템플릿 배포 명령을 사용하여 ARM 템플릿을 사용하는 복원 권한이 있는 역할을 만듭니다.

```azurecli-interactive
az role definition create --role-definition <JSON_Role_Definition_Path>
```

## <a name="next-steps"></a>다음 단계

* [Azure Portal](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md) 또는 [Azure Resource Manager](continuous-backup-restore-template.md)를 사용하여 지속적인 백업을 구성하고 관리합니다.
* [지속적인 백업 모드의 리소스 모델](continuous-backup-restore-resource-model.md)
