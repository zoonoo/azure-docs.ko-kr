---
title: Azure Cosmos DB의 역할 기반 액세스 제어
description: Azure Cosmos DB RBAC (Active directory 통합)를 사용 하 여 데이터베이스 보호를 제공 하는 방법에 대해 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: mjbrown
ms.openlocfilehash: c9a29a34b25b6834c2c343bd568f3dd3548ceff3
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92474940"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Azure Cosmos DB의 역할 기반 액세스 제어

Azure Cosmos DB은 Azure Cosmos DB의 일반적인 관리 시나리오에 대 한 기본 제공 RBAC (역할 기반 액세스 제어)를 제공 합니다. Azure Active Directory에 프로필이 있는 개인은 이러한 Azure 역할을 사용자, 그룹, 서비스 주체 또는 관리 되는 id에 할당 하 여 리소스에 대 한 액세스를 부여 하거나 거부 하 고 Azure Cosmos DB 리소스에 대 한 작업을 수행할 수 있습니다. 역할 할당은 제어 평면 액세스 전용으로 범위가 지정 됩니다. 여기에는 Azure Cosmos 계정, 데이터베이스, 컨테이너 및 제안 (처리량)에 대 한 액세스가 포함 됩니다.

## <a name="built-in-roles"></a>기본 제공 역할

Azure Cosmos DB에서 지 원하는 기본 제공 역할은 다음과 같습니다.

