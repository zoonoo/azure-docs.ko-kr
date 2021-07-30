---
title: 연결된 템플릿을 사용하여 템플릿 사양 만들기
description: 연결된 템플릿을 사용하여 템플릿 사양을 만드는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 05/04/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c4bcefadab5e9fcf19c06e3469ad077764355dad
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110696351"
---
# <a name="tutorial-create-a-template-spec-with-linked-templates"></a>자습서: 연결된 템플릿을 사용하여 템플릿 사양 만들기.

기본 템플릿과 [연결된 템플릿](linked-templates.md#linked-template)을 사용하여 [템플릿 사양](template-specs.md)을 만드는 방법에 대해 알아봅니다. 템플릿 사양을 사용하여 조직 내 다른 사용자와 ARM 템플릿을 공유합니다. 이 문서에서는 [배포 리소스](/azure/templates/microsoft.resources/deployments)의 `relativePath` 속성을 사용하여 기본 템플릿 및 연결된 템플릿을 패키징하는 템플릿 사양을 만드는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> Azure PowerShell에서 템플릿 사양을 사용하려면 [버전 5.0.0 이상](/powershell/azure/install-az-ps)을 설치해야 합니다. Azure CLI에서 사용하려면 [버전 2.14.2 이상](/cli/azure/install-azure-cli)을 사용합니다.

## <a name="create-linked-templates"></a>연결된 템플릿 만들기

기본 템플릿과 연결된 템플릿을 만듭니다.

템플릿에 연결하려면 [배포 리소스](/azure/templates/microsoft.resources/deployments)를 기본 템플릿에 추가합니다. `templateLink` 속성에서 연결된 템플릿의 상대 경로를 부모 템플릿에 따라 지정합니다.

연결 된 템플릿은 **에서linkedTemplate.json** 이라고 하며 주 템플릿이 저장 된 경로에서 **아티팩트** 라는 하위 폴더에 저장 됩니다.  relativePath에 대해 다음 값 중 하나를 사용할 수 있습니다.

- `./artifacts/linkedTemplate.json`
- `/artifacts/linkedTemplate.json`
- `artifacts/linkedTemplate.json`

`relativePath` 속성은 항상 `relativePath`가 선언된 템플릿 파일을 기준으로 합니다. 따라서 linkedTemplate.json에서 호출되는 또 다른 linkedTemplate2.json이 존재하고 linkedTemplate2.json이 동일한 아티팩트 하위 폴더에 저장되는 경우에는 linkedTemplate.json에 지정되는 relativePath는 `linkedTemplate2.json`만 됩니다.

1. 다음 JSON을 사용하여 기본 템플릿을 만듭니다. 기본 템플릿을 로컬 컴퓨터에 **azuredeploy.json** 으로 저장합니다. 이 자습서에서는 경로 **c:\Templates\linkedTS\azuredeploy.json** 에 저장했지만 모든 경로를 사용할 수 있다고 가정합니다.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "westus2",
          "metadata":{
            "description": "Specify the location for the resources."
          }
        },
        "storageAccountType": {
          "type": "string",
          "defaultValue": "Standard_LRS",
          "metadata":{
            "description": "Specify the storage account type."
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
              "relativePath": "artifacts/linkedTemplate.json"
            },
            "parameters": {
              "storageAccountType": {
                "value": "[parameters('storageAccountType')]"
              }
            }
          }
        }
      ]
    }
    ```

    > [!NOTE]
    > `Microsoft.Resources/deployments`의 apiVersion은 2020-06-01 이상이어야 합니다.

1. 기본 템플릿이 저장된 폴더에 **아티팩트** 라는 디렉터리를 만듭니다.
1. 다음 JSON을 사용하여 연결된 템플릿을 만듭니다.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "storageAccountType": {
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
        "location": {
          "type": "string",
          "defaultValue": "[resourceGroup().location]",
          "metadata": {
            "description": "Location for all resources."
          }
        }
      },
      "variables": {
        "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
      },
      "resources": [
        {
          "type": "Microsoft.Storage/storageAccounts",
          "apiVersion": "2019-04-01",
          "name": "[variables('storageAccountName')]",
          "location": "[parameters('location')]",
          "sku": {
            "name": "[parameters('storageAccountType')]"
          },
          "kind": "StorageV2",
          "properties": {}
        }
      ],
      "outputs": {
        "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
        }
      }
    }
    ```

1. 템플릿을 **아티팩트** 폴더에 **linkedTemplate.json** 으로 저장합니다.

## <a name="create-template-spec"></a>템플릿 사양 만들기

템플릿 사양은 리소스 그룹에 저장됩니다.  리소스 그룹을 만든 후 다음 스크립트를 사용하여 템플릿 사양을 만듭니다. 템플릿 사양 이름은 **webSpec** 입니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name templateSpecRG `
  -Location westus2

New-AzTemplateSpec `
  -Name webSpec `
  -Version "1.0.0.0" `
  -ResourceGroupName templateSpecRG `
  -Location westus2 `
  -TemplateFile "c:\Templates\linkedTS\azuredeploy.json"
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az group create \
  --name templateSpecRG \
  --location westus2

az ts create \
  --name webSpec \
  --version "1.0.0.0" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "<path-to-main-template>"
```

---

작업이 완료되면 Azure Portal에서 또는 다음 cmdlet을 사용하여 템플릿 사양을 볼 수 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Get-AzTemplateSpec -ResourceGroupName templatespecRG -Name webSpec
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts show --name webSpec --resource-group templateSpecRG --version "1.0.0.0"
```

---

## <a name="deploy-template-spec"></a>템플릿 사양 배포

이제 템플릿 사양을 배포할 수 있습니다. 템플릿 사양 배포는 템플릿 사양의 리소스 ID를 전달하는 것을 제외하고 포함된 템플릿을 배포하는 것과 같습니다. 동일한 배포 명령을 사용하고 필요한 경우 템플릿 사양에 대한 매개 변수 값을 전달합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name webRG `
  -Location westus2

$id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name webSpec -Version "1.0.0.0").Versions.Id

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName webRG
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az group create \
  --name webRG \
  --location westus2

id = $(az ts show --name webSpec --resource-group templateSpecRG --version "1.0.0.0" --query "id")

az deployment group create \
  --resource-group webRG \
  --template-spec $id
```

> [!NOTE]
> 템플릿 사양 ID를 가져와서 Windows PowerShell의 변수에 할당하는 것과 관련된 알려진 문제가 있습니다.

---

## <a name="next-steps"></a>다음 단계

템플릿 사양을 연결된 템플릿으로 배포하는 방법은 [자습서: 템플릿 사양을 연결된 템플릿으로 배포](template-specs-deploy-linked-template.md)를 참조하십시오.
