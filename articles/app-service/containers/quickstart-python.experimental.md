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
ms.date: 03/28/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: a386924be2f82aadafeaccbeadc68e5e27404855
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59546334"
---
# <a name="create-a-python-app-in-azure-app-service-on-linux-preview"></a>Linux의 Azure App Service에서 Python 앱 만들기(미리 보기)

[Linux의 App Service](app-service-linux-intro.md)는 Linux 운영 체제를 기반으로 확장성이 높은 자체 패치 웹 호스팅 서비스를 제공합니다. 이 빠른 시작에서는 [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)을 사용하여 App Service on Linux에서 기본 Python 이미지(미리 보기) 위에 Python 앱을 배포하는 방법을 보여줍니다.

Mac, Windows 또는 Linux 컴퓨터를 사용하여 이 문서의 단계를 수행하면 됩니다.

![Azure에서 실행되는 샘플 앱](media/quickstart-python/hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

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

다음 명령에서 <app_name>을 고유한 앱 이름으로 바꿉니다.

```bash
cd python-docs-hello-world

az webapp up -n <app_name>
```

이 명령을 실행하는 데 몇 분 정도 걸릴 수 있습니다. 실행 시 다음 예와 유사한 정보를 출력합니다.

```json
The behavior of this command has been altered by the following extension: webapp
Creating Resource group 'appsvc_rg_Linux_CentralUS' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_CentralUS' ...
App service plan creation complete
Creating app '<app_name>' ....
Webapp creation complete
Creating zip with contents of dir /home/username/quickstart/python-docs-hello-world ...
Preparing to deploy contents to app.
All done.
{
  "app_url": "https:/<app_name>.azurewebsites.net",
  "location": "Central US",
  "name": "<app_name>",
  "os": "Linux",
  "resourcegroup": "appsvc_rg_Linux_CentralUS ",
  "serverfarm": "appsvc_asp_Linux_CentralUS",
  "sku": "BASIC",
  "src_path": "/home/username/quickstart/python-docs-hello-world ",
  "version_detected": "-",
  "version_to_create": "python|3.7"
}
```

`az webapp up` 명령에는 다음 작업이 포함됩니다.

- 기본 리소스 그룹 만들기

- 기본 앱 서비스 계획 만들기

- 지정된 이름의 앱 만들기

- 현재 작업 디렉터리에서 앱까지의 [배포 파일을 압축합니다](https://docs.microsoft.com/azure/app-service/deploy-zip).

## <a name="browse-to-the-app"></a>앱으로 이동

웹 브라우저를 사용하여 배포된 애플리케이션으로 이동합니다.

```bash
http://<app_name>.azurewebsites.net
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

이제 앱을 다시 배포합니다. `<app_name>`을 앱으로 대체합니다.

```bash
az webapp up -n <app_name>
```

배포가 완료되면 **앱으로 이동** 단계에서 열린 브라우저 창으로 다시 전환하고 페이지를 새로 고칩니다.

![Azure에서 실행되는 업데이트된 샘플 앱](media/quickstart-python/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>새 Azure 앱 관리

만든 앱을 관리하려면 <a href="https://portal.azure.com" target="_blank">Azure Portal</a>로 이동합니다.

왼쪽 메뉴에서 **App Services**를 클릭한 다음, Azure 앱의 이름을 클릭합니다.

![Azure 앱에 대한 포털 탐색](./media/quickstart-python/app-service-list.png)

앱의 개요 페이지가 표시됩니다. 여기에서 찾아보기, 중지, 시작, 다시 시작, 삭제와 같은 기본 관리 작업을 수행할 수 있습니다.

![Azure Portal의 App Service 페이지](media/quickstart-python/app-service-detail.png)

왼쪽 메뉴로 앱 구성을 위한 여러가지 페이지를 볼 수 있습니다. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>다음 단계

Linux의 App Service에 기본 제공되는 Python 이미지는 현재 미리 보기로 제공되며, 앱 시작에 사용되는 명령을 사용자 지정할 수 있습니다. 사용자 지정 컨테이너를 대신 사용하여 프로덕션 Python 앱을 만들 수도 있습니다.

> [!div class="nextstepaction"]
> [자습서: PostgreSQL을 사용한 Python 앱](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Python 앱 구성](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [자습서: 프라이빗 컨테이너 리포지토리에서 배포](tutorial-custom-docker-image.md)
