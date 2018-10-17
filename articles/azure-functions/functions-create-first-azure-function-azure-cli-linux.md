---
title: Azure CLI(미리 보기)를 사용하여 Linux에서 첫 번째 함수 만들기 | Microsoft Docs
description: Azure CLI를 사용하여 기본 Linux 이미지에서 실행되는 첫 번째 Azure Function을 만드는 방법에 대해 알아봅니다.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 11/15/2017
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: 1cf20a4a93ef1b5bfb9c7818f35be5e75e45a3d2
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901093"
---
# <a name="create-your-first-function-running-on-linux-using-the-azure-cli-preview"></a>Azure CLI(미리 보기)를 사용하여 Linux에서 실행되는 첫 번째 함수 만들기

Azure Functions를 사용하면 Linux에서 기본 Azure App Service 컨테이너에 함수를 호스트할 수 있습니다. [고유한 사용자 지정 컨테이너를 가져올 수도](functions-create-function-linux-custom-image.md) 있습니다. 이 기능은 현재 미리 보기로 있으므로 [Functions 2.0 런타임](functions-versions.md)이 필요합니다.

이 빠른 시작 항목에서는 Azure Functions를 Azure CLI와 함께 사용하여 Linux에서 기본 App Service 컨테이너에 호스트되는 첫 번째 함수 앱을 만드는 방법을 설명합니다. 함수 코드 자체는 GitHub 샘플 리포지토리에서 이미지로 배포됩니다.    

다음 단계는 Mac, Windows 또는 Linux 컴퓨터에서 지원됩니다. 

## <a name="prerequisites"></a>필수 조건 

이 빠른 시작을 완료하려면 다음이 필요합니다.

+ 활성 Azure 구독.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 항목에서 Azure CLI 버전 2.0.21 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Linux App Service 계획 만들기

Functions용 Linux 호스팅은 현재 App Service 계획에서만 지원됩니다. 사용 계획 호스팅은 아직 지원되지 않습니다. 호스팅에 대한 자세한 내용은 [Azure Functions 호스팅 계획 비교](functions-scale.md)를 참조하세요. 

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

## <a name="create-a-function-app-on-linux"></a>Linux에서 함수 앱 만들기

Linux에서 함수 실행을 호스트하는 함수 앱이 있어야 합니다. 함수 앱은 함수 코드 실행을 위한 환경을 제공합니다. 이를 통해 함수를 논리 단위로 그룹화하여 더욱 쉽게 리소스를 관리, 배포 및 공유할 수 있습니다. Linux App Service 계획과 함께 [az functionapp create](/cli/azure/functionapp#az-functionapp-create) 명령을 사용하여 함수 앱을 만듭니다. 

다음 명령에서 `<app_name>` 자리 표시자 및 `<storage_name>`의 저장소 계정 이름을 고유한 함수 앱 이름으로 바꿉니다. `<app_name>`은 함수 앱의 기본 DNS 도메인으로 사용되므로 이름이 Azure의 모든 앱에서 고유해야 합니다. _deployment-source-url_ 매개 변수는 "Hello World" HTTP 트리거 함수를 포함하는 GitHub의 샘플 리포지토리입니다.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myAppServicePlan --deployment-source-url https://github.com/Azure-Samples/functions-quickstart-linux
```
함수 앱을 만들고 배포했으면 Azure CLI는 다음 예와 비슷한 정보를 표시합니다.

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

`myAppServicePlan`은 Linux 계획이기 때문에 Linux에서 함수 앱을 실행하는 컨테이너를 만드는 데 기본 제공 Docker 이미지가 사용됩니다. 

>[!NOTE]  
>현재 샘플 리포지토리에는 [deploy.sh](https://github.com/Azure-Samples/functions-quickstart-linux/blob/master/deploy.sh)와 [.deployment](https://github.com/Azure-Samples/functions-quickstart-linux/blob/master/.deployment)라는 두 개의 스크립트 파일이 있습니다. .deployment 파일은 [사용자 지정 배포 스크립트](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)로 deploy.sh를 사용하도록 배포 프로세스에 지시합니다. 현재 미리 보기 릴리스에서는 Linux 이미지에 함수 앱을 배포하기 위해 스크립트가 필요합니다.  

## <a name="configure-the-function-app"></a>함수 앱 구성

GitHub 리포지토리의 프로젝트에는 Functions 런타임 버전 1.x가 필요합니다. `FUNCTIONS_WORKER_RUNTIME` 응용 프로그램 설정을 `~1`로 설정하면 함수 앱이 최신 1.x 버전에 고정됩니다. [az functionapp config appsettings set](https://docs.microsoft.com/cli/azure/functionapp/config/appsettings#set) 명령을 사용하여 응용 프로그램 설정을 설정합니다.

다음 Azure CLI 명령에서 `<app_name>은 함수 앱의 이름입니다.

```azurecli-interactive
az functionapp config appsettings set --name <app_name> \
--resource-group myResourceGroup \
--settings FUNCTIONS_WORKER_RUNTIME=~1
```

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]
