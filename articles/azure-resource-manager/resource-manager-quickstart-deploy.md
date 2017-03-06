---
title: "Azure에 리소스 배포 | Microsoft Docs"
description: "Azure PowerShell 또는 Azure CLI를 사용하여 Azure에 리소스를 배포합니다. 리소스는 Resource Manager 템플릿에 정의됩니다."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/16/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 41d9476d4ce6b7afc2ba9c757e96db7e8e72d5ae
ms.openlocfilehash: a77354d0719240e5916b5ec945dad75e534802d3
ms.lasthandoff: 02/21/2017


---
# <a name="deploy-resources-to-azure"></a>Azure에 리소스 배포

이 토픽에서는 Azure 구독에 리소스를 배포하는 방법을 보여 줍니다. Azure PowerShell 또는 Azure CLI를 사용하여 솔루션의 인프라를 정의하는 Resource Manager 템플릿을 정의할 수 있습니다.

Resource Manager의 개념에 대한 소개는 [Azure Resource Manager 개요](resource-group-overview.md)를 참조하세요.

## <a name="steps-for-deployment"></a>배포 단계

이 토픽에서는 이 토픽의 [저장소 템플릿 예제](#example-storage-template)를 제공하는 것으로 가정합니다. 다른 템플릿을 사용할 수 있지만, 사용자가 제공하는 매개 변수가 이 토픽에 제공된 매개 변수와 다릅니다.

템플릿을 만든 후 템플릿을 배포하기 위한 일반적인 단계는 다음과 같습니다.

1. 계정에 로그인
2. 사용할 구독 선택(구독이 여러 개 있으며 기본 구독이 아닌 구독을 사용하려는 경우에만 필요한 단계)
3. 리소스 그룹 만들기
4. 템플릿 배포
5. 배포 상태 확인

다음 섹션에서는 [Azure PowerShell](#powershell) 또는 [Azure CLI](#azure-cli)를 사용하여 이러한 단계를 수행하는 방법을 보여줍니다.

## <a name="powershell"></a>PowerShell

1. Azure PowerShell을 설치하는 방법은 [Azure PowerShell cmdlet 시작](/powershell/azureps-cmdlets-docs)을 참조하세요.

2. 신속하게 배포를 시작하려면 다음 cmdlet을 사용합니다.

  ```powershell
  Login-AzureRmAccount
  Set-AzureRmContext -SubscriptionID {subscription-id}

  New-AzureRmResourceGroup -Name ExampleGroup -Location "Central US"
  New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleGroup -TemplateFile c:\MyTemplates\azuredeploy.json 
  ```

  `Set-AzureRmContext` cmdlet은 기본 구독이 아닌 다른 구독을 사용하려는 경우에만 필요합니다. 모든 구독과 해당 ID를 보려면 다음을 사용합니다.

  ```powershell
  Get-AzureRmSubscription
  ```

3. 배포가 완료될 때까지 몇 분 정도 걸릴 수 있습니다. 배포가 완료되면 다음과 비슷한 메시지가 표시됩니다.

  ```powershell
  DeploymentName          : ExampleDeployment
  CorrelationId           : 07b3b233-8367-4a0b-b9bc-7aa189065665
  ResourceGroupName       : ExampleGroup
  ProvisioningState       : Succeeded
  ...
  ```

4. 리소스 그룹 및 저장소 계정이 구독에 배포된 것을 확인하려면 다음을 사용합니다.

  ```powershell
  Get-AzureRmResourceGroup -Name ExampleGroup
  Find-AzureRmResource -ResourceGroupNameEquals ExampleGroup
  ```

5. 템플릿을 배포할 때 템플릿 매개 변수를 PowerShell 매개 변수로 지정할 수 있습니다. 앞의 예제에서는 템플릿 매개 변수를 포함하지 않았기 때문에 템플릿의 기본값이 사용되었습니다. 다른 저장소 계정을 배포하고 저장소 이름 접두사와 저장소 계정 SKU에 대한 매개 변수 값을 입력하려면 다음을 사용합니다.

  ```powershell
  New-AzureRmResourceGroupDeployment -Name ExampleDeployment2 -ResourceGroupName ExampleGroup -TemplateFile c:\MyTemplates\azuredeploy.json -storageNamePrefix "contoso" -storageSKU "Standard_GRS"
  ```

  이제 리소스 그룹에 두 개의 저장소 계정이 있습니다. 

## <a name="azure-cli"></a>Azure CLI

1. Azure CLI를 설치하는 방법은 [Azure CLI 2.0 설치](/cli/azure/install-az-cli2)를 참조하세요.

2. 신속하게 배포를 시작하려면 다음 명령을 사용합니다.

  ```azurecli
  az login
  az account set --subscription {subscription-id}

  az group create --name ExampleGroup --location "Central US"
  az group deployment create --name ExampleDeployment --resource-group ExampleGroup --template-file c:\MyTemplates\azuredeploy.json
  ```

  `az account set` 명령은 기본 구독이 아닌 다른 구독을 사용하려는 경우에만 필요합니다. 모든 구독과 해당 ID를 보려면 다음을 사용합니다.

  ```azurecli
  az account list
  ```

3. 배포가 완료될 때까지 몇 분 정도 걸릴 수 있습니다. 배포가 완료되면 다음과 비슷한 메시지가 표시됩니다.

  ```azurecli
  "provisioningState": "Succeeded",
  ```

4. 리소스 그룹 및 저장소 계정이 구독에 배포된 것을 확인하려면 다음을 사용합니다.

  ```azurecli
  az group show --name ExampleGroup
  az resource list --resource-group ExampleGroup
  ```

5. 템플릿을 배포할 때 템플릿 매개 변수를 PowerShell 매개 변수로 지정할 수 있습니다. 앞의 예제에서는 템플릿 매개 변수를 포함하지 않았기 때문에 템플릿의 기본값이 사용되었습니다. 다른 저장소 계정을 배포하고 저장소 이름 접두사와 저장소 계정 SKU에 대한 매개 변수 값을 입력하려면 다음을 사용합니다.

  ```azurecli
  az group deployment create --name ExampleDeployment2 --resource-group ExampleGroup --template-file c:\MyTemplates\azuredeploy.json --parameters '{"storageNamePrefix":{"value":"contoso"},"storageSKU":{"value":"Standard_GRS"}}'
  ```

  이제 리소스 그룹에 두 개의 저장소 계정이 있습니다. 

## <a name="example-storage-template"></a>예제 저장소 템플릿

다음 예제 템플릿을 사용하여 구독에 저장소 계정을 배포하세요.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name."
      }
    },
    "storageSKU": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS",
        "Premium_LRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "The type of replication to use for the storage account."
      }
    }
  },
  "variables": {
    "storageName": "[concat(parameters('storageNamePrefix'), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-05-01",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {  }
}
```

## <a name="next-steps"></a>다음 단계

* PowerShell을 사용하여 템플릿을 배포하는 방법에 대한 자세한 내용은 [Resource Manager 템플릿 및 Azure PowerShell을 사용하여 리소스 배포](/azure/azure-resource-manager/resource-group-template-deploy)를 참조하세요.
* Azure CLI를 사용하여 템플릿을 배포하는 방법에 대한 자세한 내용은 [Resource Manager 템플릿 및 Azure CLI를 사용하여 리소스 배포](/azure/azure-resource-manager/resource-group-template-deploy-cli)를 참조하세요.




