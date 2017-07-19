---
title: "여러 리소스 그룹에 Azure 리소스 배포 | Microsoft Docs"
description: "배포 중에 둘 이상의 Azure 리소스 그룹을 대상으로 지정하는 방법을 보여 줍니다."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/15/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: d8b041213b269775175a810e585103d3c538557f
ms.contentlocale: ko-kr
ms.lasthandoff: 06/17/2017


---

# <a name="deploy-azure-resources-to-more-than-one-resource-group"></a>둘 이상의 리소스 그룹에 Azure 리소스 배포

일반적으로 단일 리소스 그룹에 템플릿의 모든 리소스를 배포합니다. 그러나 일단의 리소스를 함께 배포하고 다른 리소스 그룹에 배치하려는 시나리오가 있습니다. 예를 들어 Azure Site Recovery의 백업 가상 컴퓨터를 별도의 리소스 그룹과 위치에 배포할 수 있습니다. Resource Manager를 사용하면 중첩된 템플릿을 사용하여 부모 템플릿에 사용된 리소스 그룹과 다른 리소스 그룹을 대상으로 지정할 수 있습니다.

리소스 그룹은 응용 프로그램 및 해당 리소스 컬렉션에 대한 수명 주기 컨테이너입니다. 템플릿 외부에서 리소스 그룹을 만들고 배포 중에 대상으로 지정할 리소스 그룹을 지정합니다. 리소스 그룹에 대한 소개는 [Azure Resource Manager 개요](resource-group-overview.md)를 참조하세요.

## <a name="example-template"></a>예제 템플릿

다른 리소스를 대상으로 지정하려면 배포 중에 중첩되거나 연결된 템플릿을 사용해야 합니다. `Microsoft.Resources/deployments` 리소스 형식은 중첩된 배포에 다른 리소스 그룹을 지정할 수 있는 `resourceGroup` 매개 변수를 제공합니다. 모든 리소스 그룹은 배포를 실행하기 전에 존재해야 합니다. 다음 예제에서는 배포 중에 지정한 리소스 그룹 및 `crossResourceGroupDeployment`이라는 리소스 그룹에 각각 하나씩 두 개의 저장소 계정을 배포합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "StorageAccountName1": {
            "type": "string"
        },
        "StorageAccountName2": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "nestedTemplate",
            "type": "Microsoft.Resources/deployments",
            "resourceGroup": "crossResourceGroupDeployment",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "name": "[parameters('StorageAccountName2')]",
                            "apiVersion": "2015-06-15",
                            "location": "West US",
                            "properties": {
                                "accountType": "Standard_LRS"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('StorageAccountName1')]",
            "apiVersion": "2015-06-15",
            "location": "West US",
            "properties": {
                "accountType": "Standard_LRS"
            }
        }
    ]
}
```

`resourceGroup`을 존재하지 않는 리소스 그룹의 이름으로 설정하면 배포에 실패합니다. `resourceGroup` 값을 제공하지 않으면 Resource Manager에서 부모 리소스 그룹을 사용합니다.  

## <a name="deploy-the-template"></a>템플릿 배포

예제 템플릿을 배포하려면 포털, Azure PowerShell 또는 Azure CLI를 사용할 수 있습니다. Azure PowerShell 또는 Azure CLI의 경우 2017년 5월 이후의 릴리스를 사용해야 합니다. 예제에서는 템플릿을 **crossrgdeployment.json**이라는 이름의 파일로 로컬로 저장한다고 가정합니다.

PowerShell의 경우:

```powershell
Login-AzureRmAccount

New-AzureRmResourceGroup -Name mainResourceGroup -Location "South Central US"
New-AzureRmResourceGroup -Name crossResourceGroupDeployment -Location "Central US"
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName mainResourceGroup `
  -TemplateFile c:\MyTemplates\crossrgdeployment.json
```

Azure CLI의 경우:

```azurecli
az login

az group create --name mainResourceGroup --location "South Central US"
az group create --name crossResourceGroupDeployment --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group mainResourceGroup \
    --template-file crossrgdeployment.json
```

배포가 완료되면 두 개의 리소스 그룹이 표시됩니다. 각 리소스 그룹에는 저장소 계정이 포함되어 있습니다.

## <a name="use-resourcegroup-function"></a>resourceGroup() 함수 사용

교차 리소스 그룹 배포를 위해 [resouceGroup() 함수](resource-group-template-functions-resource.md#resourcegroup)는 중첩 템플릿 지정 방식에 따라 다르게 확인됩니다. 

하나의 템플릿을 다른 템플릿 내에 포함하는 경우 중첩 템플릿의 resouceGroup()가 부모 리소스 그룹으로 확인됩니다. 포함된 템플릿은 다음 형식을 사용합니다.

```json
"apiVersion": "2017-05-10",
"name": "embeddedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "template": {
        ...
        resourceGroup() refers to parent resource group
    }
}
```

별도의 템플릿에 연결하는 경우 연결된 템플릿의 resouceGroup()가 중첩된 리소스 그룹으로 확인됩니다. 연결된 템플릿은 다음 형식을 사용합니다.

```json
"apiVersion": "2017-05-10",
"name": "linkedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "templateLink": {
        ...
        resourceGroup() in linked template refers to linked resource group
    }
}
```

## <a name="next-steps"></a>다음 단계

* 템플릿에서 매개 변수를 정의하는 방법을 이해하려면 [Azure Resource Manager 템플릿의 구조 및 구문 이해](resource-group-authoring-templates.md)를 참조하세요.
* 일반적인 배포 오류를 해결하는 방법은 [Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 해결](resource-manager-common-deployment-errors.md)을 참조하세요.
* SAS 토큰이 필요한 템플릿을 배포하는 데 관한 내용은 [SAS 토큰으로 개인 템플릿 배포](resource-manager-powershell-sas-token.md)를 참조하세요.

