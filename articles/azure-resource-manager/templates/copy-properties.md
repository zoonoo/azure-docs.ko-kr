---
title: 속성의 여러 인스턴스 정의
description: Azure Resource Manager 템플릿에서 복사 작업을 사용 하 여 리소스에 대 한 속성을 만들 때 여러 번 반복 합니다.
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 61122b01889da832a73f729833ab0af676904d54
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84678463"
---
# <a name="property-iteration-in-arm-templates"></a>ARM 템플릿의 속성 반복

이 문서에서는 Azure Resource Manager (ARM) 템플릿에서 속성의 인스턴스를 둘 이상 만드는 방법을 보여 줍니다. 템플릿에서 리소스의 속성 섹션에 **copy** 요소를 추가 하 여 배포 중에 속성의 항목 수를 동적으로 설정할 수 있습니다. 템플릿 구문을 반복 하지 않아도 됩니다.

[리소스](copy-resources.md), [변수](copy-variables.md)및 [출력과](copy-outputs.md)함께 copy를 사용할 수도 있습니다.

## <a name="syntax"></a>Syntax

Copy 요소의 일반적인 형식은 다음과 같습니다.

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-property>
  }
]
```

**이름**에 만들려는 리소스 속성의 이름을 입력 합니다.

**Count** 속성은 속성에 대해 원하는 반복 횟수를 지정 합니다.

**입력** 속성은 반복할 속성을 지정 합니다. **Input** 속성의 값에서 생성 된 요소의 배열을 만듭니다.

## <a name="copy-limits"></a>복사 제한

개수는 800를 초과할 수 없습니다.

개수는 음수일 수 없습니다. 최신 버전의 Azure CLI, PowerShell 또는 REST API를 사용 하 여 템플릿을 배포 하는 경우에는 0이 될 수 있습니다. 특히 다음을 사용 해야 합니다.

* Azure PowerShell **2.6** 이상
* Azure CLI **2.0.74** 이상
* REST API 버전 **2019-05-10** 이상
* 배포 리소스 종류에는 [연결 된 배포](linked-templates.md) 에서 API 버전 **2019-05-10** 이상을 사용 해야 합니다.

이전 버전의 PowerShell, CLI 및 REST API는 count에 대해 0을 지원 하지 않습니다.

## <a name="property-iteration"></a>속성 반복

다음 예제는 가상 머신에서 `copy`를 dataDisks 속성에 적용하는 방법을 보여 줍니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberOfDataDisks": {
      "type": "int",
      "minValue": 0,
      "maxValue": 16,
      "defaultValue": 16,
      "metadata": {
        "description": "The number of dataDisks to create."
      }
    },
    ...
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2017-03-30",
      ...
      "properties": {
        "storageProfile": {
          ...
          "copy": [
            {
              "name": "dataDisks",
              "count": "[parameters('numberOfDataDisks')]",
              "input": {
                "diskSizeGB": 1023,
                "lun": "[copyIndex('dataDisks')]",
                "createOption": "Empty"
              }
            }
          ]
        }
      }
    }
  ]
}
```

속성 반복 내에서 `copyIndex`를 사용하는 경우 반복의 이름을 제공해야 합니다. 또한 속성 반복은 offset 인수를 지원 합니다. 오프셋은 copyIndex (' dataDisks ', 1)와 같이 반복의 이름 뒤에와 야 합니다.

Resource Manager는 배포 중 `copy` 배열을 확장합니다. 배열 이름은 속성의 이름이 됩니다. 입력 값은 개체 속성이 됩니다. 배포된 템플릿은 다음과 같습니다.

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "dataDisks": [
        {
          "lun": 0,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 2,
          "createOption": "Empty",
          "diskSizeGB": 1023
        }
      ],
      ...
