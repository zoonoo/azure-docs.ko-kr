---
title: Azure Static Web Apps에 대한 로컬 개발 설정
description: Azure Static Web Apps에 대한 로컬 개발 환경을 설정하는 방법을 알아봅니다.
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/08/2020
ms.author: buhollan
ms.openlocfilehash: a7215790a7f052227b08f51dcd7ad5dd337bb4e9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84259272"
---
# <a name="set-up-local-development-for-azure-static-web-apps-preview"></a>Azure Static Web Apps에 대한 로컬 개발 설정 미리 보기

Azure Static Web Apps 인스턴스는 두 가지 유형의 애플리케이션으로 구성됩니다. 첫 번째는 정적 콘텐츠에 대한 웹앱입니다. 웹앱은 종종 프런트 엔드 프레임워크 및 라이브러리 또는 정적 사이트 생성기를 사용하여 생성됩니다. 두 번째 측면은 다양한 백 엔드 개발 환경을 제공하는 Azure Functions 앱인 API입니다.

클라우드에서 실행하는 경우 Azure Static Web Apps는 CORS 구성 없이 웹앱에서 Azure Functions 앱으로의 `api` 경로에 요청을 원활하게 매핑합니다. 로컬에서 이 동작을 모방하도록 애플리케이션을 구성해야 합니다.

이 문서에서는 다음 개념을 포함하여 로컬 개발에 대한 권장 모범 사례를 보여 줍니다.

- 정적 콘텐츠에 대한 웹앱 설정
- 애플리케이션의 API에 대한 Azure Functions 앱 구성
- 애플리케이션 디버깅 및 실행
- 앱의 파일 및 폴더 구조에 대한 모범 사례

## <a name="prerequisites"></a>사전 요구 사항

