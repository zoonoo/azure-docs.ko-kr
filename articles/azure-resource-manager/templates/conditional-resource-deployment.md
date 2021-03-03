---
title: 템플릿을 사용한 조건부 배포
description: Azure Resource Manager 템플릿 (ARM 템플릿)에서 리소스를 조건부로 배포 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 409d258d7dfe3ed186e5cf97cc0dbe6dc149b849
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101741177"
---
# <a name="conditional-deployment-in-arm-templates"></a>ARM 템플릿의 조건부 배포

경우에 따라 Azure Resource Manager 템플릿 (ARM 템플릿) 또는 Bicep 파일에서 리소스를 배포 해야 합니다. JSON 템플릿의 경우 요소를 사용 `condition` 하 여 리소스 배포 여부를 지정 합니다. Bicep의 경우 키워드를 사용 `if` 하 여 리소스 배포 여부를 지정 합니다. 조건 값은 true 또는 false로 확인 됩니다. 값이 true이면 리소스가 만들어집니다. 값이 false이면 리소스가 만들어지지 않습니다. 값은 전체 리소스에만 적용할 수 있습니다.

> [!NOTE]
> 조건부 배포는 [자식 리소스](child-resource-name-type.md)에 종속 되지 않습니다. 리소스 및 해당 자식 리소스를 조건부로 배포 하려면 각 리소스 종류에 동일한 조건을 적용 해야 합니다.

## <a name="deploy-condition"></a>배포 조건

리소스 배포 여부를 나타내는 매개 변수 값을 전달할 수 있습니다. 다음 예에서는 조건에 따라 DNS 영역을 배포 합니다.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "deployZone": {
      "type": "bool"
    }
  },
  "functions": [],
  "resources": [
    {
      "condition": "[parameters('deployZone')]",
      "type": "Microsoft.Network/dnsZones",
      "apiVersion": "2018-05-01",
      "name": "myZone",
      "location": "global"
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param deployZone bool

resource dnsZone 'Microsoft.Network/dnszones@2018-05-01' = if (deployZone) {
  name: 'myZone'
  location: 'global'
}
```

---

더 복잡 한 예는 [AZURE SQL 논리 서버](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server)를 참조 하세요.

## <a name="new-or-existing-resource"></a>신규 또는 기존 리소스

조건부 배포를 사용 하 여 새 리소스를 만들거나 기존 리소스를 사용할 수 있습니다. 다음 예에서는 새 저장소 계정을 배포 하거나 기존 저장소 계정을 사용 하는 방법을 보여 줍니다.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string"
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "newOrExisting": {
      "type": "string",
      "defaultValue": "new",
      "allowedValues": [
        "new",
        "existing"
      ]
    }
  },
  "functions": [],
  "resources": [
    {
      "condition": "[equals(parameters('newOrExisting'), 'new')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[parameters('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageAccountName string
param location string = resourceGroup().location

@allowed([
  'new'
  'existing'
])
param newOrExisting string = 'new'

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (newOrExisting == 'new') {
  name: storageAccountName
  location: location
  sku: {
    name: 'Standard_LRS'
    tier: 'Standard'
  }
  kind: 'StorageV2'
  properties: {
    accessTier: 'Hot'
  }
}
```

---

매개 변수가 `newOrExisting` **new** 로 설정 되 면 조건이 true로 평가 됩니다. 저장소 계정이 배포 됩니다. 그러나 `newOrExisting` 이 (가) **기존** 으로 설정 된 경우 조건이 false로 평가 되 고 저장소 계정이 배포 되지 않습니다.

`condition` 요소를 사용하는 전체 예제 템플릿은 [신규 또는 기존 가상 네트워크, 스토리지 및 공용 IP를 사용하는 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions)을 참조하세요.

## <a name="runtime-functions"></a>런타임 함수

조건부로 배포 되는 리소스에 [참조](template-functions-resource.md#reference) 또는 [목록](template-functions-resource.md#list) 함수를 사용 하는 경우 리소스가 배포 되지 않은 경우에도 함수가 평가 됩니다. 함수가 존재 하지 않는 리소스를 참조 하는 경우 오류가 발생 합니다.

[If](template-functions-logical.md#if) 함수를 사용 하 여 리소스가 배포 될 때 함수가 조건에 대해서만 계산 되도록 합니다. [](template-functions-logical.md#if) `if` `reference` 조건적으로 배포 된 리소스와 함께 및를 사용 하는 샘플 템플릿은 if 함수를 참조 하세요.

리소스를 다른 리소스와 동일 하 게 조건부 리소스에 [종속 된 것으로](define-resource-dependency.md) 설정 합니다. 조건부 리소스가 배포 되지 않은 경우 Azure Resource Manager은 필요한 종속성에서 자동으로 제거 합니다.

## <a name="complete-mode"></a>전체 모드

[전체 모드](deployment-modes.md) 를 사용 하 여 템플릿을 배포 하는 경우가 false로 평가 되므로 리소스가 배포 되지 않은 경우에 `condition` 는 템플릿을 배포 하는 데 사용 하는 REST API 버전에 따라 결과가 달라 집니다. 2019-05-10 이전 버전을 사용 하는 경우 리소스는 **삭제 되지 않습니다**. 2019-05-10 이상에서는 리소스가 **삭제 됩니다**. 최신 버전의 Azure PowerShell 하 고 조건이 false 인 경우 리소스를 삭제 Azure CLI.

## <a name="next-steps"></a>다음 단계

* 조건부 배포를 다루는 Microsoft Learn 모듈은 [고급 ARM 템플릿 기능을 사용 하 여 복잡 한 클라우드 배포 관리](/learn/modules/manage-deployments-advanced-arm-template-features/)를 참조 하세요.
* 템플릿을 만드는 방법에 대 한 권장 사항은 [ARM 템플릿 모범 사례](template-best-practices.md)를 참조 하세요.
* 리소스의 여러 인스턴스를 만들려면 [ARM 템플릿에서 리소스 반복](copy-resources.md)을 참조 하세요.
