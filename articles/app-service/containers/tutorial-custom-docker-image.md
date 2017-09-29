---
title: "컨테이너용 Azure Web App에 대한 사용자 지정 Docker 이미지 사용 | Microsoft Docs"
description: "Containers용 Azure Web Apps에 사용자 지정 Docker 이미지를 사용하는 방법"
keywords: "azure app service, 웹앱, linux, docker, 컨테이너"
services: app-service
documentationcenter: 
author: SyntaxC4
manager: SyntaxC4
editor: 
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/03/2017
ms.author: cfowler
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 57278d02a40aa92f07d61684e3c4d74aa0ac1b5b
ms.openlocfilehash: 10795d59b019d7c683abfeea611d5909b88891ec
ms.contentlocale: ko-kr
ms.lasthandoff: 09/28/2017

---
# <a name="use-a-custom-docker-image-for-azure-web-app-for-containers"></a>Containers용 Azure Web Apps에 사용자 지정 Docker 이미지 사용

[Containers용 Web App](app-service-linux-intro.md)은 PHP 7.0 및 Node.js 4.5와 같은 특정 버전에 대한 지원을 통해 Linux에서 기본 제공 Docker 이미지를 제공합니다. Containers용 Web App은 Docker 컨테이너 기술을 활용하여 기본 제공 이미지와 사용자 지정 이미지를 PaaS(platform as a service)로 모두 호스팅합니다. 이 자습서에서는 Containers용 Web App에서 사용할 사용자 지정 Docker 이미지를 빌드하는 방법을 알아봅니다. 이 작업은 해당 언어에 기본 제공 이미지가 없거나 응용 프로그램에 기본 제공 이미지 내에서 제공되지 않는 특정 구성이 필요한 경우 일반적인 패턴입니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* [Git](https://git-scm.com/downloads)
* 활성 [Azure 구독](https://azure.microsoft.com/pricing/free-trial/)
* [Docker](https://docs.docker.com/get-started/#setup)
* [Docker 허브 계정](https://docs.docker.com/docker-id/)

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>샘플 다운로드

터미널 창에서 다음 명령을 실행하여 로컬 컴퓨터에 샘플 앱 리포지토리를 복제한 후 샘플 코드를 포함하는 디렉터리로 변경합니다.

```bash
git clone https://github.com/Azure-Samples/use-custom-docker-image.git
cd use-custom-docker-image
```

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="build-the-image-from-the-docker-file"></a>Docker 파일에서 이미지 빌드

다음 Docker 파일은 응용 프로그램을 실행하는 데 필요한 Python 환경을 설명합니다. 또한 이미지는 컨테이너와 호스트 간에 보안 통신을 위해 [SSH](https://www.ssh.com/ssh/protocol/) 서버를 설정합니다.

```docker
# Use an official Python runtime as a parent image
FROM python

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Run python's package manager and install the flask package
RUN pip install flask

# Configure ports
EXPOSE 2222 80

# Run apt-get, to install the SSH server
RUN apt-get update \ 
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "root:Docker!" | chpasswd

#Copy the sshd_config file to its new location
COPY sshd_config /etc/ssh/

# Start the SSH service
RUN service ssh start

# Copy init_container.sh to the /bin directory
COPY init_container.sh /bin/
    
# Run the chmod command to change permissions on above file in the /bin directory
RUN chmod 755 /bin/init_container.sh 

# run commands in init_container.sh
CMD ["/bin/init_container.sh"]
```

Docker 이미지를 빌드하려면 `docker build` 명령을 실행하고 이름을 `mydockerimage`로 입력하고 태그를 `v1.0.0`으로 입력합니다. `<docker-id>`를 Docker 허브 계정 ID로 바꿉니다.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

명령은 다음과 유사한 출력을 생성합니다.

```bash
# The output from the commands in this article has been shortened for brevity.

Sending build context to Docker daemon  23.04kB
Step 1/13 : FROM python
 ---> 968120d8cbe8
Step 2/13 : WORKDIR /app
 ---> Using cache
 ---> dd6fdca5aa65
Step 3/13 : ADD . /app
 ---> e05c8f4beeae
Get:1 http://security.debian.org jessie/updates InRelease [63.1 kB]
Ign http://deb.debian.org jessie InRelease
Get:2 http://deb.debian.org jessie-updates InRelease [145 kB]
Get:3 http://deb.debian.org jessie Release.gpg [2373 B]
Fetched 9988 kB in 7s (1266 kB/s)
Reading package lists...
Building dependency tree...
Reading state information...
The following extra packages will be installed:
  init-system-helpers libwrap0 openssh-sftp-server
Suggested packages:
  ssh-askpass rssh molly-guard ufw monkeysphere
Recommended packages:
  tcpd xauth ncurses-term
The following NEW packages will be installed:
  init-system-helpers libwrap0 openssh-server openssh-sftp-server
0 upgraded, 4 newly installed, 0 to remove and 3 not upgraded.
Need to get 442 kB of archives.
After this operation, 1138 kB of additional disk space will be used.
Get:1 http://deb.debian.org/debian/ jessie/main libwrap0 amd64 7.6.q-25 [58.5 kB]
Creating SSH2 RSA key; this may take some time ...
2048 f0:e9:fb:69:de:62:a4:5c:a3:7c:b3:41:e9:2e:96:a3 /etc/ssh/ssh_host_rsa_key.pub (RSA)
Creating SSH2 DSA key; this may take some time ...
1024 4a:5e:89:bd:aa:2d:71:bb:0e:3a:32:94:fb:c0:b1:4d /etc/ssh/ssh_host_dsa_key.pub (DSA)
Processing triggers for systemd (215-17+deb8u7) ...
 ---> 5b416a7dcdca
Removing intermediate container 283b3b4623d7
Step 13/13 : CMD python app.py
 ---> Running in 1c776e5e0772
 ---> 1bfc1bbc968d
Removing intermediate container 1c776e5e0772
Successfully built 1bfc1bbc968d
Successfully tagged <docker-id>/myDockerImage:v1.0.0
```

Docker 컨테이너를 실행하여 빌드가 작동하는지를 테스트합니다. [docker run](https://docs.docker.com/engine/reference/commandline/run/) 명령을 실행하고 이미지의 이름 및 태그를 전달합니다. `-p` 인수를 사용하여 포트를 지정해야 합니다.

```bash
docker run -p 80:2222 <docker-ID>/mydockerimage:v1.0.0
```

웹앱을 로컬로 검색하여 웹앱 및 컨테이너가 제대로 작동하는지 확인합니다.

![로컬로 웹앱 테스트](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

## <a name="push-a-docker-image-to-docker-hub"></a>Docker 허브에 Docker 이미지 푸시

레지스트리는 이미지를 호스트하고 서비스 이미지 및 컨테이너 서비스를 제공하는 응용 프로그램입니다. 이미지를 공유하려면 레지스트리에 푸시해야 합니다. 

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry such as Azure Container Registry. Select the appropriate tab for your scenario below (your selection will switch multiple tabs on this page). -->

> [!NOTE]
> 개인 Docker 레지스트리 푸시 [개인 레지스트리에 Docker 이미지 푸시](#push-a-docker-image-to-private-registry-optional)에 대한 선택적 지침을 참조하세요.

<!--## [Docker Hub](#tab/docker-hub)-->

Docker 허브는 Docker 이미지의 레지스트리이며 고유한 공개 또는 개인 리포지토리를 호스팅할 수 있습니다. 사용자 지정 Docker 이미지를 공용 Docker 허브에 푸시하려면 [docker push](https://docs.docker.com/engine/reference/commandline/push/) 명령을 사용하여 전체 이미지 이름 및 태그를 제공합니다. 전체 이미지 이름 및 태그는 다음 샘플과 같습니다.

```bash
<docker-id>/image-name:tag
```

Docker 허브에 로그인하지 않은 경우 이미지를 푸시하기 전에 [docker login](https://docs.docker.com/engine/reference/commandline/login/) 명령을 사용하여 수행합니다.

```bash
docker login --username <docker-id> --password <docker-hub-password>
```

"로그인했습니다."라는 메시지는 사용자가 로그인했다고 확인합니다. 일단 로그인하면 [docker push](https://docs.docker.com/engine/reference/commandline/push/) 명령을 사용하여 Docker 허브에 이미지를 푸시할 수 있습니다.

```bash
docker push <docker-id>/mydockerimage:v1.0.0 .
```

명령의 출력을 검토하여 푸시가 성공했는지 확인합니다.

```bash
The push refers to a repository [docker.io/<docker-id>/python-flask]
e9aa2c6d0f34: Pushed
0fdcb490aeec: Pushed
08ae61c7869c: Pushed
2548e7db2a94: Mounted from library/python
325b9d6f2920: Pushed
815acdffadff: Mounted from library/python
97108d083e01: Mounted from library/python
5616a6292c16: Mounted from library/python
f3ed6cb59ab0: Mounted from library/python
654f45ecb7e3: Mounted from library/python
2c40c66f7667: Mounted from library/python
v1: digest: sha256:a910d5b77e6960c01745a87c35f3d1a13ba73231ac9a4664c5011b1422d59b60 size: 2632
```

<!--
# [Private Registry](#tab/private-registry)

// Move Private Registry instructions here when Tabbed Conceptual bug is fixed

---
-->

## <a name="create-web-app-for-containers"></a>컨테이너용 Web App 만들기

Azure Web Apps를 사용하여 클라우드에서 네이티브 Linux 응용 프로그램을 호스트할 수 있습니다. 컨테이너용 Web App을 만들려면 그룹, 서비스 계획 및 웹앱 자체를 만드는 Azure CLI 명령을 실행해야 합니다. 먼저 [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) 명령을 실행하고 위치 및 고유한 이름에 전달합니다.

```azurecli-interactive
az group create --location "West Europe" --name myResourceGroup
```

그러면 다음 샘플과 비슷한 출력이 표시됩니다.

```json
{
  "id": "/subscriptions/432849d3e4-4f90-a782-87c11e-5e59d6dd/resourceGroups/myResourceGroup",
  "location": "westeurope",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

[az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) 명령을 사용하여 앱 서비스 계획을 만들기 위해 그룹의 이름을 사용합니다. 또한 고유한 이름의 이름을 지정하고 `--is-linux` 플래그를 설정해야 합니다.

```azurecli-interactive
az appservice plan create --name myServicePlan --resource-group myResourceGroup --is-linux
```

서비스 계획을 만들면 다음 샘플과 비슷한 결과가 생성됩니다.

```json
  {- Starting...
  "adminSiteName": null,
  "appServicePlanName": "myServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/resourceGroups/myResourceGroup/provide
rs/Microsoft.Web/serverfarms/myServicePlan",
  "kind": "linux",
  "location": "West Europe", 
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capabilities": null,
    "capacity": 1,
    "tier": "Basic"
  },
  "status": "Ready",
  "subscription": "",
  "tags": null,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
```

리소스 그룹 및 서비스 계획을 만들었으므로 [az webapp create](https://docs.microsoft.com/cli/azure/webapp#az_webapp_create) 명령을 실행하여 웹앱을 만들 수 있습니다. 런타임 스택이 Python 3.4이며 웹앱이 이전 단계에 있는 리소스 그룹 및 서비스 계획 설정을 사용합니다.

```azurecli-interactive
az webapp create -g myResourceGroup -p myServicePlan -n <web-app-name> --runtime "python|3.4" 
```

웹앱을 만드는 명령은 여기에 표시된 출력을 생성합니다.

```json
{- Starting ..
  "availabilityState": "Normal",
   "enabled": true,
  "enabledHostNames": [
    "<web-app-name>.azurewebsites.net",
    "<web-app-name>.scm.azurewebsites.net"
  ],
  "ftpPublishingUrl": "ftp://waws-prod-am2-085.ftp.azurewebsites.windows.net/site/wwwroot",
  "gatewaySiteName": null,
  "hostNameSslStates": [
    {
      "hostType": "Standard",
      "name": "<web-app-name>.azurewebsites.net",
    },
  ],
  "hostNames": [
    "<web-app-name>.azurewebsites.net"
  ],
  "hostNamesDisabled": false,
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/5e59d6dd-d3e4-4f90-a782-43284987c11e/resourceGroups/myResourceGroup/providers/Microsoft.
Web/sites/<web-app-name>",
  "lastModifiedTimeUtc": "2017-08-08T21:09:33.693333",
  "location": "West Europe",
  "name": "<web-app-name>",
  "outboundIpAddresses": "13.81.108.99,52.232.76.83,52.166.73.203,52.233.173.39,52.233.159.48",
  "resourceGroup": "myResourceGroup"
}

```

대부분의 웹앱은 응용 프로그램 설정이 구성되어야 합니다. 다른 사용자가 작성한 기존 Docker 이미지를 사용하면 이미지는 응용 프로그램에 대해 포트 80 이외의 포트가 필요할 수도 있습니다. `WEBSITES_PORT`를 설정하려면, 다음 코드 샘플에 나와 있는 대로 [az webapp config](https://docs.microsoft.com/cli/azure/webapp/config/appsettings) 명령을 실행합니다.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <web-app-name> --settings WEBSITES_PORT=2222
```

> [!NOTE]
> 응용 프로그램 설정은 대/소문자를 구분합니다.
>

웹앱을 검색하여 작동하는지 확인합니다. 포트 번호를 잊지 마십시오.

![웹앱 포트 구성 테스트](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="configure-web-app-to-use-docker-container-from-docker-hub"></a>Docker 허브에서 Docker 컨테이너를 사용하도록 Web App 구성

[az webapp config](https://docs.microsoft.com/cli/azure/webapp/config) 명령을 사용하면 사용자 지정 Docker 이미지를 사용할 수 있습니다.

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry, such as Azure Container Registry. Select the appropriate tab for your scenario below: -->

> [!NOTE]
> 개인 Docker 레지스트리에서 배포 [개인 레지스트리에서 Docker 컨테이너를 사용하도록 Web App 구성](#configure-web-app-to-use-docker-container-from-a-private-registry-optional)에 대한 선택적 지침을 참조하세요.

<!-- # [Docker Hub](#tab/docker-hub)-->

공개 Docker 레지스트리를 사용하도록 웹앱을 구성하려면 앱 이름, 리소스 그룹, 이미지 이름 및 URL을 [az webapp config container set](https://docs.microsoft.com/cli/azure/webapp/config/container#az_webapp_config_container_set) 명령에 전달합니다.

```azurecli-interactive
az webapp config container set --name <web-app-name> --resource-group myResourceGroup --docker-custom-image-name mydockerimage
--docker-registry-server-url <docker-id>/myContainerRegistry
```

구성을 성공적으로 변경하면 컨테이너에 대한 일반 정보를 반환합니다.

```bash
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "slotSetting": false,
    "value": "{docker-id}/mydockerimage:v1.0.0"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "{docker-id}"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  }
]
```

<!-- # [Private Registry](#tab/private-registry)

// Place Private Registry text back here once Tabbed Conceptual bug is fixed

---
-->

## <a name="test-the-application-in-azure"></a>Azure에서 응용 프로그램 테스트

구성 변경 내용을 적용하려면 테스트하기 전에 [az webapp restart](https://docs.microsoft.com/cli/azure/webapp#az_webapp_restart) 명령을 사용하여 웹앱을 다시 시작해야 합니다.

```azurecli-interactive
az webapp restart --name <web-app-name> --resource-group myResourceGroup
```

다시 시작 명령이 웹앱을 자동으로 다시 시작하면 터미널에서 피드백이 표시되지 않습니다. 웹앱이 실행되면 `http://<username>.azurewebsites.net`에서 해당 URL로 이동하여 웹앱을 테스트합니다. 앱이 새 환영 메시지를 표시하는지 확인합니다.

![Azure에서 웹앱 테스트](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="change-web-app-and-redeploy"></a>웹앱 변경 및 다시 배포

`app.py`라는 Python 파일은 `using-custom-docker-image` 디렉터리에 위치합니다. 이 파일에는 `Hello World!` 메시지를 표시하는 코드 줄이 포함됩니다. `Hello World of Web Apps running in Docker Containers!` 메시지를 표시하도록 줄을 변경합니다.

```python
return "Hello World of Web Apps running in Docker Containers!"
```

Python 파일을 수정하고 저장하면 새 Docker 이미지를 다시 빌드하고 푸시해야 합니다. 그런 다음 변경 내용을 적용하려면 웹앱을 다시 시작합니다. 이 자습서에서는 이전에 사용한 동일한 명령을 사용합니다. [Docker 파일에서 이미지 빌드](#build-the-image-from-the-docker-file) 및 [Docker 이미지 푸시](#push-docker-image) 섹션을 참조할 수 있습니다. [Azure에서 응용 프로그램 테스트](#tTest-the-application-in-azure)의 지침에 따라 웹앱을 테스트합니다.

## <a name="connect-to-web-app-for-containers-using-ssh"></a>SSH를 사용하여 컨테이너용 Web App에 연결

SSH를 사용하면 컨테이너와 클라이언트 간의 보안 통신을 설정할 수 있습니다. 사용자 지정 Docker 이미지가 SSH를 지원하도록 하려면 Dockerfile에 빌드해야 합니다. Docker 파일 자체에서 SSH를 사용하도록 설정합니다. SSH 지침은 이미 샘플 dockerfile에 추가되었으므로 고유한 사용자 지정 이미지를 사용하여 이러한 지침을 따를 수 있습니다.

* `apt-get`을 호출하고 루트 계정의 암호를 `"Docker!"`로 설정하는 [RUN](https://docs.docker.com/engine/reference/builder/#run) 지침입니다.

    ```docker
    RUN apt-get update \
        && apt-get install -y --no-install-recommends openssh-server \
        && echo "root:Docker!" | chpasswd
    ```

    > [!NOTE]
    > 이 구성을 사용하면 컨테이너에 대한 외부 연결이 허용되지 않습니다. Kudu/SCM 사이트를 통해서만 SSH를 사용할 수 있습니다. Kudu/SCM 사이트는 게시 자격 증명으로 인증됩니다.

* [sshd_config](http://man.openbsd.org/sshd_config) 파일을 */etc/ssh/* 디렉터리에 복사하도록 Docker 엔진에 지시하는 [COPY](https://docs.docker.com/engine/reference/builder/#copy) 지침입니다. 구성 파일은 Azure-App-Service GitHub 리포지토리에서 [sshd_config file](https://github.com/Azure-App-Service/node/blob/master/6.11/sshd_config) 파일을 기반으로 해야 합니다.

    > [!NOTE]
    > *sshd_config* 파일에 다음이 항목되어야 합니다. 
    > * `Ciphers`는 이 목록에 적어도 하나의 항목이 있어야 합니다.`aes128-cbc,3des-cbc,aes256-cbc`
    > * `MACs`는 이 목록에 적어도 하나의 항목이 있어야 합니다.`hmac-sha1,hmac-sha1-96`

    ```docker
    #Copy the sshd_config file to its new location
    COPY sshd_config /etc/ssh/
    ```

* 컨테이너에서 2222 포트를 노출하는 [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) 지침입니다. 루트 암호를 알고 있더라도 인터넷에서 포트 2222에 액세스할 수 없습니다. 개인 가상 네트워크의 브리지 네트워크 내에 있는 컨테이너에서 액세스할 수 있는 내부 전용 포트입니다. 그런 후에 명령은 SSH 구성 세부 정보를 복사하고 `ssh` 서비스를 시작합니다.

    ```docker
    # Configure ports
    EXPOSE 2222 80

    #Copy the sshd_config file to its new location
    COPY sshd_config /etc/ssh/

    # Start the SSH service
    RUN service ssh start
    ```

샘플 코드의 `init_container.sh` 파일은 실행될 때 컨테이너를 초기화하는 방법에 대한 지침을 포함합니다. Dockerfile은 [COPY](https://docs.docker.com/engine/reference/builder/#copy), [RUN](https://docs.docker.com/engine/reference/builder/#run) 및 [CMD](https://docs.docker.com/engine/reference/builder/#cmd) 지침을 사용하여 `init_container.sh` 스크립트를 제대로 시작합니다.

```docker
# Copy init_container.sh to the /bin directory
COPY init_container.sh /bin/

# Run the chmod command to change permissions on above file in the /bin directory
RUN chmod 755 /bin/init_container.sh

# run commands in init_container.sh
CMD ["/bin/init_container.sh"]
```

특정 응용 프로그램이 컨테이너에서 실행되고 있는지를 확인하려고 합니다. 컨테이너를 검사하고 실행 중인 프로세스를 확인하려면 브라우저를 열고 `https://<app name>.scm.azurewebsites.net/webssh/host`로 이동합니다. 그런 다음 대화형 콘솔을 표시하는 페이지로 리디렉션됩니다. 프롬프트에서 `top` 명령을 발급합니다.

```bash
top
```

`top` 명령은 컨테이너에서 실행 중인 모든 프로세스를 노출합니다.

```bash
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

축하합니다. Containers용 Azure Web App에 사용자 지정 Docker 이미지를 구성했습니다.

## <a name="push-a-docker-image-to-private-registry-optional"></a>개인 레지스트리(선택 사항)에 Docker 이미지 푸시

Azure Container Registry는 개인 이미지를 호스트하기 위해 Azure에서 관리되는 Docker 서비스입니다. 배포는[Docker Swarm](https://docs.docker.com/engine/swarm/), [Kubernetes](https://kubernetes.io/) 및 Azure App Service 컨테이너를 비롯한 모든 형식일 수 있습니다. Azure Container Registry를 사용하는 것이 개인 레지스트리를 사용하는 것과 같으므로 사용자 고유의 개인 레지스트리를 사용해야 하는 경우 이 작업을 완료하는 단계도 유사합니다.

[az acr create](https://docs.microsoft.com/cli/azure/acr#az_acr_create) 명령을 사용하여 Azure Container Registry를 만듭니다. 이름, 리소스 그룹 및 SKU의 `Basic`을 전달합니다. 사용 가능한 SKU는 `Classic`, `Basic`, `Standard`, `Premium`입니다.

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

컨테이너를 만들면 다음과 같은 출력을 생성합니다.

```bash
 - Finished ..
Create a new service principal and assign access:
  az ad sp create-for-rbac --scopes /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --password <password>

Use an existing service principal and assign access:
  az role assignment create --scope /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --assignee <app-id>
{
  "adminUserEnabled": false,
  "creationDate": "2017-08-09T04:21:09.654153+00:00",
  "id": "/subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/{azure-container-registry-name>",
  "location": "westeurope",
  "loginServer": "<azure-container-registry-name>.azurecr.io",
  "name": "<azure-container-registry-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "myazurecontainerre042025"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

이미지를 레지스트리로 푸시하려면 레지스트리가 푸시를 수락하도록 자격 증명을 제공해야 합니다. [az acr show](https://docs.microsoft.com/cli/azure/acr/credential#az_acr_credential_show) 명령을 사용하여 이러한 자격 증명을 검색할 수 있습니다. 

```azurecli-interactive
az acr credential show --name {azure-container-registry-name}
```

이 명령은 사용자 이름으로 사용할 수 있는 두 개의 암호를 드러냅니다.

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
  "username": "<azure-container-registry-name>"
}
```

이제 필요한 자격 증명이 있으므로 `docker login` 명령을 사용하여 Azure Container Registry에 로그인합니다. 로그인하려면 레지스트리의 URL이 필요합니다. `http://{azure-container-registry-name>.azurecr.io` 양식을 사용합니다.

```bash
docker login <azure-container-registry-name>.azurecr.io --username <azure-container-registry-name> --password <password> 
```

로그인이 성공했다고 확인합니다. `docker push` 명령을 실행하고, 이미지 이름 및 태그 앞에 있는 레지스트리의 전체 URL을 사용하여 이미지의 태그를 지정합니다.

```bash
docker push http://<azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

ACR 저장소를 나열하여 푸시가 레지스트리에 대한 컨테이너에 성공적으로 추가되었는지 확인합니다. 

```azurecli-interactive
az acr repository list -n <azure-container-registry-name>
```

레지스트리에서 이미지를 나열하면 `mydockerimage`가 레지스트리에 위치한다고 확인합니다.

```json
[
  "mydockerimage"
]
```

## <a name="configure-web-app-to-use-docker-container-from-a-private-registry-optional"></a>개인 레지스트리(선택 사항)에서 Docker 컨테이너를 사용하도록 Web App 구성

Azure Container Registry에 저장된 컨테이너를 실행할 수 있도록 Linux에서 웹앱을 구성할 수 있습니다. Azure Container Registry를 사용하는 것이 개인 레지스트리를 사용하는 것과 같으므로 사용자 고유의 개인 레지스트리를 사용해야 하는 경우 이 작업을 완료하는 단계도 유사합니다.

[az acr credential show](https://docs.microsoft.com/cli/azure/acr/credential#az_acr_credential_show) 명령은 컨테이너 레지스트리의 암호를 표시합니다. 다음 단계에서 웹앱을 구성하는 데 사용할 수 있도록 사용자 이름 및 하나의 암호를 복사합니다.

```bash
az acr credential show --name <azure-container-registry-name>
```

```json
{
  "passwords": [
    {
      "name": "password",
      "value": "password"
    },
    {
      "name": "password2",
      "value": "password2"
    }
  ],
  "username": "<azure-container-registry-name>"
}
```

[az webapp config container set](https://docs.microsoft.com/cli/azure/webapp/config/container#az_webapp_config_container_set) 명령을 실행합니다. 이 명령은 웹앱에 사용자 지정 Docker 이미지를 할당합니다. `https://{your-registry-username}.azurecr.io` 서식으로 표시된 URL이 필요합니다. 또한 웹앱에는 컨테이너 레지스트리에 액세스하기 위해 이전 단계에서 얻은 사용자 이름과 암호가 필요합니다.

```azurecli-interactive
az webapp config container set --name <web-app-name> --resource-group myResourceGroup --docker-custom-image-name mydockerimage --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <docker-id> --docker-registry-server-password <password>
```

> [!NOTE]
> 레지스트리의 URL에 있는 `https`가 필요합니다.
>

명령은 JSON 문자열에서 다음과 같은 출력을 표시하여 구성 변경에 성공했는지 보여줍니다.

```bash
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "slotSetting": false,
    "value": "mydockerimage"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_URL",
    "slotSetting": false,
    "value": "mycontainerregistry.azurecr.io"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "{azure-container-registry-name}"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  }
]
```

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>다음 단계

[Containers용 Azure App Service Web App 관련 FAQ](app-service-linux-faq.md)

