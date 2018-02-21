---
title: "Azure Web App for Containers에 Go 앱 배포 | Microsoft Docs"
description: "Azure Web Apps for Containers에 Go 응용 프로그램을 실행하는 Docker 이미지를 배포하는 방법입니다."
keywords: "Azure App Service, 웹앱, Go, Docker, 컨테이너"
services: app-service
author: sptramer
manager: cfowler
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.devlang: go
ms.topic: quickstart
ms.date: 01/17/2018
ms.author: sttramer
ms.custom: mvc
ms.openlocfilehash: dbe4d7bc6f5f1d83a079993c9616206fd82a1b9d
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2018
---
# <a name="deploy-a-go-app-in-azure-web-app-for-containers"></a>Azure Web App for Containers에 Go 앱 배포

App Service는 PHP 7.0 및 Node.js 4.5와 같은 특정 버전에 대한 지원을 통해 Linux에 미리 정의된 응용 프로그램 스택을 제공합니다. 또한 사용자 지정 Docker 이미지를 사용하여 Azure에 아직 정의되지 않은 응용 프로그램 스택에 웹앱을 실행할 수도 있습니다. 이 빠른 시작에서는 Go 응용 프로그램이 있는 기존 Docker 컨테이너를 가져와서 Azure App Service에서 실행하는 방법을 보여 줍니다. [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)를 사용하여 웹앱을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app-for-the-container"></a>컨테이너에 대한 웹앱 만들기

[az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) 명령을 사용하여 `myAppServicePlan` App Service 계획에 [웹앱](../app-service-web-overview.md)을 만듭니다. `<app name>`을 전역적으로 고유한 앱 이름으로 바꾸어야 합니다.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name microsoft/appservice-go-quickstart
```

앞의 명령에서 `--deployment-container-image-name`은 [microsoft/appservice-go-quickstart](https://hub.docker.com/r/microsoft/appservice-go-quickstart) 공용 Docker 허브 이미지를 가리킵니다.

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

## <a name="get-data-from-the-apps-endpoint"></a>앱의 엔트포인트에서 데이터 가져오기

`curl` 명령을 사용하여 컨테이너의 응용 프로그램에서 제공하는 REST API 엔드포인트에 연결합니다.

```bash
curl -X GET http://<app_name>.azurewebsites.net:8080/hello
```

```output
Hello world!
```

**축하합니다.** Go 응용 프로그램을 실행하는 사용자 지정 Docker 이미지를 Azure Web Apps for Containers에 배포했습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [사용자 지정 Docker 이미지 사용](tutorial-custom-docker-image.md)
