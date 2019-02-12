---
title: REST API를 사용하여 청사진 만들기
description: Azure Blueprint를 사용하여 아티팩트를 만들고 정의하고 배포할 수 있습니다.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/01/2019
ms.topic: quickstart
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 78ce7c1063623e0c002bb6084d8c18139b3f889f
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2019
ms.locfileid: "55566975"
---
# <a name="define-and-assign-an-azure-blueprint-with-rest-api"></a>REST API로 Azure Blueprint 정의 및 할당

청사진을 만들고 할당하는 방법을 알면 공통 패턴 정의를 통해 Resource Manager 템플릿, 정책, 보안 등을 기반으로 재사용이 가능하고 신속하게 배포할 수 있는 구성을 개발할 수 있습니다. 이 자습서에서는 Azure Blueprint를 사용하여 조직 내에서 청사진을 작성, 게시 및 할당하는 것과 관련된 다음과 같은 일반적인 작업을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
> - 새 청사진을 만들고 지원되는 다양한 아티팩트 추가
> - 아직 **초안** 상태인 기존 청사진 변경
> - 청사진을 **게시**하여 할당할 준비가 되었다고 표시
> - 기존 구독에 청사진 할당
> - 할당된 청사진의 상태 및 진행률 확인
> - 구독에 할당된 청사진 제거

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free)을 만듭니다.

## <a name="getting-started-with-rest-api"></a>REST API 사용 시작

