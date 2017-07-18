---
title: "Azure 리소스 정책 할당 및 관리 | Microsoft Docs"
description: "Azure 리소스 정책을 구독 및 리소스 그룹에 적용하고 리소스 정책을 확인하는 방법에 대해 설명합니다."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: e9a858addb768ce051fccce0eaf83e49a83da21b
ms.contentlocale: ko-kr
ms.lasthandoff: 06/30/2017


---
# <a name="assign-and-manage-resource-policies"></a>리소스 정책 할당 및 관리

정책을 구현하려면 다음 단계를 수행해야 합니다.

1. 정책 정의(Azure에서 제공하는 기본 제공 정책 포함)에서 사용자의 요구 사항을 충족하는 정책이 구독에 이미 있는지 확인합니다.
2. 있는 경우 이름을 가져옵니다.
3. 없는 경우 JSON으로 정책 규칙을 정의하고 사용자 구독에 정책 정의로 추가합니다. 이 단계를 사용하면 정책을 할당할 수 있지만 구독에 규칙을 적용하지 않습니다.
4. 어느 경우든, 정책을 범위(예: 구독 또는 리소스 그룹)에 할당합니다. 이제 정책의 규칙이 적용됩니다.

이 문서에서는 REST API, PowerShell 또는 Azure CLI를 통해 정책 정의를 만들고 범위에 해당 정의를 할당하는 단계에 중점을 둡니다. 포털을 사용해서 정책을 할당하려면 [Azure Portal을 사용하여 리소스 정책 할당 및 관리](resource-manager-policy-portal.md)를 참조하세요. 이 문서에서 정책 정의를 만드는 구문은 집중적으로 설명하지 않습니다. 정책 구문에 대한 정보는 [리소스 정책 개요](resource-manager-policy.md)를 참조하세요.

## <a name="rest-api"></a>REST API

### <a name="create-policy-definition"></a>정책 정의 만들기

[정책 정의에 대한 REST API](/rest/api/resources/policydefinitions)를 사용하여 정책을 만들 수 있습니다. REST API를 사용하여 정책 정의를 만들고, 삭제하고, 기존 정의에 관한 정보를 가져올 수 있습니다.

정책 정의를 만들려면 다음을 실행합니다.

```HTTP
PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}
```

다음 예제와 비슷한 요청 본문을 포함합니다.

```json
{
  "properties": {
    "parameters": {
      "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying resources",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
      }
    },
    "displayName": "Allowed locations",
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
    "policyRule": {
      "if": {
        "not": {
          "field": "location",
          "in": "[parameters('allowedLocations')]"
        }
      },
      "then": {
        "effect": "deny"
      }
    }
  }
}
```

### <a name="assign-policy"></a>정책 할당

[정책 할당에 대한 REST API](/rest/api/resources/policyassignments)를 통해 원하는 범위에 정책 정의를 적용할 수 있습니다. REST API를 사용하여 정책 할당을 만들고, 삭제하고, 기존 할당에 관한 정보를 가져올 수 있습니다.

정책 할당을 만들려면 다음과 같이 실행합니다.

```HTTP
PUT https://management.azure.com /subscriptions/{subscription-id}/providers/Microsoft.authorization/policyassignments/{policyAssignmentName}?api-version={api-version}
```

{policy-assignment}는 정책 할당의 이름입니다.

다음 예제와 비슷한 요청 본문을 포함합니다.

```json
{
  "properties":{
    "displayName":"West US only policy assignment on the subscription ",
    "description":"Resources can only be provisioned in West US regions",
    "parameters": {
      "allowedLocations": { "value": ["northeurope", "westus"] }
     },
    "policyDefinitionId":"/subscriptions/{subscription-id}/providers/Microsoft.Authorization/policyDefinitions/{definition-name}",
      "scope":"/subscriptions/{subscription-id}"
  },
}
```

