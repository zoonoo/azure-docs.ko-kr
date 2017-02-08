---
title: "Azure App Service에 대한 Node.js 웹앱 개발 | Microsoft Docs"
description: "Azure 앱 서비스에서 웹앱에 Node.js 응용 프로그램을 배포하는 방법을 알아봅니다."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: wpickett
editor: 
ms.assetid: fb2b90c8-02b6-4700-929b-5de9a35d67cc
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: get-started-article
ms.date: 12/16/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 763e956004f460f2f6d0fa7325a6f6b5fca3fd5a
ms.openlocfilehash: ab16eb45f86f81af719fcd0a4b65f19576f01f01


---
# <a name="develop-nodejs-web-apps-for-azure-app-service"></a>Azure App Service에 대한 Node.js 웹앱 개발
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

이 자습서에서는 간단한 [Node.js] 응용 프로그램을 만들고 cmd.exe 또는 bash와 같은 명령줄 환경에서 [Azure App Service]에 배포하는 방법을 보여 줍니다. 이 자습서의 지침은 Node.js를 실행할 수 있는 모든 운영 체제를 따를 수 있습니다.

[!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]

<a name="prereq"></a>

## <a name="cli-versions-to-complete-the-task"></a>태스크를 완료하기 위한 CLI 버전

다음 CLI 버전 중 하나를 사용하여 태스크를 완료할 수 있습니다.

- [Azure CLI 1.0](app-service-web-nodejs-get-started-cli-nodejs.md) - 클래식 및 리소스 관리 배포 모델용 CLI
- [Azure CLI 2.0(미리 보기)](app-service-web-nodejs-get-started.md) - 리소스 관리 배포 모델용 차세대 CLI

## <a name="prerequisites"></a>필수 조건
* [Node.js]
* [Bower]
* [Yeoman]
* [Git]
* [Azure CLI 2.0 미리 보기](/cli/azure/install-az-cli2)
* Microsoft Azure 계정. 계정이 없는 경우 [무료 평가판을 등록]하거나 [Visual Studio 구독자 혜택을 활성화]할 수 있습니다.

