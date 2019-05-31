---
title: 포함 파일
description: 포함 파일
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 20dfc2413ed437fe6b08797c9c91acb5498dca23
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66138559"
---
`myAppServicePlan` App Service 계획에서 [웹앱](../articles/app-service/containers/app-service-linux-intro.md)을 만듭니다. 

Cloud Shell에서 [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) 명령을 사용할 수 있습니다. 다음 예에서 `<app-name>`을 전역적으로 고유한 앱 이름으로 바꿉니다(유효한 문자는 `a-z`, `0-9` 및 `-`). 

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --deployment-local-git
```

웹앱이 만들어지면 Azure CLI에서 다음 예제와 비슷한 출력을 표시합니다.

```json
Local git is configured with url of 'https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

> [!NOTE]
> Git 원격의 URL은 `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git` 형식으로 `deploymentLocalGitUrl` 속성에 표시됩니다. 나중에 필요하므로 이 URL을 저장합니다.
>
