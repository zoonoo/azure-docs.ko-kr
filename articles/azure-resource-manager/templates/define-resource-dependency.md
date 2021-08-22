---
title: 리소스의 배포 순서 설정
description: 배포 중에 다른 리소스에 종속되도록 Azure 리소스를 설정하는 방법을 설명합니다. 종속성은 리소스가 올바른 순서로 배포되도록 합니다.
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: 723344a4619325eb29a481a42f7dd2de5094a7b0
ms.sourcegitcommit: 5c136a01bddfccb2cc9f7e7e7741e2cf2651ddbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/03/2021
ms.locfileid: "111353508"
---
# <a name="define-the-order-for-deploying-resources-in-arm-templates"></a>ARM 템플릿에서 리소스 배포 순서 정의

리소스를 배포할 때 일부 리소스가 다른 리소스보다 먼저 존재하는지 확인해야 할 수 있습니다. 예를 들어 데이터베이스를 배포하기 전에 논리 SQL Server가 필요합니다. 하나의 리소스를 다른 리소스에 종속된 것으로 표시하여 이 관계를 설정합니다. `dependsOn` 요소를 사용하여 명시적 종속성을 정의합니다. 암시적 종속성을 정의하려면 **reference** 또는 **list** 함수를 사용합니다.

Azure Resource Manager는 리소스 간의 종속성을 평가한 후 종속된 순서에 따라 리소스를 배포합니다. 리소스가 서로 종속되어 있지 않은 경우 Resource Manager는 이를 병렬로 배포합니다. 동일한 템플릿에 배포되는 리소스에 대한 종속성만 정의하면 됩니다.

## <a name="dependson"></a>dependsOn

ARM 템플릿(Azure Resource Manager 템플릿) 내에서 `dependsOn` 요소를 사용하면 하나 이상의 리소스에 종속된 하나의 리소스를 정의할 수 있습니다. 해당 값은 각각 리소스 이름 또는 ID인 문자열의 JSON(JavaScript Object Notation) 배열입니다. 이 배열에는 [조건부 배포](conditional-resource-deployment.md)된 리소스가 포함될 수 있습니다. 조건부 리소스가 배포되지 않으면 Azure Resource Manager가 필요한 종속성에서 해당 리소스를 자동으로 제거합니다.

다음 예에서는 가상 네트워크, 네트워크 보안 그룹 및 공용 IP 주소에 따라 달라지는 네트워크 인터페이스를 보여 줍니다. 전체 템플릿은 [Linux VM용 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.compute/vm-simple-linux/azuredeploy.json)을 참조하세요.

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

리소스 간의 관계를 매핑하기 위해 `dependsOn`을 사용하는 경향이 있을 수 있지만 왜 그렇게 하는지 이해하는 것이 중요합니다. 예를 들어, 리소스가 상호 연결되는 방식을 문서화하기 위해 `dependsOn`은 올바른 접근 방식이 아닙니다. 배포 후 `dependsOn` 요소에 정의된 리소스를 쿼리할 수 없습니다. 불필요한 종속성을 설정하면 Resource Manager가 해당 리소스를 병렬로 배포할 수 없기 때문에 배포 시간이 느려집니다.

## <a name="child-resources"></a>자식 리소스

암시적 배포 종속성은 [하위 리소스](child-resource-name-type.md)와 상위 리소스 사이에 자동으로 생성되지 않습니다. 상위 리소스 뒤에 하위 리소스를 배포해야 하는 경우 `dependsOn` 속성을 설정합니다.

다음 예에서는 논리적 SQL Server 및 데이터베이스를 보여 줍니다. 데이터베이스가 서버의 자식인 경우에도 데이터베이스와 서버 간에 명시적 종속성이 정의되어 있습니다.

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

전체 템플릿은 [Azure SQL Database의 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.sql/sql-database/azuredeploy.json)을 참조하세요.

## <a name="reference-and-list-functions"></a>reference 및 list 함수

