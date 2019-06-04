---
title: 사용자 지정 이미지를 사용하여 Linux에서 Azure Functions 만들기
description: 사용자 지정 Linux 이미지에서 실행되는 Azure Functions를 만드는 방법을 알아봅니다.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 02/25/2019
ms.topic: tutorial
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: 03e1ec58b0ef3ad50a04f82ced7d20119ab3ef5b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59491460"
---
# <a name="create-a-function-on-linux-using-a-custom-image"></a>사용자 지정 이미지를 사용하여 Linux에서 함수 만들기

Azure Functions를 사용하면 사용자 지정 컨테이너에서 Linux의 함수를 호스팅할 수 있습니다. [기본 Azure App Service 컨테이너에 호스팅](functions-create-first-azure-function-azure-cli-linux.md)할 수도 있습니다. 이 기능을 사용하려면 [Functions 2.x 런타임](functions-versions.md)이 필요합니다.

이 자습서에서는 함수를 사용자 지정 Docker 이미지로 Azure에 배포하는 방법에 대해 알아봅니다. 이 패턴은 기본 제공 App Service 컨테이너 이미지를 사용자 지정해야 하는 경우에 유용합니다. 함수에 특정 언어 버전이 필요하거나 기본 제공 이미지에서 제공되지 않는 특정 종속성 또는 구성이 필요한 경우 사용자 지정 이미지를 사용할 수 있습니다. Azure Functions에 대해 지원되는 기본 이미지는 [Azure Functions 기본 이미지 리포지토리](https://hub.docker.com/_/microsoft-azure-functions-base)에 나와 있습니다. [Python 지원](functions-reference-python.md)은 현재 미리 보기로 제공됩니다.

이 자습서에서는 Azure Functions 핵심 도구를 사용하여 사용자 지정 Linux 이미지에서 함수를 만드는 방법을 안내합니다. Azure CLI를 사용하여, 만든 이미지를 Azure에서 함수 앱에 게시합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 핵심 도구를 사용하여 함수 앱과 Dockerfile을 만듭니다.
> * Docker를 사용하여 사용자 지정 이미지 빌드
> * 컨테이너 레지스트리에 사용자 지정 이미지 게시
> * Azure Storage 계정 만들기
> * Linux App Service 계획 만들기
> * Docker 허브에서 함수 앱 배포
> * 함수 앱에 애플리케이션 설정 추가
> * 연속 배포 사용

다음 단계는 Mac, Windows 또는 Linux 컴퓨터에서 지원됩니다.  

## <a name="prerequisites"></a>필수 조건

이 샘플을 실행하기 전에 다음이 있어야 합니다.

* [Azure Core Tools 버전 2.x](functions-run-local.md#v2)를 설치합니다.

* [Azure CLI]( /cli/azure/install-azure-cli)를 설치합니다. 이 문서에서 설명하는 단계를 수행하려면 Azure CLI 버전 2.0 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다.  
[Azure Cloud Shell](https://shell.azure.com/bash)을 사용할 수도 있습니다.

* 활성 Azure 구독.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-local-function-app-project"></a>로컬 함수 앱 프로젝트 만들기

명령줄에서 다음 명령을 실행하여 현재 로컬 디렉터리의 `MyFunctionProj` 폴더에 함수 앱 프로젝트를 만듭니다.

```bash
func init MyFunctionProj --docker
```

`--docker` 옵션을 포함할 때는 프로젝트에 대해 dockerfile이 생성됩니다. 이 파일은 프로젝트를 실행하는 사용자 지정 컨테이너를 만드는 데 사용됩니다. 사용하는 기본 이미지는 작업자 런타임 언어 선택에 따라 다릅니다.  

메시지가 표시되면 다음 언어 중에서 작업자 런타임을 선택합니다.

* `dotnet`: .NET 클래스 라이브러리 프로젝트(.csproj)를 만듭니다.
* `node`: JavaScript 프로젝트를 만듭니다.
* `python`: Python 프로젝트를 만듭니다.

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

명령을 실행하는 경우 다음 출력과 같이 표시됩니다.

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing Dockerfile
```

다음 명령을 사용하여 새 `MyFunctionProj` 프로젝트 폴더로 이동합니다.

```bash
cd MyFunctionProj
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-update-function-code](../../includes/functions-update-function-code.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

## <a name="build-the-image-from-the-docker-file"></a>Docker 파일에서 이미지 빌드

프로젝트 루트 폴더에서 _Dockerfile_을 확인합니다. 이 파일은 Linux에서 함수 앱을 실행하는 데 필요한 환경을 설명합니다. 다음 예제는 JavaScript(Node.js) 작업자 런타임에서 함수 앱을 실행하는 컨테이너를 만드는 Dockerfile입니다. 

```Dockerfile
FROM mcr.microsoft.com/azure-functions/node:2.0

ENV AzureWebJobsScriptRoot=/home/site/wwwroot
COPY . /home/site/wwwroot
```

> [!NOTE]
> Azure Functions에 대해 지원되는 기본 이미지의 전체 목록은 [Azure Functions 기본 이미지 페이지](https://hub.docker.com/_/microsoft-azure-functions-base)에 나와 있습니다.

### <a name="run-the-build-command"></a>`build` 명령을 실행합니다.
루트 폴더에서 [docker build](https://docs.docker.com/engine/reference/commandline/build/) 명령을 실행하고 이름(`mydockerimage`) 및 태그(`v1.0.0`)를 입력합니다. `<docker-id>`를 Docker 허브 계정 ID로 바꿉니다. 이 명령은 컨테이너에 대한 Docker 이미지를 빌드합니다.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

명령을 실행하면 다음 출력과 비슷한 것이 나타납니다. 이 경우는 JavaScript 작업자 런타임에 대한 것입니다.

```bash
Sending build context to Docker daemon  17.41kB
Step 1/3 : FROM mcr.microsoft.com/azure-functions/node:2.0
2.0: Pulling from azure-functions/node
802b00ed6f79: Pull complete
44580ea7a636: Pull complete
73eebe8d57f9: Pull complete
3d82a67477c2: Pull complete
8bd51cd50290: Pull complete
7bd755353966: Pull complete
Digest: sha256:480e969821e9befe7c61dda353f63298f2c4b109e13032df5518e92540ea1d08
Status: Downloaded newer image for mcr.microsoft.com/azure-functions/node:2.0
 ---> 7c71671b838f
Step 2/3 : ENV AzureWebJobsScriptRoot=/home/site/wwwroot
 ---> Running in ed1e5809f0b7
Removing intermediate container ed1e5809f0b7
 ---> 39d9c341368a
Step 3/3 : COPY . /home/site/wwwroot
 ---> 5e196215935a
Successfully built 5e196215935a
Successfully tagged <docker-id>/mydockerimage:v1.0.0
```

### <a name="test-the-image-locally"></a>이미지를 로컬로 테스트
로컬 컨테이너에서 Docker 이미지를 실행하여 빌드된 이미지가 작동하는지 확인합니다. [docker run](https://docs.docker.com/engine/reference/commandline/run/) 명령을 실행하고 이미지의 이름 및 태그를 전달합니다. `-p` 인수를 사용하여 포트를 지정해야 합니다.

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

로컬 Docker 컨테이너에서 실행되는 사용자 지정 이미지를 사용하여 <http://localhost:8080>으로 이동하여 함수 앱 및 컨테이너가 제대로 작동하는지 확인합니다.

![함수 앱을 로컬로 테스트](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

필요에 따라 이번에는 로컬 컨테이너에서 다음 URL을 사용하여 다시 함수를 테스트할 수 있습니다.

`http://localhost:8080/api/myhttptrigger?name=<yourname>`

컨테이너에서 함수 앱을 확인한 후 실행을 중지합니다. 이제 사용자 이미지를 Docker 허브 계정으로 푸시할 수 있습니다.

## <a name="push-the-custom-image-to-docker-hub"></a>사용자 지정 이미지를 Docker 허브에 푸시

레지스트리는 이미지를 호스트하고 서비스 이미지 및 컨테이너 서비스를 제공하는 응용 프로그램입니다. 이미지를 공유하려면 레지스트리에 푸시해야 합니다. Docker 허브는 Docker 이미지의 레지스트리이며 고유한 공개 또는 프라이빗 리포지토리를 호스팅할 수 있습니다.

이미지를 푸시하려면 먼저 [docker login](https://docs.docker.com/engine/reference/commandline/login/) 명령을 사용하여 Docker 허브에 로그인해야 합니다. `<docker-id>`를 사용자의 계정 이름으로 바꾸고, 콘솔의 프롬프트에서 암호를 입력합니다. 다른 Docker 허브 암호 옵션은 [docker login 명령 설명서](https://docs.docker.com/engine/reference/commandline/login/)를 참조하세요.

```bash
docker login --username <docker-id>
```

"로그인했습니다."라는 메시지는 사용자가 로그인했다고 확인합니다. 로그인했으면 [docker push](https://docs.docker.com/engine/reference/commandline/push/) 명령을 사용하여 이미지를 Docker 허브에 푸시합니다.

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

명령의 출력을 검토하여 푸시가 성공했는지 확인합니다.

```bash
The push refers to a repository [docker.io/<docker-id>/mydockerimage:v1.0.0]
24d81eb139bf: Pushed
fd9e998161c9: Mounted from <docker-id>/mydockerimage
e7796c35add2: Mounted from <docker-id>/mydockerimage
ae9a05b85848: Mounted from <docker-id>/mydockerimage
45c86e20670d: Mounted from <docker-id>/mydockerimage
v1.0.0: digest: sha256:be080d80770df71234eb893fbe4d... size: 1796
```

이제 Azure에서 이 이미지를 새 함수 앱에 대한 배포 원본으로 사용할 수 있습니다.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Linux App Service 계획 만들기

Functions에 대한 Linux 호스팅은 현재 소비 계획에서 지원되지 않습니다. Linux App Service 계획에서 Linux 컨테이너 앱을 호스팅해야 합니다. 호스팅에 대한 자세한 내용은 [Azure Functions 호스팅 계획 비교](functions-scale.md)를 참조하세요.

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

## <a name="create-and-deploy-the-custom-image"></a>사용자 지정 이미지 만들기 및 배포

함수 앱은 함수 실행을 호스팅합니다. [az functionapp create](/cli/azure/functionapp#az-functionapp-create) 명령을 사용하여 Docker 허브 이미지에서 함수 앱을 만듭니다.

다음 명령에서 `<app_name>` 자리 표시자 및 `<storage_name>`의 저장소 계정 이름을 고유한 함수 앱 이름으로 바꿉니다. `<app_name>`은 함수 앱의 기본 DNS 도메인으로 사용되므로 이름이 Azure의 모든 앱에서 고유해야 합니다. 이전과 마찬가지로 `<docker-id>`는 사용자의 Docker 계정 이름입니다.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myAppServicePlan --deployment-container-image-name <docker-id>/mydockerimage:v1.0.0
```

함수 앱을 만들었으면 Azure CLI는 다음 예와 비슷한 정보를 표시합니다.

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
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

_deployment-container-image-name_ 매개 변수는 Docker 허브에서 호스팅되는 이미지가 함수 앱을 만드는 데 사용됨을 나타냅니다. 배포에 사용되는 이미지에 대한 정보를 보려면 [az functionapp config container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) 명령을 사용합니다. 다른 이미지에서 배포하려면 [az functionapp config container set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) 명령을 사용합니다.

## <a name="configure-the-function-app"></a>함수 앱 구성

함수에는 기본 저장소 계정에 연결하기 위한 연결 문자열이 필요합니다. 사용자 지정 이미지를 프라이빗 컨테이너 계정에 게시하는 경우 [ENV 명령](https://docs.docker.com/engine/reference/builder/#env) 또는 비슷한 것을 사용하여 Dockerfile에서 이러한 애플리케이션 설정을 환경 변수로 대신 설정해야 합니다.

이 경우 `<storage_name>`는 만든 기본 저장소 계정의 이름입니다. [az storage account show-connection-string](/cli/azure/storage/account) 명령으로 연결 문자열을 가져옵니다. 함수 앱에서 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) 명령으로 이 응용 프로그램 설정을 추가합니다.

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <storage_name> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <app_name> \
--resource-group myResourceGroup \
--settings AzureWebJobsDashboard=$storageConnectionString \
AzureWebJobsStorage=$storageConnectionString
```

> [!NOTE]
> 컨테이너가 프라이빗인 경우 다음 애플리케이션 설정도 설정해야 합니다.  
> - DOCKER_REGISTRY_SERVER_USERNAME  
> - DOCKER_REGISTRY_SERVER_PASSWORD  
>
> 이러한 값을 선택하기 위해서는 함수 앱을 중지했다가 시작해야 합니다.

이제 Linux에서 실행되는 함수를 Azure에서 테스트할 수 있습니다.

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

## <a name="enable-application-insights"></a>Application Insights 사용

함수 실행을 모니터링할 때는 Azure Application Insights와 함수 앱을 통합하는 방식을 사용하는 것이 좋습니다. Azure Portal에서 함수 앱을 만들 때는 이 통합이 기본적으로 자동 수행됩니다. 그러나 Azure CLI를 사용하여 함수 앱을 만드는 경우 Azure에서 함수 앱 통합이 수행되지 않습니다.

함수 앱에 대해 Application Insights를 사용하도록 설정하려면 다음 단계를 수행합니다.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

자세히 알아보려면 [Azure Functions 모니터링](functions-monitoring.md)을 참조하세요.

## <a name="enable-continuous-deployment"></a>연속 배포 사용

컨테이너를 사용할 때의 한 가지 이점은 컨테이너를 레지스트리에서 업데이트할 때 업데이트를 자동으로 배포할 수 있다는 것입니다. [az functionapp deployment container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) 명령을 사용하여 연속 배포를 사용하도록 설정합니다.

```azurecli-interactive
az functionapp deployment container config --enable-cd \
--query CI_CD_URL --output tsv \
--name <app_name> --resource-group myResourceGroup
```

이 명령은 연속 배포를 사용하도록 설정한 후 배포 웹후크 URL을 반환합니다. [az functionapp deployment container show-cd-url](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url) 명령을 사용하여 이 URL을 반환할 수도 있습니다. 

배포 URL을 복사하고 DockerHub 리포지토리로 이동한 후 **웹후크** 탭을 선택하고 웹후크의 **웹후크 이름**을 입력하고 **웹후크 URL**에 URL을 붙여넣은 후 더하기 기호( **+** )를 선택합니다.

![DockerHub 리포지토리에서 웹후크 추가](media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

웹후크가 설정된 경우 DockerHub에서 연결된 이미지를 업데이트하면 Function App이 다운로드되고 최신 이미지가 설치됩니다.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 핵심 도구를 사용하여 함수 앱과 Dockerfile을 만듭니다.
> * Docker를 사용하여 사용자 지정 이미지 빌드
> * 컨테이너 레지스트리에 사용자 지정 이미지 게시
> * Azure Storage 계정 만들기
> * Linux App Service 계획 만들기
> * Docker 허브에서 함수 앱 배포
> * 함수 앱에 응용 프로그램 설정 추가

핵심 App Service 플랫폼에 기본 제공된 연속 통합 기능을 사용하는 방법을 알아봅니다. Docker 허브에서 이미지를 업데이트할 때 컨테이너가 다시 배포되도록 함수 앱을 구성할 수 있습니다.

> [!div class="nextstepaction"] 
> [Web App for Containers를 사용한 연속 배포](../app-service/containers/app-service-linux-ci-cd.md)
