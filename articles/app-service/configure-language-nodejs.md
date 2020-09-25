---
title: Node.js 앱 구성
description: Azure App Service에서 네이티브 Windows 인스턴스 또는 미리 빌드된 Linux 컨테이너의 Node.js 앱을 구성 하는 방법에 대해 알아봅니다. 이 문서에서는 가장 일반적인 구성 작업을 보여줍니다.
ms.custom: devx-track-js
ms.devlang: nodejs
ms.topic: article
ms.date: 06/02/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 48b111966d58af80b6c34fa17231034f4f0cc213
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91311838"
---
# <a name="configure-a-nodejs-app-for-azure-app-service"></a>Azure App Service에 대 한 Node.js 앱 구성

모든 필수 NPM 종속성을 사용 하 여 Node.js 앱을 배포 해야 합니다. `npm install --production` [Git 리포지토리](deploy-local-git.md)를 배포할 때 또는 빌드 자동화를 사용 하는 [Zip 패키지](deploy-zip.md) 를 배포할 때 App Service 배포 엔진이 자동으로 실행 됩니다. 그러나 [FTP/S](deploy-ftp.md)를 사용 하 여 파일을 배포 하는 경우 필요한 패키지를 수동으로 업로드 해야 합니다.

이 가이드에서는 App Service에 배포 하는 Node.js 개발자를 위한 주요 개념 및 지침을 제공 합니다. Azure App Service 사용한 적이 없는 경우 [Node.js 빠른](quickstart-nodejs.md) 시작 및 [Node.js MongoDB 자습서](tutorial-nodejs-mongodb-app.md) 를 먼저 수행 합니다.

## <a name="show-nodejs-version"></a>Node.js 버전 표시

::: zone pivot="platform-windows"  

현재 Node.js 버전을 표시 하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행 합니다.

```azurecli-interactive
az webapp config appsettings list --name <app-name> --resource-group <resource-group-name> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION'].value"
```

지원 되는 모든 Node.js 버전을 표시 하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행 합니다.

```azurecli-interactive
az webapp list-runtimes | grep node
```

::: zone-end

::: zone pivot="platform-linux"

현재 Node.js 버전을 표시 하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행 합니다.

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

지원 되는 모든 Node.js 버전을 표시 하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행 합니다.

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

::: zone-end

## <a name="set-nodejs-version"></a>Node.js 버전 설정

::: zone pivot="platform-windows"  

