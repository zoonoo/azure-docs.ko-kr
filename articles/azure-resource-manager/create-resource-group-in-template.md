---
title: Azure Resource Manager 템플릿에서 리소스 그룹 만들기 | Microsoft Docs
description: Azure Resource Manager 템플릿에서 새 리소스 그룹을 만드는 방법을 설명합니다.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/07/2018
ms.author: tomfitz
ms.openlocfilehash: 90d21ac817f6fd4730ff4a7e98500a80af10ac70
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2018
ms.locfileid: "39623211"
---
# <a name="create-resource-groups-in-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿에서 리소스 그룹 만들기

Azure Resource Manager 템플릿에서 리소스 그룹을 만들려면 리소스 그룹의 이름 및 위치를 사용하여 **Microsoft.Resources/resourceGroups** 리소스를 정의합니다. Azure 구독에 템플릿을 배포합니다. 동일한 템플릿에서 해당 리소스 그룹에 리소스를 배포할 수도 있습니다.

이 문서에서는 Azure CLI를 사용하여 템플릿을 배포합니다. 현재 PowerShell은 구독에 템플릿 배포를 지원하지 않습니다.

## <a name="create-empty-resource-group"></a>빈 리소스 그룹 만들기

다음 예제는 빈 리소스 그룹을 만듭니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "rgName": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[parameters('rgName')]",
            "properties": {}
        }
    ],
    "outputs": {}
}
```

Azure CLI를 사용하여 이 템플릿을 배포하려면 다음 기능을 사용합니다.

```azurecli-interactive
az deployment create \
  -n demoEmptyRG \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json \
  --parameters rgName=demoRG rgLocation=northcentralus
```

## <a name="create-several-resource-groups"></a>여러 리소스 그룹 만들기

리소스 그룹을 2개 이상 만들려면 리소스 그룹에서 [요소 복사](resource-group-create-multiple.md)를 사용합니다. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "rgNamePrefix": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        },
        "instanceCount": {
            "type": "int"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[concat(parameters('rgNamePrefix'), copyIndex())]",
            "copy": {
                "name": "rgCopy",
                "count": "[parameters('instanceCount')]"
            },
            "properties": {}
        }
    ],
    "outputs": {}
}
```

Azure CLI를 사용하여 이 템플릿을 배포하고 리소스 그룹 3개를 만들려면 다음 기능을 사용합니다.

```azurecli-interactive
az deployment create \
  -n demoCopyRG \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/copyRG.json \
  --parameters rgNamePrefix=demoRG rgLocation=northcentralus instanceCount=3
```

## <a name="create-resource-group-and-deploy-resource"></a>리소스 그룹을 만들고 리소스 배포

리소스 그룹을 만들고 거기에 리소스를 배포하려면 중첩 템플릿을 사용합니다. 중첩 템플릿은 리소스 그룹에 배포할 리소스를 정의합니다. 리소스를 배포하기 전에 리소스 그룹이 존재하도록 중첩 템플릿을 리소스 그룹에 종속된 것으로 설정합니다.

다음 예제는 리소스 그룹을 만들고 저장소 계정을 배포합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "rgName": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        },
        "storagePrefix": {
            "type": "string",
            "maxLength": 11
        }
    },
    "variables": {
        "storageName": "[concat(parameters('storagePrefix'), uniqueString(subscription().id, parameters('rgName')))]"
    },
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[parameters('rgName')]",
            "properties": {}
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2017-05-10",
            "name": "storageDeployment",
            "resourceGroup": "[parameters('rgName')]",
            "dependsOn": [
                "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
            ],
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
                            "apiVersion": "2017-10-01",
                            "name": "[variables('storageName')]",
                            "location": "[parameters('rgLocation')]",
                            "kind": "StorageV2",
                            "sku": {
                                "name": "Standard_LRS"
                            }
                        }
                    ],
                    "outputs": {}
                }
            }
        }
    ],
    "outputs": {}
}
```

Azure CLI를 사용하여 이 템플릿을 배포하려면 다음 기능을 사용합니다.

```azurecli-interactive
az deployment create \
  -n demoRGStorage \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/newRGWithStorage.json \
  --parameters rgName=rgStorage rgLocation=northcentralus storagePrefix=storage
```

## <a name="next-steps"></a>다음 단계
* 배포 중 종속성 문제 해결에 대해 알아보려면 [Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 해결](resource-manager-common-deployment-errors.md)을 참조하세요.
* Azure 리소스 관리자 템플릿을 만드는 방법에 대한 자세한 내용은 [템플릿 작성](resource-group-authoring-templates.md)을 참조하세요. 
* 템플릿에서 사용할 수 있는 함수 목록은 [템플릿 함수](resource-group-template-functions.md)를 참조하세요.

