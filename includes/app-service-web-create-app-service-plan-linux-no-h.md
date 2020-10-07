---
title: 포함 파일
description: 포함 파일
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 12/20/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 25003269fb6e00cadcc14d2356308cae54c70bf7
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "82085339"
---
Cloud Shell에서 [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) 명령을 사용하여 리소스 그룹에 App Service 계획을 만듭니다.

<!-- [!INCLUDE [app-service-plan](app-service-plan-linux.md)] -->

다음 예제에서는 **체험** 가격 책정 계층(`--sku F1`) 및 Linux 컨테이너(`--is-linux`)에서 `myAppServicePlan`이라는 App Service 계획을 만듭니다.

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku F1 --is-linux
```

App Service 계획을 만든 경우 Azure CLI는 다음 예제와 비슷한 정보를 표시합니다.

<pre>
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "linux",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
</pre>
