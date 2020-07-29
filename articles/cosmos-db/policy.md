---
title: Azure Policy를 사용하여 Azure Cosmos DB 리소스에 대한 거버넌스 및 제어 구현
description: Azure Policy를 사용하여 Azure Cosmos DB 리소스에 대한 거버넌스 및 제어를 구현하는 방법을 알아봅니다.
author: plzm
ms.author: paelaz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: a1b1c01f7cf720690decd9c7aac5fb14b92121ec
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84431997"
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

다음은 Azure Cosmos DB 계정이 여러 쓰기 위치에 대해 구성 되었는지 확인 하는 정책 정의의 예입니다. 사용자 지정 정책 정의에는 두 가지 규칙이 포함 되어 있습니다. 하나는 특정 유형의 속성 별칭을 확인 하 고, 다른 하나는 유형의 특정 속성 (이 경우에는 다중 쓰기 위치 설정을 저장 하는 필드)에 대 한 규칙입니다. 두 규칙 모두 별칭 이름을 사용합니다.

```json
"policyRule": {
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.DocumentDB/databaseAccounts"
      },
      {
        "field": "Microsoft.DocumentDB/databaseAccounts/enableMultipleWriteLocations",
        "notEquals": true
      }
    ]
  },
  "then": {
    "effect": "Audit"
  }
}
```

기본 제공 정책 정의를 사용하는 것처럼 사용자 지정 정책 정의를 사용하여 정책 할당을 만들 수 있습니다.

## <a name="policy-compliance"></a>정책 준수

정책 할당을 만든 후에는 Azure Policy 할당 범위에서 리소스를 평가합니다. 각 리소스의 정책 준수가 평가됩니다. 그런 다음 정책에 지정된 효과가 미준수 리소스에 적용됩니다.

[Azure Portal](../governance/policy/how-to/get-compliance-data.md#portal)에서 또는 [Azure CLI](../governance/policy/how-to/get-compliance-data.md#command-line) 또는 [Azure Monitor 로그](../governance/policy/how-to/get-compliance-data.md#azure-monitor-logs)를 통해 준수 결과 및 수정 세부 정보를 검토할 수 있습니다.

다음 스크린샷에서는 두 가지 정책 할당 예제를 보여 줍니다.

한 할당은 기본 제공 정책 정의를 기반으로 하며, Azure Cosmos DB 리소스가 허용되는 Azure 지역에만 배포되는지 확인합니다. 리소스 준수는 범위 내 리소스에 대해 정책 평가 결과 (호환 또는 비호환)를 표시 합니다.

다른 할당은 사용자 지정 정책 정의를 기반으로 합니다. 이 할당은 Cosmos DB 계정이 여러 쓰기 위치에 대해 구성 되었는지 확인 합니다.

정책 할당이 배포된 후 준수 대시보드에 평가 결과가 표시됩니다. 이 결과가 표시되려면 정책 할당 배포 후 최대 30분이 걸릴 수 있습니다. 또한 정책 할당을 만든 후 즉시 [요청 시 정책 평가 검색을 시작할 수 있습니다](../governance/policy/how-to/get-compliance-data.md#on-demand-evaluation-scan) .

스크린샷에서는 범위 내 Azure Cosmos DB 계정에 대 한 다음 준수 평가 결과를 보여 줍니다.

- 두 계정 중 0은 VNet (Virtual Network) 필터링을 구성 해야 하는 정책을 준수 합니다.
- 계정이 여러 쓰기 위치에 대해 구성 되어야 하는 정책을 준수 하는 두 계정의 0
- 두 계정 중 0은 리소스가 허용 된 Azure 지역에 배포 된 정책을 준수 합니다.

:::image type="content" source="./media/policy/compliance.png" alt-text="나열 된 Azure Policy 할당에 대 한 호환성 결과":::

비준수 리소스를 재구성 하려면 Azure Policy를 사용 하 [여 리소스](../governance/policy/how-to/remediate-resources.md)를 관리 하는 방법을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- 위에 표시 된 다중 쓰기 위치 및 VNet 필터링 정책을 포함 하 여 [Azure Cosmos DB에 대 한 샘플 사용자 지정 정책 정의를 검토](https://github.com/Azure/azure-policy/tree/master/samples/CosmosDB)합니다.
- [Azure Portal에서 정책 할당 만들기](../governance/policy/assign-policy-portal.md)
- [Azure Cosmos DB용 Azure Policy 기본 제공 정책 정의 검토](./policy-samples.md)
