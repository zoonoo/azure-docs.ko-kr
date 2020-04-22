---
title: Azure 리소스 관리자 템플릿으로 작업 영역 만들기
titleSuffix: Azure Machine Learning
description: Azure 리소스 관리자 템플릿을 사용하여 새 Azure 기계 학습 작업 영역을 만드는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 03/05/2020
ms.custom: seoapril2019
ms.openlocfilehash: b802a9c9df7e7f0c44ea66ee0061efb517b80050
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682749"
---
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]
<br>

# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>Azure 리소스 관리자 템플릿을 사용하여 Azure 기계 학습을 위한 작업 영역 을 만듭니다.

이 문서에서는 Azure 리소스 관리자 템플릿을 사용하여 Azure 기계 학습 작업 영역을 만드는 몇 가지 방법을 알아봅니다. Resource Manager 템플릿을 사용하면 조정된 단일 작업으로 리소스를 쉽게 만들 수 있습니다. 템플릿은 배포에 필요한 리소스를 정의하는 JSON 문서입니다. 배포 매개 변수도 지정할 수 있습니다. 매개 변수는 템플릿 사용 시 입력 값을 제공하는 데 사용됩니다.

자세한 내용은 [Azure 리소스 관리자 템플릿을 사용하여 애플리케이션 배포](../azure-resource-manager/templates/deploy-powershell.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* **Azure 구독**. 없는 경우 [Azure 기계 학습의 무료 또는 유료 버전을](https://aka.ms/AMLFree)사용해 보십시오.

* CLI에서 템플릿을 사용하려면 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) 또는 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)가 필요합니다.

## <a name="resource-manager-template"></a>Resource Manager 템플릿

다음 리소스 관리자 템플릿을 사용하여 Azure 기계 학습 작업 영역 및 연결된 Azure 리소스를 만들 수 있습니다.

[!code-json[create-azure-machine-learning-service-workspace](~/quickstart-templates/101-machine-learning-create/azuredeploy.json)]

이 템플릿은 다음과 같은 Azure 서비스를 만듭니다.

* Azure 리소스 그룹
* Azure Storage 계정
* Azure Key Vault
* Azure Application Insights
* Azure Container Registry
* Azure Machine Learning 작업 영역

리소스 그룹은 서비스를 보관하는 컨테이너입니다. Azure Machine Learning 작업 영역에는 다양한 서비스가 필요합니다.

예제 템플릿에는 다음 두 개의 매개 변수가 있습니다.

* **location** - 리소스 그룹 및 서비스가 생성되는 위치입니다.

    선택한 위치는 템플릿에서 대부분의 리소스에 사용됩니다. 단, 다른 서비스를 사용할 수 있는 일부 위치에서 사용할 수 없는 Application Insights 서비스는 예외입니다. 서비스를 사용할 수 없는 위치를 선택하면 미국 중남부 위치에 서비스가 생성됩니다.

* **workspace name** - Azure Machine Learning 작업 영역의 이름입니다.

    > [!NOTE]
    > 작업 영역 이름은 대/소문자를 구분하지 않습니다.

    다른 서비스의 이름은 임의로 생성됩니다.

> [!TIP]
> 이 문서와 연결된 템플릿이 새 Azure 컨테이너 레지스트리를 만드는 동안 컨테이너 레지스트리를 만들지 않고 새 작업 영역을 만들 수도 있습니다. 컨테이너 레지스트리가 필요한 작업을 수행할 때 하나가 만들어집니다. 예를 들어 모델을 교육하거나 배포합니다.
>
> 새 저장소를 만드는 대신 Azure 리소스 관리자 템플릿에서 기존 컨테이너 레지스트리 또는 저장소 계정을 참조할 수도 있습니다.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

