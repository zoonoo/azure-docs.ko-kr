---
title: "Azure 리소스 속성의 여러 복사본 | Microsoft Docs"
description: "Azure Resource Manager 템플릿에서 리소스 종류에 속성의 여러 인스턴스를 만듭니다. 이 시나리오에 대한 복사 루프 대신 take 함수를 사용해야 합니다."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/17/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: da79ff58e892bde5a35456992105de9bdbe36364
ms.lasthandoff: 04/18/2017


---
# <a name="create-multiple-instances-of-property-on-resource-type"></a>리소스 유형에서 속성의 여러 인스턴스 만들기
리소스 형식 내의 속성이 아니라 리소스 형식에서 [복사](resource-group-create-multiple.md) 를 사용할 수 있습니다. 리소스의 일부인 항목에 여러 인스턴스를 만드는 경우 이 요구 사항은 문제를 일으킬 수 있습니다. 일반적인 시나리오는 가상 컴퓨터에 대해 여러 데이터 디스크를 만드는 것입니다. dataDisks는 자체 리소스 형식이 아니라 가상 컴퓨터에 대한 속성이므로 데이터 디스크에 대해 복사를 사용할 수 없습니다. 대신 필요한 만큼 많은 데이터 디스크를 포함한 배열을 만들고 만들 데이터 디스크의 실제 수를 전달합니다. 가상 컴퓨터 정의에서 `take` 함수를 사용하여 배열에서 실제로 원하는 요소의 수를 알아냅니다.

이 패턴의 전체 예제는 [동적 데이터 디스크를 선택하여 VM 만들기](https://azure.microsoft.com/documentation/templates/201-vm-dynamic-data-disks-selection/) 템플릿에 나와 있습니다.

배포 템플릿의 관련 섹션은 이 문서에 나와 있습니다. 많은 수의 데이터 디스크를 동적으로 만드는 작업과 관련된 섹션을 중점적으로 제공하기 위해 일부 템플릿을 제거했습니다. 

## <a name="define-template-with-variable-for-the-property"></a>속성에 대한 변수로 템플릿 정의

여러 개의 데이터 디스크가 있는 가상 컴퓨터를 만들려면 필요한 것보다 많은 데이터 디스크를 정의하는 배열 변수를 추가합니다. `numDataDisks` 매개 변수에서 만들려는 데이터 디스크의 실제 수를 전달합니다. `take`를 사용하여 배열에서 가상 컴퓨터에 할당할 요소의 정확한 수를 지정합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numDataDisks": {
      "type": "int",
      "maxValue": 64,
      "metadata": {
        "description": "This parameter allows you to select the number of disks you want"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'dynamicdisk')]",
    "sizeOfDataDisksInGB": 100,
    "diskCaching": "ReadWrite",
    "diskArray": [
      {
        "name": "datadisk1",
        "lun": 0,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk1.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk2",
        "lun": 1,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk2.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk3",
        "lun": 2,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk3.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk4",
        "lun": 3,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk4.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      ... /* not all elements shown */
      {
        "name": "datadisk63",
        "lun": 62,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk63.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk64",
        "lun": 63,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk64.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      }
    ]
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "properties": {
        "storageProfile": {
          "dataDisks": "[take(variables('diskArray'),parameters('numDataDisks'))]"
        },
        ...
      }
      ...
    }
  ]
}
```

## <a name="create-multiple-virtual-machines-with-multiple-data-disks"></a>여러 개의 데이터 디스크로 여러 가상 컴퓨터 만들기

속성에 대해 가변적인 항목 수로 리소스의 여러 인스턴스를 만들어야 할 때 **take** 함수와 **copy** 요소를 함께 사용할 수 있습니다. 예를 들어, 여러 가상 컴퓨터를 만들어야 하지만 각 가상 컴퓨터에 서로 다른 수의 데이터 디스크가 있다고 가정합니다. 

각 데이터 디스크에 연결된 가상 컴퓨터를 식별하는 이름을 지정하려면 데이터 디스크 배열을 별도의 템플릿에 넣습니다. 가상 컴퓨터 이름 및 반환할 데이터 디스크 수에 대한 매개 변수를 포함합니다. 출력 섹션에서 지정된 항목 수를 반환합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "storageAccountName": {
      "type": "string"
    },
    "numDataDisks": {
      "type": "int",
      "maxValue": 16,
      "metadata": {
        "description": "This parameter allows the user to select the number of disks they want"
      }
    }
  },
  "variables": {
    "diskArray": [
      {
        "name": "[concat(parameters('vmName'), '-datadisk1')]",
        "vhd": {
          "uri": "[concat('http://', parameters('storageAccountName'),'.blob.core.windows.net/vhds/', parameters('vmName'), '-datadisk1.vhd')]"
        },
        ...
      },
      ...
    ],
  },
  "resources": [
  ],
  "outputs": {
    "result": {
      "type": "array",
      "value": "[take(variables('diskArray'),parameters('numDataDisks'))]"
    }
  }
}
``` 

부모 템플릿에서는 가상 컴퓨터의 수에 대한 매개 변수 및 각 가상 컴퓨터에 대 한 데이터 디스크 수에 대한 배열을 포함합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...
    "numberOfInstances": {
      "type": "int",
      "defaultValue": 2,
      "metadata": {
        "description": "Number of VMs to deploy"
      }
    },
    "numberOfDataDisksPerVM": {
      "type": "array",
      "defaultValue": [1,2]
    }
  },
```

리소스 섹션에서 데이터 디스크를 정의하는 템플릿의 여러 인스턴스를 배포합니다. 

```json
{
  "apiVersion": "2016-09-01",
  "name": "[concat('nested-', copyIndex())]",
  "type": "Microsoft.Resources/deployments",
  "copy": {
    "name": "deploycopy",
    "count": "[parameters('numberOfInstances')]"
  },
  "properties": {
    "mode": "incremental",
    "templateLink": {
      "uri": "{data-disk-template-uri}",
      "contentVersion": "1.0.0.0"
    },
    "parameters": {
      "vmName": { "value": "[concat('myvm', copyIndex())]" },
      "storageAccountName": { "value": "[variables('storageAccountName')]" },
      "numDataDisks": { "value": "[parameters('numberOfDataDisksPerVM')[copyIndex()]]" }
    }
  }
},
```

리소스 섹션에서 가상 컴퓨터의 여러 인스턴스를 배포합니다. 데이터 디스크의 경우 데이터 디스크에 대한 올바른 수와 데이터 디스크에 대한 올바른 이름을 포함하는 중첩된 배포를 참조합니다.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[concat('myvm', copyIndex())]",
  "copy": {
    "name": "virtualMachineLoop",
      "count": "[parameters('numberOfInstances')]"
  },
  "properties": {
    "storageProfile": {
      ...
      "dataDisks": "[reference(concat('nested-', copyIndex())).outputs.result.value]"
    },
    ...
  },
  ...
}
```

## <a name="next-steps"></a>다음 단계
* 리소스의 여러 인스턴스를 만드는 방법은 [Azure Resource Manager 템플릿에서 리소스의 여러 인스턴스 배포](resource-group-create-multiple.md)를 참조하세요.
* 순서대로 반복된 리소스를 만들려면 [Azure Resource Manager 템플릿에 대한 순차적 루핑](resource-manager-sequential-loop.md)을 참조하세요.
* 템플릿 배포 방법에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿을 사용하여 응용 프로그램 배포](resource-group-template-deploy.md)를 참조하세요.