REST API에 대해 잘 모르는 경우에는 REST API에 대한 일반적인 이해, 특히 요청 URI 및 요청 본문에 대한 일반적인 이해가 가능하도록 [Azure REST API 참조](/rest/api/azure/)를 검토하는 단계부터 시작합니다. 이 문서에서는 이러한 개념을 사용하여 Azure Blueprints를 사용하는 작업에 대한 지침을 제공하며 이에 대한 실무 지식이 있다가 가정합니다. [ARMClient](https://github.com/projectkudu/ARMClient) 등과 같은 도구는 인증을 자동으로 처리할 수 있으며 초보자가 사용하면 좋습니다.

Blueprints 사양은 [Azure Blueprints REST API](/rest/api/blueprints/)를 참조하세요.

### <a name="rest-api-and-powershell"></a>REST API 및 PowerShell

REST API 호출을 만드는 도구가 없는 경우 지침에 대해 PowerShell을 사용하는 것이 좋습니다. 다음은 Azure로 인증을 수행하기 위한 샘플 헤더입니다. 인증 헤더(다른 이름: **전달자 토큰**)를 생성하고 모든 매개 변수 또는 **요청 본문**에 연결할 REST API URI를 제공합니다.

```powershell-interactive
# Login first with Connect-AzureRmAccount if not using Cloud Shell

$azContext = Get-AzureRmContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Invoke the REST API
$restUri = 'https://management.azure.com/subscriptions/{subscriptionId}?api-version=2016-06-01'
$response = Invoke-RestMethod -Uri $restUri -Method Get -Headers $authHeader
```

구독에 대한 정보를 얻으려면 위의 **$restUri** 변수에서 `{subscriptionId}`를 대체합니다. $response 변수는 `Invoke-RestMethod` cmdlet의 결과를 보관하며 [ConvertFrom-Json](/powershell/module/microsoft.powershell.utility/convertfrom-json)과 같은 cmdlet으로 구문 분석이 가능합니다. REST API 서비스 엔드포인트에 **요청 본문**이 필요하면 JSON 형식의 변수를 `Invoke-RestMethod`의 `-Body` 매개 변수에 제공합니다.

## <a name="create-a-blueprint"></a>청사진 만들기

규정 준수를 위한 표준 패턴을 정의하는 첫 단계는 사용 가능한 리소스로 청사진을 작성하는 것입니다. 'MyBlueprint'라는 청사진을 만들어 구독의 역할 및 정책 할당을 구성하겠습니다. 그런 다음, 리소스 그룹, Resource Manager 템플릿 및 리소스 그룹에 대한 역할 할당을 추가하겠습니다.

> [!NOTE]
> REST API를 사용하는 경우 _청사진_ 개체가 먼저 생성됩니다. 매개 변수가 있는 추가할 _아티팩트_ 각각에 대해, 초기 _청사진_에 매개 변수를 미리 정의해야 합니다.

각 REST API URI에는 사용자가 자신의 값으로 대체해야 하는 변수가 있습니다.

- `{YourMG}` - 사용자의 관리 그룹 ID로 대체
- `{subscriptionId}` - 사용자의 구독 ID로 대체

1. 초기 _청사진_ 개체를 만듭니다. **요청 본문**에는 청사진에 대한 속성, 생성할 리소스 그룹 및 모든 청사진 수준의 매개 변수가 포함됩니다. 매개 변수는 할당 중에 설정되고 이후 단계에서 추가되는 아티팩트에서 사용합니다.

   - REST API URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
     ```

   - 요청 본문

     ```json
     {
         "properties": {
             "description": "This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.",
             "targetScope": "subscription",
             "parameters": {
                 "storageAccountType": {
                     "type": "string",
                     "metadata": {
                         "displayName": "storage account type.",
                         "description": null
                     }
                 },
                 "tagName": {
                     "type": "string",
                     "metadata": {
                         "displayName": "The name of the tag to provide the policy assignment.",
                         "description": null
                     }
                 },
                 "tagValue": {
                     "type": "string",
                     "metadata": {
                         "displayName": "The value of the tag to provide the policy assignment.",
                         "description": null
                     }
                 },
                 "contributors": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Contributor role at the subscription"
                     }
                 },
                 "owners": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Owner role at the resource group"
                     }
                 }
             },
             "resourceGroups": {
                 "storageRG": {
                     "description": "Contains the resource template deployment and a role assignment."
                 }
             }
         }
     }
     ```

1. 구독 시 역할 할당을 추가합니다. **요청 본문**은 아티팩트의 종류를 정의하고 속성은 역할 정의 식별자에 정렬되며 주체 ID는 값 배열의 형태로 전달됩니다. 아래 예에서 지정된 역할에 부여된 주체 ID는 청사진 할당 중에 설정되는 매개 변수로 구성됩니다. 이 예제에서는 `b24988ac-6180-42a0-ab88-20f7382dd24c`의 GUID와 함께 _기여자_ 기본 제공 역할을 사용합니다.

   - REST API URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleContributor?api-version=2018-11-01-preview
     ```

   - 요청 본문

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
             "principalIds": "[parameters('contributors')]"
         }
     }
     ```

1. 구독 시 정책 할당을 추가합니다. **요청 본문**은 아티팩트의 _종류_, 정책 또는 이니셔티브 정의에 부합하는 속성을 정의하고, 청사진 할당 중에 구성될 정의된 청사진 매개 변수를 사용하도록 정책 할당을 구성합니다. 이 예제에서는 `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`의 GUID와 함께 _리소스 그룹에 태그 및 기본값 적용_ 기본 제공 정책을 사용합니다.

   - REST API URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyTags?api-version=2018-11-01-preview
     ```

   - 요청 본문

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "description": "Apply tag and its default value to resource groups",
             "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
             "parameters": {
                 "tagName": {
                     "value": "[parameters('tagName')]"
                 },
                 "tagValue": {
                     "value": "[parameters('tagValue')]"
                 }
             }
         }
     }
     ```

1. 구독 시 Storage 태그(_storageAccountType_ 매개 변수 재사용)에 대해 또 다른 정책 할당을 추가합니다. 이렇게 추가된 정책 할당 아티팩트는 청사진에 정의된 매개 변수가 둘 이상의 아티팩트에서 사용될 수 있다는 것을 보여줍니다. 이 예에서 **storageAccountType**은 리소스 그룹에 태그를 설정하는 데 사용됩니다. 이 값은 다음 단계에서 생성되는 저장소 계정에 대한 정보를 제공합니다. 이 예제에서는 `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`의 GUID와 함께 _리소스 그룹에 태그 및 기본값 적용_ 기본 제공 정책을 사용합니다.

   - REST API URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2018-11-01-preview
     ```

   - 요청 본문

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "description": "Apply storage tag and the parameter also used by the template to resource groups",
             "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
             "parameters": {
                 "tagName": {
                     "value": "StorageType"
                 },
                 "tagValue": {
                     "value": "[parameters('storageAccountType')]"
                 }
             }
         }
     }
     ```

1. 리소스 그룹에서 템플릿을 추가합니다. Resource Manager 템플릿의 **요청 본문**은 템플릿의 일반 JSON 구성 요소를 포함하며, **properties.resourceGroup**을 사용하여 대상 리소스 그룹을 정의합니다. 이 템플릿은 또한 **storageAccountType**, **tagName** 및 **tagValue** 청사진 매개 변수를 각각 템플릿에 전달하는 방식으로 재사용합니다. 청사진 매개 변수는 **properties.parameters**를 정의하여 템플릿에서 사용할 수 있고 템플릿 JSON 내에 포함된 키-값 쌍은 값을 삽입하는 데 사용됩니다. 청사진과 템플릿 매개 변수 이름은 동일할 수 있지만 각 항목이 청사진에서 템플릿 아티팩트로 전달되는 방식을 설명하기 위해 서로 다른 이름이 사용되었습니다.

   - REST API URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/templateStorage?api-version=2018-11-01-preview
     ```

   - 요청 본문

     ```json
     {
         "kind": "template",
         "properties": {
             "template": {
                 "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                 "contentVersion": "1.0.0.0",
                 "parameters": {
                     "storageAccountTypeFromBP": {
                         "type": "string",
                         "defaultValue": "Standard_LRS",
                         "allowedValues": [
                             "Standard_LRS",
                             "Standard_GRS",
                             "Standard_ZRS",
                             "Premium_LRS"
                         ],
                         "metadata": {
                             "description": "Storage Account type"
                         }
                     },
                     "tagNameFromBP": {
                         "type": "string",
                         "defaultValue": "NotSet",
                         "metadata": {
                             "description": "Tag name from blueprint"
                         }
                     },
                     "tagValueFromBP": {
                         "type": "string",
                         "defaultValue": "NotSet",
                         "metadata": {
                             "description": "Tag value from blueprint"
                         }
                     }
                 },
                 "variables": {
                     "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
                 },
                 "resources": [{
                     "type": "Microsoft.Storage/storageAccounts",
                     "name": "[variables('storageAccountName')]",
                     "apiVersion": "2016-01-01",
                     "tags": {
                        "[parameters('tagNameFromBP')]": "[parameters('tagValueFromBP')]"
                     },
                     "location": "[resourceGroup().location]",
                     "sku": {
                         "name": "[parameters('storageAccountTypeFromBP')]"
                     },
                     "kind": "Storage",
                     "properties": {}
                 }],
                 "outputs": {
                     "storageAccountSku": {
                         "type": "string",
                         "value": "[variables('storageAccountName')]"
                     }
                 }
             },
             "resourceGroup": "storageRG",
             "parameters": {
                 "storageAccountTypeFromBP": {
                     "value": "[parameters('storageAccountType')]"
                 },
                 "tagNameFromBP": {
                     "value": "[parameters('tagName')]"
                 },
                 "tagValueFromBP": {
                     "value": "[parameters('tagValue')]"
                 }
             }
         }
     }
     ```