- [Visual Studio Code](https://code.visualstudio.com/)
- Visual Studio Code용 [Azure Functions 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
- Visual Studio Code용 [Live Server 확장](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer)
  - 프런트 엔드 JavaScript 프레임워크 또는 정적 사이트 생성기의 CLI를 사용하고 있지 않은 경우에만 필요합니다.

## <a name="run-projects-locally"></a>로컬로 프로젝트 실행

Azure Static Web App을 로컬로 실행하려면 프로젝트에 API가 포함되어 있는지 여부에 따라 3개의 프로세스가 필요합니다.

- 로컬 웹 서버 실행
- API 실행
- 웹 프로젝트를 API에 연결

웹 사이트가 빌드되는 방식에 따라 로컬 웹 서버는 브라우저에서 애플리케이션을 실행하는 데 필요할 수도 있고 그렇지 않을 수도 있습니다. 프런트 엔드 JavaScript 프레임워크 및 정적 사이트 생성기를 사용하는 경우 이 기능은 해당 CLI(명령줄 인터페이스)에 기본 제공됩니다. 다음 링크는 프레임워크, 라이브러리 및 생성기 선택 항목에 대한 CLI 참조를 가리킵니다.

### <a name="javascript-frameworks-and-libraries"></a>JavaScript 프레임워크 및 라이브러리

- [Angular CLI](https://angular.io/cli)
- [Vue CLI](https://cli.vuejs.org/guide/creating-a-project.html)
- [React CLI](https://create-react-app.dev/)

### <a name="static-site-generators"></a>정적 사이트 생성기

- [Gatsby CLI](https://www.gatsbyjs.org/docs/gatsby-cli/)
- [Hugo](https://gohugo.io/getting-started/quick-start/)
- [Jekyll](https://jekyllrb.com/docs/usage/)

CLI 도구를 사용하여 사이트를 제공하는 경우 [API 실행](#run-api-locally) 섹션으로 건너뛸 수 있습니다.

### <a name="running-a-local-web-server-with-live-server"></a>Live Server를 사용하여 로컬 웹 서버 실행

Visual Studio Code용 Live Server 확장은 정적 콘텐츠를 제공하는 로컬 개발 웹 서버를 제공합니다.

#### <a name="create-a-repository"></a>리포지토리 만들기

1. GitHub에 로그인 되어 있는지 확인 하 고 [https://github.com/staticwebdev/vanilla-api/generate](https://github.com/staticwebdev/vanilla-api/generate) 이 템플릿을 사용 하 여 **바닐라**라는 새 GitHub 프로젝트를 탐색 하 고 만듭니다.

    :::image type="content" source="media/local-development/vanilla-api.png" alt-text="GitHub 새 리포지토리 창":::

1. Visual Studio Code를 엽니다.

1. **F1** 키를 눌러 명령 팔레트를 엽니다.

1. 검색 상자에 **복제**를 입력하고 **Git: 복제**를 선택합니다.

    :::image type="content" source="media/local-development/command-palette-git-clone.png" alt-text="Visual Studio Code의 git 복제 옵션":::

1. **리포지토리 URL**에 대해 다음 값을 입력합니다.

   ```http
   git@github.com:<YOUR_GITHUB_ACCOUNT>/vanilla-api.git
   ```

1. 새 프로젝트의 폴더 위치를 선택합니다.

1. 복제된 리포지토리를 열 것인지 묻는 메시지가 표시되면 **열기**를 선택합니다.

    :::image type="content" source="media/local-development/open-new-window.png" alt-text="새 창에서 열기":::

Visual Studio Code에서 편집기에 복제된 프로젝트를 엽니다.

### <a name="run-the-website-locally-with-live-server"></a>Live Server를 사용하여 웹 사이트를 로컬로 실행

1. **F1** 키를 눌러 명령 팔레트를 엽니다.

1. 검색 상자에 **Live Server**를 입력하고 **Live Server: Live Server로 열기**를 선택합니다.

    애플리케이션을 표시하는 브라우저 탭이 열립니다.

    :::image type="content" source="media/local-development/vanilla-api-site.png" alt-text="브라우저에서 실행되는 단순 정적 사이트":::

    이 애플리케이션은 `api/message` 엔드포인트에 대한 HTTP 요청을 수행합니다. 지금은 이 애플리케이션의 API 부분을 시작해야 하기 때문에 요청이 실패합니다.

### <a name="run-api-locally"></a>로컬로 API 실행

Azure Static Web Apps API는 Azure Functions에서 제공합니다. Azure Static Web Apps 프로젝트에 API를 추가하는 방법에 대한 자세한 내용은 [Azure Functions를 사용하여 Azure Static Web Apps에 API 추가](add-api.md)를 참조하세요.

API 만들기 프로세스의 일부로 Visual Studio Code에 대한 시작 구성이 생성됩니다. 이 구성은 _.vscode_ 폴더에 있습니다. 이 폴더에는 API를 로컬로 빌드 및 실행하는 데 필요한 모든 설정이 포함되어 있습니다.

1. Visual Studio Code에서 **F5** 키를 눌러 API를 시작합니다.

1. 새 터미널 인스턴스가 열리고 API 빌드 프로세스의 출력이 표시됩니다.

    :::image type="content" source="media/local-development/terminal-api-debug.png" alt-text="Visual Studio Code 터미널에서 실행되는 API":::

   Visual Studio Code의 상태 표시줄은 이제 주황색입니다. 이 색은 이제 API가 실행 중이고 디버거가 연결되었음을 나타냅니다.

1. 다음으로 **Ctrl/Cmd**를 누르고 터미널에서 URL을 클릭하여 API를 호출하는 브라우저 창을 엽니다.

    :::image type="content" source="media/local-development/hello-from-api-endpoint.png" alt-text="API 호출의 브라우저 표시 결과":::

### <a name="debugging-the-api"></a>API 디버깅

1. Visual Studio Code에서 _api/GetMessage/index.js_ 파일을 엽니다.

1. 2줄의 왼쪽 여백을 클릭하여 중단점을 설정합니다. 중단점이 설정되었음을 나타내는 빨간색 점이 표시됩니다.

    :::image type="content" source="media/local-development/breakpoint-set.png" alt-text="Visual Studio Code의 중단점":::

1. 브라우저에서 <http://127.0.0.1:7071/api/message>에서 실행되는 페이지를 새로 고칩니다.

1. 중단점은 Visual Studio Code에서 적중되며 프로그램 실행이 일시 중지됩니다.

   :::image type="content" source="media/local-development/breakpoint-hit.png" alt-text="Visual Studio Code의 중단점 적중":::

   API에 대해 [Visual Studio Code에서 전체 디버깅 환경을 사용할 수 있습니다](https://code.visualstudio.com/Docs/editor/debugging).

1. 디버그 표시줄에서 **계속** 단추를 눌러 실행을 계속합니다.

    :::image type="content" source="media/local-development/continue-button.png" alt-text="Visual Studio Code의 계속 단추":::

### <a name="calling-the-api-from-the-application"></a>애플리케이션에서 API 호출

배포되면 Azure Static Web Apps는 이러한 요청을 _api_ 폴더의 엔드포인트에 자동으로 매핑합니다. 이 매핑을 통해 애플리케이션에서 API로의 요청을 다음 예제와 같이 확인할 수 있습니다.

```javascript
let response = await fetch("/api/message");
```

애플리케이션을 JavaScript 프레임워크 CLI로 빌드하는지 여부에 따라 애플리케이션을 로컬로 실행할 때 `api` 경로에 대한 경로를 구성하는 두 가지 방법이 있습니다.

- 환경 구성 파일(JavaScript 프레임워크 및 라이브러리에 권장)
- 로컬 프록시

### <a name="environment-configuration-files"></a>환경 구성 파일

CLI를 포함하는 프런트 엔드 프레임워크를 사용하여 앱을 빌드하는 경우 환경 구성 파일을 사용해야 합니다. 각 프레임워크 또는 라이브러리는 이러한 환경 구성 파일을 서로 다른 방식으로 처리합니다. 애플리케이션을 로컬로 실행할 때 사용되는 개발용 구성 파일과 애플리케이션을 프로덕션 환경에서 실행할 때 사용되는 프로덕션용 구성 파일을 사용하는 것이 일반적입니다. 사용 중인 JavaScript 프레임워크 또는 정적 사이트 생성기의 CLI는 앱이 Azure Static Web Apps에 의해 빌드될 때 개발 파일을 로컬로 사용하고 프로덕션 파일을 사용하는 것을 자동으로 인식합니다.

개발 구성 파일에서 API에 대한 경로를 지정할 수 있습니다. 이 경로는 사이트에 대한 API가 로컬로 실행되는 `http:127.0.0.1:7071`의 로컬 위치를 가리킵니다.

```
API=http:127.0.0.1:7071/api
```

프로덕션 구성 파일에서 API의 경로를 `api`로 지정합니다. 이러한 방식으로 애플리케이션은 프로덕션 환경에서 실행될 때 "yoursite.com/api"를 통해 api를 호출합니다.

```
API=api
```

이러한 구성 값은 웹앱의 JavaScript에서 노드 환경 변수로 참조될 수 있습니다.

```js
let response = await fetch(`${process.env.API}/message`);
```

CLI를 사용하여 개발 모드에서 사이트를 실행하거나 프로덕션용 사이트를 빌드할 때 `process.env.API` 값이 적절한 구성 파일의 값으로 바뀝니다.

프런트 엔드 JavaScript 프레임워크 및 라이브러리를 위한 환경 파일 구성에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Angular 환경 변수](https://angular.io/guide/build#configuring-application-environments)
- [React - 사용자 지정 환경 변수 추가](https://create-react-app.dev/docs/adding-custom-environment-variables/)
- [Vue - 모드 및 환경 변수](https://cli.vuejs.org/guide/mode-and-env.html)

[!INCLUDE [static-web-apps-local-proxy](../../includes/static-web-apps-local-proxy.md)]

##### <a name="restart-live-server"></a>Live Server 다시 시작

1. Visual Studio Code에서 **F1** 키를 눌러 명령 팔레트를 엽니다.

1. **Live Server**를 입력하고 **Live Server: Live Server 중지**를 선택합니다.

    :::image type="content" source="media/local-development/stop-live-server.png" alt-text="Visual Studio 명령 팔레트에서 Live Server 중지 명령":::

1. **F1** 키를 눌러 명령 팔레트를 엽니다.

1. **Live Server**를 입력하고 **Live Server: Live Server로 열기**를 선택하여 Live Server를 시작합니다.

1. `http://locahost:3000`에서 실행되는 애플리케이션을 새로 고칩니다. 이제 브라우저는 API에서 반환된 메시지를 표시합니다.

    :::image type="content" source="media/local-development/hello-from-api.png" alt-text="브라우저에 표시되는 API의 Hello":::

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱 설정 구성](application-settings.md)
