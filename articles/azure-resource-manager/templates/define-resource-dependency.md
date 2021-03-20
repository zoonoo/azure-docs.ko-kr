---
title: 리소스의 배포 순서 설정
description: 배포 중에 다른 리소스에 종속 된 Azure 리소스를 설정 하는 방법을 설명 합니다. 종속성은 리소스를 올바른 순서로 배포 하는지 확인 합니다.
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: f6b63b066da06a17c3a2e51ab0f3ab9bf521a144
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97934750"
---
# <a name="define-the-order-for-deploying-resources-in-arm-templates"></a>ARM 템플릿에서 리소스 배포 순서 정의

리소스를 배포할 때 일부 리소스가 다른 리소스 보다 먼저 존재 하는지 확인 해야 할 수 있습니다. 예를 들어 데이터베이스를 배포 하기 전에 논리 SQL server가 필요 합니다. 한 리소스를 다른 리소스에 종속 된 것으로 표시 하 여이 관계를 설정 합니다. 요소를 사용 `dependsOn` 하 여 명시적 종속성을 정의 합니다. **참조** 또는 **목록** 함수를 사용 하 여 암시적 종속성을 정의 합니다.

Azure Resource Manager는 리소스 간의 종속성을 평가 하 고 종속 순서로 배포 합니다. 리소스가 서로 종속되어 있지 않은 경우 Resource Manager는 이를 병렬로 배포합니다. 동일한 템플릿에 배포되는 리소스에 대한 종속성만 정의하면 됩니다.

## <a name="dependson"></a>dependsOn

Azure Resource Manager 템플릿 (ARM 템플릿) 내에서 요소를 사용 하 여 하나 이상의 `dependsOn` 리소스에 종속 된 하나의 리소스를 정의할 수 있습니다. 해당 값은 각각 리소스 이름 또는 ID 인 문자열의 JavaScript Object Notation (JSON) 배열입니다. 배열에는 [조건부로 배포](conditional-resource-deployment.md)되는 리소스가 포함 될 수 있습니다. 조건부 리소스가 배포 되지 않은 경우 Azure Resource Manager은 필요한 종속성에서 자동으로 제거 합니다.

다음 예제에서는 가상 네트워크, 네트워크 보안 그룹 및 공용 IP 주소에 따라 달라 지는 네트워크 인터페이스를 보여 줍니다. 전체 템플릿은 [LINUX VM에 대 한 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-simple-linux/azuredeploy.json)을 참조 하세요.

```json
{
    "type": "Microsoft.Network/networkInterfaces",
    "apiVersion": "2020-06-01",
    "name": "[variables('networkInterfaceName')]",
    "location": "[parameters('location')]",
    "dependsOn": [
      "[resourceId('Microsoft.Network/networkSecurityGroups/', parameters('networkSecurityGroupName'))]",
      "[resourceId('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
      "[resourceId('Microsoft.Network/publicIpAddresses/', variables('publicIpAddressName'))]"
    ],
    ...
}
```

쉬운데를 사용 하 여 리소스 간의 관계를 매핑하는 것은 아니지만 `dependsOn` 이 작업을 수행 하는 이유를 이해 하는 것이 중요 합니다. 예를 들어 리소스를 상호 연결 하는 방법을 문서화 하려면 `dependsOn` 적절 한 방법이 아닙니다. 배포 후 요소에 정의 된 리소스는 쿼리할 수 없습니다 `dependsOn` . 리소스 관리자은 해당 리소스를 병렬로 배포할 수 없으므로 불필요 한 종속성을 설정 하면 배포 시간이 느려집니다.

## <a name="child-resources"></a>자식 리소스

[자식 리소스](child-resource-name-type.md) 와 부모 리소스 간에는 암시적 배포 종속성이 자동으로 생성 되지 않습니다. 부모 리소스 뒤에 자식 리소스를 배포 해야 하는 경우 속성을 설정 `dependsOn` 합니다.

