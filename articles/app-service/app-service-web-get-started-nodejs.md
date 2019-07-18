---
title: Node.js 웹앱 만들기 - Azure App Service | Microsoft Docs
description: 몇 분 안에 Azure App Service Web Apps에서 첫 번째 Node.js Hello World를 배포합니다.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 02/15/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: c103e6cb3626750414ee5083dad3e34b6be4986c
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65408936"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Azure에서 Node.js 웹앱 만들기

> [!NOTE]
> 이 문서에서는 Windows의 App Service에 앱을 배포합니다. _Linux_의 App Service에 배포하려면 [Linux의 Azure App Service에서 Node.js 웹앱 만들기](./containers/quickstart-nodejs.md)를 참조하세요.
>

[Azure App Service](overview.md)는 확장성 높은 자체 패치 웹 호스팅 서비스를 제공합니다.  이 빠른 시작에서는 Node.js 앱을 Azure App Service에 배포하는 방법을 보여줍니다. [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)를 사용하여 웹앱을 만들고 ZipDeploy를 사용하여 웹앱에 샘플 Node.js 코드를 배포합니다.

![Azure에서 실행되는 샘플 앱](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

Mac, Windows 또는 Linux 컴퓨터를 사용하여 여기서 설명하는 단계를 수행하면 됩니다. 필수 구성 요소가 설치된 후 단계를 완료하는 데는 약 5분 정도 걸립니다.   

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 다음이 필요합니다.

* <a href="https://nodejs.org/" target="_blank">Node.js 및 NPM 설치</a>

## <a name="download-the-sample"></a>샘플 다운로드

[https://github.com/Azure-Samples/nodejs-docs-hello-world/archive/master.zip](https://github.com/Azure-Samples/nodejs-docs-hello-world/archive/master.zip)에서 Node.js 프로젝트 샘플을 다운로드하고 ZIP 보관 파일을 추출합니다.

_index.js_를 열어서 다음 줄을 찾습니다.

```javascript
const port = process.env.PORT || 1337;
```

App Service가 process.env.PORT를 애플리케이션에 삽입하기 때문에 코드가 변수를 사용하여 수신할 포트를 파악합니다. 

터미널 창에서 샘플 Node.js 프로젝트의 **루트 디렉터리**(_index.js_가 포함된 디렉터리)로 이동합니다.

## <a name="run-the-app-locally"></a>로컬에서 앱 실행하기

애플리케이션을 로컬로 실행하여 Azure에 애플리케이션을 배포할 때 표시되는 모양을 확인합니다. 터미널 창을 열고 `npm start` 스크립트를 사용하여 기본 제공 Node.js HTTP 서버를 시작합니다.

```bash
npm start
```

웹 브라우저를 열고 `http://localhost:1337`의 샘플 앱으로 이동합니다.

이 페이지에 표시된 샘플 앱에서 **Hello World** 메시지가 표시됩니다.

![로컬로 실행되는 샘플 앱](media/app-service-web-get-started-nodejs-poc/localhost-hello-world-in-browser.png)

터미널 창에서 **Ctrl+C**를 눌러 웹 서버를 종료합니다.

> [!NOTE]
> Azure App Service에서 앱은 [iisnode](https://github.com/Azure/iisnode)를 사용하여 IIS에서 실행됩니다. iisnode를 통해 앱을 실행하도록 설정하려면 루트 앱 디렉터리 web.config 파일이 있어야 합니다. 이 파일은 IIS에서 읽을 수 있으며, iisnode 관련 설정은 [iisnode GitHub 리포지토리](https://github.com/Azure/iisnode/blob/master/src/samples/configuration/web.config)에 설명되어 있습니다.

## <a name="create-a-project-zip-file"></a>프로젝트 ZIP 파일 만들기

여전히 샘플 프로젝트의 **루트 디렉터리**(_index.js_가 포함된 디렉터리)에 있는지 확인합니다. 프로젝트의 모든 것에 대한 ZIP 아카이브를 만듭니다. 다음 명령은 터미널의 기본 도구를 사용합니다.

```
# Bash
zip -r myAppFiles.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath myAppFiles.zip
```

나중에 이 ZIP 파일을 Azure에 업로드하고 이를 App Service에 배포합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-scus.md)] 

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-scus.md)] 

