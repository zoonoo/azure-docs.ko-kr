---
title: "Azure Virtual Machine Scale Set 템플릿 알아보기 | Microsoft Docs"
description: "Azure Virtual Machine Scale Sets에 대한 실행 가능한 최소 크기 집합 템플릿을 만드는 방법 알아보기"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/14/2017
ms.author: negat
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: 2a8b770521e67ff9a621ad9fb2fa97e3f9462e31
ms.lasthandoff: 03/24/2017

---

# <a name="learn-about-virtual-machine-scale-set-templates"></a>Virtual Machine Scale Sets 템플릿에 대해 알아보기

[Azure Resource Manager 템플릿](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment)은 관련된 리소스 그룹을 배포하는 유용한 방법입니다. 이 자습서 시리즈에서는 실행 가능한 최소 크기 집합 템플릿을 만드는 방법과 이러한 템플릿을 다양한 시나리오에 맞게 수정하는 방법을 보여 줍니다. 모든 예제는 [github 리포지토리](https://github.com/gatneil/mvss)에서 가져온 것입니다. 이 템플릿은 간단하게 제작되었습니다. 크기 집합 템플릿의 전체 예제를 보려면 [Azure 빠른 시작 템플릿 github 리포지토리](https://github.com/Azure/azure-quickstart-templates)를 참조하고 문자열 `vmss`가 포함된 폴더를 검색합니다.

## <a name="a-minimum-viable-scale-set"></a>실행 가능한 최소 크기 집합

실행 가능한 최소 크기 집합 템플릿은 [여기](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json)에서 확인할 수 있습니다. 템플릿에 익숙한 경우 [다음 단계](#next-steps) 섹션을 건너뛰고 다른 시나리오에 맞게 이 시나리오를 수정하는 방법을 확인할 수 있습니다. 그러나 템플릿에 익숙하지 않은 경우 모든 설명 부분이 유용할 것입니다. 우선 기본 템플릿을 차례대로 만들어 보겠습니다.

먼저 템플릿의 `$schema` 및 `contentVersion`을 정의합니다. `$schema`는 템플릿 언어의 버전을 정의하고 Visual Studio 구문 강조 표시와 비슷한 유효성 검사 기능에 사용됩니다. `contentVersion`은 실제로 Azure에서는 전혀 사용되지 않습니다. 대신 템플릿 버전이 어떤 것인지 추적하는 것이 도움이 됩니다.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
```

다음으로 두 개의 매개 변수인 `adminUsername` 및 `adminPassword`를 정의합니다. 매개 변수는 배포 시에 사용자가 지정하는 값입니다. `adminUsername`은 단순히 말하면 `string`이지만 `adminPassword`가 암호이므로 `securestring` 형식을 지정합니다. 이러한 매개 변수가 크기 집합 구성에 전달되는 내용은 나중에 살펴보겠습니다.

```json
  "parameters": {
    "adminUsername": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    }
  },
```

또한 Resource Manager 템플릿을 사용하여 템플릿 뒷부분에서 사용할 변수를 정의할 수 있습니다. 이 예제에서는 어떤 변수도 사용하지 않으므로 JSON 개체를 빈 상태로 두었습니다.

```json
  "variables": {},
```

그런 후에는 템플릿 리소스가 제공됩니다. 여기서 실제로 배포하려는 항목을 정의합니다. `parameters` 및 `variables`(JSON 개체)과 달리 `resources`는 JSON 개체의 JSON 목록입니다.

```json
   "resources": [
```

모든 리소스에는 `type`, `name`, `apiVersion` 및 `location`이 필요합니다. 첫 번째 리소스는 `Microsft.Network/virtualNetwork` 형식을 가지며 이름은 `myVnet`이고 apiVersion은 `2016-03-30`입니다. 리소스 형식에 대한 최신 API 버전을 확인하려면 [Azure REST API 설명서](https://docs.microsoft.com/rest/api/)를 참조하세요.

```json
     {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "apiVersion": "2016-12-01",
```

가상 네트워크에 대한 위치를 지정하기 위해 [Resource Manager 템플릿 함수](../azure-resource-manager/resource-group-template-functions.md)를 사용합니다. 이러한 함수는 `"[<template-function>]"`과 같이 큰따옴표와 대괄호로 묶어야 합니다. 이 경우에는 resourceGroup 함수를 사용합니다. 이 함수는 인수는 취하지 않고 이 배포가 배포될 리소스 그룹에 대한 메타데이터를 포함하는 JSON 개체를 반환합니다. 리소스 그룹은 배포 시에 사용자가 설정합니다. 그런 후 `.location`으로 이 JSON 개체를 인덱싱하여 JSON 개체에서의 위치를 가져옵니다.

```json
       "location": "[resourceGroup().location]",
```


각 Resource Manager 리소스에는 해당 리소스 관련 구성에 대한 자체 `properties` 섹션이 있습니다. 이 경우에는 가상 네트워크에 개인 IP 주소 범위 `10.0.0.0/16`을 사용하는 하나의 서브넷만 있도록 지정합니다. 크기 집합은 항상 하나의 서브넷에 포함되며 여러 서브넷에 걸쳐 있을 수 없습니다.

```json
       "properties": {
         "addressSpace": {
           "addressPrefixes": [
             "10.0.0.0/16"
           ]
         },
         "subnets": [
           {
             "name": "mySubnet",
             "properties": {
               "addressPrefix": "10.0.0.0/16"
             }
           }
         ]
       }
     },
```

필수 `type`, `name`, `apiVersion` 및 `location` 속성 외에, 각 리소스에는 경우에 따라 이 리소스를 배포하기 전에 이 배포의 다른 리소스가 완료해야 하는 배포를 지정하는 `dependsOn` 문자열 목록이 있을 수 있습니다. 이 경우 이 목록에는 위의 가상 네트워크만 요소로 포함됩니다. VM을 만들려면 먼저 크기 집합에 네트워크가 있어야 하므로 이러한 종속성을 지정합니다. 이러한 방식으로 크기 집합은 이전에 네트워크 속성에 지정된 IP 주소 범위의 개인 IP 주소를 이러한 VM에 제공할 수 있습니다. dependsOn 목록의 각 문자열 형식은 `<type>/<name>`입니다(이전에 가상 네트워크 리소스 정의에 사용한 것과 동일한 `type` 및 `name`).

```json
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "apiVersion": "2016-04-30-preview",
       "location": "[resourceGroup().location]",
       "dependsOn": [
         "Microsoft.Network/virtualNetworks/myVnet"
       ],
```

크기 집합은 만들 VM의 크기("sku 이름") 및 만들려는 이러한 VM의 수("sku 용량")을 알고 있어야 합니다. 사용 가능한 VM 크기를 확인하려면 [VM 크기 설명서](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-sizes)를 참조하세요.

```json
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
       },
```

또한 크기 집합은 크기 집합의 업데이트 처리 방법을 알아야 합니다. 현재 `Manual` 및 `Automatic`의 두 가지 옵션이 있습니다. 두 옵션 사이의 차이점에 대한 자세한 내용은 [크기 집합을 업그레이드하는 방법](./virtual-machine-scale-sets-upgrade-scale-set.md)에 대한 설명서를 참조하세요.

```json
       "properties": {
         "upgradePolicy": {
           "mode": "Manual"
         },
```

또한 크기 집합은 VM에 추가할 운영 체제도 알아야 합니다. 여기서는 완벽하게 패치된 Ubuntu 16.04 LTS 이미지를 사용하여 VM을 만듭니다.

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
               "publisher": "Canonical",
               "offer": "UbuntuServer",
               "sku": "16.04-LTS",
               "version": "latest"
             }
           },
```

크기 집합은 각 VM 이름을 지정하는 대신, 여러 VM을 배포하므로 여기서는 `computerNamePrefix`를 지정합니다. 크기 집합은 각 VM에 대한 이러한 접두사에 인덱스를 추가하므로 VM 이름은 `<computerNamePrefix>_<auto-generated-index>` 형식을 갖습니다. 이 코드 조각에서는 이전 매개 변수를 사용하여 크기 집합의 모든 VM에 대한 관리자 사용자 이름 및 암호를 설정하고 있음을 알 수 있습니다. 이 작업을 참조하려는 매개 변수를 지정하는 문자열을 취하고 해당 매개 변수의 값을 출력하는 `parameters` 템플릿 함수를 사용하여 수행합니다.

```json
           "osProfile": {
             "computerNamePrefix": "vm",
             "adminUsername": "[parameters('adminUsername')]",
             "adminPassword": "[parameters('adminPassword')]"
           },
```

마지막으로 크기 집합의 VM에 대한 네트워크 구성을 지정해야 합니다. 이 경우 크기 집합이 이 서브넷에 네트워크 인터페이스를 추가해야 한다는 사실을 알 수 있도록 앞서 만든 서브넷의 ID만 지정하면 됩니다. `resourceId` 템플릿 함수를 사용하여 서브넷을 포함하는 가상 네트워크의 ID를 얻을 수 있습니다. 이 함수는 리소스의 형식 및 이름을 취한 후 해당 리소스의 정규화된 식별자(`/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/<resourceProviderNamespace>/<resourceType>/<resourceName>` 형식)를 반환합니다. 그러나 가상 네트워크의 식별자로는 충분하지 않습니다. 크기 집합 VM이 포함되어야 하는 특정 서브넷을 지정해야 하므로 `/subnets/mySubnet`을 가상 네트워크의 ID와 연결합니다. 결과는 서브넷의 정규화된 ID입니다. 이 연결은 일련의 문자열을 취한 후 해당 연결을 반환하는 `concat` 함수를 사용하여 수행합니다.

```json
           "networkProfile": {
             "networkInterfaceConfigurations": [
               {
                 "name": "myNic",
                 "properties": {
                   "primary": "true",
                   "ipConfigurations": [
                     {
                       "name": "myIpConfig",
                       "properties": {
                         "subnet": {
                           "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
                         }
                       }
                     }
                   ]
                 }
               }
             ]
           }
         }
       }
     }
   ]
}

```

## <a name="next-steps"></a>다음 단계

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
