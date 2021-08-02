---
title: 템플릿 사양을 연결된 템플릿으로 배포
description: 연결된 배포에 기존 템플릿 사양을 배포하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 05/04/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d80442a2ee03bcf85d7173517661d42389542e82
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110696246"
---
# <a name="tutorial-deploy-a-template-spec-as-a-linked-template"></a>자습서: 템플릿 사양을 연결된 템플릿으로 배포

[연결된 배포](linked-templates.md#linked-template)를 사용하여 기존 [템플릿 사양](template-specs.md)을 배포하는 방법에 대해 알아봅니다. 템플릿 사양을 사용하여 조직 내 다른 사용자와 ARM 템플릿을 공유합니다. 템플릿 사양을 만든 후 Azure PowerShell 또는 Azure CLI를 사용하여 템플릿 사양을 배포할 수 있습니다. 연결된 템플릿을 사용하여 솔루션의 일부로 템플릿 사양을 배포할 수도 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> Azure PowerShell에서 템플릿 사양을 사용하려면 [버전 5.0.0 이상](/powershell/azure/install-az-ps)을 설치해야 합니다. Azure CLI에서 사용하려면 [버전 2.14.2 이상](/cli/azure/install-azure-cli)을 사용합니다.

## <a name="create-a-template-spec"></a>템플릿 사양 만들기

[빠른 시작: 템플릿 사양 만들기 및 배포](quickstart-create-template-specs.md)를 수행하여 스토리지 계정을 배포하기 위한 템플릿 사양을 만듭니다. 다음 섹션에서 템플릿 사양, 템플릿 사양 이름 및 템플릿 사양 버전의 리소스 그룹 이름이 필요합니다.

## <a name="create-the-main-template"></a>기본 템플릿 만들기

ARM 템플릿에서 템플릿 사양을 배포하려면 [배포 리소스](/azure/templates/microsoft.resources/deployments)를 기본 템플릿에 추가합니다. `templateLink` 속성에서 템플릿 사양의 리소스 ID를 지정합니다. **azuredeploy.json** 이라는 다음 JSON으로 템플릿을 만듭니다. 이 자습서에서는 경로 **c:\Templates\deployTS\azuredeploy.json** 에 저장했지만 모든 경로를 사용할 수 있다고 가정합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "tsResourceGroup":{
      "type": "string",
      "metadata": {
        "Description": "Specifies the resource group name of the template spec."
      }
    },
    "tsName": {
      "type": "string",
      "metadata": {
        "Description": "Specifies the name of the template spec."
      }
    },
    "tsVersion": {
      "type": "string",
      "defaultValue": "1.0.0.0",
      "metadata": {
        "Description": "Specifies the version the template spec."
      }
    },
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "metadata": {
        "Description": "Specifies the storage account type required by the template spec."
      }
    }
  },
  "variables": {
    "appServicePlanName": "[concat('plan', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Web/serverfarms",
      "apiVersion": "2016-09-01",
      "name": "[variables('appServicePlanName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "B1",
        "tier": "Basic",
        "size": "B1",
        "family": "B",
        "capacity": 1
      },
      "kind": "linux",
      "properties": {
        "perSiteScaling": false,
        "reserved": true,
        "targetWorkerCount": 0,
        "targetWorkerSizeId": 0
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "createStorage",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "id": "[resourceId(parameters('tsResourceGroup'), 'Microsoft.Resources/templateSpecs/versions', parameters('tsName'), parameters('tsVersion'))]"
        },
        "parameters": {
          "storageAccountType": {
            "value": "[parameters('storageAccountType')]"
          }
        }
      }
    }
  ],
  "outputs": {
    "templateSpecId": {
      "type": "string",
      "value": "[resourceId(parameters('tsResourceGroup'), 'Microsoft.Resources/templateSpecs/versions', parameters('tsName'), parameters('tsVersion'))]"
    }
  }
}
```

템플릿 사양 ID는 [`resourceID()`](template-functions-resource.md#resourceid) 함수를 사용하여 생성됩니다. TemplateSpec이 현재 배포의 동일한 리소스 그룹에 있는 경우 resourceID() 함수의 리소스 그룹 인수는 선택 사항입니다.  리소스 ID를 매개 변수로 직접 전달할 수도 있습니다. ID를 가져오려면 다음을 사용합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$id = (Get-AzTemplateSpec -ResourceGroupName $resourceGroupName -Name $templateSpecName -Version $templateSpecVersion).Versions.Id
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli-interactive
id = $(az ts show --name $templateSpecName --resource-group $resourceGroupName --version $templateSpecVersion --query "id")
```

> [!NOTE]
> 템플릿 사양 ID를 가져와서 Windows PowerShell의 변수에 할당하는 것과 관련된 알려진 문제가 있습니다.

---

템플릿 사양에 매개 변수 전달하기 위한 구문은 다음과 같습니다.

```json
"parameters": {
  "storageAccountType": {
    "value": "[parameters('storageAccountType')]"
  }
}
```

> [!NOTE]
> `Microsoft.Resources/deployments`의 apiVersion은 2020-06-01 이후여야 합니다.

## <a name="deploy-the-template"></a>템플릿 배포

연결된 템플릿을 배포하는 경우 웹 애플리케이션 및 스토리지 계정을 모두 배포합니다. 배포는 다른 ARM 템플릿을 배포하는 것과 동일합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name webRG `
  -Location westus2

New-AzResourceGroupDeployment `
  -ResourceGroupName webRG `
  -TemplateFile "c:\Templates\deployTS\azuredeploy.json" `
  -tsResourceGroup templateSpecRg `
  -tsName storageSpec `
  -tsVersion 1.0
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az group create \
  --name webRG \
  --location westus2

az deployment group create \
  --resource-group webRG \
  --template-file "c:\Templates\deployTS\azuredeploy.json" \
  --parameters tsResourceGroup=templateSpecRG tsName=storageSpec tsVersion=1.0
```

---

## <a name="next-steps"></a>다음 단계

연결된 템플릿을 포함하는 템플릿 사양을 만드는 방법에 대한 자세한 내용은 [연결된 템플릿의 템플릿 사양 만들기](template-specs-create-linked.md)를 참조하세요.
