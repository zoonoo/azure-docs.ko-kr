---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 87fd6b626efb60c7fc7ec8896f2c3758ae5cc33c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66132888"
---
### <a name="app-service-plan"></a>App Service 계획
웹앱을 호스팅하기 위한 서비스 계획을 만듭니다. **hostingPlanName** 매개 변수를 통해 계획의 이름을 제공합니다. 계획의 위치는 리소스 그룹에 사용된 위치와 동일합니다. 가격 책정 계층 및 작업자 크기는 **sku** 및 **workerSize** 매개 변수에서 지정됩니다.

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

