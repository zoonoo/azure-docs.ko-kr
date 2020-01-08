---
title: Azure Resource Manager 템플릿으로 작업 영역 만들기
titleSuffix: Azure Machine Learning
description: Azure Resource Manager 템플릿을 사용 하 여 새 Azure Machine Learning 작업 영역을 만드는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 11/04/2019
ms.custom: seoapril2019
ms.openlocfilehash: 63638dccbe489a6d63d4c1875d68ca12f0015836
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/07/2020
ms.locfileid: "75689179"
---
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>Azure Resource Manager 템플릿을 사용 하 여 Azure Machine Learning에 대 한 작업 영역을 만듭니다.

이 문서에서는 Azure Resource Manager 템플릿을 사용 하 여 Azure Machine Learning 작업 영역을 만드는 여러 가지 방법을 알아봅니다. Resource Manager 템플릿을 사용하면 조정된 단일 작업으로 리소스를 쉽게 만들 수 있습니다. 템플릿은 배포에 필요한 리소스를 정의하는 JSON 문서입니다. 배포 매개 변수도 지정할 수 있습니다. 매개 변수는 템플릿 사용 시 입력 값을 제공하는 데 사용됩니다.

자세한 내용은 [Azure 리소스 관리자 템플릿을 사용하여 애플리케이션 배포](../azure-resource-manager/templates/deploy-powershell.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

* **Azure 구독**. 없는 경우 [무료 또는 유료 버전의 Azure Machine Learning](https://aka.ms/AMLFree)을 사용해 보세요.

* CLI에서 템플릿을 사용하려면 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) 또는 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)가 필요합니다.

## <a name="resource-manager-template"></a>Resource Manager 템플릿

다음 리소스 관리자 템플릿을 사용 하 여 Azure Machine Learning 작업 영역 및 연결 된 Azure 리소스를 만들 수 있습니다.

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

    다른 서비스의 이름은 임의로 생성됩니다.

> [!TIP]
> 이 문서와 연결 된 템플릿은 새 Azure Container Registry을 만들지만 컨테이너 레지스트리를 만들지 않고 새 작업 영역을 만들 수도 있습니다. 컨테이너 레지스트리가 작업 영역에 있는 경우 컨테이너 레지스트리가 필요한 작업을 수행할 때 생성 됩니다. 예를 들어 모델을 학습 하거나 배포 합니다.
>
> 새 컨테이너 레지스트리를 만드는 대신 Azure Resource Manager 템플릿에서 기존 컨테이너 레지스트리 또는 저장소 계정을 참조할 수도 있습니다.

