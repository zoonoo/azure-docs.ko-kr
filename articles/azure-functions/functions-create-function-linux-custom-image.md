---
title: 사용자 지정 이미지를 사용하여 Linux에서 Azure Functions 만들기
description: 사용자 지정 Linux 이미지에서 실행되는 Azure Functions를 만드는 방법을 알아봅니다.
ms.date: 09/27/2019
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: e70edac09c8b2d61c148c9ba0fd04ec231e9a965
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769322"
---
# <a name="create-a-function-on-linux-using-a-custom-image"></a>사용자 지정 이미지를 사용하여 Linux에서 함수 만들기

Azure Functions를 사용하면 사용자 지정 컨테이너에서 Linux의 함수를 호스팅할 수 있습니다. [기본 Azure App Service 컨테이너에 호스팅](functions-create-first-azure-function-azure-cli-linux.md)할 수도 있습니다. 이 기능을 사용하려면 [Functions 2.x 런타임](functions-versions.md)이 필요합니다.

이 자습서에서는 함수를 사용자 지정 Docker 이미지로 Azure에 배포하는 방법에 대해 알아봅니다. 이 패턴은 기본 제공 컨테이너 이미지를 사용자 지정해야 하는 경우에 유용합니다. 함수에 특정 언어 버전이 필요하거나 기본 제공 이미지에서 제공되지 않는 특정 종속성 또는 구성이 필요한 경우 사용자 지정 이미지를 사용할 수 있습니다. Azure Functions에 대해 지원되는 기본 이미지는 [Azure Functions 기본 이미지 리포지토리](https://hub.docker.com/_/microsoft-azure-functions-base)에 나와 있습니다. 

이 자습서에서는 Azure Functions 핵심 도구를 사용하여 사용자 지정 Linux 이미지에서 함수를 만드는 방법을 안내합니다. Azure CLI를 사용하여, 만든 이미지를 Azure에서 함수 앱에 게시합니다. 나중에 Azure Queue storage에 연결하도록 함수를 업데이트합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 핵심 도구를 사용하여 함수 앱과 Dockerfile을 만듭니다.
> * Docker를 사용하여 사용자 지정 이미지 빌드
> * 컨테이너 레지스트리에 사용자 지정 이미지 게시
> * Azure Storage 계정 만들기
> * 프리미엄 호스팅 계획 만들기
> * Docker 허브에서 함수 앱 배포
> * 함수 앱에 애플리케이션 설정 추가
> * 지속적인 배포 사용
> * 컨테이너에 SSH 연결 사용
> * Queue Storage 출력 바인딩 추가 

다음 단계는 Mac, Windows 또는 Linux 컴퓨터에서 지원됩니다. 

## <a name="prerequisites"></a>사전 요구 사항

이 샘플을 실행하기 전에 다음 사항이 준비되어야 합니다.

* [Azure Core Tools 버전 2.x](functions-run-local.md#v2)를 설치합니다.

* [Azure CLI]( /cli/azure/install-azure-cli)를 설치합니다. 이 문서에서 설명하는 단계를 수행하려면 Azure CLI 버전 2.0 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다.  
[Azure Cloud Shell](https://shell.azure.com/bash)을 사용할 수도 있습니다.

* 활성화된 Azure 구독.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-cloud-shell-note](../../includes/functions-cloud-shell-note.md)]

## <a name="create-the-local-project"></a>로컬 프로젝트 만들기

명령줄에서 다음 명령을 실행하여 현재 로컬 디렉터리의 `MyFunctionProj` 폴더에 함수 앱 프로젝트를 만듭니다. Python 프로젝트의 경우 [가상 환경에서 실행 중이어야 합니다](functions-create-first-function-python.md#create-and-activate-a-virtual-environment).

```bash
func init MyFunctionProj --docker
```

`--docker` 옵션을 포함할 때는 프로젝트에 대해 dockerfile이 생성됩니다. 이 파일은 프로젝트를 실행하는 사용자 지정 컨테이너를 만드는 데 사용됩니다. 사용하는 기본 이미지는 작업자 런타임 언어 선택에 따라 다릅니다.  

메시지가 표시되면 다음 언어 중에서 작업자 런타임을 선택합니다.

* `dotnet`: .NET Core 클래스 라이브러리 프로젝트(.csproj)를 만듭니다.
* `node`: JavaScript 프로젝트를 만듭니다.
* `python`: Python 프로젝트를 만듭니다.  

다음 명령을 사용하여 새 `MyFunctionProj` 프로젝트 폴더로 이동합니다.

```bash
cd MyFunctionProj
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

## <a name="build-from-the-docker-file"></a>Docker 파일에서 빌드

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

명령이 완료되면 새 컨테이너를 로컬에서 실행할 수 있습니다.

### <a name="run-the-image-locally"></a>로컬에서 이미지 실행
로컬 컨테이너에서 Docker 이미지를 실행하여 빌드된 이미지가 작동하는지 확인합니다. [docker run](https://docs.docker.com/engine/reference/commandline/run/) 명령을 실행하고 이미지의 이름 및 태그를 전달합니다. `-p` 인수를 사용하여 포트를 지정해야 합니다.

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

로컬 Docker 컨테이너에서 실행되는 사용자 지정 이미지를 사용하여 <http://localhost:8080>으로 이동하여 함수 앱 및 컨테이너가 제대로 작동하는지 확인합니다.

![함수 앱을 로컬로 실행합니다.](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

> [!NOTE]
> 이 시점에서 특정 HTTP 함수를 호출하려고 하면 HTTP 401 오류 응답이 나타납니다. 함수가 Azure에서와 동일하게 로컬 컨테이너에서 실행되어, 함수 키가 필요하기 때문입니다. 컨테이너가 함수 앱에 아직 게시되지 않았으므로 사용 가능한 함수 키가 없습니다. 나중에 핵심 도구를 사용하여 컨테이너를 게시할 때 함수 키가 표시됩니다. 로컬 컨테이너에서 실행 중인 함수를 테스트하려는 경우 [인증 키](functions-bindings-http-webhook.md#authorization-keys)를 `anonymous`로 변경할 수 있습니다. 

컨테이너에서 함수 앱을 확인한 후 실행을 중지합니다. 이제 사용자 이미지를 Docker 허브 계정으로 푸시할 수 있습니다.

## <a name="push-to-docker-hub"></a>Docker Hub로 푸시

레지스트리는 이미지를 호스팅하고 이미지 및 컨테이너 서비스를 제공하는 애플리케이션입니다. 이미지를 공유하려면 레지스트리에 푸시해야 합니다. Docker 허브는 Docker 이미지의 레지스트리이며 고유한 공개 또는 프라이빗 리포지토리를 호스팅할 수 있습니다.

이미지를 푸시하려면 먼저 [docker login](https://docs.docker.com/engine/reference/commandline/login/) 명령을 사용하여 Docker 허브에 로그인해야 합니다. `<docker-id>`를 사용자의 계정 이름으로 바꾸고, 콘솔의 프롬프트에서 암호를 입력합니다. 다른 Docker 허브 암호 옵션은 [docker login 명령 설명서](https://docs.docker.com/engine/reference/commandline/login/)를 참조하세요.

```bash
docker login --username <docker-id>
```

“로그인했습니다.”라는 메시지를 통해 로그인 상태를 확인할 수 있습니다. 로그인했으면 [docker push](https://docs.docker.com/engine/reference/commandline/push/) 명령을 사용하여 이미지를 Docker 허브에 푸시합니다.

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

푸시가 성공하면 이미지를 Azure에서 새 함수 앱에 대한 배포 원본으로 사용할 수 있습니다.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-premium-plan"></a>프리미엄 플랜 만들기

사용자 지정 Functions 컨테이너에 대한 Linux 호스팅은 [전용(App Service) 플랜](functions-scale.md#app-service-plan) 및 [프리미엄 플랜](functions-premium-plan.md#features)에서 지원됩니다. 이 자습서에서는 필요에 따라 크기를 조정할 수 있는 프리미엄 플랜을 사용합니다. 호스팅에 대한 자세한 내용은 [Azure Functions 호스팅 계획 비교](functions-scale.md)를 참조하세요.

다음 예에서는 **탄력적 프리미엄 1** 가격 책정 계층(`--sku EP1`), 미국 서부 지역(`-location WestUS`) 및 Linux 컨테이너(`--is-linux`)에 `myPremiumPlan`이라는 프리미엄 플랜을 만듭니다.

```azurecli-interactive
az functionapp plan create --resource-group myResourceGroup --name myPremiumPlan \
--location WestUS --number-of-workers 1 --sku EP1 --is-linux
```

## <a name="create-an-app-from-the-image"></a>이미지에서 앱 만들기

함수 앱은 호스팅 계획에서 함수 실행을 관리합니다. [az functionapp create](/cli/azure/functionapp#az-functionapp-create) 명령을 사용하여 Docker 허브 이미지에서 함수 앱을 만듭니다.

다음 명령에서 `<app_name>` 자리 표시자 및 `<storage_name>`의 스토리지 계정 이름을 고유한 함수 앱 이름으로 바꿉니다. `<app_name>`은 함수 앱의 기본 DNS 도메인으로 사용되므로 이름이 Azure의 모든 앱에서 고유해야 합니다. 이전과 마찬가지로 `<docker-id>`는 사용자의 Docker 계정 이름입니다.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myPremiumPlan --deployment-container-image-name <docker-id>/mydockerimage:v1.0.0
```

_deployment-container-image-name_ 매개 변수는 Docker 허브에서 호스팅되는 이미지가 함수 앱을 만드는 데 사용됨을 나타냅니다. 배포에 사용되는 이미지에 대한 정보를 보려면 [az functionapp config container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) 명령을 사용합니다. 다른 이미지에서 배포하려면 [az functionapp config container set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) 명령을 사용합니다.

## <a name="configure-the-function-app"></a>함수 앱 구성

함수에는 기본 스토리지 계정에 연결하기 위한 연결 문자열이 필요합니다. 사용자 지정 이미지를 프라이빗 컨테이너 계정에 게시하는 경우 [ENV 명령](https://docs.docker.com/engine/reference/builder/#env)이나 유사한 명령을 사용하여 Dockerfile에서 이 애플리케이션 설정을 환경 변수로 대신 설정해야 합니다.

이 경우 `<storage_name>`는 만든 기본 스토리지 계정의 이름입니다. [az storage account show-connection-string](/cli/azure/storage/account) 명령으로 연결 문자열을 가져옵니다. 함수 앱에서 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) 명령으로 이 애플리케이션 설정을 추가합니다.

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <storage_name> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <app_name> \
--resource-group myResourceGroup \
--settings AzureWebJobsStorage=$storageConnectionString
```

> [!NOTE]
> 컨테이너가 프라이빗인 경우 다음 애플리케이션 설정도 설정해야 합니다.  
> - DOCKER_REGISTRY_SERVER_USERNAME  
> - DOCKER_REGISTRY_SERVER_PASSWORD  
>
> 이러한 값을 선택하기 위해서는 함수 앱을 중지했다가 시작해야 합니다.

## <a name="verify-your-functions"></a>함수 확인

<!-- we should replace this with a CLI or API-based approach, when we get something better than REST -->

만든 HTTP 트리거 함수는 엔드포인트를 호출할 때 [함수 키](functions-bindings-http-webhook.md#authorization-keys)가 필요합니다. 현재, 키를 포함하여 함수 URL을 가져오는 가장 쉬운 방법은 [Azure Portal]에서 가져오는 것입니다. 

> [!TIP]
> [키 관리 API](https://github.com/Azure/azure-functions-host/wiki/Key-management-API)를 사용하여 함수 키를 구할 수도 있으며, 이 경우 [인증을 위한 전달자 토큰](/cli/azure/account#az-account-get-access-token)을 제시해야 합니다.

페이지 맨 위에 있는 **검색** 상자에 함수 앱 이름을 입력하고 **App Service** 리소스를 선택하여 [Azure Portal]에서 새 함수 앱을 찾습니다.

**MyHttpTrigger** 함수를 선택하고 **</> 함수 URL 가져오기** > **기본값(함수 키)**  > **복사**를 선택합니다.

![Azure Portal에서 함수 URL 복사](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key.png)

이 URL에서 함수 키는 `code` 쿼리 매개 변수입니다. 

> [!NOTE]  
> 함수 앱이 컨테이너로 배포되기 때문에 포털에서 함수 코드를 변경할 수 없습니다. 대신 로컬 컨테이너에서 프로젝트를 업데이트하고 Azure에 다시 게시해야 합니다.

함수 URL을 브라우저의 주소 표시줄에 붙여 넣습니다. `&name=<yourname>` 쿼리 문자열을 이 URL의 마지막에 추가하고 키보드에서 `Enter` 키를 눌러 요청을 실행합니다. 브라우저에 함수에서 반환한 응답이 표시될 것입니다.

다음 예에서는 브라우저의 응답을 보여 줍니다.

![브라우저에 함수 응답.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

요청 URL에는 기본적으로 HTTP를 통해 함수에 액세스하는 데 필요한 키가 포함됩니다. 

## <a name="enable-continuous-deployment"></a>연속 배포 사용

컨테이너를 사용하는 이점 중 하나는 지속적인 배포에 대한 지원입니다. Functions를 사용하면 컨테이너가 레지스트리에서 업데이트될 때 업데이트를 자동으로 배포할 수 있습니다. [az functionapp deployment container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) 명령을 사용하여 연속 배포를 사용하도록 설정합니다.

```azurecli-interactive
az functionapp deployment container config --enable-cd \
--query CI_CD_URL --output tsv \
--name <app_name> --resource-group myResourceGroup
```

이 명령은 연속 배포를 사용하도록 설정한 후 배포 웹후크 URL을 반환합니다. [az functionapp deployment container show-cd-url](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url) 명령을 사용하여 이 URL을 반환할 수도 있습니다. 

배포 URL을 복사하고 DockerHub 리포지토리로 이동한 후 **웹후크** 탭을 선택하고 웹후크의 **웹후크 이름**을 입력하고 **웹후크 URL**에 URL을 붙여넣은 후 더하기 기호( **+** )를 선택합니다.

![DockerHub 리포지토리에서 웹후크 추가](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

웹후크가 설정된 경우 DockerHub에서 연결된 이미지를 업데이트하면 함수 앱이 다운로드되고 최신 이미지가 설치됩니다.

## <a name="enable-ssh-connections"></a>SSH 연결 사용

SSH를 사용하면 컨테이너와 클라이언트 간의 보안 통신을 설정할 수 있습니다. SSH를 사용하면 App Service 고급 도구(Kudu)를 사용하여 컨테이너에 연결할 수 있습니다. SSH를 사용하여 컨테이너에 쉽게 연결할 수 있도록, Functions는 SSH를 사용하도록 이미 설정된 기본 이미지를 제공합니다. 

### <a name="change-the-base-image"></a>기본 이미지 변경

dockerfile에서 `FROM` 명령의 기본 이미지에 문자열 `-appservice`를 추가합니다. JavaScript 프로젝트의 경우 다음과 같은 모양입니다.

```docker
FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
```

두 기본 이미지의 차이점으로 인해 컨테이너에 SSH 연결이 가능합니다. 이러한 차이점은 [이 App Services 자습서](../app-service/containers/tutorial-custom-docker-image.md#enable-ssh-connections)에 자세히 설명되어 있습니다.

### <a name="rebuild-and-redeploy-the-image"></a>이미지 다시 빌드 및 다시 배포

루트 폴더에서 [docker build](https://docs.docker.com/engine/reference/commandline/build/) 명령을 다시 실행하고 이전처럼 `<docker-id>`를 Docker Hub 계정 ID로 바꿉니다. 

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

업데이트된 이미지를 Docker Hub로 다시 푸시합니다.

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

업데이트된 이미지가 함수 앱에 다시 배포됩니다.

### <a name="connect-to-your-container-in-azure"></a>Azure에서 컨테이너에 연결

브라우저에서 함수 앱 컨테이너의 다음 고급 도구(Kudu) `scm.` 엔드포인트로 이동하여 `<app_name>`을 함수 앱의 이름으로 바꿉니다.

```
https://<app_name>.scm.azurewebsites.net/
```

Azure 계정에 로그인 한 다음, **SSH** 탭을 선택하여 컨테이너에 SSH 연결을 만듭니다.

연결이 설정되면 `top` 명령을 실행하여 현재 실행 중인 프로세스를 확인합니다. 

![SSH 세션에서 실행되는 Linux top 명령](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-queue-storage"></a>Queue Storage에 쓰기

Functions를 사용하면 자체 통합 코드를 작성하지 않고도 Azure 서비스 및 기타 리소스를 함수에 연결할 수 있습니다. 입력과 출력을 모두 나타내는 이러한 *바인딩*은 함수 정의 내에서 선언됩니다. 바인딩의 데이터는 함수에 매개 변수로 제공됩니다. *트리거*는 특수한 형식의 입력 바인딩입니다. 함수에는 하나의 트리거만 있지만, 여러 개의 입력 및 출력 바인딩이 있을 수 있습니다. 자세한 내용은 [Azure Functions 트리거 및 바인딩 개념](functions-triggers-bindings.md)을 참조하세요.

이 섹션에서는 함수를 Azure Storage 큐와 통합하는 방법을 보여줍니다. 이 함수에 추가하는 출력 바인딩은 HTTP 요청의 데이터를 큐의 메시지에 씁니다.

### <a name="download-the-function-app-settings"></a>함수 앱 설정 다운로드

[!INCLUDE [functions-app-settings-download-local-cli](../../includes/functions-app-settings-download-local-cli.md)]

### <a name="enable-extension-bundles"></a>확장 번들 사용

Queue storage 출력 바인딩을 사용하므로 프로젝트를 실행하기 전에 스토리지 바인딩 확장을 설치해야 합니다. 


# <a name="javascript--pythontabnodejspython"></a>[JavaScript/Python](#tab/nodejs+python)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

HTTP 및 타이머 트리거를 제외하고 바인딩은 확장 패키지로 구현됩니다. 터미널 창에서 다음 [dotnet add package](/dotnet/core/tools/dotnet-add-package) 명령을 실행하여 프로젝트에 스토리지 확장 패키지를 추가합니다.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

> [!TIP]
> Visual Studio를 사용하는 경우, NuGet 패키지 관리자를 사용하여 이 패키지를 추가할 수도 있습니다.

---

이제 프로젝트에 Storage 출력 바인딩을 추가할 수 있습니다.

### <a name="add-an-output-binding"></a>출력 바인딩 추가

Functions에서 각 바인딩 형식의 `direction`, `type` 및 고유한 `name`을 function.json 파일에 정의해야 합니다. 이러한 특성을 정의하는 방법은 함수 앱의 언어에 따라 달라집니다.

# <a name="javascript--pythontabnodejspython"></a>[JavaScript/Python](#tab/nodejs+python)

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

---

### <a name="add-code-that-uses-the-output-binding"></a>출력 바인딩을 사용하는 코드 추가

바인딩이 정의되면 바인딩의 `name`을 사용하여 함수 시그니처의 특성으로 액세스할 수 있습니다. 출력 바인딩을 사용하면 인증을 받거나 큐 참조를 가져오거나 데이터를 쓸 때 Azure Storage SDK 코드를 사용할 필요가 없습니다. Functions 런타임 및 큐 출력 바인딩이 이러한 작업을 알아서 처리합니다.

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]

# <a name="pythontabpython"></a>[Python](#tab/python)

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

---

### <a name="update-the-hosted-container"></a>호스트된 컨테이너 업데이트

루트 폴더에서 [docker build](https://docs.docker.com/engine/reference/commandline/build/) 명령을 다시 실행하고 이번에는 태그의 버전을 `v1.0.2`로 업데이트합니다. 이전처럼 `<docker-id>`를 Docker Hub 계정 ID로 바꿉니다. 

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.2
```

업데이트된 이미지를 리포지토리로 다시 푸시합니다.

```bash
docker push <docker-id>/mydockerimage:v1.0.2
```

### <a name="verify-the-updates-in-azure"></a>Azure에서 업데이트 확인

브라우저에서 이전과 동일한 URL을 사용하여 함수를 트리거합니다. 동일한 응답이 표시됩니다. 단, 이번에는 `name` 매개 변수로 전달한 문자열이 `outqueue` 스토리지 큐에 기록됩니다.

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>다음 단계

Azure의 함수 앱에 사용자 지정 컨테이너를 성공적으로 배포했으면, 다음 항목에 대해 자세히 읽어보세요.

+ [함수 모니터링](functions-monitoring.md)
+ [비율 크기 조정 및 호스팅 옵션](functions-scale.md)
+ [Kubernetes 기반 서버리스 호스팅](functions-kubernetes-keda.md)

[Azure Portal]: https://portal.azure.com
