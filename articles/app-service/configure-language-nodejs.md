---
title: Windows Node.js 앱 구성
description: App Service의 네이티브 Windows 인스턴스에서 Node.js 앱을 구성 하는 방법에 대해 알아봅니다. 이 문서에서는 가장 일반적인 구성 작업을 보여줍니다.
ms.custom: devx-track-javascript
ms.devlang: nodejs
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 0fc6ed5cb090653e381d82f484d355a514520c62
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87170901"
---
# <a name="configure-a-windows-nodejs-app-for-azure-app-service"></a>Azure App Service에 대 한 Windows Node.js 앱 구성

모든 필수 NPM 종속성을 사용 하 여 Node.js 앱을 배포 해야 합니다. `npm install --production` [Git 리포지토리](deploy-local-git.md)를 배포할 때 또는 빌드 자동화를 사용 하는 [Zip 패키지](deploy-zip.md) 를 배포할 때 App Service 배포 엔진이 자동으로 실행 됩니다. 그러나 [FTP/S](deploy-ftp.md)를 사용 하 여 파일을 배포 하는 경우 필요한 패키지를 수동으로 업로드 해야 합니다. Linux 앱에 대 한 자세한 내용은 [Azure App Service에 대 한 LINUX PHP 앱 구성](containers/configure-language-nodejs.md)을 참조 하세요.

이 가이드에서는 App Service에 배포 하는 Node.js 개발자를 위한 주요 개념 및 지침을 제공 합니다. Azure App Service 사용한 적이 없는 경우 [Node.js 빠른](app-service-web-get-started-nodejs.md) 시작 및 [Node.js MongoDB 자습서](app-service-web-tutorial-nodejs-mongodb-app.md) 를 먼저 수행 합니다.

## <a name="show-nodejs-version"></a>Node.js 버전 표시

현재 Node.js 버전을 표시 하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행 합니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION'].value"
```

지원 되는 모든 Node.js 버전을 표시 하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행 합니다.

```azurecli-interactive
az webapp list-runtimes | grep node
```

## <a name="set-nodejs-version"></a>Node.js 버전 설정

앱을 [지원 되는 Node.js 버전](#show-nodejs-version)으로 설정 하려면 [Cloud Shell](https://shell.azure.com) 에서 다음 명령을 실행 하 여 `WEBSITE_NODE_DEFAULT_VERSION` 지원 되는 버전으로 설정 합니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_NODE_DEFAULT_VERSION="10.15"
```

이 설정은 런타임에 및 App Service 빌드 자동화 중에 자동화 된 패키지 복원 중에 사용할 Node.js 버전을 지정 합니다.

> [!NOTE]
> 프로젝트의에서 Node.js 버전을 설정 해야 합니다 `package.json` . 배포 엔진은 지원 되는 모든 Node.js 버전을 포함 하는 별도의 프로세스에서 실행 됩니다.

## <a name="access-environment-variables"></a>환경 변수 액세스

App Service에서, 앱 코드 외부에서 [앱 설정](configure-common.md)을 지정할 수 있습니다. 그런 다음 표준 Node.js 패턴을 사용 하 여 액세스할 수 있습니다. 예를 들어 앱 설정 `NODE_ENV`에 액세스하려면 다음 코드를 사용합니다.

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Grunt/Bower/Gulp 실행

기본적으로 App Service 빌드 자동화는 `npm install --production` 응용 프로그램이 Git (또는 빌드 자동화를 사용 하는 Zip 배포를 사용 하 여 배포 됨)를 통해 배포 되 Node.js는 경우 빌드 자동화를 실행 합니다. 앱이 Grunt, Bower 또는 Gulp와 같은 인기 있는 자동화 도구를 필요로 하는 경우이를 실행 하려면 [사용자 지정 배포 스크립트](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) 를 제공 해야 합니다.

이러한 도구를 실행 하기 위해 리포지토리를 사용 하도록 설정 하려면package.js의 종속성에 해당 도구를 추가 해야 *합니다.* 예:

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

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="troubleshooting"></a>문제 해결

작업 Node.js 앱이 App Service에서 다르게 동작 하거나 오류가 발생 하는 경우 다음을 시도 합니다.

- [로그 스트림에 액세스](#access-diagnostic-logs)합니다.
- 프로덕션 모드에서 로컬 상태로 앱을 테스트합니다. App Service가 프로덕션 모드에서 Node.js 앱을 실행하므로 프로젝트가 프로덕션 모드에서 로컬 상태로 예상과 같이 작동하는지 확인해야 합니다. 예를 들면 다음과 같습니다.
    - *package.js*에 따라 프로덕션 모드 ( `dependencies` vs)에 대해 서로 다른 패키지를 설치할 수 있습니다 `devDependencies` .
    - 특정 웹 프레임워크는 프로덕션 모드에서 다른 고정 파일을 배포할 수 있습니다.
    - 특정 웹 프레임워크는 프로덕션 모드에서 실행될 때 사용자 지정 시작 스크립트를 사용합니다.
- 개발 모드의 App Service에서 앱을 실행 합니다. 예를 들어 [MEAN.js](https://meanjs.org/)에서 [ `NODE_ENV` 앱 설정을 설정](configure-common.md)하 여 런타임 시 앱을 개발 모드로 설정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: MongoDB를 사용하는 Node.js 앱](app-service-web-tutorial-nodejs-mongodb-app.md)

