---
title: Web App for Containers에 대한 사용자 지정 이미지 빌드 - Azure App Service | Microsoft Docs
description: Web App for Containers에 사용자 지정 Docker 이미지를 사용하는 방법.
keywords: azure app service, 웹앱, linux, docker, 컨테이너
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 8463ffcb9d9983ff435c01f75dd48f68bde31767
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59545605"
---
# <a name="tutorial-build-a-custom-image-and-run-in-app-service-from-a-private-registry"></a>자습서: 프라이빗 레지스트리의 App Service에서 사용자 지정 이미지 빌드 및 실행

[App Service](app-service-linux-intro.md)는 Linux에서 PHP 7.0 및 Node.js 4.5와 같은 특정 버전을 지원하는 기본 제공 Docker 이미지를 제공합니다. App Service는 Docker 컨테이너 기술을 사용하여 기본 제공 이미지와 사용자 지정 이미지를 모두 PaaS(Platform as a Service)로 호스팅합니다. 이 자습서에서는 App Service에서 사용자 지정 이미지를 빌드하고 실행하는 방법을 알아봅니다. 이 패턴은 기본 제공 이미지에 선택한 언어가 포함되어 있지 않거나 기본 제공 이미지에 제공되지 않는 특정 구성이 애플리케이션에 필요한 경우에 유용합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 프라이빗 컨테이너 레지스트리에 사용자 지정 이미지 배포
> * App Service에서 사용자 지정 이미지 실행
> * 환경 변수 구성
> * 이미지 업데이트 및 다시 배포
> * 진단 로그 액세스
> * SSH를 사용하여 컨테이너에 연결

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* [Git](https://git-scm.com/downloads)
* [Docker](https://docs.docker.com/get-started/#setup)

## <a name="download-the-sample"></a>샘플 다운로드

터미널 창에서 다음 명령을 실행하여 로컬 컴퓨터에 샘플 앱 리포지토리를 복제한 후 샘플 코드를 포함하는 디렉터리로 변경합니다.

```bash
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
cd docker-django-webapp-linux
```

## <a name="build-the-image-from-the-docker-file"></a>Docker 파일에서 이미지 빌드

Git 리포지토리에서 _Dockerfile_을 살펴봅니다. 이 Docker 파일은 애플리케이션을 실행하는 데 필요한 Python 환경을 설명합니다. 또한 이미지는 컨테이너와 호스트 간에 보안 통신을 위해 [SSH](https://www.ssh.com/ssh/protocol/) 서버를 설정합니다.

```Dockerfile
FROM python:3.4

RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt
ADD . /code/

# ssh
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "$SSH_PASSWD" | chpasswd 

COPY sshd_config /etc/ssh/
COPY init.sh /usr/local/bin/
    
RUN chmod u+x /usr/local/bin/init.sh
EXPOSE 8000 2222
#CMD ["python", "/code/manage.py", "runserver", "0.0.0.0:8000"]
ENTRYPOINT ["init.sh"]
```

`docker build` 명령을 사용하여 Docker 이미지를 빌드합니다.

```bash
docker build --tag mydockerimage .
```

Docker 컨테이너를 실행하여 빌드가 작동하는지를 테스트합니다. [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) 명령을 실행하고 이미지의 이름 및 태그를 전달합니다. `-p` 인수를 사용하여 포트를 지정해야 합니다.

```bash
docker run -p 8000:8000 mydockerimage
```

`http://localhost:8000`로 이동하여 웹앱과 컨테이너가 제대로 작동하는지 확인합니다.

![로컬로 웹앱 테스트](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>Azure에 앱 배포

방금 만든 이미지를 사용하는 앱을 만들려면 Azure CLI 명령을 실행하여 리소스 그룹을 만들고, 이미지를 푸시한 다음, 이를 실행할 App Service 계획 웹앱을 만듭니다.

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-an-azure-container-registry"></a>Azure Container Registry 만들기

Cloud Shell에서 [`az acr create`](/cli/azure/acr?view=azure-cli-latest#az-acr-create) 명령을 사용하여 Azure Container Registry를 만듭니다.

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

### <a name="sign-in-to-azure-container-registry"></a>Azure Container Registry에 로그인

이미지를 레지스트리로 푸시하려면 프라이빗 레지스트리를 사용하여 인증해야 합니다. Cloud Shell에서 [`az acr show`](/cli/azure/acr?view=azure-cli-latest#az-acr-show) 명령을 사용하여 만든 레지스트리에서 자격 증명을 검색합니다.

```azurecli-interactive
az acr credential show --name <azure-container-registry-name>
```

출력에는 사용자 이름과 함께 두 개의 암호가 표시됩니다.

```json
<
  "passwords": [
    {
      "name": "password",
      "value": "{password}"
    },
    {
      "name": "password2",
      "value": "{password}"
    }
  ],
  "username": "<registry-username>"
}
```

로컬 터미널 창에서 다음 예제와 같이 `docker login` 명령을 사용하여 Azure Container Registry에 로그인합니다. *\<azure-container-registry-name>* 및 *\<registry-username>* 을 레지스트리 값으로 바꿉니다. 메시지가 표시되면 이전 단계의 암호 중 하나를 입력합니다.

```bash
docker login <azure-container-registry-name>.azurecr.io --username <registry-username>
```

로그인이 성공했는지 확인합니다.

### <a name="push-image-to-azure-container-registry"></a>Azure Container Registry에 이미지 푸시하기

Azure Container Registry용 로컬 이미지에 대한 태그를 지정합니다. 예:
```bash
docker tag mydockerimage <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

`docker push` 명령을 사용하여 이미지를 푸시합니다. 레지스트리의 이름 뒤에 이미지 이름과 태그를 붙여 이미지에 대한 태그를 지정합니다.

```bash
docker push <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Cloud Shell로 돌아가서 푸시가 성공했는지 확인합니다.

```azurecli-interactive
az acr repository list -n <azure-container-registry-name>
```

다음과 같은 출력이 표시됩니다.

```json
[
  "mydockerimage"
]
```

### <a name="create-app-service-plan"></a>App Service 플랜 만들기

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-web-app"></a>웹앱 만들기

Cloud Shell에서 [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) 명령을 사용하여 `myAppServicePlan` App Service 계획에 [웹앱](app-service-linux-intro.md)을 만듭니다. _\<app-name>_ 을 고유한 앱 이름으로, _\<azure-container-registry-name>_ 을 레지스트리 이름으로 바꿉니다.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --deployment-container-image-name <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
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
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="configure-registry-credentials-in-web-app"></a>웹앱에서 레지스트리 자격 증명 구성

App Service에서 프라이빗 이미지를 끌어오려면 레지스트리와 이미지에 대한 정보가 필요합니다. Cloud Shell에서 [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) 명령을 제공합니다. *\<app-name>* , *\<azure-container-registry-name>* , _\<registry-username>_ 및 _\<password>_ 를 바꿉니다.

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group myResourceGroup --docker-custom-image-name <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0 --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <registry-username> --docker-registry-server-password <password>
```

> [!NOTE]
> Docker 허브 이외의 레지스트리를 사용하는 경우 `--docker-registry-server-url`은 `https://` 뒤에 레지스트리의 정규화된 도메인 이름이 나오는 형식으로 지정해야 합니다.
>

### <a name="configure-environment-variables"></a>환경 변수 구성

대부분의 Docker 이미지는 80 이외의 포트와 같은 사용자 지정 환경 변수를 사용합니다. `WEBSITES_PORT` 앱 설정을 사용하여 이미지에서 사용하는 포트를 App Service에 알려줍니다. [이 자습서의 Python 샘플](https://github.com/Azure-Samples/docker-django-webapp-linux)에 대한 GitHub 페이지에서 `WEBSITES_PORT`를 _8000_으로 설정해야 함을 보여 줍니다.

앱 설정을 지정하려면 Cloud Shell에서 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) 명령을 사용합니다. 앱 설정은 대/소문자를 구분하고 공백으로 구분합니다.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WEBSITES_PORT=8000
```

### <a name="test-the-web-app"></a>웹앱 테스트

웹앱(`http://<app-name>.azurewebsites.net`)을 찾아서 작동하는지 확인합니다.

> [!NOTE]
> 앱에 처음 액세스하는 경우 App Service에서 전체 이미지를 가져와야 하므로 시간이 약간 걸릴 수 있습니다. 브라우저에서 시간이 초과되면 페이지를 새로 고칩니다.

![웹앱 포트 구성 테스트](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="change-web-app-and-redeploy"></a>웹앱 변경 및 다시 배포

로컬 Git 리포지토리에서 app/templates/app/index.html을 엽니다. 첫 번째 HTML 요소를 찾아서 변경합니다.

```python
<nav class="navbar navbar-inverse navbar-fixed-top">
    <div class="container">
      <div class="navbar-header">
        <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>
      </div>
    </div>
  </nav>
```

Python 파일을 수정하고 저장하면 새 Docker 이미지를 다시 빌드하고 푸시해야 합니다. 그런 다음 변경 내용을 적용하려면 웹앱을 다시 시작합니다. 이 자습서에서는 이전에 사용한 동일한 명령을 사용합니다. [Docker 파일에서 이미지 빌드](#build-the-image-from-the-docker-file) 및 [Azure Container Registry에 이미지 푸시](#push-image-to-azure-container-registry) 섹션을 참조할 수 있습니다. [웹앱 테스트](#test-the-web-app)의 지침에 따라 웹앱을 테스트합니다.

## <a name="access-diagnostic-logs"></a>진단 로그 액세스

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="enable-ssh-connections"></a>SSH 연결 사용

SSH를 사용하면 컨테이너와 클라이언트 간의 보안 통신을 설정할 수 있습니다. 컨테이너에 대한 SSH 연결을 사용하도록 설정하려면 해당 컨테이너에 대한 사용자 지정 이미지를 구성해야 합니다. 필요한 구성을 이미 갖추고 있는 리포지토리 샘플을 살펴보겠습니다.

* [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile)에서 다음 코드는 SSH 서버를 설치하고 로그인 자격 증명도 설정합니다.

    ```Dockerfile
    ENV SSH_PASSWD "root:Docker!"
    RUN apt-get update \
            && apt-get install -y --no-install-recommends dialog \
            && apt-get update \
      && apt-get install -y --no-install-recommends openssh-server \
      && echo "$SSH_PASSWD" | chpasswd 
    ```

    > [!NOTE]
    > 이 구성을 사용하면 컨테이너에 대한 외부 연결이 허용되지 않습니다. Kudu/SCM 사이트를 통해서만 SSH를 사용할 수 있습니다. Kudu/SCM 사이트는 Azure 계정으로 인증됩니다.

* [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L18)에서 [sshd_config](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/sshd_config file in the repository)를 */etc/ssh/* 디렉터리에 복사합니다.

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

* [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L22)에서 2222 포트를 컨테이너에 공개합니다. 프라이빗 가상 네트워크의 브리지 네트워크 내에 있는 컨테이너에서 액세스할 수 있는 내부 전용 포트입니다. 

    ```Dockerfile
    EXPOSE 8000 2222
    ```

* [진입 스크립트](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/init.sh#L5)에서 SSH 서버를 시작합니다.

      ```bash
      #!/bin/bash
      service ssh start
    ```

### Open SSH connection to container

SSH connection is available only through the Kudu site, which is accessible at `https://<app-name>.scm.azurewebsites.net`.

To connect, browse to `https://<app-name>.scm.azurewebsites.net/webssh/host` and sign in with your Azure account.

You are then redirected to a page displaying an interactive console.

You may wish to verify that certain applications are running in the container. To inspect the container and verify running processes, issue the `top` command at the prompt.

```bash
top
```

`top` 명령은 컨테이너에서 실행 중인 모든 프로세스를 노출합니다.

```
PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
 1 root      20   0  945616  35372  15348 S  0.0  2.1   0:04.63 node
20 root      20   0   55180   2776   2516 S  0.0  0.2   0:00.00 sshd
42 root      20   0  944596  33340  15352 S  0.0  1.9   0:05.80 node /opt/s+
56 root      20   0   59812   5244   4512 S  0.0  0.3   0:00.93 sshd
58 root      20   0   20228   3128   2664 S  0.0  0.2   0:00.00 bash
62 root      20   0   21916   2272   1944 S  0.0  0.1   0:03.15 top
63 root      20   0   59812   5344   4612 S  0.0  0.3   0:00.03 sshd
65 root      20   0   20228   3140   2672 S  0.0  0.2   0:00.00 bash
71 root      20   0   59812   5380   4648 S  0.0  0.3   0:00.02 sshd
73 root      20   0   20228   3160   2696 S  0.0  0.2   0:00.00 bash
77 root      20   0   21920   2304   1972 R  0.0  0.1   0:00.00 top
```

축하합니다! App Service에서 사용자 지정 Linux 컨테이너를 구성했습니다.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>다음 단계

학습한 내용은 다음과 같습니다.

> [!div class="checklist"]
> * 프라이빗 컨테이너 레지스트리에 사용자 지정 이미지 배포
> * App Service에서 사용자 지정 이미지 실행
> * 환경 변수 구성
> * 이미지 업데이트 및 다시 배포
> * 진단 로그 액세스
> * SSH를 사용하여 컨테이너에 연결

다음 자습서로 이동하여 사용자 지정 DNS 이름을 앱에 매핑하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [자습서: 앱에 사용자 지정 DNS 이름 매핑](../app-service-web-tutorial-custom-domain.md)

또는 다른 리소스를 확인합니다.

> [!div class="nextstepaction"]
> [사용자 지정 컨테이너 구성](configure-custom-container.md)

> [!div class="nextstepaction"]
> [자습서: 다중 컨테이너 WordPress 앱](tutorial-multi-container-app.md)
