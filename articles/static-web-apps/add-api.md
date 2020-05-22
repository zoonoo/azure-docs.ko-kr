---
title: Azure Functions를 사용하여 Azure Static Web Apps에 API 추가
description: Azure Functions를 사용하여 정적 웹앱에 서버리스 API를 추가하여 Azure Static Web Apps를 시작합니다.
services: static-web-apps
author: manekinekko
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/08/2020
ms.author: wachegha
ms.openlocfilehash: a6aee5c8049e03a43c547f419f6c6646617e651c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596152"
---
# <a name="add-an-api-to-azure-static-web-apps-preview-with-azure-functions"></a>Azure Functions를 사용하여 Azure Static Web Apps 미리 보기에 API 추가

Azure Functions와의 통합을 통해 Azure Static Web Apps에 서버리스 API를 추가할 수 있습니다. 이 문서에서는 Azure Static Web Apps 사이트에 API를 추가하고 배포하는 방법을 보여 줍니다.

API 경로를 보호하는 방법에 대한 자세한 내용은 [라우팅 가이드](routes.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정 [체험 계정을 만듭니다](https://azure.microsoft.com/free).
- [Visual Studio Code](https://code.visualstudio.com/)
- Visual Studio Code용 [Azure Functions 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
- [Live Server Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) 확장

## <a name="create-a-git-repository"></a>git 리포지토리 만들기 

다음 단계에서는 새 리포지토리를 만들고 파일을 컴퓨터에 복제하는 방법을 보여 줍니다.

1. https://github.com/staticwebdev/vanilla-basic/generate 로 이동하여 새 리포지토리를 만듭니다.
1. _리포지토리 이름_ 상자에 **my-vanilla-api**를 입력합니다.
1. **템플릿에서 리포지토리 만들기**를 클릭합니다.

   :::image type="content" source="media/add-api/create-repository.png" alt-text="vanilla에서 새 리포지토리 만들기-기본":::

프로젝트를 만든 후 Visual Studio Code를 사용하여 Git 리포지토리를 복제할 수 있습니다.

1. **F1** 키를 눌러 명령 팔레트에서 명령을 엽니다.
1. URL을 _Git: 복제_ 프롬프트에 붙여넣고, **Enter** 키를 누릅니다.

   :::image type="content" source="media/add-api/vscode-git-0.png" alt-text="Visual Studio Code를 사용하여 GitHub 프로젝트 복제":::

   :::image type="content" source="media/add-api/github-clone-url.png" alt-text="Visual Studio Code를 사용하여 GitHub 프로젝트 복제":::


## <a name="create-your-local-project"></a>로컬 프로젝트 만들기

이 섹션에서는 Visual Studio Code를 사용하여 로컬 Azure Functions 프로젝트를 만듭니다. 나중에 함수 앱을 Azure에 게시합니다.

1. _my-vanilla-api_ 프로젝트 내에서 **api**라는 하위 폴더를 만듭니다.

   > [!NOTE]
   > 이 폴더에 이름을 지정할 수 있습니다. 이 예에서는 `api`를 사용합니다. 

2. **F1** 키를 눌러 명령 팔레트를 엽니다.
3. **Azure Functions: 새 프로젝트 만들기...** 를 입력합니다.
4. **Enter** 키를 누릅니다.
5. **찾아보기**를 선택합니다.
6. 프로젝트 작업 영역에 대한 디렉터리로 **api** 폴더를 선택합니다.
7. **선택**을 선택합니다.

   :::image type="content" source="media/add-api/create-azure-functions-vscode-1.png" alt-text="Visual Studio Code를 사용하여 새 Azure Functions 만들기":::

8. 프롬프트에서 다음 정보를 제공합니다.

    - _함수 프로젝트에 대한 언어 선택_: **JavaScript**를 선택합니다.
    - _프로젝트의 첫 번째 함수에 대한 템플릿 선택_: **HTTP 트리거**를 선택합니다.
    - _함수 이름 입력_: **GetMessage**를 입력합니다.
    - _권한 부여 수준_: 누구나 함수 엔드포인트를 호출할 수 있도록 하는 **익명**을 선택합니다.
        - 권한 부여 수준에 대해 알아보려면 [권한 부여 키](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)를 참조하세요.

9. Visual Studio Code는 이 정보를 사용하여 HTTP 트리거를 통해 Azure Functions 프로젝트를 생성합니다.
    - Visual Studio Code의 탐색기 창에서 로컬 프로젝트 파일을 볼 수 있습니다.
    - 생성된 파일에 대한 자세한 내용은 [생성된 프로젝트 파일](https://docs.microsoft.com/azure/azure-functions/functions-develop-vs-code#generated-project-files)을 참조하세요.

10. 이제 앱은 이 예제와 비슷한 프로젝트 구조를 갖게 됩니다.

    ```files
    ├── api
    │   ├── GetMessage
    │   │   ├── function.json
    │   │   ├── index.js
    │   │   └── sample.dat
    │   ├── host.json
    │   ├── local.settings.json
    │   ├── package.json
    │   └── proxies.json
    ├── index.html
    ├── readme.md
    └── styles.css
    ```

11. 다음으로 다음 코드를 사용하여 _api/GetMessage/index.js_에서 `GetMessage` 함수를 업데이트합니다.

    ```JavaScript
    module.exports = async function (context, req) {
      context.res = {
        body: { 
          text: "Hello from the API" 
        }
      };
    };
    ```

12. 다음 설정을 사용하여 `api/GetMessage/function.json`의 `GetMessage` 구성을 업데이트합니다.

    ```json
    {
      "bindings": [
        {
          "authLevel": "anonymous",
          "type": "httpTrigger",
          "direction": "in",
          "name": "req",
          "methods": [
            "get"
          ],
          "route": "message"
        },
        {
          "type": "http",
          "direction": "out",
          "name": "res"
        }
      ]
    }
    ```
    
위의 설정을 사용하는 경우 API 엔드포인트는 다음과 같습니다.

- 함수에 HTTP 요청이 실행되어 트리거됨
- 인증 상태에 관계 없이 모든 요청에 사용 가능
- _/api/message_ 경로를 통해 노출됨

## <a name="run-the-function-locally"></a>로컬에서 함수 실행

Visual Studio Code는 [Azure Functions Core Tools](https://docs.microsoft.com/azure/azure-functions/functions-run-local)와 통합되어 Azure에 게시하기 전에 로컬 개발 컴퓨터에서 이 프로젝트를 실행할 수 있습니다.

1. **F5** 키를 눌러 함수를 실행하고 함수 앱을 시작합니다. 핵심 도구 출력이 _터미널_ 패널에 표시됩니다.

2. Azure Functions Core Tools를 아직 설치하지 않은 경우 프롬프트에서 **설치**를 선택합니다.

    핵심 도구가 설치되면 _터미널_ 패널에서 앱이 시작됩니다. 출력의 일부로 로컬에서 실행 중인 HTTP 트리거 함수의 URL 엔드포인트를 볼 수 있습니다.

    :::image type="content" source="media/add-api/create-azure-functions-vscode-2.png" alt-text="Visual Studio Code를 사용하여 새 Azure Functions 만들기":::

3. 핵심 도구를 실행 중인 상태에서 다음 URL로 이동하여 `GET` 요청을 실행합니다.

   <http://localhost:7071/api/message>

   브라우저에서 다음과 같이 표시되는 응답이 반환됩니다.

   :::image type="content" source="media/add-api/create-azure-functions-vscode-3.png" alt-text="Visual Studio Code를 사용하여 새 Azure Functions 만들기":::

함수가 올바르게 실행되는지 확인한 후 JavaScript 애플리케이션에서 API를 호출할 수 있습니다.

### <a name="call-the-api-from-the-application"></a>애플리케이션에서 API 호출

[!INCLUDE [static-web-apps-local-proxy](../../includes/static-web-apps-local-proxy.md)]


#### <a name="update-files-to-access-the-api"></a>API에 액세스하도록 파일 업데이트

1. 다음으로 _index.html_ 파일의 콘텐츠를 다음 코드로 업데이트하여 API 함수에서 텍스트를 인출하고 화면에 표시합니다.

   ```html
   <!DOCTYPE html>
   <html lang="en">

   <head>
     <meta charset="UTF-8">
     <meta name="viewport" content="width=device-width, initial-scale=1.0">
     <link rel="stylesheet" href="styles.css">
     <title>Vanilla JavaScript App</title>
   </head>

   <body>
     <main>
       <h1>Vanilla JavaScript App</h1>
       <p>Loading message from the API: <b id="name">...</b></p>
     </main>

     <script>
       (async function() {
         let { text } = await( await fetch(`/api/message`)).json();
         document.querySelector('#name').textContent = text;
       }())
     </script>
   </body>

   </html>
   ```

   핵심 도구를 실행 중인 상태에서 [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) Visual Studio Code 확장을 사용하여 _index.html_ 파일을 제공하고 브라우저에서 이 파일을 엽니다. 

2. **F1** 키를 누르고 **Live Server: Live Server로 열기**를 선택합니다.

   :::image type="content" source="media/add-api/create-azure-functions-vscode-4.png" alt-text="Visual Studio Code를 사용하여 새 Azure Functions 만들기":::

   > [!NOTE]
   > 다른 HTTP 서버 또는 프록시를 사용하여 `index.html` 파일을 제공할 수 있습니다. `file:///`에서 `index.html`에 대한 액세스가 작동하지 않습니다.

### <a name="commit-and-push-your-changes-to-github"></a>GitHub에 변경 내용을 커밋 및 푸시

Visual Studio Code를 사용하여 변경 내용을 원격 git 리포지토리에 커밋하고 푸시합니다.

1. **F1** 키를 눌러 명령 팔레트 열기
1. **Git: 모두 커밋**을 입력합니다.
1. 커밋 메시지를 추가합니다.
1. **Git: push**를 입력합니다.

## <a name="create-a-static-web-app"></a>정적 웹앱 만들기

:::image type="content" source="media/add-api/create-static-app-on-azure-portal-1.png" alt-text="Azure Portal에서 정적 앱 만들기 - 화면 1":::

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
1. **리소스 만들기**를 클릭합니다.
1. **Static Web Apps**를 검색합니다.
1. **Static Web Apps(미리 보기)** 를 클릭합니다.
1. **만들기**
1. _Azure 구독_을 선택합니다.
1. 새 _리소스 그룹_을 선택하거나 만듭니다.
1. 앱 이름을 **my-vanilla-api**로 지정합니다.
1. 가장 가까운 _지역_을 선택합니다.
1. **무료** _SKU_를 선택합니다.
1. **GitHub로 로그인** 단추를 클릭하고 GitHub로 인증합니다.
1. 원하는 _조직_을 선택합니다.
1. _리포지토리_ 드롭다운에서 **my-vanilla-api**를 선택합니다.
1. _분기_ 드롭다운에서 **마스터**를 선택합니다.
1. **다음: 빌드 >** 단추를 클릭하여 빌드 구성을 편집합니다.

:::image type="content" source="media/add-api/create-static-app-on-azure-portal-2.png" alt-text="Azure Portal에서 정적 앱 만들기 - 화면 2":::

다음으로 빌드 세부 정보를 추가합니다.

1. _앱 위치_에 대해 **./** 를 입력합니다.

1. _API 위치_ 상자에 **api**를 입력합니다.

   이전 단계에서 만든 API 폴더의 이름입니다.
   
1. _앱 아티팩트 위치_에서 기본값을 지워 상자를 비워 둡니다.

1. **검토 + 만들기**를 클릭합니다.

| 설정 | Description             | 필수 |
| -------- | ----------------------- |
|  앱 위치 | 정적 애플리케이션 소스 코드의 위치입니다. | 예 |
|  API 위치 | API 백 엔드의 위치입니다. 이는 Azure Functions 앱 프로젝트의 루트 폴더를 가리킵니다. | 예 |
|  앱 아티팩트 위치 | 빌드 출력 폴더의 위치입니다. 일부 프런트 엔드 JavaScript 프레임워크에는 프로덕션 파일을 폴더에 배치하는 빌드 단계가 있습니다. 이 설정은 빌드 출력 폴더를 가리킵니다. | 예 |

:::image type="content" source="media/add-api/create-static-app-on-azure-portal-3.png" alt-text="Azure Portal에서 정적 앱 만들기 - 화면 3":::

1. **만들기**
1. 배포가 완료될 때까지 대기합니다(몇 분 정도 걸릴 수 있음).
1. `https://github.com/<YOUR_GITHUB_ACCOUNT>/my-vanilla-api/actions?query=workflow%3A"Azure+Pages+CI%2FCD"`로 이동합니다.
1. 성공적으로 빌드되었는지 확인합니다.

:::image type="content" source="media/add-api/github-workflow-0.png" alt-text="GitHub 워크플로":::

배포된 API는 `https://<STATIC_APP_NAME>.azurestaticapps.net/api/<FUNCTION_OR_ROUTE_NAME>`에서 제공됩니다.

:::image type="content" source="media/add-api/github-workflow-1.png" alt-text="GitHub 워크플로":::

Azure Portal에서 애플리케이션 URL을 찾을 수도 있습니다.

:::image type="content" source="media/add-api/static-app-url-from-portal.png" alt-text="Azure Portal에서 정적 앱 URL에 액세스":::

또는 `https://<STATIC_APP_NAME>.azurestaticapps.net`에서 Azure Static Web App에 직접 액세스하고 브라우저에서 결과를 확인할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 나중에 사용하기 위해 유지하지 않으려는 경우 다음 단계를 사용하여 Azure Static Web App 및 관련 리소스를 삭제할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
1. 위쪽 검색 창에 **리소스 그룹**을 입력합니다.
1. **리소스 그룹**을 클릭합니다. 
1. **myResourceGroup**을 선택합니다.
1. _myResourceGroup_ 페이지에서 나열된 리소스가 삭제하려는 리소스인지 확인합니다.
1. **삭제** 선택
1. 텍스트 상자에 **myResourceGroup**을 입력합니다.
1. **삭제**를 선택합니다.


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱 설정 구성](./application-settings.md)