다음 예에서는 논리 SQL server 및 데이터베이스를 보여 줍니다. 데이터베이스가 서버의 자식인 경우에도 데이터베이스와 서버 간에 명시적 종속성이 정의 되어 있는지 확인 합니다.

```json
"resources": [
  {
    "type": "Microsoft.Sql/servers",
    "apiVersion": "2020-02-02-preview",
    "name": "[parameters('serverName')]",
    "location": "[parameters('location')]",
    "properties": {
      "administratorLogin": "[parameters('administratorLogin')]",
      "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
    },
    "resources": [
      {
        "type": "databases",
        "apiVersion": "2020-08-01-preview",
        "name": "[parameters('sqlDBName')]",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
          },
        "dependsOn": [
          "[resourceId('Microsoft.Sql/servers', concat(parameters('serverName')))]"
        ]
      }
    ]
  }
]
```

전체 템플릿은 [Azure SQL Database의 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/blob/master/101-sql-database/azuredeploy.json)을 참조 하세요.

## <a name="reference-and-list-functions"></a>reference 및 list 함수

[reference 함수](template-functions-resource.md#reference) 를 사용하면 식을 다른 JSON 이름 및 값 쌍 또는 런타임 리소스에서 해당 값을 파생시키는 식을 작성할 수 있습니다. [list* 함수](template-functions-resource.md#list)는 목록 작업에서 리소스에 대한 값을 반환합니다.

참조 및 목록 식은 한 리소스가 다른 리소스에 종속 되도록 암시적으로 선언 합니다. 가능하면 불필요한 종속성을 추가하지 않도록 암시적 참조를 사용합니다.

암시적 종속성을 적용 하려면 리소스 ID가 아닌 이름별로 리소스를 참조 하세요. 리소스 ID를 reference 또는 list 함수로 전달하는 경우 암시적 참조가 생성되지 않습니다.

함수의 일반적인 형식은 `reference` 다음과 같습니다.

```json
reference('resourceName').propertyPath
```

함수의 일반적인 형식은 `listKeys` 다음과 같습니다.

```json
listKeys('resourceName', 'yyyy-mm-dd')
```

다음 예제에서 CDN 엔드포인트는 CDN 프로필에 명시적으로 종속되고 웹앱에 암시적으로 종속됩니다.

```json
{
    "name": "[variables('endpointName')]",
    "apiVersion": "2016-04-02",
    "type": "endpoints",
    "location": "[resourceGroup().location]",
    "dependsOn": [
      "[variables('profileName')]"
    ],
    "properties": {
      "originHostHeader": "[reference(variables('webAppName')).hostNames[0]]",
      ...
    }
```

자세한 내용은 [reference 함수](template-functions-resource.md#reference)를 참조하세요.

## <a name="depend-on-resources-in-a-loop"></a>루프의 리소스에 따라 달라짐

[복사 루프](copy-resources.md)의 리소스에 종속 된 리소스를 배포 하기 위해 두 가지 옵션이 있습니다. 루프 또는 전체 루프에서 개별 리소스에 대 한 종속성을 설정할 수 있습니다.

> [!NOTE]
> 대부분의 시나리오에서는 복사 루프 내에서 개별 리소스에 대 한 종속성을 설정 해야 합니다. 다음 리소스를 만들기 전에 루프의 모든 리소스가 있어야 하는 경우 전체 루프에만 의존 합니다. 전체 루프에서 종속성을 설정 하면 특히 해당 루프 리소스가 다른 리소스에 종속 된 경우 종속성 그래프가 크게 확장 됩니다. 확장 된 종속성을 사용 하면 배포가 효율적으로 완료 되기 어렵습니다.

다음 예제에서는 여러 가상 컴퓨터를 배포 하는 방법을 보여 줍니다. 템플릿은 동일한 수의 네트워크 인터페이스를 만듭니다. 각 가상 머신은 전체 루프가 아닌 하나의 네트워크 인터페이스에 종속 됩니다.

```json
{
  "type": "Microsoft.Network/networkInterfaces",
  "apiVersion": "2020-05-01",
  "name": "[concat(variables('nicPrefix'),'-',copyIndex())]",
  "location": "[parameters('location')]",
  "copy": {
    "name": "nicCopy",
    "count": "[parameters('vmCount')]"
  },
  ...
},
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2020-06-01",
  "name": "[concat(variables('vmPrefix'),copyIndex())]",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Network/networkInterfaces',concat(variables('nicPrefix'),'-',copyIndex()))]"
  ],
  "copy": {
    "name": "vmCopy",
    "count": "[parameters('vmCount')]"
  },
  "properties": {
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(variables('nicPrefix'),'-',copyIndex()))]",
          "properties": {
            "primary": "true"
          }
        }
      ]
    },
    ...
  }
}
```

다음 예제에서는 가상 컴퓨터를 배포 하기 전에 저장소 계정 3 개를 배포 하는 방법을 보여 줍니다. `copy`요소가 `name` 로 설정 되어 `storagecopy` 있고 `dependsOn` 가상 컴퓨터의 요소도로 설정 되어 있는지 확인 `storagecopy` 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 3
      },
      "properties": {}
    },
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2015-06-15",
      "name": "[concat('VM', uniqueString(resourceGroup().id))]",
      "dependsOn": ["storagecopy"],
      ...
    }
  ],
  "outputs": {}
}
```

## <a name="circular-dependencies"></a>순환적 종속성

Resource Manager는 템플릿의 유효성을 검사하는 동안 순환적 종속성을 식별합니다. 순환 종속성에 대 한 오류가 표시 되 면 템플릿을 평가 하 여 종속성을 제거할 수 있는지 확인 합니다. 종속성 제거가 작동 하지 않는 경우 일부 배포 작업을 자식 리소스로 이동 하 여 순환 종속성을 방지할 수 있습니다. 순환 종속성이 있는 리소스 뒤에 자식 리소스를 배포 합니다. 예를 들어 두 개의 가상 머신을 배포하지만 각 컴퓨터에 서로를 참조하는 속성을 설정해야 한다고 가정합니다. 이런 경우 다음과 같은 순서로 배포할 수 있습니다.

1. vm1
2. vm2
3. vm1의 확장은 vm1 및 vm2에 종속됩니다. 이 확장은 vm2에서 얻은 값을 vm1에 설정합니다.
4. vm2의 확장은 vm1 및 vm2에 종속됩니다. 이 확장은 vm1에서 얻은 값을 vm2에 설정합니다.

배포 순서를 평가하고 종속성 오류를 해결하는 방법에 대한 자세한 내용은 [Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 해결](common-deployment-errors.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* 자습서를 진행 하려면 [자습서: 종속 리소스를 사용 하 여 ARM 템플릿 만들기](template-tutorial-create-templates-with-dependent-resources.md)를 참조 하세요.
* 리소스 종속성을 다루는 Microsoft Learn 모듈은 [고급 ARM 템플릿 기능을 사용하여 복잡한 클라우드 배포 관리](/learn/modules/manage-deployments-advanced-arm-template-features/)를 참조하세요.
* 종속성 설정 시 권장 사항은 [ARM 템플릿 모범 사례](template-best-practices.md)를 참조 하세요.
* 배포 중 종속성 문제 해결에 대해 알아보려면 [Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 해결](common-deployment-errors.md)을 참조하세요.
* Azure Resource Manager 템플릿을 만드는 방법에 대 한 자세한 내용은 [ARM 템플릿의 구조 및 구문 이해](template-syntax.md)를 참조 하세요.
* 템플릿에서 사용할 수 있는 함수 목록은 [ARM 템플릿 함수](template-functions.md)를 참조 하세요.
