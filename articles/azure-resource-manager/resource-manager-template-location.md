---
title: "템플릿의 Azure 리소스 위치 | Microsoft Docs"
description: "Azure Resource Manager 템플릿의 리소스에 대한 위치 설정 방법을 보여 줍니다."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 1f7d7ad6dcec3c7d3b6fec7abcad7c36d2c02b70
ms.openlocfilehash: 6342b2e5f3efa498a911bb82a642fa4672f77180


---
# <a name="set-resource-location-in-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿에서 리소스 위치 설정
템플릿을 배포할 때는 각 리소스의 위치를 지정해야 합니다. 이 항목에서는 각 리소스 유형에 대한 구독에서 사용할 수 있는 위치를 확인하는 방법을 보여 줍니다.

## <a name="determine-supported-locations"></a>지원되는 위치 확인

각 리소스 유형에 대해 지원되는 위치의 전체 목록은 [지역별 사용 가능한 제품](https://azure.microsoft.com/regions/services/)을 참조하세요. 그러나 구독에 해당 목록의 모든 위치에 대한 액세스 권한이 없을 수도 있습니다. 구독에서 사용할 수 있는 위치의 사용자 지정된 목록을 보려면 Azure PowerShell 또는 Azure CLI를 사용합니다. 

다음 예제에서는 PowerShell을 사용하여 `Microsoft.Web\sites` 리소스 유형에 대한 위치를 가져옵니다.

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

다음 예제에서는 Azure CLI 2.0(미리 보기)을 사용하여 `Microsoft.Web\sites` 리소스 유형에 대한 위치를 가져옵니다.

```azurecli
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

## <a name="set-location-in-template"></a>템플릿에서 위치 설정

리소스에 대해 지원되는 위치를 확인한 후 템플릿에서 해당 위치를 설정해야 합니다. 이 값을 설정하는 가장 쉬운 방법은 리소스 유형을 지원하는 위치에 리소스 그룹을 만들고 해당 위치를 `[resourceGroup().location]`으로 설정합니다. 여러 다른 위치의 리소스 그룹에 템플릿을 다시 배포하고 템플릿 또는 매개 변수의 값을 변경하지 않을 수 있습니다. 

다음 예제에서는 리소스 그룹과 같은 위치에 배포되는 저장소 계정을 보여 줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
      "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageName')]",
      "location": "[resourceGroup().location]",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

템플릿에서 해당 위치를 하드 코드해야 할 경우 지원되는 하위 지역 중 하나의 이름을 제공합니다. 다음 예제에서는 항상 미국 중북부에 배포되는 저장소 계정을 보여 줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storageloc', uniqueString(resourceGroup().id))]",
      "location": "North Central US",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

## <a name="next-steps"></a>다음 단계
* 템플릿 작성 방법에 대한 권장 사항은 [Azure Resource Manager 템플릿 생성 모범 사례](resource-manager-template-best-practices.md)를 참조하세요.




<!--HONumber=Feb17_HO1-->


