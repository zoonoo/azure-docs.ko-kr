---
title: Node.js 앱 구성
description: 앱에 대해 미리 작성 된 node.js 컨테이너를 구성 하는 방법에 대해 알아봅니다. 이 문서에서는 가장 일반적인 구성 작업을 보여줍니다.
ms.devlang: nodejs
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: fdc5129fc395f99cb4c244414ea952b2776dc4dc
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79252728"
---
# <a name="configure-a-linux-nodejs-app-for-azure-app-service"></a>Azure App Service에 대 한 Linux node.js 앱 구성

Node.js 앱은 모든 필수 NPM 종속성과 함께 배포 해야 합니다. Kudu (App Service deployment engine)는 [Git 리포지토리](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)를 배포할 때 또는 빌드 프로세스가 전환 된 [Zip 패키지](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) 를 배포할 때 자동으로 `npm install --production`를 실행 합니다. 그러나 [FTP/S](../deploy-ftp.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)를 사용 하 여 파일을 배포 하는 경우 필요한 패키지를 수동으로 업로드 해야 합니다.

이 가이드에서는 App Service의 기본 제공 Linux 컨테이너를 사용 하는 node.js 개발자를 위한 주요 개념 및 지침을 제공 합니다. Azure App Service를 사용한 적이 없는 경우 [node.js 빠른](quickstart-nodejs.md) 시작 및 node.js를 사용 [하는 Node.js를 먼저 MongoDB 자습서](tutorial-nodejs-mongodb-app.md) 를 따르세요.

## <a name="show-nodejs-version"></a>Node.js 버전 표시

현재 node.js 버전을 표시 하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행 합니다.

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

모든 지원 되는 node.js 버전을 표시 하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행 합니다.

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

## <a name="set-nodejs-version"></a>Node.js 버전 설정

