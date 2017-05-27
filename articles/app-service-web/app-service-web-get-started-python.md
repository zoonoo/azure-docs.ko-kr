---
title: "5분 내에 Azure에서 첫 번째 Python 웹앱 만들기 | Microsoft Docs"
description: "몇 분 안에 App Service Web App에서 첫 번째 Python Hello World를 배포합니다."
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
ms.assetid: 928ee2e5-6143-4c0c-8546-366f5a3d80ce
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: cfowler
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: df34052acc401fb5bb1e3f808c649c0ea0bcf33c
ms.contentlocale: ko-kr
ms.lasthandoff: 05/25/2017


---
# <a name="create-a-python-application-on-web-app"></a>Web App에서 Python 응용 프로그램 만들기

이 빠른 시작 자습서에서는 Python 앱을 개발하고 Azure에 배포하는 방법을 설명합니다. Azure App Service를 사용하여 앱을 실행하고, Azure CLI를 사용하여 새 웹앱을 만들고 구성합니다. 그런 다음 Git를 사용하여 Python 앱을 Azure에 배포합니다.

![hello-world-in-browser](media/app-service-web-get-started-python/hello-world-in-browser.png)

Mac, Windows 또는 Linux 컴퓨터를 사용하여 아래 단계를 따르면 됩니다. 다음 단계를 모두 완료하려면 약 5분이 소요됩니다.

## <a name="prerequisites"></a>필수 조건

이 샘플을 실행하기 전에 다음 필수 조건을 로컬로 설치합니다.

