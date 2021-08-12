---
title: 템플릿을 사용하여 조건부 배포
description: ARM 템플릿(Azure Resource Manager 템플릿)에서 리소스를 조건부로 배포하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 05/07/2021
ms.openlocfilehash: 352ee71fea77608ae27552630a7d302b215374a1
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111969500"
---
# <a name="conditional-deployment-in-arm-templates"></a>ARM 템플릿의 조건부 배포

경우에 따라 ARM 템플릿(Azure Resource Manager 템플릿)에서 리소스를 조건부로 배포해야 합니다. 리소스 배포 여부를 지정하려면 `condition` 요소를 사용합니다. 조건 값은 true 또는 false로 확인됩니다. 값이 true이면 리소스가 만들어집니다. 값이 false이면 리소스가 만들어지지 않습니다. 값은 전체 리소스에만 적용할 수 있습니다.

> [!NOTE]
> 조건부 배포는 [하위 리소스](child-resource-name-type.md)로 연결되지 않습니다. 리소스 및 해당 자식 리소스를 조건부로 배포하려면 각 리소스 종류에 동일한 조건을 적용해야 합니다.

## <a name="deploy-condition"></a>배포 조건

리소스 배포 여부를 나타내는 매개 변수 값을 전달할 수 있습니다. 다음 예제에서는 조건부로 DNS 영역을 배포합니다.

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

좀 더 복잡한 예제는 [Azure SQL 논리 서버](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.sql/sql-logical-server)를 참조하세요.

## <a name="new-or-existing-resource"></a>신규 또는 기존 리소스

조건부 배포를 사용하여 새 리소스를 만들거나 기존 리소스를 사용할 수 있습니다. 다음 예에서는 새 스토리지 계정을 배포하거나 기존 스토리지 계정을 사용하는 방법을 보여 줍니다.

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

`newOrExisting` 매개 변수가 **new** 로 설정되면 조건이 true로 평가됩니다. 스토리지 계정이 배포됩니다. 그러나 `newOrExisting`이 **existing** 으로 설정되면 조건이 false로 평가되고 스토리지 계정이 배포되지 않습니다.

`condition` 요소를 사용하는 전체 예제 템플릿은 [신규 또는 기존 가상 네트워크, 스토리지 및 공용 IP를 사용하는 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-new-or-existing-conditions)을 참조하세요.

## <a name="runtime-functions"></a>런타임 기능

조건부로 배포된 리소스와 함께 [reference](template-functions-resource.md#reference) 또는 [list](template-functions-resource.md#list) 함수를 사용하는 경우 리소스가 배포되지 않은 경우에도 함수가 평가됩니다. 함수가 존재하지 않는 리소스를 참조하면 오류가 발생합니다.

리소스가 배포되는 경우에만 함수의 조건이 평가되도록 하려면 [if](template-functions-logical.md#if) 함수를 사용합니다. `if` 및 `reference`를 조건부로 배포된 리소스와 함께 사용하는 샘플 템플릿에 대해서는 [if 함수](template-functions-logical.md#if)를 참조하세요.

다른 리소스와 마찬가지로 조건부 리소스에 대해 [리소스를 종속 리소스로 설정](./resource-dependency.md)합니다. 조건부 리소스가 배포되지 않으면 Azure Resource Manager가 필요한 종속성에서 해당 리소스를 자동으로 제거합니다.

## <a name="complete-mode"></a>전체 모드

[완료 모드](deployment-modes.md)로 템플릿을 배포하며, `condition`이 false로 평가되므로 리소스가 배포되지 않은 경우 템플릿을 배포하는 데 사용하는 REST API 버전에 따라 결과가 달라집니다. 2019년 5월 10일 이전 버전을 사용하는 경우 리소스는 **삭제되지 않습니다**. 2019년 5월 10일 이후 버전에서는 리소스가 **삭제됩니다**. 조건이 false일 때 최신 버전의 Azure PowerShell 및 Azure CLI는 리소스를 삭제합니다.

## <a name="next-steps"></a>다음 단계

* 조건부 배포를 다루는 Microsoft Learn 모듈은 [고급 ARM 템플릿 기능을 사용하여 복잡한 클라우드 배포 관리](/learn/modules/manage-deployments-advanced-arm-template-features/)를 참조하세요.
* 템플릿을 만드는 방법에 대한 권장 사항은 [Azure 템플릿 모범 사례](./best-practices.md)를 참조하세요.
* 리소스의 여러 인스턴스를 만들려면 [ARM 템플릿의 리소스 반복](copy-resources.md)을 참조하세요.