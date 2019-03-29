---
title: Linux에서 Python 앱 만들기 - Azure App Service | Microsoft Docs
description: 몇 분 안에 Linux의 Azure App Service에서 첫 번째 Python Hello World 앱을 배포합니다.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 02/08/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 5cca0d866442583c87665b3a1db8c65c66d12f0a
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58285380"
---
# <a name="create-a-python-app-in-azure-app-service-on-linux-preview"></a>Linux의 Azure App Service에서 Python 앱 만들기(미리 보기)

[Linux의 App Service](app-service-linux-intro.md)는 Linux 운영 체제를 기반으로 확장성이 높은 자체 패치 웹 호스팅 서비스를 제공합니다. 이 빠른 시작에서는 [Azure CLI](/cli/azure/install-azure-cli)를 사용하여 App Service에서 기본 Python 이미지(미리 보기) 위에 Python 앱을 배포하는 방법을 보여 줍니다.

Mac, Windows 또는 Linux 컴퓨터를 사용하여 이 문서의 단계를 수행하면 됩니다.

![Azure에서 실행되는 샘플 앱](media/quickstart-python/hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 다음이 필요합니다.

* <a href="https://www.python.org/downloads/" target="_blank">Python 3.7 설치</a>
* <a href="https://git-scm.com/" target="_blank">Git 설치</a>

## <a name="download-the-sample-locally"></a>로컬로 샘플 다운로드

터미널 창에서 다음 명령을 실행하여, 샘플 애플리케이션을 로컬 머신에 복제하고 샘플 코드가 들어 있는 디렉터리로 이동합니다.

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
cd python-docs-hello-world
```

리포지토리는 리포지토리에 Flask 앱이 포함되었음을 App Service에 알려 주는 *application.py*를 포함합니다. 자세한 내용은 [컨테이너 시작 프로세스 및 사용자 지정](how-to-configure-python.md)을 참조하세요.

## <a name="run-the-app-locally"></a>로컬에서 앱 실행하기

애플리케이션을 로컬로 실행하여 Azure에 애플리케이션을 배포할 때 표시되는 모양을 확인합니다. 터미널 창을 열고 아래 명령을 사용하여 필요한 종속성을 설치하고 기본 제공 개발 서버를 시작합니다. 

```bash
# In Bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
FLASK_APP=application.py flask run

# In PowerShell
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
Set-Item Env:FLASK_APP ".\application.py"
flask run
```

웹 브라우저를 열고 `http://localhost:5000/`의 샘플 앱으로 이동합니다.

이 페이지에 표시된 샘플 앱의 **Hello World!** 메시지가 표시됩니다.

![로컬로 실행되는 샘플 앱](media/quickstart-python/hello-world-in-browser.png)

터미널 창에서 **Ctrl+C**를 눌러 웹 서버를 종료합니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>샘플 다운로드

Cloud Shell에서 quickstart 디렉터리를 만든 다음, 해당 디렉토리로 이동합니다.

```bash
mkdir quickstart

cd quickstart
```

이어서 다음 명령을 실행하여 quickstart 디렉터리에 샘플 앱 리포지토리를 복제합니다.

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

실행 시 다음 예와 유사한 정보를 출력합니다.

```bash
Cloning into 'python-docs-hello-world'...
remote: Enumerating objects: 43, done.
remote: Total 43 (delta 0), reused 0 (delta 0), pack-reused 43
Unpacking objects: 100% (43/43), done.
Checking connectivity... done.
```

## <a name="create-a-web-app"></a>웹앱 만들기

샘플 코드가 들어있는 디렉토리로 이동한 후 `az webapp up` 명령을 실행합니다.

다음 명령에서 <app-name>을 고유한 앱 이름으로 바꿉니다.

```bash
cd python-docs-hello-world

az webapp up -n <app-name>
```

이 명령을 실행하는 데 몇 분 정도 걸릴 수 있습니다. 실행 시 다음 예와 유사한 정보를 출력합니다.

```json
The behavior of this command has been altered by the following extension: webapp
Creating Resource group 'appsvc_rg_Linux_CentralUS' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_CentralUS' ...
App service plan creation complete
Creating app '<app-name>' ....
Webapp creation complete
Creating zip with contents of dir /home/username/quickstart/python-docs-hello-world ...
Preparing to deploy contents to app.
All done.
{
  "app_url": "https:/<app-name>.azurewebsites.net",
  "location": "Central US",
  "name": "<app-name>",
  "os": "Linux",
  "resourcegroup": "appsvc_rg_Linux_CentralUS ",
  "serverfarm": "appsvc_asp_Linux_CentralUS",
  "sku": "BASIC",
  "src_path": "/home/username/quickstart/python-docs-hello-world ",
  "version_detected": "-",
  "version_to_create": "python|3.7"
}
```

[!INCLUDE [AZ Webapp Up Note](../../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>앱으로 이동

웹 브라우저를 사용하여 배포된 애플리케이션으로 이동합니다.

```bash
http://<app-name>.azurewebsites.net
```

Python 샘플 코드가 기본 제공 이미지가 있는 Linux의 App Service에서 실행됩니다.

![Azure에서 실행되는 샘플 앱](media/quickstart-python/hello-world-in-browser.png)

**축하합니다.** Linux에서 App Service에 첫 번째 Python 앱을 배포했습니다.

## <a name="update-locally-and-redeploy-the-code"></a>로컬로 코드 업데이트 및 다시 배포

Cloud Shell에서 `code application.py`을 입력하여 Cloud Shell 편집기를 엽니다.

![application.py 코드](media/quickstart-python/code-applicationpy.png)

 `return`에 대한 호출에서 텍스트를 약간 변경합니다.

```python
return "Hello Azure!"
```

변경 내용을 저장하고 편집기를 종료합니다. `^S` 명령을 사용하여 저장하고 `^Q` 명령을 사용하여 종료합니다.

이제 앱을 다시 배포합니다. `<app-name>`을 앱으로 대체합니다.

```bash
az webapp up -n <app-name>
```

배포가 완료되면 **앱으로 이동** 단계에서 열린 브라우저 창으로 다시 전환하고 페이지를 새로 고칩니다.

![Azure에서 실행되는 업데이트된 샘플 앱](media/quickstart-python/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>새 Azure 앱 관리

만든 앱을 관리하려면 <a href="https://portal.azure.com" target="_blank">Azure Portal</a>로 이동합니다.

왼쪽 메뉴에서 **App Services**를 클릭한 다음, Azure 앱의 이름을 클릭합니다.

![Azure 앱에 대한 포털 탐색](./media/quickstart-python/app-service-list.png)

앱의 개요 페이지가 표시됩니다. 여기에서 찾아보기, 중지, 시작, 다시 시작, 삭제와 같은 기본 관리 작업을 수행할 수 있습니다.

![Azure Portal의 App Service 페이지](media/quickstart-python/app-service-detail.png)

왼쪽 메뉴는 앱 구성을 위한 다양한 페이지를 제공합니다. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>다음 단계

Linux의 App Service에 기본 제공되는 Python 이미지는 현재 미리 보기로 제공되며, 앱 시작에 사용되는 명령을 사용자 지정할 수 있습니다. 사용자 지정 컨테이너를 대신 사용하여 프로덕션 Python 앱을 만들 수도 있습니다.

> [!div class="nextstepaction"]
> [PostgreSQL을 사용하는 Python](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [사용자 지정 시작 명령 구성](how-to-configure-python.md#customize-startup-command)

> [!div class="nextstepaction"]
> [문제 해결](how-to-configure-python.md#troubleshooting)

> [!div class="nextstepaction"]
> [사용자 지정 이미지 사용](tutorial-custom-docker-image.md)
