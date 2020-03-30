---
title: Node.js 앱 구성
description: 앱에 대해 미리 빌드된 Node.js 컨테이너를 구성하는 방법에 대해 알아봅니다. 이 문서에서는 가장 일반적인 구성 작업을 보여줍니다.
ms.devlang: nodejs
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: fdc5129fc395f99cb4c244414ea952b2776dc4dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252728"
---
# <a name="configure-a-linux-nodejs-app-for-azure-app-service"></a>Azure 앱 서비스에 대한 Linux Node.js 앱 구성

Node.js 앱은 필요한 모든 NPM 종속성과 함께 배포되어야 합니다. [Git 리포지토리](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)또는 빌드 `npm install --production` 프로세스가 켜져 있는 Zip [패키지를](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) 배포할 때 Kudu(앱 서비스 배포 엔진)가 자동으로 실행됩니다. 그러나 [FTP/S를](../deploy-ftp.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)사용하여 파일을 배포하는 경우 필요한 패키지를 수동으로 업로드해야 합니다.

이 가이드는 App Service에서 기본 제공 Linux 컨테이너를 사용하는 Node.js 개발자를 위한 주요 개념 및 지침을 제공합니다. Azure 앱 서비스를 사용한 적이 없는 경우 먼저 MongoDB 자습서를 사용하여 [Node.js 퀵스타트](quickstart-nodejs.md) 및 [Node.js를](tutorial-nodejs-mongodb-app.md) 따릅니다.

## <a name="show-nodejs-version"></a>노드.js 버전 표시

