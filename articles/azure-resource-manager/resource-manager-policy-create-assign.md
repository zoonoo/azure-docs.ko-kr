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
ms.date: 02/10/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 138197bab775ec854f2761a64c52a9a9c8baca60
ms.openlocfilehash: dec7ebd834514a10d02d739264b03b01a65a4375


---
# <a name="assign-and-manage-resource-policies"></a>리소스 정책 할당 및 관리

정책을 구현하려면 다음과 같은 세 가지 단계를 수행해야 합니다.

1. 정책 규칙을 JSON으로 정의합니다.
2. 이전 단계에서 만든 JSON의 구독에서 정책 정의를 만듭니다. 이 단계를 사용하면 정책을 할당할 수 있지만 구독에 규칙을 적용하지 않습니다.
3. 정책을 범위(예: 구독 또는 리소스 그룹)에 할당합니다. 이제 정책의 규칙이 적용됩니다.

Azure에서는 몇 가지 미리 정의된 정책을 제공하여 정의해야 하는 정책의 수를 줄일 수 있습니다. 미리 정의된 정책이 시나리오에서 작동하는 경우 처음 두 단계를 건너뛰고 미리 정의된 정책을 범위에 할당합니다.

이 항목에서는 정책 정의를 만들고 범위에 해당 정의를 할당하는 단계에 중점을 둡니다. 정책 정의를 만드는 구문에 집중하지 않습니다. 정책 구문에 대한 정보는 [리소스 정책 개요](resource-manager-policy.md)를 참조하세요.

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
      "listOfAllowedLocations": { "value": ["West US", "West US 2"] }
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

### <a name="create-policy-definition"></a>정책 정의 만들기
`New-AzureRmPolicyDefinition` cmdlet을 사용하여 정책 정의를 만들 수 있습니다. 아래 예제에서는 유럽 북부와 유럽 서부에서만 리소스를 허용하는 정책 정의를 만듭니다.

```powershell
$policy = New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain regions" -Policy '{    
  "if" : {
    "not" : {
      "field" : "location",
      "in" : ["northeurope" , "westeurope"]
    }
  },
  "then" : {
    "effect" : "deny"
  }
}'
```            

출력 `$policy` 개체에 저장되어 정책 할당 중에 사용됩니다. 

JSON을 매개 변수로 지정하지 않고 정책 규칙을 포함하는 .json 파일의 경로를 제공할 수 있습니다.

```powershell
$policy = New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain regions" -Policy "c:\policies\storageskupolicy.json"
```

### <a name="assign-policy"></a>정책 할당

`New-AzureRmPolicyAssignment` cmdlet을 사용하여 원하는 범위에서 정책을 적용합니다.

```powershell
New-AzureRmPolicyAssignment -Name regionPolicyAssignment -PolicyDefinition $policy -Scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

### <a name="view-policy-assignment"></a>정책 할당 보기

정책을 가져오려면 다음 cmdlet을 사용합니다.

```powershell
(Get-AzureRmPolicyAssignment -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/{definition-name}").Properties.policyRule | ConvertTo-Json
```

정책 정의에 대한 JSON이 반환됩니다.

### <a name="remove-policy-assignment"></a>정책 할당 제거 

정책 할당을 제거하려면 다음을 사용합니다.

```powershell
Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="azure-cli-20-preview"></a>Azure CLI 2.0(미리 보기)

### <a name="create-policy-definition"></a>정책 정의 만들기

정책 정의 명령과 함께 Azure CLI 2.0(미리 보기)를 사용하여 정책 정의를 만들 수 있습니다. 아래 예제에서는 유럽 북부와 유럽 서부에서만 리소스를 허용하는 정책을 만듭니다.

```azurecli
az policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --rules '{    
  "if" : {
    "not" : {
      "field" : "location",
      "in" : ["northeurope" , "westeurope"]
    }
  },
  "then" : {
    "effect" : "deny"
  }
}'    
```

### <a name="assign-policy"></a>정책 할당

정책 할당 명령을 사용하여 정책을 원하는 범위에 적용할 수 있습니다.

```azurecli
az policy assignment create --name regionPolicyAssignment --policy regionPolicyDefinition --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

### <a name="view-policy-definition"></a>정책 정의 보기
정책 정의를 가져오려면 다음 명령을 사용합니다.

```azurecli
az policy definition show --name regionPolicyAssignment
```

### <a name="remove-policy-assignment"></a>정책 할당 제거 

정책 할당을 제거하려면 다음을 사용합니다.

```azurecli
az policy assignment delete --name regionPolicyAssignment --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="azure-cli-10"></a>Azure CLI 1.0

### <a name="create-policy-definition"></a>정책 정의 만들기

정책 정의 명령과 함께 Azure CLI를 사용하여 정책 정의를 만들 수 있습니다. 아래 예제에서는 유럽 북부와 유럽 서부에서만 리소스를 허용하는 정책을 만듭니다.

```azurecli
azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy-string '{    
  "if" : {
    "not" : {
      "field" : "location",
      "in" : ["northeurope" , "westeurope"]
    }
  },
  "then" : {
    "effect" : "deny"
  }
}'    
```

정책 인라인을 지정하지 않고 정책이 포함된 .json 파일에 대한 경로를 지정할 수 있습니다.

```azurecli
azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy "path-to-policy-json-on-disk"
```

### <a name="assign-policy"></a>정책 할당

정책 할당 명령을 사용하여 정책을 원하는 범위에 적용할 수 있습니다.

```azurecli
azure policy assignment create --name regionPolicyAssignment --policy-definition-id /subscriptions/{subscription-id}/providers/Microsoft.Authorization/policyDefinitions/{policy-name} --scope    /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

여기서 범위는 지정하는 리소스 그룹의 이름입니다. 매개 변수 정책 정의 ID 값을 알 수 없는 경우 Azure CLI를 통해 얻을 수 있습니다. 

```azurecli
azure policy definition show {policy-name}
```

### <a name="view-policy"></a>정책 보기
정책을 가져오려면 다음 명령을 사용합니다.

```azurecli
azure policy definition show {definition-name} --json
```

### <a name="remove-policy-assignment"></a>정책 할당 제거 

정책 할당을 제거하려면 다음을 사용합니다.

```azurecli
azure policy assignment delete --name regionPolicyAssignment --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="next-steps"></a>다음 단계
* 엔터프라이즈에서 리소스 관리자를 사용하여 구독을 효과적으로 관리할 수 있는 방법에 대한 지침은 [Azure 엔터프라이즈 스캐폴드 - 규범적 구독 거버넌스](resource-manager-subscription-governance.md)를 참조하세요.




<!--HONumber=Feb17_HO3-->