1. [Git 다운로드 및 설치](https://git-scm.com/)
1. [Python 다운로드 및 설치](https://www.python.org/downloads/)
1. [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) 다운로드 및 설치

## <a name="download-the-sample"></a>샘플 다운로드

로컬 컴퓨터에 Hello World 샘플 앱 리포지토리를 복제합니다.

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

> [!TIP]
> 또는 zip 파일로 [샘플을 다운로드](https://github.com/Azure-Samples/Python-docs-hello-world/archive/master.zip)하고 압축을 풀 수 있습니다.

샘플 코드를 포함하는 디렉터리로 변경합니다.

```bash
cd Python-docs-hello-world
```

## <a name="run-the-app-locally"></a>로컬에서 앱 실행

Python 웹 서버에서 기본 제공을 시작하려면 이 샘플의 `Python` 명령줄을 사용하는 터미널 창을 열어서 응용 프로그램을 로컬로 실행합니다.

```bash
python main.py
```

웹 브라우저를 열고 샘플로 이동합니다.

```bash
http://localhost:5000
```

이 페이지에 표시된 샘플 앱에서 **Hello World** 메시지를 볼 수 있습니다.

![localhost-hello-world-in-browser](media/app-service-web-get-started-python/localhost-hello-world-in-browser.png)

터미널 창에서 **Ctrl+C**를 눌러 웹 서버를 종료합니다.

## <a name="log-in-to-azure"></a>Azure에 로그인

이제 터미널 창에서 Azure CLI 2.0을 사용하여 Azure에서 Python 앱을 호스팅하는 데 필요한 리소스를 만들 예정입니다. [az login](/cli/azure/#login) 명령으로 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

```azurecli
az login
```

## <a name="configure-a-deployment-user"></a>배포 사용자 구성

FTP 및 로컬 Git의 경우 배포에 인증하기 위해 배포 사용자를 서버에 구성할 필요가 있습니다. 배포 사용자를 만드는 작업은 한 번만 구성하면 됩니다. 아래 단계에서 사용할 수 있도록 사용자 이름 및 암호를 적어 둡니다.

> [!NOTE]
> 배포 사용자는 웹앱에 대한 FTP 및 로컬 Git 배포가 필요합니다.
> `username` 및 `password`는 계정 수준입니다. 따라서 Azure 구독 자격 증명과 다릅니다. **이러한 자격 증명은 한 번에 만들어야만 합니다**.
>

[az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) 명령을 사용하여 계정 수준 자격 증명을 만듭니다.

```azurecli
az appservice web deployment user set --user-name <username> --password <password>
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#create)를 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 웹앱, 데이터베이스, 저장소 계정이 관리되었는지 등 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="create-an-azure-app-service"></a>Azure App Service 만들기

[az appservice plan create](/cli/azure/appservice/plan#create) 명령으로 App Service 계획을 만듭니다.

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

다음 예에서는 **체험** 가격 책정 계층을 사용하여 `quickStartPlan`이라는 App Service 계획을 만듭니다.

```azurecli
az appservice plan create --name quickStartPlan --resource-group myResourceGroup --sku FREE
```

App Service 계획을 만든 경우 Azure CLI는 다음 예제와 비슷한 정보를 표시합니다.

```json
{
"appServicePlanName": "quickStartPlan",
"geoRegion": "North Europe",
"id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
"kind": "app",
"location": "North Europe",
"maximumNumberOfWorkers": 1,
"name": "quickStartPlan",
"provisioningState": "Succeeded",
"resourceGroup": "myResourceGroup",
"sku": {
  "capacity": 0,
  "family": "F",
  "name": "F1",
  "size": "F1",
  "tier": "Free"
},
"status": "Ready",
"type": "Microsoft.Web/serverfarms",
}
```

## <a name="create-a-web-app"></a>웹앱 만들기

이제 App Service 계획을 만들었으므로 `quickStartPlan` App Service 계획 내에서 웹앱을 만듭니다. 웹앱은 코드를 배포할 호스팅 공간을 제공할 뿐만 아니라 배포된 응용 프로그램을 확인하도록 URL도 제공합니다. [az appservice web create](/cli/azure/appservice/web#create) 명령을 사용하여 웹앱을 만듭니다.

아래 명령에서 `<app_name>` 자리 표시자를 고유한 앱 이름으로 바꿉니다. `<app_name>`은 웹앱의 기본 DNS 사이트로 사용되므로 이름이 Azure의 모든 앱에서 고유해야 합니다. 나중에 사용자에게 노출하기 전에 웹앱에 사용자 지정 DNS 항목을 매핑할 수 있습니다.

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan quickStartPlan
```

웹앱을 만들었으면 Azure CLI는 다음 예제와 비슷한 정보를 표시합니다.

```json
{
  "clientAffinityEnabled": true,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "<app_name>.azurewebsites.net",
    "<app_name>.scm.azurewebsites.net"
  ],
  "hostNames": [
    "<app_name>.azurewebsites.net"
  ],
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/<app_name>",
  "kind": "app",
  "location": "North Europe",
  "outboundIpAddresses": "13.69.190.80,13.69.191.239,13.69.186.193,13.69.187.34",
  "resourceGroup": "myResourceGroup",
  "serverFarmId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
  "state": "Running",
  "type": "Microsoft.Web/sites",
}
```

사이트로 이동하여 새로 만든 웹앱을 봅니다.

```bash
http://<app_name>.azurewebsites.net
```

![app-service-web-service-created](media/app-service-web-get-started-python/app-service-web-service-created.png)

이제 Azure에서 비어 있는 새 웹앱을 만들었습니다. 이제 Python을 사용하고 여기에 앱을 배포하도록 웹앱을 구성하겠습니다.

## <a name="configure-to-use-python"></a>Python을 사용하도록 구성

[az appservice web config update](/cli/azure/app-service/web/config#update) 명령을 사용하여 Python 버전 `3.4`를 사용하도록 웹앱을 구성합니다.

> [!TIP]
> 사용자가 [az appservice web config container update](https://docs.microsoft.com/cli/azure/appservice/web/config/container#update) 명령에 대한 CLI 참조를 의미하는 고유한 컨테이너를 사용하려는 경우 이러한 방식으로 Python 버전을 설정하는 작업은 플랫폼에서 제공하는 기본 컨테이너를 사용합니다.

```azurecli
az appservice web config update --python-version 3.4 --name <app-name> --resource-group myResourceGroup
```

## <a name="configure-local-git-deployment"></a>로컬 Git 배포 구성

FTP, 로컬 Git뿐만 아니라 GitHub, Visual Studio Team Services 및 Bitbucket을 비롯한 다양한 방법으로 웹앱에 배포할 수 있습니다.

[az appservice web source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git) 명령을 사용하여 웹앱에 대한 로컬 Git 액세스 권한을 구성합니다.

```azurecli
az appservice web source-control config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

다음 단계에서 사용할 수 있는 터미널의 출력을 복사합니다.

```bash
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
```

## <a name="push-to-azure-from-git"></a>Git에서 Azure에 푸시

로컬 Git 리포지토리에 Azure 원격을 추가합니다.

```bash
git remote add azure <paste-previous-command-output-here>
```

Azure 원격에 푸시하여 응용 프로그램을 배포합니다. 배포 사용자를 만드는 작업의 일부로 이전에 입력한 암호를 묻는 메시지가 나타납니다.

```azurecli
git push azure master
```

배포하는 동안 Azure App Service는 Git에서 진행률을 조율합니다.

```bash
Counting objects: 18, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (16/16), done.
Writing objects: 100% (18/18), 4.31 KiB | 0 bytes/s, done.
Total 18 (delta 4), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '44e74fe7dd'.
remote: Generating deployment script.
remote: Generating deployment script for python Web Site
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling python deployment.
remote: KuduSync.NET from: 'D:\home\site\repository' to: 'D:\home\site\wwwroot'
remote: Deleting file: 'hostingstart.html'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'main.py'
remote: Copying file: 'README.md'
remote: Copying file: 'requirements.txt'
remote: Copying file: 'virtualenv_proxy.py'
remote: Copying file: 'web.2.7.config'
remote: Copying file: 'web.3.4.config'
remote: Detected requirements.txt.  You can skip Python specific steps with a .skipPythonDeployment file.
remote: Detecting Python runtime from site configuration
remote: Detected python-3.4
remote: Creating python-3.4 virtual environment.
remote: .................................
remote: Pip install requirements.
remote: Successfully installed Flask click itsdangerous Jinja2 Werkzeug MarkupSafe
remote: Cleaning up...
remote: .
remote: Overwriting web.config with web.3.4.config
remote:         1 file(s) copied.
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

## <a name="browse-to-the-app"></a>앱으로 이동

웹 브라우저를 사용하여 배포된 응용 프로그램으로 이동합니다.

```bash
http://<app_name>.azurewebsites.net
```

이번에는 Azure App Service 웹앱으로 실행되는 Python 코드를 사용하여 Hello World 메시지를 표시하는 페이지가 실행되고 있습니다.

![hello-world-in-browser](media/app-service-web-get-started-python/hello-world-in-browser.png)

## <a name="updating-and-deploying-the-code"></a>코드 업데이트 및 배포

로컬 텍스트 편집기를 사용하여 Python 앱 내에서 `main.py` 파일을 열고 `return` 문 옆에 있는 문자열 내의 텍스트를 약간 변경합니다.

```python
return 'Hello, Azure!'
```

Git에서 변경 내용을 커밋한 다음 Azure에 코드 변경 내용을 푸시합니다.

```bash
git commit -am "updated output"
git push azure master
```

배포가 완료되면 앱 단계에 대한 찾아보기에서 열린 브라우저 창으로 다시 전환하고 새로 고침을 누릅니다.

![hello-azure-in-browser](media/app-service-web-get-started-python/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-web-app"></a>새로운 Azure 웹앱 관리

Azure Portal로 이동하여 방금 만든 웹앱을 살펴봅니다.

이 작업을 수행하려면 [https://portal.azure.com](https://portal.azure.com)에 로그인합니다.

왼쪽 메뉴에서 **App Services**를 클릭한 다음 Azure 웹앱의 이름을 클릭합니다.

![Azure 웹앱에 대한 포털 탐색](./media/app-service-web-get-started-python/app-service-list.png)

웹앱의 _블레이드_(가로로 열리는 포털 페이지)로 이동했습니다.

기본적으로 웹앱의 블레이드는 **개요** 페이지를 표시합니다. 이 페이지에서는 앱이 어떻게 작동하고 있는지를 보여 줍니다. 여기에서 찾아보기, 중지, 시작, 다시 시작, 삭제와 같은 기본 관리 작업을 수행할 수 있습니다. 블레이드의 왼쪽에 있는 탭에서는 열 수 있는 다른 구성 페이지를 보여 줍니다.

![Azure Portal의 App Service 블레이드](media/app-service-web-get-started-python/app-service-detail.png)

블레이드의 이러한 탭은 웹앱에 추가할 수 있는 유용한 많은 기능을 보여 줍니다. 다음은 몇 가지 가능성을 제공합니다.

* 사용자 지정 DNS 이름 매핑
* 사용자 지정 SSL 인증서 바인딩
* 지속적 배포 구성
* 수평 및 수직 확장
* 사용자 인증 추가

**축하합니다.** App Service에 첫 번째 Python 앱을 배포했습니다.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [샘플 Web Apps CLI 스크립트 탐색](app-service-cli-samples.md)