현재 Node.js 버전을 표시하려면 [클라우드 셸에서](https://shell.azure.com)다음 명령을 실행합니다.

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

지원되는 모든 Node.js 버전을 표시하려면 클라우드 [셸에서](https://shell.azure.com)다음 명령을 실행합니다.

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

## <a name="set-nodejs-version"></a>노드.js 버전 설정

앱을 [지원되는 Node.js 버전으로](#show-nodejs-version)설정하려면 [클라우드 셸에서](https://shell.azure.com)다음 명령을 실행합니다.

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

이 설정은 런타임 및 Kudu에서 자동화된 패키지 복원 중에 사용할 Node.js 버전을 지정합니다.

> [!NOTE]
> 프로젝트의 에서 Node.js 버전을 설정해야 `package.json`합니다. 배포 엔진은 지원되는 모든 Node.js 버전을 포함하는 별도의 컨테이너에서 실행됩니다.

## <a name="customize-build-automation"></a>빌드 자동화 사용자 지정

빌드 자동화가 켜져 있는 Git 또는 zip 패키지를 사용하여 앱을 배포하는 경우 App Service는 다음 순서를 통해 자동화 단계를 빌드합니다.

1. 에 의해 지정된 `PRE_BUILD_SCRIPT_PATH`경우 사용자 지정 스크립트를 실행합니다.
1. npm `preinstall` 및 스크립트를 `postinstall` 포함하고 설치되는 플래그 없이 `devDependencies`실행합니다. `npm install`
1. 빌드 `npm run build` 스크립트가 *package.json*에 지정된 경우 실행합니다.
1. build:azure 스크립트가 `npm run build:azure` *package.json 에*지정된 경우 실행합니다.
1. 에 의해 지정된 `POST_BUILD_SCRIPT_PATH`경우 사용자 지정 스크립트를 실행합니다.

> [!NOTE]
> [npm 문서에](https://docs.npmjs.com/misc/scripts)설명된 대로 지정된 `prebuild` `postbuild` 스크립트는 지정된 `build`경우 각각 이전과 이후를 지정합니다. `preinstall`이전 `postinstall` 과 이후를 `install`각각 실행합니다.

`PRE_BUILD_COMMAND`기본적으로 `POST_BUILD_COMMAND` 비어 있는 환경 변수입니다. 미리 빌드 명령을 실행하려면 `PRE_BUILD_COMMAND`을 정의합니다. 빌드 후 명령을 실행하려면 `POST_BUILD_COMMAND`을 정의합니다.

다음 예제에서는 쉼표로 구분된 일련의 명령으로 두 변수를 지정합니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

빌드 자동화를 사용자 지정하는 추가 환경 변수는 [Oryx 구성을](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)참조하십시오.

앱 서비스가 Linux에서 Node.js 앱을 실행하고 빌드하는 방법에 대한 자세한 내용은 [Oryx 설명서: Node.js 앱을 검색하고 빌드하는 방법을](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/nodejs.md)참조하십시오.

## <a name="configure-nodejs-server"></a>노드.js 서버 구성

Node.js 컨테이너에는 생산 프로세스 관리자인 [PM2가](https://pm2.keymetrics.io/)함께 제공됩니다. 앱을 PM2로 시작하거나 NPM또는 사용자 지정 명령으로 시작하도록 앱을 구성할 수 있습니다.

- [사용자 지정 명령 실행](#run-custom-command)
- [npm 시작 실행](#run-npm-start)
- [PM2로 실행](#run-with-pm2)

### <a name="run-custom-command"></a>사용자 지정 명령 실행

앱 서비스는 *run.sh*와 같은 실행 파일을 사용하여 앱을 시작할 수 있습니다. 예를 들어 `npm run start:prod`실행하려면 Cloud [Shell에서](https://shell.azure.com)다음 명령을 실행합니다.

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>npm 시작 실행

을 사용하여 `npm start`앱을 시작하려면 스크립트가 `start` *package.json* 파일에 있는지 확인하십시오. 예를 들어:

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

프로젝트에서 사용자 지정 *package.json을* 사용하려면 [클라우드 셸에서](https://shell.azure.com)다음 명령을 실행합니다.

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>PM2로 실행

프로젝트에서 일반적인 Node.js 파일 중 하나가 발견되면 컨테이너가 PM2로 앱을 자동으로 시작합니다.

- *빈/www*
- *server.js*
- *app.js*
- *index.js*
- *호스팅 스타트.js*
- 다음 [PM2 파일](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file)중 하나 : *process.json* 및 *ecosystem.config.js*

다음과 같은 확장으로 사용자 지정 시작 파일을 구성할 수도 있습니다.

- *.js* 파일
- 확장자가 있는 [PM2 파일](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) *. json,* *.config.js,* *.yaml*또는 *.yml*

사용자 지정 시작 파일을 추가하려면 Cloud [Shell에서](https://shell.azure.com)다음 명령을 실행합니다.

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>원격 디버깅

> [!NOTE]
> 원격 디버깅은 현재 미리 보기상태입니다.

*.config.js, *.yml 또는 *.yaml을*사용하여 실행하는 경우를 제외하고 [PM2로 실행하도록](#run-with-pm2)구성하면 [Visual Studio Code에서](https://code.visualstudio.com/) Node.js 앱을 원격으로 디버깅할 수 있습니다.

대부분의 경우 앱에 추가 구성이 필요하지 않습니다. 앱이 *process.json* 파일(기본값 또는 사용자 지정)으로 `script` 실행되는 경우 JSON 루트에 속성이 있어야 합니다. 예를 들어:

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

원격 디버깅을 위한 Visual Studio 코드를 설정하려면 [앱 서비스 확장을](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)설치합니다. 확장 페이지의 지침을 따르고 Visual Studio 코드에서 Azure에 로그인합니다.

Azure 탐색기에서 디버깅할 앱을 찾고 마우스 오른쪽 단추로 클릭한 다음 **원격 디버깅 시작을**선택합니다. 앱을 사용하려면 **예를** 클릭합니다. 앱 서비스는 터널 프록시를 시작하고 디버거를 연결합니다. 그런 다음 앱에 요청을 하고 중단점에서 디버거가 일시 중지되는 것을 볼 수 있습니다.

디버깅이 완료되면 **연결 끊기를**선택하여 디버거를 중지합니다. 메시지가 표시되면 **예를** 클릭하여 원격 디버깅을 비활성화해야 합니다. 나중에 비활성화하려면 Azure 탐색기에서 앱을 다시 마우스 오른쪽 단추로 클릭하고 **원격 디버깅 사용 안 함**을 선택합니다.

## <a name="access-environment-variables"></a>환경 변수 액세스

App Service에서, 앱 코드 외부에서 [앱 설정](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)을 지정할 수 있습니다. 그런 다음 표준 Node.js 패턴을 사용하여 액세스할 수 있습니다. 예를 들어 앱 설정 `NODE_ENV`에 액세스하려면 다음 코드를 사용합니다.

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>실행 그런트 / 바우어 / 걸프

기본적으로 Kudu는 `npm install --production` Node.js 앱이 배포된 것을 인식할 때 실행됩니다. 앱에 Grunt, Bower 또는 Gulp와 같은 인기 있는 자동화 도구가 필요한 경우 이를 실행하기 위해 [사용자 지정 배포 스크립트를](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) 제공해야 합니다.

리포지토리에서 이러한 도구를 실행할 수 있도록 하려면 *package.json의* 종속성에 해당 도구를 추가해야 합니다. 예를 들어:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

로컬 터미널 창에서 디렉터리를 리포지토리 루트로 변경하고 다음 명령을 실행합니다.

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

저장소 루트에는 .deployment 및 *deploy.sh*두 개의 추가 파일이 *있습니다.*

*deploy.sh* 열고 다음과 `Deployment` 같은 섹션을 찾습니다.

```bash
##################################################################################################################################
# Deployment
# ----------
```

이 섹션은 `npm install --production`실행으로 끝납니다. 섹션 의 끝에서 필요한 도구를 실행하는 데 필요한 코드 섹션을 추가합니다. *at the end* `Deployment`

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

배포 스크립트가 사용자 지정 `npm install` 명령을 실행하는 [MEAN.js 샘플의 예제를](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135)참조하십시오.

### <a name="bower"></a>Bower

이 코드 조각은 실행됩니다. `bower install`

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

이 코드 조각은 실행됩니다. `gulp imagemin`

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

이 코드 조각은 실행됩니다. `grunt`

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

인기 있는 웹 프레임워크를 사용하여 표준 앱 패턴의 `X-Forwarded-*` 정보에 액세스할 수 있습니다. [Express에서](https://expressjs.com/) [트러스트 프록시를](https://expressjs.com/guide/behind-proxies.html)사용할 수 있습니다. 예를 들어:

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

작업 Node.js 앱이 앱 서비스에서 다르게 작동하거나 오류가 있는 경우 다음을 시도해 보십시오.

- [로그 스트림에 액세스](#access-diagnostic-logs)합니다.
- 프로덕션 모드에서 로컬로 앱을 테스트합니다. 앱 서비스는 프로덕션 모드에서 Node.js 앱을 실행하므로 프로젝트가 프로덕션 모드에서 예상대로 작동하는지 확인해야 합니다. 예를 들어:
    - *package.json에*따라 프로덕션`dependencies` `devDependencies`모드(대)에 대해 다른 패키지가 설치될 수 있습니다.
    - 특정 웹 프레임워크는 프로덕션 모드에서 정적 파일을 다르게 배포할 수 있습니다.
    - 프로덕션 모드에서 실행할 때 특정 웹 프레임워크는 사용자 지정 시작 스크립트를 사용할 수 있습니다.
- 개발 모드에서 앱 서비스에서 앱을 실행합니다. 예를 들어 [MEAN.js에서는](https://meanjs.org/) [ `NODE_ENV` 앱 설정을 설정하여](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)런타임에 앱을 개발 모드로 설정할 수 있습니다.

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [튜토리얼 : MongoDB와 Node.js 응용 프로그램](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [App Service Linux FAQ](app-service-linux-faq.md)