앱을 [지원 되는 Node.js 버전](#show-nodejs-version)으로 설정 하려면 [Cloud Shell](https://shell.azure.com) 에서 다음 명령을 실행 하 여 `WEBSITE_NODE_DEFAULT_VERSION` 지원 되는 버전으로 설정 합니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_NODE_DEFAULT_VERSION="10.15"
```

이 설정은 런타임에 및 App Service 빌드 자동화 중에 자동화 된 패키지 복원 중에 사용할 Node.js 버전을 지정 합니다.

> [!NOTE]
> 프로젝트의에서 Node.js 버전을 설정 해야 합니다 `package.json` . 배포 엔진은 지원 되는 모든 Node.js 버전을 포함 하는 별도의 프로세스에서 실행 됩니다.

::: zone-end

::: zone pivot="platform-linux"

앱을 [지원 되는 Node.js 버전](#show-nodejs-version)으로 설정 하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행 합니다.

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

이 설정은 런타임에 및 Kudu의 자동화 된 패키지 복원 중에 사용할 Node.js 버전을 지정 합니다.

> [!NOTE]
> 프로젝트의에서 Node.js 버전을 설정 해야 합니다 `package.json` . 배포 엔진은 지원 되는 모든 Node.js 버전을 포함 하는 별도의 컨테이너에서 실행 됩니다.

::: zone-end

::: zone pivot="platform-linux"

## <a name="customize-build-automation"></a>빌드 자동화 사용자 지정

빌드 자동화가 설정된 상태에서 Git 또는 zip 패키지를 사용하여 앱을 배포하는 경우 App Service는 다음 시퀀스를 통해 자동화 단계를 빌드합니다.

1. `PRE_BUILD_SCRIPT_PATH`에 지정된 경우 사용자 지정 스크립트를 실행합니다.
1. `npm install`Npm `preinstall` 및 스크립트를 포함 `postinstall` 하 고도 설치 하는 플래그 없이를 실행 `devDependencies` 합니다.
1. `npm run build` *package.js*에 빌드 스크립트가 지정 된 경우를 실행 합니다.
1. `npm run build:azure`빌드 시 실행: azure script가 *package.js*에 지정 되어 있습니다.
1. `POST_BUILD_SCRIPT_PATH`에 지정된 경우 사용자 지정 스크립트를 실행합니다.

> [!NOTE]
> [Npm docs](https://docs.npmjs.com/misc/scripts)에 설명 된 것 처럼, `prebuild` 및 스크립트는 `postbuild` `build` 각각 지정 된 경우 및 이후에 실행 됩니다. `preinstall` 및 `postinstall` 는 각각 전후에 실행 `install` 됩니다.

`PRE_BUILD_COMMAND` 및 `POST_BUILD_COMMAND`는 기본적으로 비어 있는 환경 변수입니다. 빌드 전 명령을 실행하려면 `PRE_BUILD_COMMAND`를 정의합니다. 빌드 후 명령을 실행하려면 `POST_BUILD_COMMAND`를 정의합니다.

다음 예제에서는 일련의 명령에 대한 두 변수를 쉼표로 구분하여 지정합니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

빌드 자동화를 사용자 지정하는 추가 환경 변수는 [Oryx 구성](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)을 참조하세요.

App Service 실행 하 고 Linux에서 Node.js 앱을 빌드하는 방법에 대 한 자세한 내용은 [Oryx 설명서: 앱 검색 및 빌드 Node.js 방법](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/nodejs.md)을 참조 하세요.

## <a name="configure-nodejs-server"></a>Node.js 서버 구성

Node.js 컨테이너는 프로덕션 프로세스 관리자 인 [PM2](https://pm2.keymetrics.io/)와 함께 제공 됩니다. PM2 또는 NPM를 사용 하거나 사용자 지정 명령을 사용 하 여 시작 하도록 앱을 구성할 수 있습니다.

- [사용자 지정 명령 실행](#run-custom-command)
- [Npm start 실행](#run-npm-start)
- [PM2를 사용 하 여 실행](#run-with-pm2)

### <a name="run-custom-command"></a>사용자 지정 명령 실행

*Run.sh*같은 실행 파일과 같은 사용자 지정 명령을 사용 하 여 앱을 시작할 수 App Service. 예를 들어를 실행 하려면 `npm run start:prod` [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행 합니다.

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>Npm start 실행

를 사용 하 여 앱을 시작 하려면 `npm start` `start` 스크립트가 파일 * 에package.js* 있는지 확인 합니다. 예를 들면 다음과 같습니다.

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

프로젝트에서 사용자 지정 *package.js* 를 사용 하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행 합니다.

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>PM2를 사용 하 여 실행

이 컨테이너는 일반적인 Node.js 파일 중 하나가 프로젝트에 있는 경우 PM2를 사용 하 여 앱을 자동으로 시작 합니다.

- *bin/www*
- *server.js*
- *app.js*
- *index.js*
- *hostingstart.js*
- 다음 [PM2 파일](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file)중 하나: *process.json* 및 *ecosystem.config.js*

다음 확장을 사용 하 여 사용자 지정 시작 파일을 구성할 수도 있습니다.

- *.Js* 파일
- PM2, *.config.js* *,*.yml 또는 *.yml* 확장명을 포함 하는 [파일](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) *입니다.*

사용자 지정 시작 파일을 추가 하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행 합니다.

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>원격 디버깅

> [!NOTE]
> 원격 디버깅은 현재 미리 보기로 제공 됩니다.

PM2를 사용 하 여 [실행](#run-with-pm2)하도록 구성 하는 경우 * .config.js, * .yml 또는 *.yaml*을 사용 하 여 실행 하는 경우를 제외 하 고는 [Visual Studio Code](https://code.visualstudio.com/) 에서 원격으로 Node.js 앱을 디버그할 수 있습니다.

대부분의 경우에는 앱에 대 한 추가 구성이 필요 하지 않습니다. 앱이 파일 (기본값 또는 사용자 지정) * 에서process.js* 를 사용 하 여 실행 되는 경우 `script` JSON 루트에 속성이 있어야 합니다. 예를 들면 다음과 같습니다.

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

::: zone-end

## <a name="access-environment-variables"></a>환경 변수 액세스

App Service에서, 앱 코드 외부에서 [앱 설정](configure-common.md)을 지정할 수 있습니다. 그런 다음 표준 Node.js 패턴을 사용 하 여 액세스할 수 있습니다. 예를 들어 앱 설정 `NODE_ENV`에 액세스하려면 다음 코드를 사용합니다.

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Grunt/Bower/Gulp 실행

기본적으로 빌드 자동화 `npm install --production` 는 빌드 자동화를 사용 하 여 Git 또는 Zip 배포를 통해 배포 된 Node.js 앱을 인식할 때 실행 App Service. 앱이 Grunt, Bower 또는 Gulp와 같은 인기 있는 자동화 도구를 필요로 하는 경우이를 실행 하려면 [사용자 지정 배포 스크립트](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) 를 제공 해야 합니다.

이러한 도구를 실행 하기 위해 리포지토리를 사용 하도록 설정 하려면package.js의 종속성에 해당 도구를 추가 해야 * 합니다.* 예를 들면 다음과 같습니다.

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

*Deploy.sh* 을 열고 `Deployment` 다음과 같은 섹션을 찾습니다.

```bash
##################################################################################################################################
# Deployment
# ----------
```

이 섹션은를 실행 하는 것으로 끝납니다 `npm install --production` . 섹션의 *끝에서* 필요한 도구를 실행 하는 데 필요한 코드 섹션을 추가 합니다 `Deployment` .

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

배포 스크립트가 사용자 지정 명령을 실행 하는 [MEAN.js 샘플의 예제](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135)를 참조 하세요 `npm install` .

### <a name="bower"></a>Bower

이 코드 조각은 `bower install` 를 실행 합니다.

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

이 코드 조각은 `gulp imagemin` 를 실행 합니다.

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

이 코드 조각은 `grunt` 를 실행 합니다.

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

인기 있는 웹 프레임워크를 사용하여 표준 앱 패턴의 `X-Forwarded-*` 정보에 액세스할 수 있습니다. [Express](https://expressjs.com/)에서는 [트러스트 프록시](https://expressjs.com/guide/behind-proxies.html)를 사용할 수 있습니다. 예를 들면 다음과 같습니다.

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>진단 로그 액세스

::: zone pivot="platform-windows"  

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end

## <a name="troubleshooting"></a>문제 해결

작업 Node.js 앱이 App Service에서 다르게 동작 하거나 오류가 발생 하는 경우 다음을 시도 합니다.

- [로그 스트림에 액세스](#access-diagnostic-logs)합니다.
- 프로덕션 모드에서 로컬 상태로 앱을 테스트합니다. App Service가 프로덕션 모드에서 Node.js 앱을 실행하므로 프로젝트가 프로덕션 모드에서 로컬 상태로 예상과 같이 작동하는지 확인해야 합니다. 예를 들면 다음과 같습니다.
    - *package.js*에 따라 프로덕션 모드 ( `dependencies` vs)에 대해 서로 다른 패키지를 설치할 수 있습니다 `devDependencies` .
    - 특정 웹 프레임워크는 프로덕션 모드에서 다른 고정 파일을 배포할 수 있습니다.
    - 특정 웹 프레임워크는 프로덕션 모드에서 실행될 때 사용자 지정 시작 스크립트를 사용합니다.
- 개발 모드의 App Service에서 앱을 실행 합니다. 예를 들어 [MEAN.js](https://meanjs.org/)에서 [ `NODE_ENV` 앱 설정을 설정](configure-common.md)하 여 런타임 시 앱을 개발 모드로 설정할 수 있습니다.

::: zone pivot="platform-linux"

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: MongoDB를 사용하는 Node.js 앱](tutorial-nodejs-mongodb-app.md)

::: zone pivot="platform-linux"

> [!div class="nextstepaction"]
> [App Service Linux FAQ](faq-app-service-linux.md)

::: zone-end