템플릿에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure 리소스 관리자 템플릿 작성](../azure-resource-manager/templates/template-syntax.md)
* [Azure Resource Manager 템플릿으로 애플리케이션 배포](../azure-resource-manager/templates/deploy-powershell.md)
* [Microsoft.MachineLearningServices 리소스 종류](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="use-the-azure-portal"></a>Azure Portal 사용

1. [사용자 지정 템플릿에서 리소스 배포](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template)의 단계를 수행합니다. __템플릿 편집__ 화면이 표시되면 이 문서의 템플릿을 붙여넣습니다.
1. __저장__을 선택하여 템플릿을 사용합니다. 다음 정보를 제공하고 나열된 사용 약관에 동의합니다.

   * 구독: 이러한 리소스에 사용할 Azure 구독을 선택 합니다.
   * 리소스 그룹: 서비스를 포함할 리소스 그룹을 선택 하거나 만듭니다.
   * 작업 영역 이름: 생성 될 Azure Machine Learning 작업 영역에 사용할 이름입니다. 작업 영역 이름은 3자에서 33자 사이여야 합니다. 영숫자와 ‘-’만 포함할 수 있습니다.
   * 위치: 리소스를 만들 위치를 선택 합니다.

자세한 내용은 [사용자 지정 템플릿에서 리소스 배포](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)를 참조하세요.

## <a name="use-azure-powershell"></a>Azure PowerShell 사용

이 예제에서는 현재 디렉터리의 `azuredeploy.json` 파일에 템플릿을 저장했다고 가정합니다.

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace" -sku "basic"
```

자세한 내용은 [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](../azure-resource-manager/resource-group-template-deploy.md) 및 [SAS 토큰과 Azure PowerShell을 사용하여 프라이빗 Resource Manager 템플릿 배포](../azure-resource-manager/secure-template-with-sas-token.md)를 참조하세요.

## <a name="use-azure-cli"></a>Azure CLI 사용

이 예제에서는 현재 디렉터리의 `azuredeploy.json` 파일에 템플릿을 저장했다고 가정합니다.

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus sku=basic
```

자세한 내용은 [Resource Manager 템플릿과 Azure CLI로 리소스 배포](../azure-resource-manager/resource-group-template-deploy-cli.md) 및 [SAS 토큰과 Azure CLI를 사용하여 프라이빗 Resource Manager 템플릿 배포](../azure-resource-manager/secure-template-with-sas-token.md)를 참조하세요.

## <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault 액세스 정책 및 Azure Resource Manager 템플릿

Azure Resource Manager 템플릿을 사용 하 여 작업 영역 및 연결 된 리소스 (Azure Key Vault 포함)를 여러 번 만드는 경우 예를 들어 연속 통합 및 배포 파이프라인의 일부와 동일한 매개 변수를 사용 하 여 템플릿을 여러 번 사용 합니다.

템플릿을 통한 대부분의 리소스 생성 작업은 idempotent 템플릿이 사용 될 때마다 액세스 정책을 지우는 Key Vault. 액세스 정책을 지우면 사용 중인 기존 작업 영역에 대 한 Key Vault 액세스가 중단 됩니다. 예를 들어 Azure Notebooks VM의 중지/만들기 기능이 실패할 수 있습니다.  

이 문제를 방지 하려면 다음 방법 중 하나를 사용 하는 것이 좋습니다.

* 동일한 매개 변수에 템플릿을 두 번 이상 배포 하지 마십시오. 또는 템플릿을 사용 하 여 기존 리소스를 다시 만들기 전에 삭제 합니다.
  
* Key Vault 액세스 정책을 검토 한 후 이러한 정책을 사용 하 여 템플릿의 `accessPolicies` 속성을 설정 합니다. 액세스 정책을 보려면 다음 Azure CLI 명령을 사용 합니다.

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query properties.accessPolicies
    ```

    템플릿의 `accessPolicies` 섹션 사용에 대 한 자세한 내용은 [AccessPolicyEntry 개체 참조](https://docs.microsoft.com/azure/templates/Microsoft.KeyVault/2018-02-14/vaults#AccessPolicyEntry)를 참조 하세요.

* Key Vault 리소스가 이미 있는지 확인 합니다. 이 경우 템플릿을 통해 다시 만들지 마세요. 예를 들어 새 항목을 만드는 대신 기존 Key Vault를 사용 하려면 템플릿을 다음과 같이 변경 합니다.

    * 기존 Key Vault 리소스의 ID를 허용 하는 매개 변수를 **추가** 합니다.

        ```json
        "keyVaultId":{
          "type": "string",
          "metadata": {
            "description": "Specify the existing Key Vault ID."
          }
        }
      ```

    * Key Vault 리소스를 만드는 섹션을 **제거** 합니다.

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
    
    * 작업 영역의 `dependsOn` 섹션에서 `"[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",` 줄을 **제거** 합니다. 또한 작업 영역의 `properties` 섹션에서 `keyVaultId` 매개 변수를 참조 하도록 `keyVault` 항목을 **변경** 합니다.

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
      
    이러한 변경 후 템플릿을 실행할 때 기존 Key Vault 리소스의 ID를 지정할 수 있습니다. 그러면 템플릿에서 작업 영역의 `keyVault` 속성을 해당 ID로 설정 하 여 Key Vault를 다시 사용 합니다.

    Key Vault의 ID를 가져오려면 원래 템플릿 실행의 출력을 참조 하거나 Azure CLI를 사용 합니다. 다음 명령은 Azure CLI를 사용 하 여 Key Vault 리소스 ID를 가져오는 예입니다.

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query id
    ```

    이 명령은 다음 텍스트와 유사한 값을 반환합니다.

    ```text
    /subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault
    ```



## <a name="next-steps"></a>다음 단계

* [Resource Manager 템플릿 및 Resource Manager REST API를 사용하여 리소스 배포](../azure-resource-manager/resource-group-template-deploy-rest.md)
* [Visual Studio를 통해 Azure 리소스 그룹 만들기 및 배포](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)