앱을 [지원 되는 node.js 버전](#show-nodejs-version)으로 설정 하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행 합니다.

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

이 설정은 런타임에 및 Kudu의 자동화 된 패키지 복원 중에 사용할 node.js 버전을 지정 합니다.

> [!NOTE]
> 프로젝트의 `package.json`에서 node.js 버전을 설정 해야 합니다. 배포 엔진은 지원 되는 node.js 버전을 모두 포함 하는 별도의 컨테이너에서 실행 됩니다.

## <a name="customize-build-automation"></a>빌드 자동화 사용자 지정

빌드 자동화가 설정 된 상태에서 Git 또는 zip 패키지를 사용 하 여 앱을 배포 하는 경우 App Service 다음 시퀀스를 통해 자동화 단계를 빌드합니다.

1. `PRE_BUILD_SCRIPT_PATH`에 지정 된 경우 사용자 지정 스크립트를 실행 합니다.
1. Npm `preinstall` 및 `postinstall` 스크립트를 포함 하 고 `devDependencies`도 설치 하는 플래그 없이 `npm install`를 실행 합니다.
1. 빌드 스크립트가 *package. json*에 지정 된 경우 `npm run build`를 실행 합니다.
1. 빌드 인 경우 `npm run build:azure`를 실행 합니다 *. json*에 azure 스크립트가 지정 되어 있습니다.
1. `POST_BUILD_SCRIPT_PATH`에 지정 된 경우 사용자 지정 스크립트를 실행 합니다.

> [!NOTE]
> [Npm 문서](https://docs.npmjs.com/misc/scripts)에 설명 된 대로 `prebuild` 및 `postbuild` 스크립트는 각각 지정 된 경우 `build`전후에 실행 됩니다. `preinstall` 및 `postinstall` 각각 `install`전후에 실행 됩니다.

`PRE_BUILD_COMMAND` 및 `POST_BUILD_COMMAND`은 기본적으로 비어 있는 환경 변수입니다. 빌드 전 명령을 실행 하려면 `PRE_BUILD_COMMAND`를 정의 합니다. 빌드 후 명령을 실행 하려면 `POST_BUILD_COMMAND`를 정의 합니다.

다음 예에서는 일련의 명령에 대 한 두 변수를 쉼표로 구분 하 여 지정 합니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

빌드 자동화를 사용자 지정 하는 추가 환경 변수는 [Oryx 구성](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)을 참조 하세요.

App Service 실행 하 고 Linux에서 node.js 앱을 빌드하는 방법에 대 한 자세한 내용은 [Oryx 설명서: node.js 앱이 검색 되 고 빌드되는 방법](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/nodejs.md)을 참조 하세요.

## <a name="configure-nodejs-server"></a>Node.js 서버 구성

Node.js 컨테이너는 프로덕션 프로세스 관리자 인 [PM2](https://pm2.keymetrics.io/)와 함께 제공 됩니다. PM2 또는 NPM를 사용 하거나 사용자 지정 명령을 사용 하 여 시작 하도록 앱을 구성할 수 있습니다.

- [사용자 지정 명령 실행](#run-custom-command)
- [Npm start 실행](#run-npm-start)
- [PM2를 사용 하 여 실행](#run-with-pm2)

### <a name="run-custom-command"></a>사용자 지정 명령 실행

*Run.sh*같은 실행 파일과 같은 사용자 지정 명령을 사용 하 여 앱을 시작할 수 App Service. 예를 들어 `npm run start:prod`를 실행 하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행 합니다.

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>Npm start 실행

`npm start`를 사용 하 여 앱을 시작 하려면 `start` 스크립트가 *package. json* 파일에 있는지 확인 하면 됩니다. 다음은 그 예입니다.

```json
{
  ...
  "scripts": {
    "start": "gulp",
    ...
  },
  ...
}
```

프로젝트에서 사용자 지정 *package. json* 을 사용 하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행 합니다.

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>PM2를 사용 하 여 실행

이 컨테이너는 일반적인 node.js 파일 중 하나가 프로젝트에 있는 경우 PM2를 사용 하 여 앱을 자동으로 시작 합니다.

- *bin/www*
- *node.js*
- *app.js*
- *index.js*
- *hostingstart*
- [PM2 및 파일](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file)중 *하나입니다.*

다음 확장을 사용 하 여 사용자 지정 시작 파일을 구성할 수도 있습니다.

- *.Js* 파일
- 확장명이 *json*, *..config* *,* *..yml* 인 [PM2 파일](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file)

사용자 지정 시작 파일을 추가 하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행 합니다.

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>원격 디버깅

> [!NOTE]
> 원격 디버깅은 현재 미리 보기로 제공 됩니다.

PM2, * .yml 또는 *.yaml*을 사용 하 여 실행 하는 경우를 제외 하 고, [에서 실행](#run-with-pm2)하도록 구성 하는 경우에는 [Visual Studio Code](https://code.visualstudio.com/) 에서 원격으로 node.js 앱을 디버그할 수 있습니다.

대부분의 경우에는 앱에 대 한 추가 구성이 필요 하지 않습니다. 응용 프로그램을 *process. json* 파일 (기본 또는 사용자 지정)로 실행 하는 경우 json 루트에 `script` 속성이 있어야 합니다. 다음은 그 예입니다.

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

원격 디버깅에 대 한 Visual Studio Code를 설정 하려면 [App Service 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)을 설치 합니다. 확장 페이지의 지침에 따라 Visual Studio Code에서 Azure에 로그인 합니다.

Azure 탐색기에서 디버그할 앱을 찾아 마우스 오른쪽 단추로 클릭 하 고 **원격 디버깅 시작**을 선택 합니다. **예** 를 클릭 하 여 앱에 대해 사용 하도록 설정 합니다. App Service는 터널 프록시를 시작 하 고 디버거를 연결 합니다. 그런 다음 앱에 대 한 요청을 수행 하 고 디버거가 중단점에서 일시 중지 되는 것을 볼 수 있습니다.

디버깅이 완료 되 면 **연결 끊기**를 선택 하 여 디버거를 중지 합니다. 메시지가 표시 되 면 **예** 를 클릭 하 여 원격 디버깅을 사용 하지 않도록 설정 해야 합니다. 나중에 사용 하지 않도록 설정 하려면 Azure 탐색기에서 앱을 다시 마우스 오른쪽 단추로 클릭 하 고 **원격 디버깅 사용 안 함**을 선택 합니다.

## <a name="access-environment-variables"></a>환경 변수 액세스

App Service에서, 앱 코드 외부에서 [앱 설정](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)을 지정할 수 있습니다. 그런 다음 표준 node.js 패턴을 사용 하 여 액세스할 수 있습니다. 예를 들어 앱 설정 `NODE_ENV`에 액세스하려면 다음 코드를 사용합니다.

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Grunt/Bower/Gulp 실행

기본적으로 Kudu는 node.js 앱을 인식할 때 `npm install --production`를 실행 합니다. 앱이 Grunt, Bower 또는 Gulp와 같은 인기 있는 자동화 도구를 필요로 하는 경우이를 실행 하려면 [사용자 지정 배포 스크립트](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) 를 제공 해야 합니다.

이러한 도구를 실행 하기 위해 리포지토리를 사용 하도록 설정 하려면 이러한 도구를 패키지의 종속성에 추가 해야 합니다 *.* 다음은 그 예입니다.

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

로컬 터미널 창에서 디렉터리를 리포지토리 루트로 변경 하 고 다음 명령을 실행 합니다.

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

이제 리포지토리 루트에 *배포* 및 *deploy.sh*의 두 가지 추가 파일이 있습니다.

*Deploy.sh* 을 열고 다음과 같은 `Deployment` 섹션을 찾습니다.

```bash
##################################################################################################################################
# Deployment
# ----------
```

이 섹션은 `npm install --production`를 실행 하는 것으로 끝납니다. `Deployment` 섹션의 *끝에서* 필요한 도구를 실행 하는 데 필요한 코드 섹션을 추가 합니다.

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

배포 스크립트가 사용자 지정 `npm install` 명령도 실행 하는 [MEAN 샘플의 예제](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135)를 참조 하세요.

### <a name="bower"></a>Bower

이 코드 조각은 `bower install`를 실행 합니다.

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

이 코드 조각은 `gulp imagemin`를 실행 합니다.

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

이 코드 조각은 `grunt`를 실행 합니다.

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

## <a name="detect-https-session"></a>HTTPS 세션 검색

App Service에서, [SSL 종료](https://wikipedia.org/wiki/TLS_termination_proxy)는 네트워크 부하 분산 장치에서 발생하므로 모든 HTTPS 요청은 암호화되지 않은 HTTP 요청으로 앱에 도달합니다. 앱 논리에서 사용자 요청의 암호화 여부를 확인해야 하는 경우 `X-Forwarded-Proto` 헤더를 검사합니다.

인기 있는 웹 프레임워크를 사용하여 표준 앱 패턴의 `X-Forwarded-*` 정보에 액세스할 수 있습니다. [Express](https://expressjs.com/)에서는 [트러스트 프록시](https://expressjs.com/guide/behind-proxies.html)를 사용할 수 있습니다. 다음은 그 예입니다.

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>진단 로그 액세스

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>브라우저에서 SSH 세션 열기

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>문제 해결

작업 중인 node.js 앱이 App Service에서 다르게 동작 하거나 오류가 발생 하는 경우 다음을 시도 합니다.

- [로그 스트림에 액세스](#access-diagnostic-logs)합니다.
- 프로덕션 모드에서 로컬로 앱을 테스트 합니다. App Service 프로덕션 모드에서 node.js 앱을 실행 하므로 프로젝트가 프로덕션 모드에서 로컬로 예상 대로 작동 하는지 확인 해야 합니다. 다음은 그 예입니다.
    - *Package. json*에 따라 프로덕션 모드 (`dependencies` `devDependencies`)에 대해 서로 다른 패키지를 설치할 수 있습니다.
    - 특정 웹 프레임 워크는 프로덕션 모드에서 다른 방식으로 정적 파일을 배포할 수 있습니다.
    - 특정 웹 프레임 워크는 프로덕션 모드에서 실행 되는 경우 사용자 지정 시작 스크립트를 사용할 수 있습니다.
- 개발 모드의 App Service에서 앱을 실행 합니다. 예를 들어, [default.js](https://meanjs.org/)에서 [`NODE_ENV` 앱 설정을 설정](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)하 여 런타임의 앱을 개발 모드로 설정할 수 있습니다.

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: MongoDB를 사용 하는 node.js 앱](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [App Service Linux FAQ](app-service-linux-faq.md)
