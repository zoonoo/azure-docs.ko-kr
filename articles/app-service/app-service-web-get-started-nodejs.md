---
title: '빠른 시작: Node.js 웹앱 만들기'
description: 몇 분 안에 첫 번째 Node.js Hello World를 Azure App Service에 배포합니다. App Service에 배포하는 여러 가지 방법 중 하나인 Visual Studio Code를 사용하여 배포합니다.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 03/04/2020
ms.custom: mvc, devcenter, seodec18
ms.openlocfilehash: df1e0809c0122eae593543cd193a6477b03d0938
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2020
ms.locfileid: "82690786"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Azure에서 Node.js 웹앱 만들기 

Visual Studio Code를 사용하여 Node.js/Express 앱을 로컬로 만든 다음, 앱을 클라우드에 배포하여 Azure App Service를 시작합니다. 무료 App Service 계층을 사용하므로 이 빠른 시작을 완료하는 데 비용이 들지 않습니다.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension).
- [Node.js 및 npm](https://nodejs.org). `node --version` 명령을 실행하여 Node.js가 설치되어 있는지 확인합니다.
- [Visual Studio Code](https://code.visualstudio.com/)
- Visual Studio Code용 [Azure App Service 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)

## <a name="clone-and-run-a-local-nodejs-application"></a>로컬 Node.js 애플리케이션 복제 및 실행

1. 로컬 컴퓨터에서 터미널을 열고 샘플 리포지토리를 복제합니다.

    ```bash
    git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
    ```

1. 새 앱 폴더로 이동합니다.

    ```bash
    cd nodejs-docs-hello-world
    ```

1. 앱을 시작하여 로컬로 테스트합니다.

    ```bash
    npm start
    ```
    
1. 브라우저를 열고 `http://localhost:1337`로 이동합니다. 브라우저에서 "Hello World!"가 표시됩니다.

1. 터미널에서 **Ctrl**+**C**를 눌러 서버를 중지합니다.

> [!div class="nextstepaction"]
> [문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-the-app-to-azure"></a>Azure에 앱 배포

이 섹션에서는 VS Code와 Azure App Service 확장을 사용하여 Node.js 앱을 Azure에 배포합니다.

1. 터미널에서 *nodejs-docs-hello-world* 폴더에 있는지 확인한 다음, 다음 명령을 사용하여 Visual Studio Code를 시작합니다.

    ```bash
    code .
    ```

1. VS Code 작업 막대에서 Azure 로고를 선택하여 **AZURE APP SERVICE** 탐색기를 표시합니다. **Azure에 로그인...** 을 선택하고 지침을 따릅니다. (오류가 발생하면 아래의 [Azure 로그인 문제 해결](#troubleshooting-azure-sign-in)을 참조하세요.) 로그인하면 탐색기에 Azure 구독 이름이 표시됩니다.

    ![Azure에 로그인](containers/media/quickstart-nodejs/sign-in.png)

1. VS Code의 **AZURE APP SERVICE** 탐색기에서 파란색 위쪽 화살표 아이콘을 선택하여 앱을 Azure에 배포합니다. ('웹앱에 배포'를 입력하고 **Azure App Service: 웹앱에 배포**를 선택하여 **명령 팔레트**(**Ctrl**+**Shift**+**P**)에서 동일한 명령을 호출할 수도 있습니다.)

    ![웹앱에 배포](containers/media/quickstart-nodejs/deploy.png)
        
1. *nodejs-docs-hello-world* 폴더를 선택합니다.

1. 배포하려는 운영 체제에 따라 만들기 옵션을 선택합니다.

    - Linux: **새 웹앱 만들기**를 선택합니다.
    - Windows: **새 웹앱 만들기... 고급**을 선택합니다.

1. 웹앱에 대해 전역적으로 고유한 이름을 입력하고, **Enter** 키를 누릅니다. 이름은 모든 Azure에서 고유해야 하며, 영숫자 문자('A-Z', 'a-z' 및 '0-9')와 하이픈('-')만 사용해야 합니다.

1. Linux를 대상으로 하는 경우 메시지가 표시되면 Node.js 버전을 선택합니다. **LTS** 버전을 권장합니다.

1. Windows를 대상으로 하는 경우 추가 프롬프트를 따릅니다.
    1. **새 리소스 그룹 만들기**를 선택한 다음, 리소스 그룹 이름(예: `AppServiceQS-rg`)을 입력합니다.
    1. 운영 체제에 대해 **Windows**를 선택합니다.
    1. **새 App Service 계획 만들기**를 선택하고, 계획 이름(예: `AppServiceQS-plan`)을 입력한 다음, 가격 책정 계층에 대해 **F1 체험**을 선택합니다.
    1. Application Insights에 대한 메시지가 표시되면 **지금 건너뛰기**를 선택합니다.
    1. 액세스하려는 사용자 근처의 지역 또는 근처의 리소스를 선택합니다.

1. 모든 프롬프트에 응답하면 VS Code의 알림 팝업에 앱용으로 만들어지는 Azure 리소스가 표시됩니다.

    Linux에 배포하는 경우 대상 Linux 서버에서 `npm install`을 실행하도록 구성을 업데이트하라는 메시지가 표시되면 **예**를 선택합니다.

    ![대상 Linux 서버에서 구성을 업데이트하도록 요구하는 메시지](containers/media/quickstart-nodejs/server-build.png)

1. **항상 "nodejs-docs-hello-world" 작업 영역을 (앱 이름)에 배포합니다.** 라는 메시지가 표시되면 **예**를 선택합니다. **예**를 선택하면 VS Code에서 자동으로 후속 배포에서 동일한 App Service Web App을 대상으로 지정하도록 지시합니다.

1. Linux에 배포하는 경우 배포가 완료되면 프롬프트에서 **웹 사이트 찾아보기**를 선택하여 새로 배포된 웹앱을 확인합니다. 브라우저에서 "Hello World!"가 표시됩니다.

1. Windows로 배포하는 경우 먼저 웹앱의 Node.js 버전 번호를 설정해야 합니다.

    1. VS Code에서 새 앱 서비스의 노드를 펼치고, 마우스 오른쪽 단추로 **애플리케이션 설정**을 클릭하고, **새 설정 추가...** 를 선택합니다.

        ![앱 설정 추가 명령](containers/media/quickstart-nodejs/add-setting.png)

    1. 설정 키에 대해 `WEBSITE_NODE_DEFAULT_VERSION`을 입력합니다.
    1. 설정 값에 대해 `10.15.2`를 입력합니다.
    1. 마우스 오른쪽 단추로 앱 서비스의 노드를 클릭하고, **다시 시작**을 선택합니다.

        ![앱 서비스 다시 시작 명령](containers/media/quickstart-nodejs/restart.png)

    1. 마우스 오른쪽 단추로 앱 서비스의 노드를 한 번 더 클릭하고, **웹 사이트 찾아보기**를 선택합니다.

> [!div class="nextstepaction"]
> [문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="troubleshooting-azure-sign-in"></a>Azure 로그인 문제 해결

Azure에 로그인할 때 **"이름이 [구독 ID]인 구독을 찾을 수 없습니다"** 라는 오류가 표시되는 경우 백그라운드에서 프록시를 사용하고 있고 Azure API에 연결할 수 없기 때문일 수 있습니다. `export`를 사용하여 터미널의 프록시 정보를 사용하여 `HTTP_PROXY` 및 `HTTPS_PROXY` 환경 변수를 구성합니다.

```bash
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

환경 변수를 설정해도 문제가 해결되지 않으면 위에 있는 **문제가 발생했습니다.** 단추를 선택하여 문의하세요.

### <a name="update-the-app"></a>앱 업데이트

VS Code에서 편집하고, 파일을 저장한 다음, 새 앱을 만드는 대신 기존 앱을 선택하기 전과 동일한 프로세스를 사용하여 이 앱에 대한 변경 내용을 배포할 수 있습니다.

## <a name="viewing-logs"></a>로그 보기

VS Code 출력 창에서 직접 앱의 로그 출력(`console.log`에 대한 호출)을 볼 수 있습니다.

1. **AZURE APP SERVICE** 탐색기에서 마우스 오른쪽 단추로 앱 노드를 클릭하고, **스트리밍 로그 시작**을 선택합니다.

    ![스트리밍 로그 시작](containers/media/quickstart-nodejs/view-logs.png)

1. 메시지가 표시되면 로깅을 사용하도록 설정하고 애플리케이션을 다시 시작합니다. 애플리케이션이 다시 시작되면 VS Code 출력 창이 열려 로그 스트림에 연결됩니다. 

    ![로깅 사용 및 다시 시작](containers/media/quickstart-nodejs/enable-restart.png)

1. 몇 초 후 출력 창에 로그 스트리밍 서비스에 연결되었다는 메시지가 표시됩니다. 브라우저에서 페이지를 새로 고쳐 더 많은 출력 활동을 생성할 수 있습니다.

    <pre>
    Connecting to log stream...
    2020-03-04T19:29:44  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours.
    Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).    
    </pre>

> [!div class="nextstepaction"]
> [문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>다음 단계

축하합니다! 이 빠른 시작을 성공적으로 완료했습니다!

다음으로, 다른 Azure 확장을 확인합니다.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Docker 도구](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Azure CLI 도구](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Azure Resource Manager 도구](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

또는 [Azure용 Node 팩](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) 확장 팩을 설치하여 모두 가져옵니다.
