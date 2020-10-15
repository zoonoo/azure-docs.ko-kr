---
title: 리소스 공급자 만들기
description: 리소스 공급 기업을 만들고 사용자 지정 리소스 유형을 배포하는 방법을 설명합니다.
author: MSEvanhi
ms.topic: tutorial
ms.date: 06/24/2020
ms.author: evanhi
ms.openlocfilehash: 541d140716e52b4fe1db4bc999682914a380a5f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85368110"
---
# <a name="quickstart-create-a-custom-provider-and-deploy-custom-resources"></a>빠른 시작: 사용자 지정 공급자 만들기 및 사용자 지정 리소스 배포

이 빠른 시작에서는 사용자의 고유한 리소스 공급 기업을 만들고 해당 리소스 공급 기업을 위한 사용자 지정 리소스 형식을 배포합니다. 사용자 지정 공급 기업에 대한 자세한 내용은 [Azure 사용자 지정 공급 기업 미리 보기 개요](overview.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- 이 빠른 시작의 단계를 완료하려면 `REST` 작업을 호출해야 합니다. [REST 요청을 전송하는 여러 가지 방법](/rest/api/azure/)이 있습니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

- [custom-providers](/cli/azure/ext/custom-providers/custom-providers/resource-provider) 명령에는 확장이 필요합니다. 자세한 내용은 [Azure CLI에서 확장 사용](/cli/azure/azure-cli-extensions-overview)을 참조하세요.
- Azure CLI 예제에서는 `REST` 요청에 대해 `az rest`를 사용합니다. 자세한 내용은 [az rest](/cli/azure/reference-index#az-rest)를 참조하세요.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- PowerShell 명령은 PowerShell 7 이상 및 Azure PowerShell 모듈을 사용하여 로컬로 실행됩니다. 자세한 내용은 [Azure PowerShell 설치](/powershell/azure/install-az-ps)를 참조하세요.
- `REST` 작업을 위한 도구가 아직 없는 경우 [ARMClient](https://github.com/projectkudu/ARMClient)를 설치합니다. 이는 Azure Resource Manager API를 호출하여 간소화하는 오픈 소스 명령줄 도구입니다.
- **ARMClient**를 설치한 후 `armclient.exe`를 입력하여 PowerShell 명령 프롬프트에서 사용 정보를 표시할 수 있습니다. 또는 [ARMClient wiki](https://github.com/projectkudu/ARMClient/wiki)로 이동합니다.

---

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-custom-provider"></a>사용자 지정 공급 기업 배포

사용자 지정 공급 기업을 설정하려면 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/custom-providers/customprovider.json)을 사용자의 Azure 구독에 배포합니다.

템플릿을 배포한 후 사용자 구독에는 다음 리소스가 표시됩니다.

- 리소스 및 동작에 대한 작업을 사용하는 함수 앱.
- 사용자 지정 공급 기업을 통해 생성된 사용자를 저장하기 위한 스토리지 계정.
- 사용자 지정 리소스 형식 및 동작을 정의하는 사용자 지정 공급 기업. 요청을 보내기 위한 함수 앱 엔드포인트를 사용합니다.
- 사용자 지정 공급 기업의 사용자 지정 리소스.

사용자 지정 공급자를 배포하려면 Azure CLI, PowerShell 또는 Azure Portal을 사용합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

이 예제에서는 리소스 그룹, 위치 및 공급자의 함수 앱 이름을 입력하라는 메시지를 표시합니다. 이름은 다른 명령에 사용되는 변수에 저장됩니다. [az group create](/cli/azure/group#az-group-create) 및 [az deployment group create](/cli/azure/deployment/group#az-deployment-group-create) 명령을 통해 리소스를 배포합니다.

```azurecli-interactive
read -p "Enter a resource group name:" rgName &&
read -p "Enter the location (i.e. eastus):" location &&
read -p "Enter the provider's function app name:" funcName &&
templateUri="https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json" &&
az group create --name $rgName --location "$location" &&
az deployment group create --resource-group $rgName --template-uri $templateUri --parameters funcName=$funcName &&
echo "Press [ENTER] to continue ..." &&
read
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

이 예제에서는 리소스 그룹, 위치 및 공급자의 함수 앱 이름을 입력하라는 메시지를 표시합니다. 이름은 다른 명령에 사용되는 변수에 저장됩니다. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 및 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) 명령을 통해 리소스를 배포합니다.

```powershell
$rgName = Read-Host -Prompt "Enter a resource group name"
$location = Read-Host -Prompt "Enter the location (i.e. eastus)"
$funcName = Read-Host -Prompt "Enter the provider's function app name"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json"
New-AzResourceGroup -Name $rgName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $rgName -TemplateUri $templateUri -funcName $funcName
Read-Host -Prompt "Press [ENTER] to continue ..."
```

---

Azure Portal에서 솔루션을 배포할 수도 있습니다. **Azure에 배포** 단추를 선택하여 Azure Portal에서 템플릿을 엽니다.

[![Azure에 배포](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docs-json-samples%2Fmaster%2Fcustom-providers%2Fcustomprovider.json)

## <a name="view-custom-provider-and-resource"></a>사용자 지정 공급 기업 및 리소스 보기

포털에서 사용자 지정 공급 기업은 숨겨진 리소스 형식입니다. 리소스 공급자가 배포되었는지 확인하려면 리소스 그룹으로 이동합니다. 옵션을 **숨겨진 형식 표시**로 선택합니다.

![숨겨진 리소스 형식 표시](./media/create-custom-provider/show-hidden.png)

배포한 사용자 지정 리소스 형식을 확인하려면 리소스 형식에서 `GET` 작업을 사용합니다.

```http
GET https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users?api-version=2018-09-01-preview
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
subID=$(az account show --query id --output tsv)
requestURI="https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users?api-version=2018-09-01-preview"
az rest --method get --uri $requestURI
```

다음과 같은 응답이 수신됩니다.

```json
{
  "value": [
    {
      "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/santa",
      "name": "santa",
      "properties": {
        "FullName": "Santa Claus",
        "Location": "NorthPole",
        "provisioningState": "Succeeded"
      },
      "resourceGroup": "<rg-name>",
      "type": "Microsoft.CustomProviders/resourceProviders/users"
    }
  ]
}
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$subID = (Get-AzContext).Subscription.Id
$requestURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users?api-version=2018-09-01-preview"

armclient GET $requestURI
```

다음과 같은 응답이 수신됩니다.

```json
{
  "value": [
    {
      "properties": {
        "provisioningState": "Succeeded",
        "FullName": "Santa Claus",
        "Location": "NorthPole"
      },
      "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/santa",
      "name": "santa",
      "type": "Microsoft.CustomProviders/resourceProviders/users"
    }
  ]
}
```

---

## <a name="call-action"></a>호출 동작

사용자 지정 공급자에는 `ping`이라는 동작도 있습니다. 요청을 처리하는 코드는 함수 앱에서 구현됩니다. `ping` 동작은 인사말을 사용하여 회신합니다.

`ping` 요청을 보내려면 사용자 지정 공급자에서 `POST` 작업을 사용합니다.

```http
POST https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/ping?api-version=2018-09-01-preview
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
pingURI="https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/ping?api-version=2018-09-01-preview"
az rest --method post --uri $pingURI
```

다음과 같은 응답이 수신됩니다.

```json
{
  "message": "hello <function-name>.azurewebsites.net",
  "pingcontent": {
    "source": "<function-name>.azurewebsites.net"
  }
}
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$pingURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/ping?api-version=2018-09-01-preview"

armclient POST $pingURI
```

다음과 같은 응답이 수신됩니다.

```json
{
  "pingcontent": {
    "source": "<function-name>.azurewebsites.net"
  },
  "message": "hello <function-name>.azurewebsites.net"
}
```

---

## <a name="create-a-resource-type"></a>리소스 종류 만들기

사용자 지정 리소스 형식을 만들기 위해 템플릿에서 리소스를 배포할 수 있습니다. 이 접근 방식은 이 빠른 시작에서 배포한 템플릿에 표시되어 있습니다. 리소스 형식에 대한 `PUT` 요청을 보낼 수도 있습니다.

```http
PUT https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/<resource-name>?api-version=2018-09-01-preview

{"properties":{"FullName": "Test User", "Location": "Earth"}}
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
addURI="https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users/testuser?api-version=2018-09-01-preview"
az rest --method put --uri $addURI --body "{'properties':{'FullName': 'Test User', 'Location': 'Earth'}}"
```

다음과 같은 응답이 수신됩니다.

```json
{
  "id": "/subscriptions/<sub-ID>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/testuser",
  "name": "testuser",
  "properties": {
    "FullName": "Test User",
    "Location": "Earth",
    "provisioningState": "Succeeded"
  },
  "resourceGroup": "<rg-name>",
  "type": "Microsoft.CustomProviders/resourceProviders/users"
}
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$addURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users/testuser?api-version=2018-09-01-preview"
$requestBody = "{'properties':{'FullName': 'Test User', 'Location': 'Earth'}}"

armclient PUT $addURI $requestBody
```

다음과 같은 응답이 수신됩니다.

```json
{
  "properties": {
    "provisioningState": "Succeeded",
    "FullName": "Test User",
    "Location": "Earth"
  },
  "id": "/subscriptions/<sub-ID>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/testuser",
  "name": "testuser",
  "type": "Microsoft.CustomProviders/resourceProviders/users"
}
```

---

## <a name="custom-resource-provider-commands"></a>사용자 지정 리소스 공급자 명령

[custom-providers](/cli/azure/ext/custom-providers/custom-providers/resource-provider) 명령을 사용하여 사용자 지정 리소스 공급자를 사용할 수 있습니다.

### <a name="list-custom-resource-providers"></a>사용자 지정 리소스 공급자 나열

`list` 명령을 사용하여 구독에 있는 모든 사용자 지정 리소스 공급자를 표시합니다. 기본값은 현재 구독의 사용자 지정 리소스 공급자를 나열하거나 `--subscription` 매개 변수를 지정할 수 있습니다. 리소스 그룹을 나열하려면 `--resource-group` 매개 변수를 사용합니다.

```azurecli-interactive
az custom-providers resource-provider list --subscription $subID
```

```json
[
  {
    "actions": [
      {
        "endpoint": "https://<provider-name>.azurewebsites.net/api/{requestPath}",
        "name": "ping",
        "routingType": "Proxy"
      }
    ],
    "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceproviders/<provider-name>",
    "location": "eastus",
    "name": "<provider-name>",
    "provisioningState": "Succeeded",
    "resourceGroup": "<rg-name>",
    "resourceTypes": [
      {
        "endpoint": "https://<provider-name>.azurewebsites.net/api/{requestPath}",
        "name": "users",
        "routingType": "Proxy, Cache"
      }
    ],
    "tags": {},
    "type": "Microsoft.CustomProviders/resourceproviders",
    "validations": null
  }
]
```

### <a name="show-the-properties"></a>속성 표시

`show` 명령을 사용하여 사용자 지정 리소스 공급자의 속성을 표시합니다. 출력 형식은 `list` 출력과 비슷합니다.

```azurecli-interactive
az custom-providers resource-provider show --resource-group $rgName --name $funcName
```

### <a name="create-a-new-resource"></a>새 리소스 만들기

`create` 명령을 사용하여 사용자 지정 리소스 공급자를 만들거나 업데이트합니다. 이 예제에서는 `actions` 및 `resourceTypes`를 업데이트합니다.

```azurecli-interactive
az custom-providers resource-provider create --resource-group $rgName --name $funcName \
--action name=ping endpoint=https://myTestSite.azurewebsites.net/api/{requestPath} routing_type=Proxy \
--resource-type name=users endpoint=https://myTestSite.azurewebsites.net/api{requestPath} routing_type="Proxy, Cache"
```

```json
"actions": [
  {
    "endpoint": "https://myTestSite.azurewebsites.net/api/{requestPath}",
    "name": "ping",
    "routingType": "Proxy"
  }
],

"resourceTypes": [
  {
    "endpoint": "https://myTestSite.azurewebsites.net/api{requestPath}",
    "name": "users",
    "routingType": "Proxy, Cache"
  }
],
```

### <a name="update-the-providers-tags"></a>공급자의 태그를 업데이트합니다.

`update` 명령은 사용자 지정 리소스 공급자에 대한 태그만 업데이트합니다. Azure Portal에서 사용자 지정 리소스 공급자의 앱 서비스는 태그를 표시합니다.

```azurecli-interactive
az custom-providers resource-provider update --resource-group $rgName --name $funcName --tags new=tag
```

```json
"tags": {
  "new": "tag"
},
```

### <a name="delete-a-custom-resource-provider"></a>사용자 지정 리소스 공급자 삭제

`delete` 명령은 사용자 지정 리소스 공급자만 표시하고 삭제합니다. 스토리지 계정, 앱 서비스 및 앱 서비스 계획은 삭제되지 않습니다. 공급자를 삭제한 후 명령 프롬프트로 돌아갑니다.

```azurecli-interactive
az custom-providers resource-provider delete --resource-group $rgName --name $funcName
```

## <a name="next-steps"></a>다음 단계

사용자 지정 공급자에 대한 소개는 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [Azure 사용자 지정 공급자 미리 보기 개요](overview.md)
