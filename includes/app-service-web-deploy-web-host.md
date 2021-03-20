---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 733fc9620cbd17e5e4d8bb101c54ff646a06d6a4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "86050321"
---
### <a name="app-service-plan"></a>App Service 요금제
웹앱을 호스팅하기 위한 서비스 계획을 만듭니다. **hostingPlanName** 매개 변수를 통해 계획의 이름을 제공합니다. 계획의 위치는 리소스 그룹에 사용된 위치와 동일합니다. 가격 책정 계층 및 작업자 크기는 **sku** 및 **workerSize** 매개 변수에서 지정됩니다.

```config
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('hostingPlanName')]",
  "type": "Microsoft.Web/serverfarms",
  "location": "[resourceGroup().location]",
  "sku": {
    "name": "[parameters('sku')]",
    "capacity": "[parameters('workerSize')]"
  },
  "properties": {
    "name": "[parameters('hostingPlanName')]"
  }
},
```