템플릿에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure 리소스 관리자 템플릿 작성](../azure-resource-manager/templates/template-syntax.md)
* [Azure Resource Manager 템플릿으로 애플리케이션 배포](../azure-resource-manager/templates/deploy-powershell.md)
* [Microsoft.MachineLearningServices 리소스 종류](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

### <a name="advanced-template"></a>고급 템플릿

다음 예제 템플릿에서는 세 가지 설정을 사용하여 작업 영역을 만드는 방법을 보여 줍니다.

* 작업 영역에 대한 높은 기밀 설정 사용
* 작업 영역에 대한 암호화 사용
* 기존 Azure 키 자격 증명 모음을 사용하여 고객 관리 키 검색

자세한 내용은 [미사용 암호화를](concept-enterprise-security.md#encryption-at-rest)참조하십시오.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the Azure Machine Learning workspace."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "southcentralus",
      "allowedValues": [
        "eastus",
        "eastus2",
        "southcentralus",
        "southeastasia",
        "westcentralus",
        "westeurope",
        "westus2"
      ],
      "metadata": {
        "description": "Specifies the location for all resources."
      }
    },
    "sku":{
      "type": "string",
      "defaultValue": "basic",
      "allowedValues": [
        "basic",
        "enterprise"
      ],
      "metadata": {
        "description": "Specifies the sku, also referred to as 'edition' of the Azure Machine Learning workspace."
      }
    },
    "high_confidentiality":{
      "type": "string",
      "defaultValue": "false",
      "allowedValues": [
        "false",
        "true"
      ],
      "metadata": {
        "description": "Specifies that the Azure Machine Learning workspace holds highly confidential data."
      }
    },
    "encryption_status":{
      "type": "string",
      "defaultValue": "Disabled",
      "allowedValues": [
        "Enabled",
        "Disabled"
      ],
      "metadata": {
        "description": "Specifies if the Azure Machine Learning workspace should be encrypted with the customer managed key."
      }
    },
    "cmk_keyvault":{
      "type": "string",
      "metadata": {
        "description": "Specifies the customer managed keyvault Resource Manager ID."
      }
    },
    "resource_cmk_uri":{
      "type": "string",
      "metadata": {
        "description": "Specifies the customer managed keyvault key uri."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat('sa',uniqueString(resourceGroup().id))]",
    "storageAccountType": "Standard_LRS",
    "keyVaultName": "[concat('kv',uniqueString(resourceGroup().id))]",
    "tenantId": "[subscription().tenantId]",
    "applicationInsightsName": "[concat('ai',uniqueString(resourceGroup().id))]",
    "containerRegistryName": "[concat('cr',uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2018-07-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[variables('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {
        "encryption": {
          "services": {
            "blob": {
              "enabled": true
            },
            "file": {
              "enabled": true
            }
          },
          "keySource": "Microsoft.Storage"
        },
        "supportsHttpsTrafficOnly": true
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2018-02-14",
      "name": "[variables('keyVaultName')]",
      "location": "[parameters('location')]",
      "properties": {
        "tenantId": "[variables('tenantId')]",
        "sku": {
          "name": "standard",
          "family": "A"
        },
        "accessPolicies": []
      }
    },
    {
      "type": "Microsoft.Insights/components",
      "apiVersion": "2015-05-01",
      "name": "[variables('applicationInsightsName')]",
      "location": "[if(or(equals(parameters('location'),'eastus2'),equals(parameters('location'),'westcentralus')),'southcentralus',parameters('location'))]",
      "kind": "web",
      "properties": {
        "Application_Type": "web"
      }
    },
    {
      "type": "Microsoft.ContainerRegistry/registries",
      "apiVersion": "2017-10-01",
      "name": "[variables('containerRegistryName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard"
      },
      "properties": {
        "adminUserEnabled": true
      }
    },
    {
      "type": "Microsoft.MachineLearningServices/workspaces",
      "apiVersion": "2020-01-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",
        "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]",
        "[resourceId('Microsoft.ContainerRegistry/registries', variables('containerRegistryName'))]"
      ],
      "identity": {
        "type": "systemAssigned"
      },
      "sku": {
            "tier": "[parameters('sku')]",
            "name": "[parameters('sku')]"
      },
      "properties": {
        "friendlyName": "[parameters('workspaceName')]",
        "keyVault": "[resourceId('Microsoft.KeyVault/vaults',variables('keyVaultName'))]",
        "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
        "containerRegistry": "[resourceId('Microsoft.ContainerRegistry/registries',variables('containerRegistryName'))]",
        "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]",
         "encryption": {
                "status": "[parameters('encryption_status')]",
                "keyVaultProperties": {
                    "keyVaultArmId": "[parameters('cmk_keyvault')]",
                    "keyIdentifier": "[parameters('resource_cmk_uri')]"
                  }
            },
        "hbiWorkspace": "[parameters('high_confidentiality')]"
      }
    }
  ]
}
```

키 볼트의 ID와 이 템플릿에 필요한 키 URI를 얻으려면 Azure CLI를 사용할 수 있습니다. 다음 명령은 키 볼트 ID를 가져옵니다.

```azurecli-interactive
az keyvault show --name mykeyvault --resource-group myresourcegroup --query "id"
```

이 명령은 `"/subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault"`와 비슷한 값을 반환합니다.

고객이 관리하는 키에 대한 URI를 얻으려면 다음 명령을 사용합니다.

```azurecli-interactive
az keyvault key show --vault-name mykeyvault --name mykey --query "key.kid"
```

이 명령은 `"https://mykeyvault.vault.azure.net/keys/mykey/{guid}"`와 비슷한 값을 반환합니다.

> [!IMPORTANT]
> 작업 영역이 만들어지면 기밀 데이터, 암호화, 키 자격 증명 모음 ID 또는 키 식별자에 대한 설정을 변경할 수 없습니다. 이러한 값을 변경하려면 새 값을 사용하여 새 작업 영역을 만들어야 합니다.

## <a name="use-the-azure-portal"></a>Azure Portal 사용

1. [사용자 지정 템플릿에서 리소스 배포](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template)의 단계를 수행합니다. __템플릿 편집__ 화면이 표시되면 이 문서의 템플릿을 붙여넣습니다.
1. __저장__을 선택하여 템플릿을 사용합니다. 다음 정보를 제공하고 나열된 사용 약관에 동의합니다.

   * 구독: 이러한 리소스에 사용할 Azure 구독을 선택합니다.
   * 리소스 그룹: 서비스를 포함할 리소스 그룹을 선택하거나 만듭니다.
   * 작업 영역 이름: 생성될 Azure 기계 학습 작업 영역에 사용할 이름입니다. 작업 영역 이름은 3자에서 33자 사이여야 합니다. 영숫자와 ‘-’만 포함할 수 있습니다.
   * 위치: 리소스가 생성될 위치를 선택합니다.

자세한 내용은 [사용자 지정 템플릿에서 리소스 배포](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)를 참조하세요.

## <a name="use-azure-powershell"></a>Azure PowerShell 사용

이 예제에서는 현재 디렉터리의 `azuredeploy.json` 파일에 템플릿을 저장했다고 가정합니다.

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace" -sku "basic"
```

