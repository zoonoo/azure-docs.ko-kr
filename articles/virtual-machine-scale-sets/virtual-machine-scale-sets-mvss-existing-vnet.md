---
title: Azure 확장 집합 템플릿에서 기존 가상 네트워크를 참조 합니다.
description: 기존 Azure Virtual Machine Scale Set 템플릿에 가상 네트워크를 추가하는 방법 알아보기
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: networking
ms.date: 04/26/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: fab6e6742fa43e1e38ee661b67896ae4aa11b3ed
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124825"
---
# <a name="add-reference-to-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Azure 확장 집합 템플릿에 기존 가상 네트워크에 대한 참조 추가

이 문서에서는 [기본 확장 집합 템플릿을](virtual-machine-scale-sets-mvss-start.md) 수정 하 여 새 가상 네트워크를 만드는 대신 기존 가상 네트워크에 배포 하는 방법을 보여 줍니다.

## <a name="change-the-template-definition"></a>템플릿 정의 변경

[이전 문서](virtual-machine-scale-sets-mvss-start.md) 에서는 기본 확장 집합 템플릿을 만들었습니다. 이제 이전 템플릿을 사용 하 고 수정 하 여 기존 가상 네트워크에 확장 집합을 배포 하는 템플릿을 만듭니다. 

먼저 `subnetId` 매개 변수를 추가합니다. 이 문자열은 확장 집합 구성에 전달되어 확장 집합에서 미리 만든 서브넷을 식별하여 가상 머신을 배포할 수 있게 합니다. 이 문자열의 형식은 다음과 같아야 합니다.`/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`

예를 들어 `myvnet` 이름, `mysubnet` 서브넷, `myrg` 리소스 그룹 및 `00000000-0000-0000-0000-000000000000` 구독을 사용하여 기존 가상 네트워크에 확장 집합을 배포하려면 subnetId가 `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`이 됩니다.

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "subnetId": {
+      "type": "string"
     }
   },
```

다음으로 기존 가상 네트워크를 사용하고 있고 새 가상 네트워크를 배포할 필요가 없기 때문에 `resources` 배열에서 가상 네트워크 리소스를 삭제합니다.

```diff
   "variables": {},
   "resources": [
-    {
-      "type": "Microsoft.Network/virtualNetworks",
-      "name": "myVnet",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "2018-11-01",
-      "properties": {
-        "addressSpace": {
-          "addressPrefixes": [
-            "10.0.0.0/16"
-          ]
-        },
-        "subnets": [
-          {
-            "name": "mySubnet",
-            "properties": {
-              "addressPrefix": "10.0.0.0/16"
-            }
-          }
-        ]
-      }
-    },
```

템플릿을 배포하기 전에 가상 네트워크가 이미 있으므로 확장 집합에서 가상 네트워크로 dependsOn 절을 지정할 필요가 없습니다. 다음 줄을 삭제합니다.

```diff
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "location": "[resourceGroup().location]",
       "apiVersion": "2019-03-01",
-      "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
-      ],
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
```

마지막으로,를 사용 하는 `subnetId` 대신 사용자가 설정한 매개 변수를 전달 하 여 `resourceId` 동일한 배포에서 VNET의 ID를 가져옵니다 .이는 기본 실행 가능 크기 집합 템플릿에서 수행 됩니다.

```diff
                       "name": "myIpConfig",
                       "properties": {
                         "subnet": {
-                          "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
+                          "id": "[parameters('subnetId')]"
                         }
                       }
                     }
```




## <a name="next-steps"></a>다음 단계

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
