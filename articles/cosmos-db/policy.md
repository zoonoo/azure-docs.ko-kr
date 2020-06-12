---
title: Azure Policy를 사용하여 Azure Cosmos DB 리소스에 대한 거버넌스 및 제어 구현
description: Azure Policy를 사용하여 Azure Cosmos DB 리소스에 대한 거버넌스 및 제어를 구현하는 방법을 알아봅니다.
author: plzm
ms.author: paelaz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 2249dbdebecc52a8f5d6decccb83d3b1fc0777f7
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83747370"
---
# <a name="use-azure-policy-to-implement-governance-and-controls-for-azure-cosmos-db-resources"></a>Azure Policy를 사용하여 Azure Cosmos DB 리소스에 대한 거버넌스 및 제어 구현

[Azure Policy](../governance/policy/overview.md)는 조직의 거버넌스 표준을 적용하고, 리소스 호환성을 평가하고, 자동 수정을 구현하는 데 도움이 됩니다. 일반적인 사용 사례에는 보안, 비용 관리, 구성 일관성이 포함됩니다.

Azure Policy는 기본 제공 정책 정의를 제공합니다. 기본 제공 정책 정의로 해결되지 않는 시나리오에 대해서는 사용자 지정 정책 정의를 만들 수 있습니다. 자세한 내용은 [Azure Policy 설명서](../governance/policy/overview.md)를 참조하세요.

## <a name="assign-a-built-in-policy-definition"></a>기본 제공 정책 정의 할당

정책 정의는 리소스 준수 조건 및 조건 충족 시 영향을 설명합니다. 정책 할당은 정책 정의에서 만듭니다. Azure Cosmos DB 리소스에 대해 기본 제공 또는 사용자 지정 정책 정의를 사용할 수 있습니다. 정책 할당은 Azure 관리 그룹, Azure 구독 또는 리소스 그룹으로 범위가 지정되고, 선택된 범위 내의 리소스에 적용됩니다. 필요에 따라 범위에서 특정 리소스를 제외할 수 있습니다.

[Azure Portal](../governance/policy/assign-policy-portal.md), [Azure PowerShell](../governance/policy/assign-policy-powershell.md), [Azure CLI](../governance/policy/assign-policy-azurecli.md) 또는 [ARM 템플릿](../governance/policy/assign-policy-template.md)을 사용하여 정책 할당을 만들 수 있습니다.

Azure Cosmos DB에 대한 기본 제공 정책 정의에서 정책 할당을 만들려면 [Azure Portal을 사용하여 정책 할당 만들기](../governance/policy/assign-policy-portal.md) 문서의 단계를 사용합니다.

정책 정의를 선택하는 단계에서 검색 필드에 `Cosmos DB`를 입력하여 사용 가능한 기본 제공 정책 정의 목록을 필터링합니다. 사용 가능한 기본 제공 정책 정의 중 하나를 선택한 다음 **선택**을 선택하여 정책 할당을 계속 만듭니다.

> [!TIP]
> Azure PowerShell, Azure CLI 또는 ARM 템플릿을 사용하여 **사용 가능한 정의** 창에 표시된 기본 제공 정책 정의 이름을 사용하여 정책 할당을 만들 수도 있습니다.

:::image type="content" source="./media/policy/available-definitions.png" alt-text="Azure Cosmos DB 기본 제공 정책 정의 검색":::

## <a name="create-a-custom-policy-definition"></a>사용자 지정 정책 정의 만들기

기본 제공 정책으로 해결되지 않는 특정 시나리오의 경우 [사용자 지정 정책 정의](../governance/policy/tutorials/create-custom-policy-definition.md)를 만들 수 있습니다. 나중에 사용자 지정 정책 정의에서 정책 할당을 만듭니다.

### <a name="property-types-and-property-aliases-in-policy-rules"></a>정책 규칙의 속성 형식 및 속성 별칭

[사용자 지정 정책 정의 단계](../governance/policy/tutorials/create-custom-policy-definition.md)를 사용하여 정책 규칙을 만드는 데 필요한 리소스 속성 및 속성 별칭을 식별할 수 있습니다.

Azure Cosmos DB 고유 속성 별칭을 식별하려면 사용자 지정 정책 정의 단계 문서에 나와 있는 방법 중 하나를 통해 네임스페이스 `Microsoft.DocumentDB`를 사용합니다.

#### <a name="use-the-azure-cli"></a>Azure CLI 사용:
```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Get Azure Policy aliases for namespace Microsoft.DocumentDB
az provider show --namespace Microsoft.DocumentDB --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
```

#### <a name="use-azure-powershell"></a>Azure PowerShell 사용:
```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Use Get-AzPolicyAlias to list aliases for Microsoft.DocumentDB namespace
(Get-AzPolicyAlias -NamespaceMatch 'Microsoft.DocumentDB').Aliases
```