## <a name="create-a-web-app"></a>웹앱 만들기

Cloud Shell에서 [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) 명령을 사용하여 `myAppServicePlan` App Service 계획에 웹앱을 만듭니다. 

다음 예에서 `<app_name>`을 전역적으로 고유한 앱 이름으로 바꿉니다(유효한 문자는 `a-z`, `0-9` 및 `-`).

```azurecli-interactive
# Bash and Powershell
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name>
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
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="set-nodejs-runtime"></a>Node.js 런타임 설정

노드 런타임을 10.14.1로 설정합니다. 지원되는 모든 런타임을 보려면 [`az webapp list-runtimes`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes)를 실행합니다.

```azurecli-interactive
# Bash and Powershell
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITE_NODE_DEFAULT_VERSION=10.14.1
```

새로 만든 웹앱으로 이동합니다. `<app_name>`을 고유한 앱 이름으로 바꿉니다.

```bash
http://<app_name>.azurewebsites.net
```

새로운 웹앱은 다음과 같아야 합니다.

![빈 웹앱 페이지](media/app-service-web-get-started-nodejs-poc/app-service-web-service-created.png)

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]

## <a name="browse-to-the-app"></a>앱으로 이동

웹 브라우저를 사용하여 배포된 애플리케이션으로 이동합니다.

```
http://<app_name>.azurewebsites.net
```

Node.js 샘플 코드는 Azure App Service 웹앱에서 실행 중입니다.

![Azure에서 실행되는 샘플 앱](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

**축하합니다.** App Service에 첫 번째 Node.js 앱을 배포했습니다.

## <a name="update-and-redeploy-the-code"></a>코드 업데이트 및 다시 배포

텍스트 편집기를 사용하여 Node.js 앱에서 `index.js` 파일을 열고 `response.end`에 대한 호출에서 텍스트를 약간 변경합니다.

```javascript
response.end("Hello Azure!");
```

로컬 터미널 창에서 애플리케이션의 **루트 디렉터리**(_index.js_가 포함된 디렉터리)로 이동하고, 업데이트된 프로젝트에 대한 새 ZIP 파일을 만듭니다.

```azurecli-interactive
# Bash
zip -r myUpdatedAppFiles.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath myUpdatedAppFiles.zip
```

[ZIP 파일 배포](#deploy-zip-file)와 동일한 단계를 사용하여 새로운 ZIP 파일을 App Service에 배포합니다.

**앱으로 이동** 단계에서 연 브라우저 창으로 다시 전환하고 페이지를 새로 고칩니다.

![Azure에서 실행되는 업데이트된 샘플 앱](media/app-service-web-get-started-nodejs-poc/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>새 Azure 앱 관리

만든 웹앱을 관리하려면 <a href="https://portal.azure.com" target="_blank">Azure Portal</a>로 이동합니다.

왼쪽 메뉴에서 **App Services**를 클릭한 다음, Azure 앱의 이름을 클릭합니다.

![Azure 앱에 대한 포털 탐색](./media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-list.png)

웹앱의 개요 페이지가 표시됩니다. 여기에서 찾아보기, 중지, 시작, 다시 시작, 삭제와 같은 기본 관리 작업을 수행할 수 있습니다. 

![Azure Portal의 App Service 페이지](media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-detail.png)

왼쪽 메뉴로 앱 구성을 위한 여러가지 페이지를 볼 수 있습니다. 

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [MongoDB를 사용하는 Node.js](app-service-web-tutorial-nodejs-mongodb-app.md)
