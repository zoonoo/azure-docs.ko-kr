---
title: "Web App for Containers에서 사용자 지정 Docker Hub 이미지 실행 | Microsoft Docs"
description: "Web App for Containers에 사용자 지정 Docker 이미지를 사용하는 방법."
keywords: "azure app service, 웹앱, linux, docker, 컨테이너"
services: app-service
documentationcenter: 
author: naziml
manager: cfowler
editor: 
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/05/2017
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: eadc0f7eb20b9e8d1cacc79b2907559e2b2535a2
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2017
---
# <a name="run-a-custom-docker-hub-image-in-web-app-for-containers"></a>Web App for Containers에서 사용자 지정 Docker Hub 이미지 실행

App Service는 PHP 7.0 및 Node.js 4.5와 같은 특정 버전에 대한 지원을 통해 Linux에 미리 정의된 응용 프로그램 스택을 제공합니다. 또한 사용자 지정 Docker 이미지를 사용하여 Azure에 아직 정의되지 않은 응용 프로그램 스택에 웹앱을 배포할 수도 있습니다. 이 요약에서는 웹앱을 만들어 Python 기반 Docker 이미지를 배포하는 방법을 설명합니다. [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)를 사용하여 웹앱을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app-for-container"></a>Web App for Container 만들기

[az webapp create](/cli/azure/webapp#create) 명령을 사용하여 `myAppServicePlan` App Service 계획에 [웹앱](../app-service-web-overview.md)을 만듭니다. `<app name>`을 고유한 앱 이름으로 대체해야 합니다.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name elnably/dockerimagetest
```

이전 명령에서 `--deployment-container-image-name`은 공용 Docker Hub 이미지 [https://hub.docker.com/r/elnably/dockerimagetest/](https://hub.docker.com/r/elnably/dockerimagetest/)를 가리킵니다. [https://github.com/rachelappel/docker-image](https://github.com/rachelappel/docker-image)에서 그 콘텐츠를 점검할 수 있습니다.

웹앱이 만들어지면 Azure CLI에서 다음 예제와 비슷한 출력을 표시합니다.

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app name>.scm.azurewebsites.net/<app name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

## <a name="browse-to-the-app"></a>앱으로 이동

웹 브라우저에서 다음 URL로 이동합니다.

```bash
http://<app_name>.azurewebsites.net
```

![Azure에서 실행되는 샘플 앱](media/quickstart-custom-docker-image/hello-world-in-browser.png)

**축하합니다.** 컨테이너용 Azure Web App에 사용자 지정 Docker 이미지를 배포하였습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure에서 Docker Python 및 PostgreSQL 웹앱 빌드](tutorial-docker-python-postgresql-app.md)
