---
title: Azure Web App for Containers에 Python 앱 배포
description: Web Apps for Containers에 Python 응용 프로그램을 실행하는 Docker 이미지를 배포하는 방법을 설명합니다.
keywords: azure app service, 웹앱, python, docker, 컨테이너
services: app-service
author: cephalin
manager: jeconnoc
ms.service: app-service
ms.devlang: python
ms.topic: quickstart
ms.date: 07/13/2018
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 6d328d8a3556f565e7eac8ee079bd191b7dcadef
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39433445"
---
# <a name="deploy-a-python-web-app-in-web-app-for-containers"></a>Web App for Containers에 Python 웹앱 배포

[Linux의 App Service](app-service-linux-intro.md)는 Linux 운영 체제를 기반으로 확장성이 높은 자체 패치 웹 호스팅 서비스를 제공합니다. 이 빠른 시작에서는 사용자 지정 Docker 허브 이미지를 사용하여 웹앱을 만들고 간단한 Flask 앱을 배포하는 방법을 설명합니다. [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)를 사용하여 웹앱을 만듭니다.

![Azure에서 실행되는 샘플 앱](media/quickstart-python/hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* <a href="https://git-scm.com/" target="_blank">Git 설치</a>
* <a href="https://www.docker.com/community-edition" target="_blank">Docker Community Edition 설치</a>
* <a href="https://hub.docker.com/" target="_blank">Docker 허브 계정 등록</a>

## <a name="download-the-sample"></a>샘플 다운로드

터미널 창에서 다음 명령을 실행하여, 샘플 응용 프로그램을 로컬 컴퓨터에 복제하고 샘플 코드가 들어 있는 디렉터리로 이동합니다.

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
cd python-docs-hello-world
```

이 리포지토리의 _/app_ 폴더는 간단한 Flask 응용 프로그램과 다음 세 가지를 지정하는 _Dockerfile_을 포함하고 있습니다.

- [tiangolo/uwsgi-nginx-flask:python3.6-alpine3.7](https://hub.docker.com/r/tiangolo/uwsgi-nginx-flask/) 기본 이미지를 사용합니다.
- 컨테이너는 포트 8000에서 수신 대기해야 합니다.
- `/app` 디렉터리를 컨테이너의 `/app` 디렉터리에 복사합니다.

구성은 [기본 이미지에 대한 지침](https://hub.docker.com/r/tiangolo/uwsgi-nginx-flask/)을 따릅니다.

## <a name="run-the-app-locally"></a>로컬에서 앱 실행하기

Docker 컨테이너에서 앱을 실행합니다.

```bash
docker build --rm -t flask-quickstart .
docker run --rm -it -p 8000:8000 flask-quickstart
```

웹 브라우저를 열고 `http://localhost:8000`의 샘플 앱으로 이동합니다.

이 페이지에 표시된 샘플 앱에서 **Hello,Azure!** 메시지를 볼 수 있습니다.

![로컬로 실행되는 샘플 앱](media/quickstart-python/localhost-hello-world-in-browser.png)

터미널 창에서 **Ctrl+C**를 눌러 컨테이너를 중지합니다.

## <a name="deploy-image-to-docker-hub"></a>Docker 허브에 이미지 배포하기

Docker 허브 계정에 로그인합니다. 프롬프트를 따라 Docker 허브 자격 증명을 입력합니다.

```bash
docker login
```

이미지 태그를 지정하고 새 _공개_ 리포지토리 Docker 허브 계정, `flask-quickstart` 리포지토리에 푸시합니다. *\<dockerhub_id>* 를 Docker 허브 ID로 바꿉니다.

```bash
docker tag flask-quickstart <dockerhub_id>/flask-quickstart
docker push <dockerhub_id>/flask-quickstart
```

> [!NOTE]
> `docker push`는 지정된 리포지토리를 찾을 수 없는 경우에 공용 리포지토리를 만듭니다. 이 빠른 시작에서는 Docker 허브에 공용 리포지토리를 사용하는 것으로 가정합니다. 개인 리포지토리에 푸시하려면 나중에 Azure App Service에서 Docker 허브 자격 증명을 구성해야 합니다. [웹앱 만들기](#create-a-web-app)를 참조하세요.

이미지 푸시가 완료되면 Azure 웹앱에서 이미지를 사용할 수 있습니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>웹앱 만들기

[az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) 명령을 사용하여 `myAppServicePlan` App Service 계획에 [웹앱](../app-service-web-overview.md)을 만듭니다. *\<app name>* 을 전역적으로 고유한 앱 이름으로 바꾸고, *\<dockerhub_id>* 를 Docker 허브 ID로 바꿉니다.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name <dockerhub_id>/flask-quickstart
```

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

앞에서 개인 리포지토리에 업로드한 경우, App Service에서 Docker 허브 자격 증명도 구성해야 합니다. 자세한 내용은 [Docker 허브에서 개인 이미지 사용](tutorial-custom-docker-image.md#use-a-private-image-from-docker-hub-optional)을 참조하세요.

### <a name="specify-container-port"></a>컨테이너 포트 지정

_Dockerfile_에 지정된 대로, 컨테이너는 포트 8000에서 수신 대기합니다. App Service가 요청을 올바른 포트에 라우팅하도록 *WEBSITES_PORT* 앱 설정을 지정해야 합니다.

Cloud Shell에서 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) 명령을 실행합니다.


```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings WEBSITES_PORT=8000
```

## <a name="browse-to-the-app"></a>앱으로 이동

```bash
http://<app_name>.azurewebsites.net/
```

![Azure에서 실행되는 샘플 앱](media/quickstart-python/hello-world-in-browser.png)

> [!NOTE]
> 앱이 처음으로 요청될 때 Docker 허브 이미지를 다운로드하여 실행해야 하므로 웹앱이 시작될 때까지 조금 시간이 걸립니다. 첫 실행에서 시간이 한참 지난 후 오류가 표시되면 페이지를 새로 고칩니다.

**축하합니다.** Python 앱을 실행하는 사용자 지정 Docker 이미지가 Web Apps for Containers에 배포되었습니다.

## <a name="update-locally-and-redeploy"></a>로컬로 업데이트 및 다시 배포

로컬 텍스트 편집기를 사용하여 Python 앱에서 `app/main.py` 파일을 열고 `return` 문 옆의 텍스트를 약간 변경합니다.

```python
return 'Hello, Azure!'
```

이미지를 다시 빌드하여 Docker 허브에 다시 푸시합니다.

```bash
docker build --rm -t flask-quickstart .
docker tag flask-quickstart <dockerhub_id>/flask-quickstart
docker push <dockerhub_id>/flask-quickstart
```

Cloud Shell에서 앱을 다시 시작합니다. 앱을 다시 시작하면 모든 설정이 적용되고 레지스트리에서 최신 컨테이너를 가져옵니다.

```azurecli-interactive
az webapp restart --resource-group myResourceGroup --name <app_name>
```

App Service가 업데이트된 이미지를 끌어올 때까지 약 15초 정도 기다립니다. **앱으로 이동** 단계에서 연 브라우저 창으로 다시 전환하고 페이지를 새로 고칩니다.

![Azure에서 실행되는 업데이트된 샘플 앱](media/quickstart-python/hello-azure-in-browser.png)

## <a name="manage-your-azure-web-app"></a>Azure Web App 관리

[Azure Portal](https://portal.azure.com)로 이동하여 만든 웹앱을 확인합니다.

왼쪽 메뉴에서 **App Services**를 클릭한 다음 Azure 웹앱의 이름을 클릭합니다.

![Azure 웹앱에 대한 포털 탐색](./media/quickstart-python/app-service-list.png)

기본적으로 포털에는 웹앱의 **개요** 페이지가 표시됩니다. 이 페이지에서는 앱이 어떻게 작동하고 있는지를 보여 줍니다. 여기에서 찾아보기, 중지, 시작, 다시 시작, 삭제와 같은 기본 관리 작업을 수행할 수 있습니다. 페이지의 왼쪽에 있는 탭에서는 열 수 있는 여러 구성 페이지를 보여 줍니다.

![Azure Portal의 App Service 페이지](./media/quickstart-python/app-service-detail.png)

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [PostgreSQL을 사용하는 Python](tutorial-docker-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [사용자 지정 이미지 사용](tutorial-custom-docker-image.md)