### <a name="view-policy"></a>정책 보기
정책을 가져오려면 [정책 정의 가져오기](https://docs.microsoft.com/rest/api/resources/policydefinitions#PolicyDefinitions_Get) 작업을 사용합니다.

### <a name="get-aliases"></a>별칭 가져오기
REST API를 통해 별칭을 검색할 수 있습니다.

```HTTP
GET /subscriptions/{id}/providers?$expand=resourceTypes/aliases&api-version=2015-11-01
```

다음 예제는 별칭의 정의를 보여 줍니다. 여기에서 볼 수 있듯이 별칭은 속성 이름을 변경하는 경우에도 서로 다른 API 버전에 경로를 정의합니다. 

```json
"aliases": [
    {
      "name": "Microsoft.Storage/storageAccounts/sku.name",
      "paths": [
        {
          "path": "properties.accountType",
          "apiVersions": [
            "2015-06-15",
            "2015-05-01-preview"
          ]
        },
        {
          "path": "sku.name",
          "apiVersions": [
            "2016-01-01"
          ]
        }
      ]
    }
]
```

## <a name="powershell"></a>PowerShell

PowerShell 예제를 진행하기 전에 [최신 버전의 Azure PowerShell을 설치](/powershell/azure/install-azurerm-ps)했는지 확인합니다. 정책 매개 변수가 버전 3.6.0에 추가되었습니다. 이전 버전이 있는 경우 예제에서는 매개 변수를 찾을 수 없음을 나타내는 오류를 반환합니다.

### <a name="view-policy-definitions"></a>정책 정의 보기
구독에서 모든 정책 정의를 보려면 다음 명령을 사용합니다.

```powershell
Get-AzureRmPolicyDefinition
```

기본 제공 정책을 비롯한 사용 가능한 모든 정책 정의를 반환합니다. 각 정책은 다음 형식으로 반환됩니다.

```powershell
Name               : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceId         : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceName       : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceType       : Microsoft.Authorization/policyDefinitions
Properties         : @{displayName=Allowed locations; policyType=BuiltIn; description=This policy enables you to
                     restrict the locations your organization can specify when deploying resources. Use to enforce
                     your geo-compliance requirements.; parameters=; policyRule=}
PolicyDefinitionId : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
```

정책 정의 만들기를 진행하기 전에 기본 제공 정책을 확인합니다. 필요한 한도를 적용하는 기본 제공 정책을 찾을 경우 정책 정의 만들기를 건너뛸 수 있습니다. 대신, 기본 제공 정책을 원하는 범위에 할당합니다.

### <a name="create-policy-definition"></a>정책 정의 만들기
`New-AzureRmPolicyDefinition` cmdlet을 사용하여 정책 정의를 만들 수 있습니다.

```powershell
$policy = New-AzureRmPolicyDefinition -Name coolAccessTier -Description "Policy to specify access tier." -Policy '{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}'
```            

출력 `$policy` 개체에 저장되어 정책 할당 중에 사용됩니다. 

JSON을 매개 변수로 지정하지 않고 정책 규칙을 포함하는 .json 파일의 경로를 제공할 수 있습니다.

```powershell
$policy = New-AzureRmPolicyDefinition -Name coolAccessTier -Description "Policy to specify access tier." -Policy "c:\policies\coolAccessTier.json"
```

### <a name="assign-policy"></a>정책 할당

`New-AzureRmPolicyAssignment` cmdlet을 사용하여 원하는 범위에서 정책을 적용합니다. 다음 예제에서는 리소스 그룹에 정책을 할당합니다.

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
New-AzureRMPolicyAssignment -Name accessTierAssignment -Scope $rg.ResourceId -PolicyDefinition $policy
```

매개 변수를 요구하는 정책을 할당하려면 해당 값으로 개체를 만듭니다. 다음 예제에서는 기본 제공 정책을 검색하고 매개 변수 값에 전달합니다.

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
$policy = Get-AzureRmPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/e5662a6-4747-49cd-b67b-bf8b01975c4c
$array = @("West US", "West US 2")
$param = @{"listOfAllowedLocations"=$array}
New-AzureRMPolicyAssignment -Name locationAssignment -Scope $rg.ResourceId -PolicyDefinition $policy -PolicyParameterObject $param
```

### <a name="view-policy-assignment"></a>정책 할당 보기

특정 정책 할당을 가져오려면 다음을 사용합니다.

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
(Get-AzureRmPolicyAssignment -Name accessTierAssignment -Scope $rg.ResourceId
```

정책 정의에 대한 정책 규칙을 보려면 다음을 사용합니다.

```powershell
(Get-AzureRmPolicyDefinition -Name coolAccessTier).Properties.policyRule | ConvertTo-Json
```

### <a name="remove-policy-assignment"></a>정책 할당 제거 

정책 할당을 제거하려면 다음을 사용합니다.

```powershell
Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="azure-cli"></a>Azure CLI

### <a name="view-policy-definitions"></a>정책 정의 보기
구독에서 모든 정책 정의를 보려면 다음 명령을 사용합니다.

```azurecli
az policy definition list
```

기본 제공 정책을 비롯한 사용 가능한 모든 정책 정의를 반환합니다. 각 정책은 다음 형식으로 반환됩니다.

```azurecli
{                                                            
  "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",                      
  "displayName": "Allowed locations",                                                                                                                "id": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",                                                 "name": "e56962a6-4747-49cd-b67b-bf8b01975c4c",                                                                                                    "policyRule": {                                                                                                                                      "if": {                                                                                                                                              "not": {                                                                                                                                             "field": "location",                                                                                                                               "in": "[parameters('listOfAllowedLocations')]"                                                                                                   }                                                                                                                                                },                                                                                                                                                 "then": {                                                                                                                                            "effect": "Deny"                                                                                                                                 }                                                                                                                                                },                                                                                                                                                 "policyType": "BuiltIn"
}
```

정책 정의 만들기를 진행하기 전에 기본 제공 정책을 확인합니다. 필요한 한도를 적용하는 기본 제공 정책을 찾을 경우 정책 정의 만들기를 건너뛸 수 있습니다. 대신, 기본 제공 정책을 원하는 범위에 할당합니다.

### <a name="create-policy-definition"></a>정책 정의 만들기

정책 정의 명령과 함께 Azure CLI를 사용하여 정책 정의를 만들 수 있습니다.

```azurecli
az policy definition create --name coolAccessTier --description "Policy to specify access tier." --rules '{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}'    
```

### <a name="assign-policy"></a>정책 할당

정책 할당 명령을 사용하여 정책을 원하는 범위에 적용할 수 있습니다. 다음 예제에서는 리소스 그룹에 정책을 할당합니다.

```azurecli
az policy assignment create --name coolAccessTierAssignment --policy coolAccessTier --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

### <a name="view-policy-assignment"></a>정책 할당 보기

정책 할당을 보려면 정책 할당 이름 및 범위를 제공합니다.

```azurecli
az policy assignment show --name coolAccessTierAssignment --scope "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}"
```

### <a name="remove-policy-assignment"></a>정책 할당 제거 

정책 할당을 제거하려면 다음을 사용합니다.

```azurecli
az policy assignment delete --name coolAccessTier --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="next-steps"></a>다음 단계
* 엔터프라이즈에서 리소스 관리자를 사용하여 구독을 효과적으로 관리할 수 있는 방법에 대한 지침은 [Azure 엔터프라이즈 스캐폴드 - 규범적 구독 거버넌스](resource-manager-subscription-governance.md)를 참조하세요.