[reference 함수](template-functions-resource.md#reference) 를 사용하면 식을 다른 JSON 이름 및 값 쌍 또는 런타임 리소스에서 해당 값을 파생시키는 식을 작성할 수 있습니다. [list* 함수](template-functions-resource.md#list)는 목록 작업에서 리소스에 대한 값을 반환합니다.

참조 및 목록 식은 한 리소스가 다른 리소스에 종속되어 있음을 암시적으로 선언합니다. 가능하면 불필요한 종속성을 추가하지 않도록 암시적 참조를 사용합니다.

암시적 종속성을 적용하려면 리소스 ID가 아닌 이름으로 리소스를 참조하세요. 리소스 ID를 reference 또는 list 함수로 전달하는 경우 암시적 참조가 생성되지 않습니다.

`reference` 함수의 일반 형식은 다음과 같습니다.

```json
reference('resourceName').propertyPath
```

`listKeys` 함수의 일반 형식은 다음과 같습니다.

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

[복사 루프](copy-resources.md)의 리소스에 의존하는 리소스를 배포하려면 두 가지 옵션이 있습니다. 루프 또는 전체 루프에서 개별 리소스에 대한 종속성을 설정할 수 있습니다.

> [!NOTE]
> 대부분의 시나리오에서는 복사 루프 내에서 개별 리소스에 대한 종속성을 설정해야 합니다. 다음 리소스를 만들기 전에 루프의 모든 리소스가 존재해야 하는 경우에만 전체 루프에 의존합니다. 전체 루프에 대한 종속성을 설정하면 특히 루프된 리소스가 다른 리소스에 종속된 경우 종속성 그래프가 크게 확장됩니다. 확장된 종속성으로 인해 배포를 효율적으로 완료하기가 어렵습니다.

다음 예는 여러 가상 머신을 배포하는 방법을 보여 줍니다. 템플릿은 동일한 수의 네트워크 인터페이스를 만듭니다. 각 가상 머신은 전체 루프가 아닌 하나의 네트워크 인터페이스에 종속됩니다.

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

다음 예에서는 가상 머신을 배포하기 전에 스토리지 계정 3개를 배포하는 방법을 보여줍니다. `copy` 요소에는 `name`이 `storagecopy`로 설정되어 있고 가상 머신의 `dependsOn` 요소도 `storagecopy`로 설정되어 있습니다.

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

Resource Manager는 템플릿의 유효성을 검사하는 동안 순환적 종속성을 식별합니다. 순환 종속성에 대한 오류가 발생하면 템플릿을 평가하여 종속성을 제거할 수 있는지 확인합니다. 종속성 제거가 작동하지 않는 경우 일부 배포 작업을 하위 리소스로 이동하여 순환 종속성을 피할 수 있습니다. 순환 종속성이 있는 리소스 뒤의 자식 리소스를 배포합니다. 예를 들어 두 개의 가상 머신을 배포하지만 각 컴퓨터에 서로를 참조하는 속성을 설정해야 한다고 가정합니다. 이런 경우 다음과 같은 순서로 배포할 수 있습니다.

1. vm1
2. vm2
3. vm1의 확장은 vm1 및 vm2에 종속됩니다. 이 확장은 vm2에서 얻은 값을 vm1에 설정합니다.
4. vm2의 확장은 vm1 및 vm2에 종속됩니다. 이 확장은 vm1에서 얻은 값을 vm2에 설정합니다.

배포 순서를 평가하고 종속성 오류를 해결하는 방법에 대한 자세한 내용은 [Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 해결](common-deployment-errors.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* 자습서를 따라 진행하려면 [자습서: 종속 리소스를 사용하여 ARM 템플릿 만들기](template-tutorial-create-templates-with-dependent-resources.md)를 참조하세요.
* 리소스 종속성을 다루는 Microsoft Learn 모듈은 [고급 ARM 템플릿 기능을 사용하여 복잡한 클라우드 배포 관리](/learn/modules/manage-deployments-advanced-arm-template-features/)를 참조하세요.
* 종속성 설정 시 권장 사항은 [ARM 템플릿 모범 사례](template-best-practices.md)를 참조하세요.
* 배포 중 종속성 문제 해결에 대해 알아보려면 [Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 해결](common-deployment-errors.md)을 참조하세요.
* Azure Resource Manager 템플릿 만들기에 대해 알아보려면 [ARM 템플릿의 구조 및 구문 이해](template-syntax.md)를 참조하세요.
* 템플릿에서 사용 가능한 함수 목록은 [ARM 템플릿 함수](template-functions.md)를 참조하세요.