|**기본 제공 역할**  |**설명**  |
|---------|---------|
|[DocumentDB 계정 기여자](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Azure Cosmos DB 계정을 관리할 수 있습니다.|
|[Cosmos DB 계정 판독기](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Azure Cosmos DB 계정 데이터를 읽을 수 있음.|
|[Cosmos Backup 운영자](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)|Azure Cosmos 데이터베이스 또는 컨테이너에 대 한 복원 요청을 제출할 수 있습니다. 데이터에 액세스 하거나 데이터 탐색기를 사용할 수 없습니다.|
|[Cosmos DB 운영자](../role-based-access-control/built-in-roles.md#cosmos-db-operator)|Azure Cosmos 계정, 데이터베이스 및 컨테이너를 프로 비전 할 수 있습니다. 데이터에 액세스 하거나 데이터 탐색기를 사용할 수 없습니다.|

> [!IMPORTANT]
> Azure Cosmos DB의 RBAC 지원은 제어 평면 작업에만 적용 됩니다. 데이터 평면 작업은 기본 키 또는 리소스 토큰을 사용 하 여 보호 됩니다. 자세한 내용은 [Azure Cosmos DB의 데이터에 안전](secure-access-to-data.md) 하 게 액세스를 참조 하세요.

## <a name="identity-and-access-management-iam"></a>Id 및 액세스 관리 (IAM)

Azure Portal의 **액세스 제어 (IAM)** 창은 Azure Cosmos 리소스에 대 한 역할 기반 액세스 제어를 구성 하는 데 사용 됩니다. 역할은 Active Directory의 사용자, 그룹, 서비스 사용자 및 관리 되는 id에 적용 됩니다. 개인 및 그룹에 대 한 기본 제공 역할 또는 사용자 지정 역할을 사용할 수 있습니다. 다음 스크린샷은 Azure Portal의 IAM (access control)을 사용한 RBAC (Active Directory 통합)를 보여 줍니다.

:::image type="content" source="./media/role-based-access-control/database-security-identity-access-management-rbac.png" alt-text="Azure Portal에서 액세스 제어(IAM) - 데이터베이스 보안 설명":::

## <a name="custom-roles"></a>사용자 지정 역할

사용자는 기본 제공 역할 외에도 Azure에서 [사용자 지정 역할](../role-based-access-control/custom-roles.md) 을 만들고 해당 Active Directory 테 넌 트 내의 모든 구독에서 서비스 주체에 이러한 역할을 적용할 수 있습니다. 사용자 지정 역할은 사용자가 리소스 공급자 작업의 사용자 지정 집합을 사용 하 여 Azure 역할 정의를 만드는 방법을 제공 합니다. Azure Cosmos DB에 대 한 사용자 지정 역할을 빌드하는 데 사용할 수 있는 작업에 대 한 자세한 내용은 [Azure Cosmos DB 리소스 공급자 작업](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb) 을 참조 하세요.

> [!TIP]
> Cosmos DB 내에 저장 된 데이터에 액세스 하거나 Azure Portal에서 데이터 탐색기를 사용 해야 하는 사용자 지정 역할은 작업을 수행 해야 합니다 `Microsoft.DocumentDB/databaseAccounts/listKeys/*` .

## <a name="preventing-changes-from-the-azure-cosmos-db-sdks"></a><a id="prevent-sdk-changes"></a>Azure Cosmos DB Sdk에서 변경 방지

계정 키를 사용 하 여 연결 하는 클라이언트에서 리소스를 변경 하는 것을 방지 하기 위해 Azure Cosmos DB 리소스 공급자를 잠글 수 있습니다. 즉, Azure Cosmos SDK를 통해 연결 하는 응용 프로그램입니다. 여기에는 Azure Portal에서 수행 된 변경 내용도 포함 됩니다. 이 기능은 프로덕션 환경에 대 한 제어 및 관리를 더 많이 원하는 사용자에 게 적합할 수 있습니다. SDK를 변경 하지 못하게 하면 제어 평면 작업에 대 한 리소스 잠금 및 진단 로그와 같은 기능을 사용할 수 있습니다. Azure Cosmos DB SDK에서 연결 하는 클라이언트는 Azure Cosmos 계정, 데이터베이스, 컨테이너 및 처리량에 대 한 속성을 변경할 수 없습니다. Cosmos 컨테이너 자체에 대 한 데이터 읽기 및 쓰기와 관련 된 작업은 영향을 받지 않습니다.

이 기능을 사용 하도록 설정 하면 올바른 Azure 역할이 있는 사용자만 리소스를 변경할 수 있으며 관리 되는 서비스 Id를 비롯 한 Azure Active Directory 자격 증명을 사용할 수 있습니다.

> [!WARNING]
> 이 기능을 사용 하도록 설정 하면 응용 프로그램에 영향을 줄 수 있습니다. 사용 하도록 설정 하기 전에 영향을 이해 해야 합니다.

### <a name="check-list-before-enabling"></a>활성화 하기 전에 목록 확인

이 설정은 Cosmos DB SDK, 계정 키를 통해 연결 된 모든 도구 또는 Azure Portal을 포함 하 여 계정 키를 사용 하 여 연결 하는 모든 클라이언트에서 Cosmos 리소스를 변경 하는 것을 방지 합니다. 이 기능을 사용 하도록 설정한 후 응용 프로그램의 문제 또는 오류를 방지 하려면 다음을 포함 하 여이 기능을 활성화 하기 전에 응용 프로그램 또는 Azure Portal 사용자가 다음 작업 중 하나를 수행 하는지 확인 합니다.

- 모든 속성을 포함 하거나 지역을 추가 하거나 제거 하는 Cosmos 계정에 대 한 변경

- 데이터베이스 및 컨테이너와 같은 자식 리소스를 만들고 삭제 합니다. 여기에는 Cassandra, MongoDB, Gremlin 및 table 리소스와 같은 다른 Api에 대 한 리소스가 포함 됩니다.

- 데이터베이스 또는 컨테이너 수준 리소스에 대 한 처리량 업데이트

- 인덱스 정책, TTL 및 고유 키를 비롯 한 컨테이너 속성을 수정 합니다.

- 저장 프로시저, 트리거 또는 사용자 정의 함수 수정

응용 프로그램 (또는 Azure Portal를 통한 사용자)이 이러한 작업을 수행 하는 경우 [ARM 템플릿](./manage-with-templates.md), [PowerShell](manage-with-powershell.md), [Azure CLI](manage-with-cli.md), REST 또는 [Azure 관리 라이브러리](https://github.com/Azure-Samples/cosmos-management-net)를 통해 실행 하려면 마이그레이션해야 합니다. Azure Management는 [여러 언어로](/azure/?product=featured#languages-and-tools)제공 됩니다.

### <a name="set-via-arm-template"></a>ARM 템플릿을 통해 설정

ARM 템플릿을 사용 하 여이 속성을 설정 하려면 기존 템플릿을 업데이트 하거나 현재 배포에 대 한 새 템플릿을 내보낸 다음 리소스의 속성에를 포함 합니다 `"disableKeyBasedMetadataWriteAccess": true` `databaseAccounts` . 다음은이 속성 설정을 사용 하는 Azure Resource Manager 템플릿의 기본 예입니다.

```json
{
    {
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "name": "[variables('accountName')]",
      "apiVersion": "2020-04-01",
      "location": "[parameters('location')]",
      "kind": "GlobalDocumentDB",
      "properties": {
        "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
        "locations": "[variables('locations')]",
        "databaseAccountOfferType": "Standard",
        "disableKeyBasedMetadataWriteAccess": true
        }
    }
}
```

> [!IMPORTANT]
> 이 속성을 사용 하 여 redploying 때 계정 및 자식 리소스에 대 한 다른 속성을 포함 해야 합니다. 이 템플릿을 그대로 배포 하거나 모든 계정 속성을 다시 설정 합니다.

### <a name="set-via-azure-cli"></a>Azure CLI를 통해 설정

Azure CLI를 사용 하도록 설정 하려면 아래 명령을 사용 합니다.

```azurecli-interactive
az cosmosdb update  --name [CosmosDBAccountName] --resource-group [ResourceGroupName]  --disable-key-based-metadata-write-access true

```

### <a name="set-via-powershell"></a>PowerShell을 통해 설정

Azure PowerShell를 사용 하도록 설정 하려면 아래 명령을 사용 합니다.

```azurepowershell-interactive
Update-AzCosmosDBAccount -ResourceGroupName [ResourceGroupName] -Name [CosmosDBAccountName] -DisableKeyBasedMetadataWriteAccess true
```

## <a name="next-steps"></a>다음 단계

- [Azure 역할 기반 access control (Azure RBAC) 이란?](../role-based-access-control/overview.md)
- [Azure 사용자 지정 역할](../role-based-access-control/custom-roles.md)
- [Azure Cosmos DB 리소스 공급자 작업](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)