---
title: '빠른 시작: Node.js 웹앱 만들기'
description: 몇 분 안에 첫 번째 Node.js Hello World를 Azure App Service에 배포합니다.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: mvc, devcenter, seodec18
zone_pivot_groups: app-service-platform-windows-linux
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 7cd9add699d9bd4a3eff9cdcf1e65af0d754b70a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101747556"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Azure에서 Node.js 웹앱 만들기

Windows Server 2019 <abbr title="웹 애플리케이션, REST API 및 모바일 백 엔드를 호스트하는 HTTP 기반 서비스입니다.">Azure App Service</abbr> Visual Studio Code를 사용하여 로컬에 Node.js/Express 앱을 만든 다음, Azure 클라우드에 앱을 배포합니다. 다음을 사용하기 때문에 <abbr title="Azure App Service에서 다른 고객의 앱을 포함하여 다른 앱과 동일한 VM에서 앱이 실행되는 기본 계층입니다. 이 계층은 개발 및 테스트용입니다.">체험 계층</abbr>빠른 시작을 완료하는 데 비용이 들지 않습니다.

## <a name="1-prepare-your-environment"></a>1. 환경 준비

- 활성 상태인 Azure 계정 <abbr title="Azure 구독은 Azure에서 리소스를 프로비저닝하는 데 사용되는 논리적 컨테이너입니다. VM(가상 머신), 데이터베이스 등 모든 리소스에 대한 세부 정보를 보관합니다.">subscription</abbr>. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension).
- <a href="https://git-scm.com/" target="_blank">Git 설치</a>
- [Node.js 및 npm](https://nodejs.org). `node --version` 명령을 실행하여 Node.js가 설치되어 있는지 확인합니다.
- [Visual Studio Code](https://code.visualstudio.com/)
- Visual Studio Code용 [Azure App Service 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)

[문제 보고](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)




<br>
<hr/>

## <a name="2-clone-and-run-a-local-nodejs-application"></a>2. 로컬 Node.js 애플리케이션 복제 및 실행

1. 로컬 컴퓨터에서 터미널을 열고 샘플 리포지토리를 복제합니다.

    ```bash
    git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
    ```

1. 새 앱 폴더로 이동합니다.

    ```bash
    cd nodejs-docs-hello-world
    ```

1. 종속성을 설치합니다.

    ```bash
    npm install
    ```

1. 앱을 시작하여 로컬로 테스트합니다.

    ```bash
    npm start
    ```
    
1. 브라우저를 열고 `http://localhost:1337`로 이동합니다. 브라우저에서 "Hello World!"가 표시됩니다.

1. 터미널에서 <kbd>Ctrl</kbd> + <kbd>C</kbd>를 눌러 서버를 중지합니다.

[문제 보고](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)


<br>
<hr/>




<!-- VS Code extension works differently for Windows/Linus - Step 3 -->

::: zone pivot="platform-windows"  

[!INCLUDE [Windows](./includes/quickstart-nodejs-uiex-windows.md)]


::: zone-end

::: zone pivot="platform-linux"  

[!INCLUDE [Windows](./includes/quickstart-nodejs-uiex-linux.md)]

::: zone-end


## <a name="4-viewing-logs-from-visual-studio-code"></a>4. Visual Studio Code에서 로그 보기

보기 <abbr title="앱에서 `console.log`에 대한 모든 호출은 Visual Studio Code의 출력 창에 표시됩니다.">log</abbr> 실행 중인 App Service 앱의

1. **AZURE APP SERVICE** 탐색기에서 앱을 찾고 앱 이름을 마우스 오른쪽 단추로 클릭한 후 **스트리밍 로그 시작** 을 선택합니다.

1. Visual Studio Code 출력 창이 열립니다.

    ![스트리밍 로그 보기](./media/quickstart-nodejs/view-logs.png)

    :::image type="content" source="./media/quickstart-nodejs/enable-restart.png" alt-text="[예] 단추를 선택한 상태에서 파일 로깅을 활성화하고 웹앱을 다시 시작하는 VS Code 프롬프트의 스크린샷.":::

1. 몇 초 후에 로그 스트리밍 서비스에 연결되었다는 메시지가 표시됩니다. 
1. 페이지를 몇 번 새로 고쳐 더 많은 작업을 볼 수 있습니다.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    2020-09-20 20:37:39.574 INFO  - Initiating warmup request to container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node
    2020-09-20 20:37:55.011 INFO  - Waiting for response to warmup request for container msdocs-vscode-node_2_00ac292a. Elapsed time = 15.4373071 sec
    2020-09-20 20:38:08.233 INFO  - Container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node initialized successfully and is ready to serve requests.
    2020-09-20T20:38:21  Startup Request, url: /Default.cshtml, method: GET, type: request, pid: 61,1,7, SCM_SKIP_SSL_VALIDATION: 0, SCM_BIN_PATH: /opt/Kudu/bin, ScmType: None
    </pre>

<br>

[문제 보고](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)

<br>
<hr/>

## <a name="5-clean-up-resources"></a>5. 리소스 정리

**AZURE APP SERVICE** 탐색기에서 앱을 찾고 앱 이름을 마우스 오른쪽 단추로 클릭한 후 **삭제** 를 선택합니다. 

:::image type="content" source="./media/quickstart-nodejs/delete-resource-ieux.png" alt-text="`**`AZURE APP SERVICE`**` 탐색기에서 앱을 찾고 앱 이름을 마우스 오른쪽 단추로 클릭한 후 `삭제`를 선택합니다.":::

## <a name="next-steps"></a>다음 단계

축하합니다! 이 빠른 시작을 성공적으로 완료했습니다! 동일한 프로세스를 사용하여 새 앱을 만들지 않고 기존 앱을 선택하여 변경 내용을 해당 앱에 배포할 수 있습니다.

다음으로, 다른 Azure 확장을 확인합니다.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Docker 도구](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Azure CLI 도구](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Azure Resource Manager 도구](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

또는 [Azure용 Node 팩](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) 확장 팩을 설치하여 모두 가져옵니다.