자세한 내용은 [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](../azure-resource-manager/templates/deploy-powershell.md) 및 [SAS 토큰과 Azure PowerShell을 사용하여 프라이빗 Resource Manager 템플릿 배포](../azure-resource-manager/templates/secure-template-with-sas-token.md)를 참조하세요.

## <a name="use-the-azure-cli"></a>Azure CLI 사용

이 예제에서는 현재 디렉터리의 `azuredeploy.json` 파일에 템플릿을 저장했다고 가정합니다.

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus sku=basic
```

자세한 내용은 [Resource Manager 템플릿과 Azure CLI로 리소스 배포](../azure-resource-manager/templates/deploy-cli.md) 및 [SAS 토큰과 Azure CLI를 사용하여 프라이빗 Resource Manager 템플릿 배포](../azure-resource-manager/templates/secure-template-with-sas-token.md)를 참조하세요.

## <a name="troubleshooting"></a>문제 해결

### <a name="resource-provider-errors"></a>리소스 공급자 오류

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure 키 볼트 액세스 정책 및 Azure 리소스 관리자 템플릿

Azure 리소스 관리자 템플릿을 사용하여 작업 영역 및 관련 리소스(Azure Key Vault 포함)를 여러 번 만드는 경우 예를 들어 연속 통합 및 배포 파이프라인의 일부로 동일한 매개 변수를 사용하여 템플릿을 여러 번 사용합니다.

템플릿을 통한 대부분의 리소스 만들기 작업은 idempotent이지만 Key Vault는 템플릿을 사용할 때마다 액세스 정책을 지웁습니다. 액세스 정책을 지우면 해당 정책을 사용하는 기존 작업 영역에 대한 Key Vault에 대한 액세스가 중단됩니다. 예를 들어 Azure 전자 필기장 VM의 중지/만들기 기능이 실패할 수 있습니다.  

이 문제를 방지하려면 다음 방법 중 하나를 사용하는 것이 좋습니다.

* 동일한 매개 변수에 대해 템플릿을 두 번 이상 배포하지 마십시오. 또는 템플릿을 사용하여 다시 만들기 전에 기존 리소스를 삭제합니다.

* Key Vault 액세스 정책을 검사한 다음 이러한 `accessPolicies` 정책을 사용하여 템플릿의 속성을 설정합니다. 액세스 정책을 보려면 다음 Azure CLI 명령을 사용합니다.

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query properties.accessPolicies
    ```

    템플릿 섹션 `accessPolicies` 사용에 대한 자세한 내용은 [AccessPolicyEntry 개체 참조를](https://docs.microsoft.com/azure/templates/Microsoft.KeyVault/2018-02-14/vaults#AccessPolicyEntry)참조하십시오.

* 키 볼트 리소스가 이미 있는지 확인합니다. 이 경우 템플릿을 통해 다시 만들지 마십시오. 예를 들어 새 키 볼트를 만드는 대신 기존 키 볼트를 사용하려면 템플릿을 다음과 같은 변경 사항으로 변경합니다.

    * 기존 키 볼트 리소스의 ID를 허용하는 매개 변수를 **추가합니다.**

        ```json
        "keyVaultId":{
          "type": "string",
          "metadata": {
            "description": "Specify the existing Key Vault ID."
          }
        }
      ```

    * 키 볼트 리소스를 만드는 섹션을 **제거합니다.**

        ```json
        {
          "type": "Microsoft.KeyVault/vaults",
          "apiVersion": "2018-02-14",
          "name": "[variables('keyVaultName')]",
          "location": "[parameters('location')]",
          "properties": {
            "tenantId": "[variables('tenantId')]",
            "sku": {
              "name": "standard",
              "family": "A"
            },
            "accessPolicies": [
            ]
          }
        },
        ```

    * 작업 `"[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",` 영역의 `dependsOn` 섹션에서 선을 **제거합니다.** 또한 작업 `keyVault` 영역의 `properties` 섹션에 있는 항목을 `keyVaultId` **변경하여** 매개 변수를 참조합니다.

        ```json
        {
          "type": "Microsoft.MachineLearningServices/workspaces",
          "apiVersion": "2019-11-01",
          "name": "[parameters('workspaceName')]",
          "location": "[parameters('location')]",
          "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
            "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]"
          ],
          "identity": {
            "type": "systemAssigned"
          },
          "sku": {
            "tier": "[parameters('sku')]",
            "name": "[parameters('sku')]"
          },
          "properties": {
            "friendlyName": "[parameters('workspaceName')]",
            "keyVault": "[parameters('keyVaultId')]",
            "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
            "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]"
          }
        }
        ```

    이러한 변경 후 템플릿을 실행할 때 기존 Key Vault 리소스의 ID를 지정할 수 있습니다. 그러면 템플릿은 작업 영역의 `keyVault` 속성을 ID로 설정하여 키 자격 증명 모음을 다시 사용합니다.

    키 볼트의 ID를 얻으려면 원래 템플릿 실행의 출력을 참조하거나 Azure CLI를 사용할 수 있습니다. 다음 명령은 Azure CLI를 사용하여 키 볼트 리소스 ID를 얻는 예제입니다.

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query id
    ```

    이 명령은 다음 텍스트와 유사한 값을 반환합니다.

    ```text
    /subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault
    ```

## <a name="next-steps"></a>다음 단계

* [리소스 관리자 템플릿 및 리소스 관리자 REST API를 사용 하 고 리소스를 배포합니다.](../azure-resource-manager/templates/deploy-rest.md)
* [Visual Studio를 통해 Azure 리소스 그룹을 만들고 배포합니다.](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)