> [!NOTE]
> Azure 계정 없이 [App Service를 체험](https://azure.microsoft.com/try/app-service/)할 수 있습니다. 시작 앱을 만들고 최대 한 시간 동안 해당 앱을 사용하여 재생합니다. -- 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.
> 
> 

## <a name="create-and-configure-a-simple-nodejs-app-for-azure"></a>간단한 Azure용 Node.js 앱 만들기 및 구성
1. 원하는 명령줄 터미널을 열고 [Yeoman Express 생성기]를 설치합니다.
   
        npm install -g generator-express

2. `CD` 하고 다음 구문을 사용하여 Express 앱을 생성합니다.
   
        yo express
   
    메시지가 표시되면 다음 옵션을 선택합니다.  
   
    `? Would you like to create a new directory for your project?` **예**  
    `? Enter directory name` **{appname}**  
    `? Select a version to install:` **MVC**  
    `? Select a view engine to use:` **Jade**  
    `? Select a css preprocessor to use (Sass Requires Ruby):` **없음**  
    `? Select a database to use:` **없음**  
    `? Select a build tool to use:` **Grunt**

3. 새 앱의 루트 디렉터리에 대해 `CD`하고 이를 시작하여 개발 환경에서 실행되는지 확인합니다.
   
        npm start
   
    브라우저에서 <http://localhost:3000>으로 이동하여 Express 홈 페이지를 볼 수 있는지 확인합니다. 앱이 제대로 실행되는지 확인했으면 `Ctrl-C` 를 사용하여 중지합니다.

6. 응용 프로그램의 루트에서 ./config/config.js 파일을 열고 프로덕션 포트를 `process.env.port`로 변경합니다. `config` 개체의 `production` 속성은 다음 예제와 같아야 합니다.
   
        production: {
            root: rootPath,
            app: {
                name: 'express1'
            },
            port: process.env.port,
        }
   
    > [!NOTE] 
    > 기본적으로 Azure App Service는 `production`환경 변수(`process.env.NODE_ENV="production"`)로 Node.js 응용 프로그램을 실행합니다.
    > 따라서 Azure에서 Node.js 앱은 iisnode에서 수신하는 기본 포트에서 웹 요청에 응답할 수 있습니다.
    >
    >

7. ./package.json을 열고 `engines` 속성을 추가하여 [원하는 Node.js 버전을 지정](#version)합니다.
   
        "engines": {
            "node": "6.9.1"
        }, 

8. 변경 내용을 저장한 후 응용 프로그램의 루트에 있는 Git 리포지토리를 초기화하고 다음 코드를 커밋합니다.
   
        git add .
        git add -f config
        git commit -m "{your commit message}"

## <a name="deploy-your-nodejs-app-to-azure"></a>Azure에 Node.js 앱 배포

1. 다음을 실행하여 Azure에 로그인합니다([Azure CLI 2.0 미리 보기](#prereq) 필요).
   
        az login
   
    프롬프트를 따라 Azure 구독을 보유하고 있는 Microsoft 계정을 사용하여 브라우저에 로그인을 계속합니다.

3. App Service의 배포 사용자를 설정합니다. 나중에 이러한 자격 증명을 사용하여 코드를 배포합니다.
   
        az appservice web deployment user set --user-name <username> --password <password>

3. 새 [리소스 그룹](../azure-resource-manager/resource-group-overview.md)을 만듭니다. 이 node.js 자습서에서는 실제로 무엇인지 알 필요가 없습니다.

        az group create --location "<location>" --name my-nodejs-app-group

    `<location>`에 사용할 수 있는 가능한 값을 보려면 `az appservice list-locations` CLI 명령을 사용합니다.

3. 새로운 "무료" [App Service 계획](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)을 만듭니다. 이 node.js 자습서에서는 이 계획에서 웹앱에 대한 요금이 부과되지 않습니다.

        az appservice plan create --name my-nodejs-appservice-plan --resource-group my-nodejs-app-group --sku FREE

4. `<app_name>`에 고유한 이름이 있는 새 웹앱을 만듭니다.

        az appservice web create --name <app_name> --resource-group my-nodejs-app-group --plan my-nodejs-appservice-plan

5. 다음 명령을 사용하여 새 웹앱에 대한 로컬 Git 배포를 구성합니다.

        az appservice web source-control config-local-git --name <app_name> --resource-group my-nodejs-app-group

    다음과 같은 JSON 출력을 받으며, 이는 원격 Git 리포지토리가 구성되었음을 의미합니다.

        {
        "url": "https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git"
        }

6. JSON의 URL을 로컬 리포지토리의 Git 원격으로 추가합니다(간단히 `azure`라고 함).

        git remote add azure https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git
   
7. 샘플 코드를 `azure` Git 원격에 배포합니다. 메시지가 표시되면 이전에 구성한 배포 자격 증명을 사용합니다.

        git push azure master
   
    Express 생성기가 .gitignore 파일을 이미 제공하므로 `git push`가 node_modules/ 디렉터리로 업로드를 시도하는 대역폭을 사용하지 않습니다.

9. 마지막으로 브라우저에서 라이브 Azure 앱을 시작합니다.
   
        az appservice web browse --name <app_name> --resource-group my-nodejs-app-group
   
    이제 Azure 앱 서비스에서 라이브로 실행되는 Node.js 웹앱이 표시됩니다.
   
    ![배포된 응용 프로그램에 대한 검색의 예제][deployed-express-app]

## <a name="update-your-nodejs-web-app"></a>Node.js 웹앱 업데이트
앱 서비스에서 실행되는 Node.js 웹앱을 업데이트하려면 웹앱을 처음 배포할 때처럼 `git add`, `git commit` 및 `git push`을 실행합니다.

## <a name="how-app-service-deploys-your-nodejs-app"></a>앱 서비스에서 Node.js 앱을 배포하는 방법
Azure 앱 서비스는 [iisnode] 를 사용하여 Node.js 앱을 실행합니다. Azure CLI 2.0 미리 보기와 Kudu 엔진(Git 배포)은 함께 작동하여 명령줄에서 Node.js 앱을 개발하고 배포할 때 능률적인 환경을 제공합니다. 

* 루트 디렉터리에 iisnode.yml 파일을 만들고 이를 사용하여 iisnode 속성을 사용자 지정할 수 있습니다. 구성 가능한 모든 설정에 대해서는 [여기](https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/iisnode.yml)서 설명합니다.
* `git push azure master`에서 Kudu는 다음 배포 작업을 자동화합니다.
  
  * package.json이 리포지토리 루트에 있는 경우 `npm install --production`을 실행합니다.
  * package.json의 시작 스크립트를 가리키는 iisnode에 대한 Web.config를 생성합니다(예: server.js 또는 app.js).
  * Node-inspector로 디버깅을 위해 앱을 준비하는 데 Web.config를 사용자 지정합니다.

## <a name="use-a-nodejs-framework"></a>Node.js 프레임워크 사용
앱을 개발하기 위해 [Sails.js][SAILSJS] 또는 [MEAN.js][MEANJS]와 같이 널리 사용되는 Node.js 프레임워크를 사용하는 경우 App Service에 배포할 수 있습니다. 널리 사용되는 Node.js 프레임워크에는 특정 쿼크가 있고 해당 패키지 종속성은 계속 업데이트됩니다. 그러나 App Service에서는 stdout 및 stderr 로그가 제공되므로 앱으로 수행하는 작업을 정확히 알고 적절히 변경할 수 있습니다. 자세한 내용은 [iisnode에서 stdout 및 stderr 로그 가져오기](#iisnodelog)를 참조하세요.

다음 자습서에서는 앱 서비스에서 특정 프레임워크를 사용하는 방법을 보여 줍니다.

* [Azure 앱 서비스에 Sails.js 웹앱 배포]
* [Azure 앱 서비스에서 Socket.IO를 사용하여 Node.js 채팅 응용 프로그램 만들기]
* [Azure 앱 서비스 웹앱에서 io.js를 사용하는 방법]

<a name="version"></a>

## <a name="use-a-specific-nodejs-engine"></a>특정 Node.js 엔진 사용
일반적인 워크플로에서는 package.json에서 일반적으로 하는 것처럼 특정 Node.js 엔진을 사용하도록 App Service에 지시합니다.
예:

    "engines": {
        "node": "6.9.1"
    }, 

Kudu 배포 엔진은 다음 순서로 사용할 Node.js 엔진을 결정합니다.

* 먼저 iisnode.yml에서 `nodeProcessCommandLine`이 지정되었는지 확인합니다. 지정된 경우 사용합니다.
* 다음으로 package.json에서 `engines` 개체에 `"node": "..."`가 지정되었는지 확인합니다. 지정된 경우 사용합니다.
* 기본적으로 기본 Node.js 버전을 선택합니다.

Azure App Service에서 지원하는 모든 Node.js/NPM 버전의 업데이트된 목록을 보려면 앱의 다음 URL에 액세스합니다.

    https://<app_name>.scm.azurewebsites.net/api/diagnostics/runtime

> [!NOTE]
> 원하는 Node.js 엔진을 명시적으로 정의하는 것이 좋습니다. 기본 Node.js 버전을 변경하면 기본 Node.js 버전이 사용자의 앱에 적절하지 않기 때문에 Azure 웹앱에서 오류가 발생할 수 있습니다.
> 
> 

<a name="iisnodelog"></a>

## <a name="get-stdout-and-stderr-logs-from-iisnode"></a>iisnode에서 stdout 및 stderr 로그 가져오기
iisnode 로그를 읽으려면 다음 단계를 수행하세요.

> [!NOTE]
> 다음 단계를 완료한 후에 오류가 발생할 때까지 로그 파일이 존재하지 않을 수 있습니다.
> 
> 

1. Azure CLI 2.0 미리 보기에서 제공하는 iisnode.yml 파일을 엽니다.
2. 이때 다음 두 매개 변수를 지정합니다. 
   
        loggingEnabled: true
        logDirectory: iisnode
   
    함께 stdout 및 stderror 출력을 D:\home\site\wwwroot\**iisnode** 디렉터리에서 배치하도록 App Service의 iisnode에 지시합니다.
3. 변경 내용을 저장한 후 다음 Git 명령을 사용하여 Azure에 푸시합니다.
   
        git add .
        git commit -m "{your commit message}"
        git push azure master
   
    이제 iisnode가 구성됩니다. 다음 단계에서는 이러한 로그에 액세스하는 방법을 보여 줍니다.
4. 브라우저에서 다음과 같이 앱에 대한 Kudu 디버그 콘솔에 액세스합니다.
   
        https://{appname}.scm.azurewebsites.net/DebugConsole 
   
    이 URL은 DNS 이름에 "*.scm.*"이 추가되어 웹앱 URL과 다릅니다. URL에 해당 추가를 생락하면 404 오류가 발생하게 됩니다.
5. D:\home\site\wwwroot\iisnode로 이동합니다.
   
    ![iisnode 로그 파일의 위치 탐색][iislog-kudu-console-find]
6. 읽을 로그에 대한 **편집** 아이콘을 클릭합니다. 원하는 경우 **다운로드** 또는 **삭제**를 클릭할 수도 있습니다.
   
    ![iisnode 로그 파일 열기][iislog-kudu-console-open]
   
    이제 앱 서비스 배포를 디버깅할 수 있도록 로그를 볼 수 있습니다.
   
    ![iisnode 로그 파일 검사][iislog-kudu-console-read]

## <a name="debug-your-app-with-node-inspector"></a>Node-Inspector로 앱 디버그
Node-Inspector를 사용하여 Node.js 앱을 디버깅하는 경우 라이브 앱 서비스 앱에 이를 사용할 수 있습니다. Node-Inspector는 앱 서비스에 대한 iisnode 설치에 미리 설치되어 있습니다. Git을 통해 배포하는 경우 Kudu에서 자동 생성된 Web.config에는 Node-Inspector를 사용하는 데 필요한 모든 구성이 이미 들어 있습니다.

Node-Inspector를 사용하려면 다음 단계를 수행하세요.

1. 리포지토리 루트에서 iisnode.yml을 열고 다음 매개 변수를 지정합니다. 
   
        debuggingEnabled: true
        debuggerExtensionDll: iisnode-inspector.dll
2. 변경 내용을 저장한 후 다음 Git 명령을 사용하여 Azure에 푸시합니다.
   
        git add .
        git commit -m "{your commit message}"
        git push azure master
3. 이제 package.json의 시작 스크립트에 지정된 대로 URL에 추가된 /debug와 함께 앱의 시작 파일로 이동합니다. 예를 들면 다음과 같습니다.
   
        http://{appname}.azurewebsites.net/server.js/debug
   
    또는
   
        http://{appname}.azurewebsites.net/app.js/debug

## <a name="more-resources"></a>추가 리소스
* [Azure 응용 프로그램에서 Node.js 버전 지정](../nodejs-specify-node-version-azure-apps.md)
* [Azure에서 Node.js 응용 프로그램에 대한 모범 사례 및 문제 해결 가이드](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md)
* [Azure 앱 서비스에서 Node.js 웹 앱을 디버그하는 방법](web-sites-nodejs-debug.md)
* [Azure 응용 프로그램에 Node.js 모듈 사용](../nodejs-use-node-modules-azure-apps.md)
* [Azure 앱 서비스 웹앱: Node.js](http://blogs.msdn.com/b/silverlining/archive/2012/06/14/windows-azure-websites-node-js.aspx)
* [Node.js 개발자 센터](/develop/nodejs/)
* [Azure 앱 서비스에서 웹 앱 시작](app-service-web-get-started.md)
* [Super 암호 Kudu 디버그 콘솔 탐색]

<!-- URL List -->

[Azure App Service]: ../app-service/app-service-value-prop-what-is.md
[Visual Studio 구독자 혜택을 활성화]: http://go.microsoft.com/fwlink/?LinkId=623901
[Bower]: http://bower.io/
[Azure 앱 서비스에서 Socket.IO를 사용하여 Node.js 채팅 응용 프로그램 만들기]: ./web-sites-nodejs-chat-app-socketio.md
[Azure 앱 서비스에 Sails.js 웹앱 배포]: ./app-service-web-nodejs-sails.md
[Super 암호 Kudu 디버그 콘솔 탐색]: /documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[Yeoman Express 생성기]: https://github.com/petecoop/generator-express
[Git]: http://www.git-scm.com/downloads
[Azure 앱 서비스 웹앱에서 io.js를 사용하는 방법]: ./web-sites-nodejs-iojs.md
[iisnode]: https://github.com/tjanczuk/iisnode/wiki
[MEANJS]: http://meanjs.org/
[Node.js]: http://nodejs.org
[SAILSJS]: http://sailsjs.org/
[무료 평가판을 등록]: http://go.microsoft.com/fwlink/?LinkId=623901
[web app]: ./app-service-web-overview.md
[Yeoman]: http://yeoman.io/

<!-- IMG List -->

[deployed-express-app]: ./media/app-service-web-nodejs-get-started/deployed-express-app.png
[iislog-kudu-console-find]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-navigate.png
[iislog-kudu-console-open]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-open.png
[iislog-kudu-console-read]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-read.png



<!--HONumber=Feb17_HO1-->