이러한 명령은 Azure Cosmos DB 속성에 대한 속성 별칭 이름 목록을 출력합니다. 다음은 출력에서 발췌한 것입니다.

```json
[
  "Microsoft.DocumentDB/databaseAccounts/sku.name",
  "Microsoft.DocumentDB/databaseAccounts/virtualNetworkRules[*]",
  "Microsoft.DocumentDB/databaseAccounts/virtualNetworkRules[*].id",
  "Microsoft.DocumentDB/databaseAccounts/isVirtualNetworkFilterEnabled",
  "Microsoft.DocumentDB/databaseAccounts/consistencyPolicy.defaultConsistencyLevel",
  "Microsoft.DocumentDB/databaseAccounts/enableAutomaticFailover",
  "Microsoft.DocumentDB/databaseAccounts/Locations",
  "Microsoft.DocumentDB/databaseAccounts/Locations[*]",
  "Microsoft.DocumentDB/databaseAccounts/Locations[*].locationName",
  "..."
]
```

[사용자 지정 정책 정의 규칙](../governance/policy/tutorials/create-custom-policy-definition.md#policy-rule)에 이러한 속성 별칭 이름을 모두 사용할 수 있습니다.

다음은 Azure Cosmos DB SQL 데이터베이스의 프로비전된 처리량이 허용되는 최대 제한인 400RU/s보다 큰지 확인하는 정책 정의의 예입니다. 사용자 지정 정책 정의에는 두 가지 규칙이 포함됩니다. 하나는 속성 별칭이 특정 형식인지 확인하는 규칙이고, 다른 하나는 형식이 특정 속성인지 확인하는 규칙입니다. 두 규칙 모두 별칭 이름을 사용합니다.

```json
"policyRule": {
  "if": {
    "allOf": [
      {
      "field": "type",
      "equals": "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/throughputSettings"
      },
      {
      "field": "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/throughputSettings/default.resource.throughput",
      "greater": 400
      }
    ]
  }
}
```

기본 제공 정책 정의를 사용하는 것처럼 사용자 지정 정책 정의를 사용하여 정책 할당을 만들 수 있습니다.

## <a name="policy-compliance"></a>정책 준수

정책 할당을 만든 후에는 Azure Policy 할당 범위에서 리소스를 평가합니다. 각 리소스의 정책 준수가 평가됩니다. 그런 다음 정책에 지정된 효과가 미준수 리소스에 적용됩니다.

[Azure Portal](../governance/policy/how-to/get-compliance-data.md#portal)에서 또는 [Azure CLI](../governance/policy/how-to/get-compliance-data.md#command-line) 또는 [Azure Monitor 로그](../governance/policy/how-to/get-compliance-data.md#azure-monitor-logs)를 통해 준수 결과 및 수정 세부 정보를 검토할 수 있습니다.

다음 스크린샷에서는 두 가지 정책 할당 예제를 보여 줍니다. 한 할당은 기본 제공 정책 정의를 기반으로 하며, Azure Cosmos DB 리소스가 허용되는 Azure 지역에만 배포되는지 확인합니다. 다른 할당은 사용자 지정 정책 정의를 기반으로 합니다. 이 할당은 Azure Cosmos DB 리소스의 프로비전된 처리량이 지정된 최대 제한을 초과하지 않는지 확인합니다.

정책 할당이 배포된 후 준수 대시보드에 평가 결과가 표시됩니다. 이 결과가 표시되려면 정책 할당 배포 후 최대 30분이 걸릴 수 있습니다.

스크린샷에는 다음과 같은 규정 준수 평가 결과가 나와 있습니다.

- 지정된 범위의 Azure Cosmos DB 계정 1개 중 0개가 리소스를 허용된 지역에 배포했는지 확인하기 위한 정책 할당을 준수합니다.
- 지정된 범위의 Azure Cosmos DB 계정 2개 중 1개가 프로비전된 처리량이 지정된 최대 제한을 초과하는지 확인하기 위한 정책 할당을 준수합니다.

:::image type="content" source="./media/policy/compliance.png" alt-text="Azure Cosmos DB 기본 제공 정책 정의 검색":::

비준수 리소스를 수정하려면 [Azure Policy로 재구성된](../governance/policy/how-to/remediate-resources.md) 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB용 샘플 사용자 지정 정책 정의 검토](https://github.com/Azure/azure-policy/tree/master/samples/CosmosDB)
- [Azure Portal에서 정책 할당 만들기](../governance/policy/assign-policy-portal.md)
- [Azure Cosmos DB용 Azure Policy 기본 제공 정책 정의 검토](./policy-samples.md)