1. 리소스 그룹에서 역할 할당을 추가합니다. 이전 역할 할당과 유사하게, 아래 예제에서는 **소유자** 역할에 대한 정의 식별자를 사용하고 청사진과 다른 매개 변수를 제공합니다. 이 예제에서는 `8e3af657-a8ff-443c-a75c-2fe8c4bcb635`의 GUID와 함께 _소유자_ 기본 제공 역할을 사용합니다.

   - REST API URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2018-11-01-preview
     ```

   - 요청 본문

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "resourceGroup": "storageRG",
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
             "principalIds": "[parameters('owners')]"
         }
     }
     ```

## <a name="publish-a-blueprint"></a>청사진 게시

아티팩트가 청사진에 추가되었으면 이제 게시할 차례입니다. 게시한 후에는 구독에 할당할 수 있습니다.

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/versions/{BlueprintVersion}?api-version=2018-11-01-preview
  ```

`{BlueprintVersion}`의 값은 최대 길이가 20자인 문자, 숫자 및 하이픈(공백이나 다른 특수 문자 제외)으로 이루어진 문자열입니다. **v20180622-135541**처럼 고유하고 정보가 될만한 값을 사용합니다.

## <a name="assign-a-blueprint"></a>청사진 할당

REST API를 사용하여 청사진을 게시하고 나면 구독에 할당할 수 있습니다. 작성한 청사진을 관리 그룹 계층 구조에 속하는 구독 중 하나에 할당합니다. 구독에 청사진이 저장되면 해당 구독에만 할당될 수 있습니다. **요청 본문**은 할당될 청사진을 지정하고, 청사진 정의에 포함된 모든 리소스 그룹에 이름과 위치를 제공하며 청사진에 정의되고 하나 이상의 연결된 아티팩트에 사용되는 모든 매개 변수를 제공합니다.

1. Azure Blueprint 서비스 주체에게 대상 구독에서 **소유자** 역할을 제공합니다. AppId는 정적(`f71766dc-90d9-4b7d-bd9d-4499c4331c3f`)이지만 서비스 주체 ID는 테넌트별로 다양합니다. 테넌트에 대한 세부 정보는 다음 REST API를 사용하여 요청할 수 있습니다. 여기에는 권한 부여가 다른 [Azure Active Directory Graph API](../../active-directory/develop/active-directory-graph-api.md)가 사용됩니다.

   - REST API URI

     ```http
     GET https://graph.windows.net/{tenantId}/servicePrincipals?api-version=1.6&$filter=appId eq 'f71766dc-90d9-4b7d-bd9d-4499c4331c3f'
     ```

1. 청사진 배포를 구독에 할당하여 실행합니다. **contributors** 및 **owners** 매개 변수에는 역할 할당에 부여될 주체의 objectId 배열이 필요하기 때문에, [Azure Active Directory Graph API](../../active-directory/develop/active-directory-graph-api.md)를 사용하여 사용자, 그룹 또는 서비스 주체에 대한 **요청 본문**에 사용될 objectId를 수집합니다.

   - REST API URI

     ```http
     PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
     ```

   - 요청 본문

     ```json
     {
         "properties": {
             "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint",
             "resourceGroups": {
                 "storageRG": {
                     "name": "StorageAccount",
                     "location": "eastus2"
                 }
             },
             "parameters": {
                 "storageAccountType": {
                     "value": "Standard_GRS"
                 },
                 "tagName": {
                     "value": "CostCenter"
                 },
                 "tagValue": {
                     "value": "ContosoIT"
                 },
                 "contributors": {
                     "value": [
                         "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                         "38833b56-194d-420b-90ce-cff578296714"
                     ]
                 },
                 "owners": {
                     "value": [
                         "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                         "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                     ]
                 }
             }
         },
         "identity": {
             "type": "systemAssigned"
         },
         "location": "westus"
     }
     ```

## <a name="unassign-a-blueprint"></a>청사진 할당 취소

구독에서 청사진을 제거할 수 있습니다. 아티팩트 리소스가 더 이상 필요 없는 경우에 청사진을 제거하는 경우가 많습니다. 청사진을 제거해도 해당 청사진의 일부분으로 할당된 아티팩트는 남아 있습니다. 청사진 할당을 제거하려면 다음 REST API 작업을 사용합니다.

- REST API URI

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
  ```

## <a name="delete-a-blueprint"></a>청사진 삭제

청사진 자체를 제거하려면 다음 REST API 작업을 사용합니다.

- REST API URI

  ```http
  DELETE https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
  ```

## <a name="next-steps"></a>다음 단계

- [청사진 수명 주기](./concepts/lifecycle.md)에 대해 알아보기
- [정적 및 동적 매개 변수](./concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](./concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](./concepts/resource-locking.md)을 활용하는 방법 알아보기
- [기존 할당을 업데이트](./how-to/update-existing-assignments.md)하는 방법 알아보기
- [일반 문제 해결 방법](./troubleshoot/general.md)을 통해 청사진 할당 중에 발생하는 문제 해결