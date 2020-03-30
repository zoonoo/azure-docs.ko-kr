---
title: 템플릿이 있는 조건부 배포
description: Azure 리소스 관리자 템플릿에 리소스를 조건부로 배포하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 12/03/2019
ms.openlocfilehash: f170710118c0e3de6f3643b6216ed55b83b5c7df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153423"
---
# <a name="conditional-deployment-in-arm-templates"></a>ARM 템플릿의 조건부 배포

경우에 따라 AZURE 리소스 관리자(ARM) 템플릿에 리소스를 선택적으로 배포해야 합니다. `condition` 요소를 사용하여 리소스가 배포되는지 여부를 지정합니다. 이 요소 값은 true 또는 false로 확인됩니다. 값이 true이면 리소스가 만들어집니다. 값이 false이면 리소스가 만들어지지 않습니다. 값은 전체 리소스에만 적용할 수 있습니다.

## <a name="new-or-existing-resource"></a>새 리소스 또는 기존 리소스

조건부 배포를 사용하여 새 리소스를 만들거나 기존 리소스를 사용할 수 있습니다. 다음 예제에서는 조건을 사용하여 새 저장소 계정을 배포하거나 기존 저장소 계정을 사용하는 방법을 보여 주며 있습니다.

```json
{
  "condition": "[equals(parameters('newOrExisting'),'new')]",
  "type": "Microsoft.Storage/storageAccounts",
  "apiVersion": "2017-06-01",
  "name": "[variables('storageAccountName')]",
  "location": "[parameters('location')]",
  "sku": {
    "name": "[variables('storageAccountType')]"
  },
  "kind": "Storage",
  "properties": {}
}
```

매개 변수 **newOrExisting** **새로**설정 되 면 조건이 true로 평가 됩니다. 저장소 계정이 배포됩니다. 그러나 **newOrExisting** **기존 기존**설정 하는 경우 조건이 false로 평가 하 고 저장소 계정이 배포 되지 않습니다.

`condition` 요소를 사용하는 전체 예제 템플릿은 [신규 또는 기존 가상 네트워크, 스토리지 및 공용 IP를 사용하는 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions)을 참조하세요.

## <a name="allow-condition"></a>조건 허용

조건이 허용되는지 여부를 나타내는 매개 변수 값을 전달할 수 있습니다. 다음 예제에서는 SQL 서버를 배포 하 고 선택적으로 Azure IP를 허용 합니다.

```json
{
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2015-05-01-preview",
  "name": "[parameters('serverName')]",
  "location": "[parameters('location')]",
  "properties": {
    "administratorLogin": "[parameters('administratorLogin')]",
    "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
    "version": "12.0"
  },
  "resources": [
    {
      "condition": "[parameters('allowAzureIPs')]",
      "type": "firewallRules",
      "apiVersion": "2015-05-01-preview",
      "name": "AllowAllWindowsAzureIps",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Sql/servers/', parameters('serverName'))]"
      ],
      "properties": {
        "endIpAddress": "0.0.0.0",
        "startIpAddress": "0.0.0.0"
      }
    }
  ]
}
```

전체 템플릿은 [Azure SQL 논리 서버](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server)를 참조하십시오.

## <a name="runtime-functions"></a>런타임 기능

조건부로 배포된 리소스와 함께 [참조](template-functions-resource.md#reference) 또는 [목록](template-functions-resource.md#list) 함수를 사용하는 경우 리소스가 배포되지 않은 경우에도 함수가 평가됩니다. 함수가 존재하지 않는 리소스를 참조하면 오류가 발생합니다.

[if](template-functions-logical.md#if) 함수를 사용하여 리소스가 배포될 때 함수가 조건에 대해서만 평가되도록 합니다. 조건부배포 된 리소스와 함께 if 및 참조를 사용하는 샘플 템플릿에 대한 [if 함수를](template-functions-logical.md#if) 참조하십시오.

리소스를 다른 리소스와 똑같이 조건부 리소스에 [종속으로](define-resource-dependency.md) 설정합니다. 조건부 리소스가 배포되지 않으면 Azure Resource Manager는 필요한 종속성에서 자동으로 제거합니다.

## <a name="condition-with-complete-mode"></a>전체 모드로 조건 조정

조건이 false로 평가되기 때문에 [전체 모드가](deployment-modes.md) 있는 템플릿을 배포하고 리소스가 배포되지 않은 경우 결과는 템플릿을 배포하는 데 사용하는 REST API 버전에 따라 달라집니다. 2019-05-10 이전 버전을 사용하는 경우 리소스가 **삭제되지 않습니다.** 2019-05-10 이상에서는 리소스가 **삭제됩니다.** Azure PowerShell 및 Azure CLI의 최신 버전은 조건이 false이면 리소스를 삭제합니다.

## <a name="next-steps"></a>다음 단계

* 템플릿 만들기에 대한 권장 사항은 [ARM 템플릿 모범 사례를](template-best-practices.md)참조하십시오.
* 리소스의 여러 인스턴스를 만들려면 [ARM 템플릿의 리소스 반복을](copy-resources.md)참조하십시오.