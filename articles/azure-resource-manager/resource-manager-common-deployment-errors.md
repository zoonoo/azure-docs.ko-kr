---
title: "일반적인 Azure 배포 오류 해결 | Microsoft Docs"
description: "Azure 리소스 관리자를 사용하여 Azure에 리소스를 배포할 때 발생하는 일반적인 오류를 해결하는 방법을 설명합니다."
services: azure-resource-manager
documentationcenter: 
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
keywords: "배포 오류 Azure 배포, Azure에 배포"
ms.assetid: c002a9be-4de5-4963-bd14-b54aa3d8fa59
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 30adc10d01290f14a3e116813b19916fa36ab0bc
ms.contentlocale: ko-kr
ms.lasthandoff: 08/19/2017

---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Azure 리소스 관리자를 사용한 일반적인 Azure 배포 오류 해결
이 항목에서는 발생할 수 있는 일반적인 Azure 배포 오류 중 일부를 해결할 수 있는 방법에 대해 설명합니다.

다음 오류 코드는 이 항목에 설명되어 있습니다.

* [AccountNameInvalid](#accountnameinvalid)
* [권한 부여 실패](#authorization-failed)
* [BadRequest](#badrequest)
* [DeploymentFailed](#deploymentfailed)
* [DisallowedOperation](#disallowedoperation)
* [InvalidContentLink](#invalidcontentlink)
* [InvalidTemplate](#invalidtemplate)
* [MissingSubscriptionRegistration](#noregisteredproviderfound)
* [NotFound](#notfound)
* [NoRegisteredProviderFound](#noregisteredproviderfound)
* [OperationNotAllowed](#quotaexceeded)
* [ParentResourceNotFound](#parentresourcenotfound)
* [QuotaExceeded](#quotaexceeded)
* [RequestDisallowedByPolicy](#requestdisallowedbypolicy)
* [ResourceNotFound](#notfound)
* [SkuNotAvailable](#skunotavailable)
* [StorageAccountAlreadyExists](#storagenamenotunique)
* [StorageAccountAlreadyTaken](#storagenamenotunique)

## <a name="deploymentfailed"></a>DeploymentFailed

이 오류 코드는 일반 배포 오류를 나타내지만 문제 해결을 시작해야 하는 오류 코드는 아닙니다. 문제 해결에 실제로 도움이 되는 오류 코드는 보통 해당 오류 한 수준 아래에 있습니다. 예를 들어 다음 이미지는 배포 오류에 속하는 **RequestDisallowedByPolicy** 오류 코드를 보여줍니다.

![오류 코드 표시](./media/resource-manager-common-deployment-errors/error-code.png)

## <a name="skunotavailable"></a>SkuNotAvailable

리소스를 배포할 때(일반적으로 가상 컴퓨터) 다음 오류 코드 및 오류 메시지가 나타날 수 있습니다.

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>' 
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

선택한 리소스 SKU(예: VM 크기)를 선택한 위치에 사용할 수 없는 경우 이 오류가 나타납니다. 이 문제를 해결하려면 지역에서 사용할 수 있는 SKU가 무엇인지 알아내야 합니다. PowerShell, 포털 또는 REST 작업을 사용하여 사용 가능한 SKU를 찾을 수 있습니다.

- PowerShell에서는 [Get-AzureRmComputeResourceSku](/powershell/module/azurerm.compute/get-azurermcomputeresourcesku) 및 위치별 필터링을 사용합니다. 이 명령이 작동하려면 최신 버전의 PowerShell이 있어야 합니다.

  ```powershell
  Get-AzureRmComputeResourceSku | where {$_.Locations.Contains("southcentralus")}
  ```

  결과에는 위치에 대한 SKU 목록과 해당 SKU에 대한 제한 사항이 포함됩니다.

  ```powershell
  ResourceType                Name      Locations Restriction                      Capability Value
  ------------                ----      --------- -----------                      ---------- -----
  availabilitySets         Classic southcentralus             MaximumPlatformFaultDomainCount     3
  availabilitySets         Aligned southcentralus             MaximumPlatformFaultDomainCount     3
  virtualMachines      Standard_A0 southcentralus
  virtualMachines      Standard_A1 southcentralus
  virtualMachines      Standard_A2 southcentralus
  ```

- [포털](https://portal.azure.com)을 사용하려면 포털에 로그인하고 인터페이스를 통해 리소스를 추가합니다. 값을 설정하면 해당 리소스에 사용 가능한 SKU가 표시됩니다. 배포를 완료할 필요는 없습니다.

    ![사용 가능한 SKU](./media/resource-manager-common-deployment-errors/view-sku.png)

- 가상 컴퓨터에 대해 REST API를 사용하려면 다음 요청을 보냅니다.

  ```HTTP 
  GET
  https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/skus?api-version=2016-03-30
  ```

  다음과 같은 형식으로 사용 가능한 SKU 및 지역을 반환합니다.

  ```json
  {
    "value": [
      {
        "resourceType": "virtualMachines",
        "name": "Standard_A0",
        "tier": "Standard",
        "size": "A0",
        "locations": [
          "eastus"
        ],
        "restrictions": []
      },
      {
        "resourceType": "virtualMachines",
        "name": "Standard_A1",
        "tier": "Standard",
        "size": "A1",
        "locations": [
          "eastus"
        ],
        "restrictions": []
      },
      ...
    ]
  }    
  ```

해당 위치 또는 대체 위치에서 비즈니스 요구를 충족하는 적합한 SKU를 찾을 수 없는 경우 [SKU 요청](https://aka.ms/skurestriction)을 Azure 지원에 제출하세요.

## <a name="disallowedoperation"></a>DisallowedOperation

```
Code: DisallowedOperation
Message: The current subscription type is not permitted to perform operations on any provider 
namespace. Please use a different subscription.
```

이 오류가 발생하는 경우 Azure Active Directory 이외의 모든 Azure 서비스에 대한 액세스가 허용되지 않은 구독을 사용하고 있는 것입니다. 클래식 포털에 액세스해야 하지만 리소스를 배포하도록 허용되지 않는 경우 이러한 유형의 구독을 보유하고 있는 것일 수 있습니다. 이 문제를 해결하려면 리소스를 배포할 수 있는 권한이 있는 구독을 사용해야 합니다.  

PowerShell을 통해 사용할 수 있는 구독을 보려면 다음을 사용합니다.

```powershell
Get-AzureRmSubscription
```

또한 현재 구독 설정을 설정하려면 다음을 사용합니다.

```powershell
Set-AzureRmContext -SubscriptionName {subscription-name}
```

Azure CLI 2.0을 통해 사용할 수 있는 구독을 보려면 다음을 사용합니다.

```azurecli
az account list
```

또한 현재 구독 설정을 설정하려면 다음을 사용합니다.

```azurecli
az account set --subscription {subscription-name}
```

## <a name="invalidtemplate"></a>InvalidTemplate
이 오류로 인해 별도의 몇 가지 유형의 오류가 발생할 수 있습니다.

- 구문 오류

   템플릿 유효성 검사 실패를 나타내는 오류 메시지가 표시되면 템플릿 구문에 문제가 있습니다.

  ```
  Code=InvalidTemplate
  Message=Deployment template validation failed
  ```

   이 오류는 템플릿 식이 복잡할 수 있기 때문에 쉽게 발생합니다. 예를 들어 저장소 계정에 대한 다음 이름 할당에는 대괄호 집합 1개, 함수 3개, 괄호 집합 3개, 작은 따옴표 집합 1개, 속성 1개가 포함됩니다.

  ```json
  "name": "[concat('storage', uniqueString(resourceGroup().id))]",
  ```

   일치하는 구문을 제공하지 않으면 템플릿에서 의도와는 다른 값을 생성합니다.

   이러한 오류 유형을 수신하면 식 구문을 주의 깊게 검토합니다. [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) 또는 [Visual Studio Code](resource-manager-vs-code.md)와 같이 구문 오류에 대해 경고할 수 있는 JSON 편집기를 사용하는 것을 고려해 보세요.

- 잘못된 세그먼트 길이

   리소스 이름이 올바른 형식이 아닐 경우 다른 잘못된 템플릿 오류가 발생합니다.

  ```
  Code=InvalidTemplate
  Message=Deployment template validation failed: 'The template resource {resource-name}'
  for type {resource-type} has incorrect segment lengths.
  ```

   루트 수준 리소스에는 리소스 형식에 포함된 세그먼트보다 이름에 포함된 세그먼트가 1개 더 적어야 합니다. 각 세그먼트는 슬래시로 구분됩니다. 다음 예제에서는 2개 세그먼트가 형식에 있고 1개 세그먼트가 이름에 있으므로 **유효한 이름**입니다.

  ```json
  {
    "type": "Microsoft.Web/serverfarms",
    "name": "myHostingPlanName",
    ...
  }
  ```

   하지만 그 다음 예제의 경우 형식과 이름의 세그먼트 수가 같으므로 **유효한 이름이 아닙니다** .

  ```json
  {
    "type": "Microsoft.Web/serverfarms",
    "name": "appPlan/myHostingPlanName",
    ...
  }
  ```

   자식 리소스의 경우 형식과 이름의 세그먼트 수는 같아야 합니다. 자식 리소스의 이름과 형식 전체에 부모 리소의 이름과 형식이 포함되기 때문에 이 세그먼트 수는 적합합니다. 이에 따라 전체 이름에는 여전히 전체 형식보다 하나가 적은 세그먼트가 있습니다.

  ```json
  "resources": [
      {
          "type": "Microsoft.KeyVault/vaults",
          "name": "contosokeyvault",
          ...
          "resources": [
              {
                  "type": "secrets",
                  "name": "appPassword",
                  ...
              }
          ]
      }
  ]
  ```

   리소스 공급자 간에 적용되는 리소스 관리자 형식에서 세그먼트를 제대로 갖추는 것이 까다로울 수 있습니다. 예를 들어 웹 사이트에 리소스 잠금을 적용하려면 4개 세그먼트가 있는 형식이 필요합니다. 따라서 이름에는 다음과 같이 3개 세그먼트가 있습니다.

  ```json
  {
      "type": "Microsoft.Web/sites/providers/locks",
      "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
      ...
  }
  ```

- 인덱스 복사는 사용할 수 없음

   **copy** 요소를 지원하지 않는 템플릿의 일부에 이 요소를 적용한 경우에 이러한 **InvalidTemplate** 오류가 발생합니다. copy 요소는 리소스 종류에만 적용할 수 있습니다. 리소스 형식 내에서 속성에 복사를 적용할 수 없습니다. 예를 들어 가상 컴퓨터에 복사를 적용하지만 가상 컴퓨터에 대한 OS 디스크에는 적용할 수 없습니다. 경우에 따라 자식 리소스를 부모 리소스로 변환하여 복사 루프를 만들 수 있습니다. 복사 사용에 대한 자세한 내용은 [Azure 리소스 관리자에서 리소스의 여러 인스턴스 만들기](resource-group-create-multiple.md)를 참조하세요.

- 잘못된 매개 변수

   템플릿에서 매개 변수에 허용되는 값을 지정했지만 이러한 값 중 하나가 아닌 값을 제공하는 경우 다음과 비슷한 오류 메시지가 표시됩니다.

  ```
  Code=InvalidTemplate;
  Message=Deployment template validation failed: 'The provided value {parameter value}
  for the template parameter {parameter name} is not valid. The parameter value is not
  part of the allowed values
  ``` 

   템플릿에 허용되는 값을 다시 한 번 확인하고 배포 시 값 하나를 제공합니다.

- 순환 종속성이 감지됨

   이 오류 메시지는 배포가 시작될 수 없도록 리소스가 서로 종속되어 있는 경우에 표시됩니다. 상호 종속성의 조합은 둘 이상의 리소스가 이미 대기 중인 다른 리소스를 기다리게 만듭니다. 예를 들어 리소스1은 리소스3에 종속되고, 리소스2는 리소스1에 종속되고, 리소스3은 리소스2에 종속됩니다. 일반적으로 이런 문제는 불필요한 종속성을 제거하여 해결할 수 있습니다. 

<a id="notfound" />
### <a name="notfound-and-resourcenotfound"></a>NotFound 및 ResourceNotFound
해석할 수 없는 리소스 이름이 포함된 템플릿인 경우 다음과 비슷한 오류 메시지가 표시됩니다.

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

템플릿에서 누락된 리소스를 배포하려는 경우 종속성을 추가해야 하는지 여부를 확인합니다. 리소스 관리자는 가능한 경우 리소스를 병렬로 만들어 배포를 최적화합니다. 한 리소스가 다른 리소스 뒤에 배포되어야 하는 경우 템플릿에서 **dependsOn** 요소를 사용하여 다른 리소스에 대한 종속성을 만들어야 합니다. 예를 들어 웹앱을 배포할 때는 App Service 계획이 반드시 존재해야 합니다. 웹앱이 App Service 계획에 종속된다고 지정하지 않으면 리소스 관리자에서 두 리소스를 모두 만듭니다. 웹앱에 속성을 설정하려고 할 때 App Service 계획 리소스가 아직 없기 때문에 이 리소스를 찾을 수 없다는 오류 메시지가 표시됩니다. 웹앱에서 종속성을 설정하면 이러한 오류를 방지할 수 있습니다.

```json
{
  "apiVersion": "2015-08-01",
  "type": "Microsoft.Web/sites",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

종속성 오류 문제 해결을 위한 제안 사항은 [배포 시퀀스 확인](#check-deployment-sequence)을 참조하세요.

또한 배포되는 그룹이 아닌 다른 리소스 그룹에 리소스가 있는 경우에도 이러한 오류 메시지가 표시됩니다. 이 경우 리소스의 정규화 된 이름을 가져오는 [resourceId 함수](resource-group-template-functions-resource.md#resourceid)를 사용합니다.

```json
"properties": {
    "name": "[parameters('siteName')]",
    "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

해석할 수 없는 리소스에 [reference](resource-group-template-functions-resource.md#reference) 또는 [listKeys](resource-group-template-functions-resource.md#listkeys) 함수를 사용하려는 경우에는 다음과 같은 오류 메시지가 표시됩니다.

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

**reference** 함수를 포함하는 식을 찾습니다. 매개 변수 값이 올바른지 다시 한 번 확인합니다.

## <a name="parentresourcenotfound"></a>ParentResourceNotFound

한 리소스가 다른 리소스의 부모이면 부모 리소스는 자식 리소스를 만들기 전에 존재해야 합니다. 아직 존재하지 않을 경우 다음과 같은 오류가 나타납니다.

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

자식 리소스의 이름에 부모 이름이 포함됩니다. 예를 들어 SQL Database는 다음과 같이 정의될 수 있습니다.

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

그러나 부모 리소스에 대한 종속성을 지정하지 않으면 자식 리소스는 부모보다 먼저 배포될 수 있습니다. 이 오류를 해결하려면 종속성을 포함하세요.

```json
"dependsOn": [
    "[variables('databaseServerName')]"
]
```

<a id="storagenamenotunique" />

## <a name="storageaccountalreadyexists-and-storageaccountalreadytaken"></a>torageAccountAlreadyExists 및 StorageAccountAlreadyTaken
저장소 계정에 대해서는 Azure에서 고유한 리소스 이름을 제공해야 합니다. 고유한 이름을 제공하지 않으면 다음과 같은 오류 메시지가 표시됩니다.

```
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

명명 규칙과 [uniqueString](resource-group-template-functions-string.md#uniquestring) 함수 결과를 연결하여 고유한 이름을 만들 수 있습니다.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

구독에서 기존 저장소 계정과 동일한 이름의 저장소 계정을 배포하지만 다른 위치를 제공하는 경우 해당 저장소 계정이 이미 다른 위치에 이미 있다고 나타내는 오류 메시지가 표시됩니다. 이 경우 기존 저장소 계정을 삭제하거나 기존 저장소 계정과 동일한 위치를 제공합니다.

## <a name="accountnameinvalid"></a>AccountNameInvalid
저장소 계정에 사용이 금지된 문자를 포함하는 이름을 제공하려는 경우 **AccountNameInvalid** 오류 메시지가 표시됩니다. 저장소 계정 이름은 3자에서 24자 사이여야 하고 숫자 및 소문자만 사용해야 합니다. [uniqueString](resource-group-template-functions-string.md#uniquestring) 함수는 13자를 반환합니다. **uniqueString** 결과에 접두어를 연결하는 경우 11자 미만의 접두어를 제공합니다.

## <a name="badrequest"></a>BadRequest

속성에 대해 잘못된 값을 제공하면 BadRequest 상태가 발생할 수 있습니다. 예를 들어 저장소 계정에 대해 잘못된 SKU 값을 제공하는 경우 배포가 실패합니다. 속성에 대해 유효한 값을 확인하려면 [REST API](/rest/api)에서 배포 중인 리소스 유형을 살펴봅니다.

<a id="noregisteredproviderfound" />

## <a name="noregisteredproviderfound-and-missingsubscriptionregistration"></a>NoRegisteredProviderFound 및 MissingSubscriptionRegistration
리소스를 배포할 때 다음 오류 코드 및 메시지가 나타날 수 있습니다.

```
Code: NoRegisteredProviderFound
Message: No registered resource provider found for location {location}
and API version {api-version} for type {resource-type}.
```

또는 다음과 유사한 메시지가 나타날 수 있습니다.

```
Code: MissingSubscriptionRegistration
Message: The subscription is not registered to use namespace {resource-provider-namespace}
```

세 가지 이유 중 하나로 이러한 오류가 나타납니다.

1. 리소스 공급자가 구독에 대해 등록되지 않았습니다.
2. 해당 리소스 종류에 대해 API 버전이 지원되지 않습니다.
3. 해당 리소스 종류에 대해 위치가 지원되지 않습니다.

오류 메시지는 지원되는 위치 및 API 버전에 대해 제안을 제공해야 합니다. 템플릿을 제안된 값 중 하나로 변경할 수 있습니다. 대부분의 공급자는 Azure Portal이나 사용 중인 명령줄 인터페이스에 의해 자동으로 등록되지만, 그렇지 않은 경우도 있습니다. 특정 리소스 공급자를 전에 사용하지 않은 경우 해당 공급자를 등록해야 합니다. PowerShell 또는 Azure CLI를 통해 리소스 공급자에 대해 자세히 확인할 수 있습니다.

**포털**

등록 상태를 볼 수 있으며 포털을 통해 리소스 공급자 네임스페이스를 등록할 수 있습니다.

1. 구독의 경우 **리소스 공급자**를 선택합니다.

   ![리소스 공급자 선택](./media/resource-manager-common-deployment-errors/select-resource-provider.png)

2. 리소스 공급자의 목록을 확인하고 필요한 경우 **등록** 링크를 선택하여 배포하려는 유형의 리소스 공급자를 등록합니다.

   ![리소스 공급자 나열](./media/resource-manager-common-deployment-errors/list-resource-providers.png)

**PowerShell**

등록 상태를 보려면 **Get-AzureRmResourceProvider**를 사용합니다.

```powershell
Get-AzureRmResourceProvider -ListAvailable
```

공급자를 등록하려면 **Register-AzureRmResourceProvider**를 사용하여 등록할 리소스 공급자의 이름을 제공합니다.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn
```

특정 종류의 리소스에 대해 지원되는 위치를 확인하려면 다음을 사용합니다.

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

특정 종류의 리소스에 대해 지원되는 API 버전을 확인하려면 다음을 사용합니다.

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

**Azure CLI**

공급자가 등록되어 있는지 확인하려면 `azure provider list` 명령을 사용합니다.

```azurecli
az provider list
```

리소스 공급자를 등록하려면 `azure provider register` 명령을 사용하고 등록할 *네임스페이스*를 지정합니다.

```azurecli
az provider register --namespace Microsoft.Cdn
```

리소스 유형에 대해 지원되는 위치 및 API 버전을 보려면 다음을 사용합니다.

```azurecli
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

<a id="quotaexceeded" />

## <a name="quotaexceeded-and-operationnotallowed"></a>QuotaExceeded 및 OperationNotAllowed
또한 배포가 리소스 그룹, 구독, 계정 및 기타 범위당 할당량을 초과할 경우 문제가 발생할 수 있습니다. 예를 들어 지역에 대한 코어 수를 제한하도록 구독을 구성할 수 있습니다. 허용량보다 많은 코어가 있는 가상 컴퓨터를 배포하려는 경우 할당량을 초과했다는 오류 메시지가 표시됩니다.
전체 할당량 정보는 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)을 참조하세요.

코어에 대한 구독 할당량을 검사하려면 Azure CLI의 `azure vm list-usage` 명령을 사용할 수 있습니다. 다음 예제에서는 무료 평가판 계정에 대한 코어 할당량이 4개임을 보여 줍니다.

```azurecli
az vm list-usage --location "South Central US"
```

반환하는 내용은 다음과 같습니다.

```azurecli
[
  {
    "currentValue": 0,
    "limit": 2000,
    "name": {
      "localizedValue": "Availability Sets",
      "value": "availabilitySets"
    }
  },
  ...
]
```

미국 서부 지역의 코어를 5개 이상 만드는 템플릿을 배포하는 경우에 다음과 같은 배포 오류 메시지가 표시됩니다.

```
Code=OperationNotAllowed
Message=Operation results in exceeding quota limits of Core.
Maximum allowed: 4, Current in use: 4, Additional requested: 2.
```

또는 PowerShell에서 **Get-AzureRmVMUsage** Cmdlet을 사용할 수 있습니다.

```powershell
Get-AzureRmVMUsage
```

반환하는 내용은 다음과 같습니다.

```powershell
...
CurrentValue : 0
Limit        : 4
Name         : {
                 "value": "cores",
                 "localizedValue": "Total Regional Cores"
               }
Unit         : null
...
```

이러한 경우 포털로 이동하여 사용자가 배포하고 싶은 지역의 할당량을 올려서 지원 문제를 해결합니다.

> [!NOTE]
> 리소스 그룹에 대한 할당량은 구독 전체가 아니라 각 개별 지역에 대한 할당량입니다. 사용자가 미국 서부에 30개의 코어를 배포해야 하면 미국 서부에 30개의 리소스 관리자 코어를 요청해야 합니다. 사용자가 액세스하는 임의의 지역에서 30개 코어를 배포해야 하는 경우 모든 지역에 대해 30개 리소스 관리자 코어를 요청해야 합니다.
>
>

## <a name="invalidcontentlink"></a>InvalidContentLink
다음과 같은 오류 메시지가 표시되는 경우가 있습니다.

```
Code=InvalidContentLink
Message=Unable to download deployment content from ...
```

아마 사용할 수 없는 중첩된 템플릿에 연결하려고 했을 것입니다. 중첩된 템플릿에 제공된 URI를 다시 한 번 확인합니다. 저장소 계정에 해당 템플릿이 있는 경우 액세스 가능한 URI인지 확인합니다. SAS 토큰을 전달해야 합니다. 자세한 내용은 [Azure 리소스 관리자에서 연결된 템플릿 사용](resource-group-linked-templates.md)을 참조하세요.

## <a name="requestdisallowedbypolicy"></a>RequestDisallowedByPolicy
구독에 배포 중에 수행을 시도하는 작업을 방해하는 리소스 정책이 포함된 경우 이 오류가 발생합니다. 오류 메시지에서 정책 식별자를 찾습니다.

```
Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'
```

**PowerShell**에서 해당 정책 식별자를 **Id** 매개 변수로 제공하여 배포를 차단한 정책에 대한 정보를 검색합니다.

```powershell
(Get-AzureRmPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

**Azure CLI**에서 정책 정의 이름을 제공합니다.

```azurecli
az policy definition show --name regionPolicyAssignment
```

자세한 내용은 다음 문서를 참조하세요.

- [RequestDisallowedByPolicy 오류](resource-manager-policy-requestdisallowedbypolicy-error.md)
- [정책을 사용하여 리소스 및 컨트롤 액세스 관리](resource-manager-policy.md)

## <a name="authorization-failed"></a>권한 부여 실패
리소스를 배포하려고 하는 계정 또는 서비스 주체에게 작업 수행을 위한 액세스 권한이 없으므로 배포 중에 오류가 발생할 수 있습니다. 개발자 또는 관리자는 Azure Active Directory를 사용하여 ID와 해당 ID가 액세스할 수 있는 리소스를 자세히 제어할 수 있습니다. 예를 들어 계정이 읽기 역할에 할당되면 새 리소스를 만들 수 없습니다. 이 경우 권한 부여에 실패했다는 오류 메시지가 표시됩니다.

역할 기반 액세스 제어에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어](../active-directory/role-based-access-control-configure.md)를 참조하세요.


## <a name="next-steps"></a>다음 단계
* 감사 작업에 대해 알아보려면 [리소스 관리자로 작업 감사](resource-group-audit.md)를 참조하세요.
* 배포 중 오류를 확인하는 작업에 대해 알아보려면 [배포 작업 보기](resource-manager-deployment-operations.md)를 참조하세요.