```

복사 작업은 배열의 각 요소를 반복할 수 있으므로 배열을 사용할 때 유용합니다. 배열의 `length` 함수를 사용하여 반복 횟수를 지정하고, `copyIndex`를 사용하여 배열의 현재 인덱스를 검색합니다.

다음 예제 템플릿에서는 배열로 전달 되는 데이터베이스에 대 한 장애 조치 (failover) 그룹을 만듭니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "primaryServerName": {
            "type": "string"
        },
        "secondaryServerName": {
            "type": "string"
        },
        "databaseNames": {
            "type": "array",
            "defaultValue": [
                "mydb1",
                "mydb2",
                "mydb3"
            ]
        }
    },
    "variables": {
        "failoverName": "[concat(parameters('primaryServerName'),'/', parameters('primaryServerName'),'failovergroups')]"
    },
    "resources": [
        {
            "type": "Microsoft.Sql/servers/failoverGroups",
            "apiVersion": "2015-05-01-preview",
            "name": "[variables('failoverName')]",
            "properties": {
                "readWriteEndpoint": {
                    "failoverPolicy": "Automatic",
                    "failoverWithDataLossGracePeriodMinutes": 60
                },
                "readOnlyEndpoint": {
                    "failoverPolicy": "Disabled"
                },
                "partnerServers": [
                    {
                        "id": "[resourceId('Microsoft.Sql/servers', parameters('secondaryServerName'))]"
                    }
                ],
                "copy": [
                    {
                        "name": "databases",
                        "count": "[length(parameters('databaseNames'))]",
                        "input": "[resourceId('Microsoft.Sql/servers/databases', parameters('primaryServerName'), parameters('databaseNames')[copyIndex('databases')])]"
                    }
                ]
            }
        }
    ],
    "outputs": {
    }
}
```

copy 요소는 배열이므로 리소스에 대해 1 초과 속성을 지정할 수 있습니다.

```json
{
  "type": "Microsoft.Network/loadBalancers",
  "apiVersion": "2017-10-01",
  "name": "exampleLB",
  "properties": {
    "copy": [
      {
        "name": "loadBalancingRules",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      },
      {
        "name": "probes",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      }
    ]
  }
}
```

리소스 및 속성 반복을 함께 사용할 수 있습니다. 이름별로 속성 반복을 참조하세요.

```json
{
  "type": "Microsoft.Network/virtualNetworks",
  "apiVersion": "2018-04-01",
  "name": "[concat(parameters('vnetname'), copyIndex())]",
  "copy":{
    "count": 2,
    "name": "vnetloop"
  },
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[parameters('addressPrefix')]"
      ]
    },
    "copy": [
      {
        "name": "subnets",
        "count": 2,
        "input": {
          "name": "[concat('subnet-', copyIndex('subnets'))]",
          "properties": {
            "addressPrefix": "[variables('subnetAddressPrefix')[copyIndex('subnets')]]"
          }
        }
      }
    ]
  }
}
```

## <a name="example-templates"></a>예제 템플릿

다음 예제에서는 속성에 대 한 값을 두 개 이상 만드는 일반적인 시나리오를 보여 줍니다.

|템플릿  |Description  |
|---------|---------|
|[가변적인 수의 데이터 디스크를 사용한 VM 배포](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |가상 머신을 사용하여 여러 데이터 디스크를 배포합니다. |

## <a name="next-steps"></a>다음 단계

* 자습서를 진행 하려면 [자습서: ARM 템플릿을 사용 하 여 여러 리소스 인스턴스 만들기](template-tutorial-create-multiple-instances.md)를 참조 하세요.
* Copy 요소의 다른 용도는 다음을 참조 하세요.
  * [ARM 템플릿의 리소스 반복](copy-resources.md)
  * [ARM 템플릿의 변수 반복](copy-variables.md)
  * [ARM 템플릿의 출력 반복](copy-outputs.md)
* 템플릿의 섹션에 대해 알아보려면 [ARM 템플릿 제작](template-syntax.md)을 참조 하세요.
* 템플릿을 배포 하는 방법에 대 한 자세한 내용은 [ARM 템플릿을 사용 하 여 응용 프로그램 배포](deploy-powershell.md)를 참조 하세요.

