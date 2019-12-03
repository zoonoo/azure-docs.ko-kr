---
title: PHP 앱 구성
description: 앱에 대해 미리 작성 된 PHP 컨테이너를 구성 하는 방법에 대해 알아봅니다. 이 문서에서는 가장 일반적인 구성 작업을 보여 줍니다.
ms.devlang: php
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: a3de4769193d95a3ef483924c4d65c4fa1cc9f8d
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671847"
---
# <a name="configure-a-linux-php-app-for-azure-app-service"></a>Azure App Service에 대 한 Linux PHP 앱 구성

이 가이드에서는 Azure App Service의 웹 앱, 모바일 백 엔드 및 API 앱에 대 한 기본 제공 PHP 런타임을 구성 하는 방법을 보여 줍니다.

이 가이드에서는 App Service의 기본 제공 Linux 컨테이너를 사용 하는 PHP 개발자를 위한 주요 개념 및 지침을 제공 합니다. Azure App Service 사용한 적이 없는 경우 먼저 [php 퀵 스타트](quickstart-php.md) 및 php를 사용 하는 [MySQL 자습서](tutorial-php-mysql-app.md) 를 따르세요.

## <a name="show-php-version"></a>PHP 버전 표시

현재 PHP 버전을 표시 하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행 합니다.

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

모든 지원 되는 PHP 버전을 표시 하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행 합니다.

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

## <a name="set-php-version"></a>PHP 버전 설정

[Cloud Shell](https://shell.azure.com) 에서 다음 명령을 실행 하 여 PHP 버전을 7.2로 설정 합니다.

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

## <a name="run-composer"></a>작성기 실행

기본적으로 Kudu는 [작성기](https://getcomposer.org/)를 실행 하지 않습니다. Kudu 배포 중에 작성기 자동화를 사용 하도록 설정 하려면 [사용자 지정 배포 스크립트](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)를 제공 해야 합니다.

로컬 터미널 창에서 디렉터리를 리포지토리 루트로 변경 합니다. [명령줄 설치 단계](https://getcomposer.org/download/) 에 따라 *작성기. phar*를 다운로드 합니다.

다음 명령을 실행합니다.

```bash
npm install kuduscript -g
kuduscript --php --scriptType bash --suppressPrompt
```

이제 리포지토리 루트에는 deploy.sh와 함께 두 개의 새 파일이 있습니다. *phar*: *. 배포* 및. 이러한 파일은 App Service의 Windows 및 Linux 버전 모두에서 작동 합니다.

*Deploy.sh* 를 열고 `Deployment` 섹션을 찾습니다. 전체 섹션을 다음 코드로 바꿉니다.

```bash
##################################################################################################################################
# Deployment
# ----------

echo PHP deployment

# 1. KuduSync
if [[ "$IN_PLACE_DEPLOYMENT" -ne "1" ]]; then
  "$KUDU_SYNC_CMD" -v 50 -f "$DEPLOYMENT_SOURCE" -t "$DEPLOYMENT_TARGET" -n "$NEXT_MANIFEST_PATH" -p "$PREVIOUS_MANIFEST_PATH" -i ".git;.hg;.deployment;deploy.sh"
  exitWithMessageOnError "Kudu Sync failed"
fi

# 3. Initialize Composer Config
initializeDeploymentConfig

# 4. Use composer
echo "$DEPLOYMENT_TARGET"
if [ -e "$DEPLOYMENT_TARGET/composer.json" ]; then
  echo "Found composer.json"
  pushd "$DEPLOYMENT_TARGET"
  php composer.phar install $COMPOSER_ARGS
  exitWithMessageOnError "Composer install failed"
  popd
fi
##################################################################################################################################
```

모든 변경 내용을 커밋하고 코드를 다시 배포 합니다. 이제 작성기는 배포 자동화의 일부로 실행 됩니다.

## <a name="customize-start-up"></a>시작 사용자 지정

기본적으로 기본 제공 PHP 컨테이너는 Apache 서버를 실행 합니다. 시작 시 `apache2ctl -D FOREGROUND"`실행 됩니다. 원하는 경우 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행 하 여 시작 시 다른 명령을 실행할 수 있습니다.

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

## <a name="access-environment-variables"></a>환경 변수 액세스

App Service에서 앱 코드 외부에 [앱 설정](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)을 지정할 수 있습니다. 그런 다음 표준 [getenv ()](https://secure.php.net/manual/function.getenv.php) 패턴을 사용 하 여 액세스할 수 있습니다. 예를 들어 앱 설정 `DB_HOST`에 액세스하려면 다음 코드를 사용합니다.

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>사이트 루트 변경

선택한 웹 프레임 워크는 하위 디렉터리를 사이트 루트로 사용할 수 있습니다. 예를 들어, [Laravel](https://laravel.com/)는 `public/` 하위 디렉터리를 사이트 루트로 사용 합니다.

App Service에 대 한 기본 PHP 이미지는 Apache를 사용 하며 앱에 대 한 사이트 루트를 사용자 지정할 수 없습니다. 이 제한을 해결 하려면 다음 내용으로 리포지토리 루트에 .htaccess 파일을 추가 *합니다* .

```
<IfModule mod_rewrite.c>
    RewriteEngine on

    RewriteRule ^.*$ /public/$1 [NC,L,QSA]
</IfModule>
```

*.htaccess* 다시 쓰기를 사용하지 않으려면 [사용자 지정 Docker 이미지](quickstart-docker-go.md)를 대신 사용하여 Laravel 애플리케이션을 배포할 수 있습니다.

## <a name="detect-https-session"></a>HTTPS 세션 검색

App Service에서, [SSL 종료](https://wikipedia.org/wiki/TLS_termination_proxy)는 네트워크 부하 분산 장치에서 발생하므로 모든 HTTPS 요청은 암호화되지 않은 HTTP 요청으로 앱에 도달합니다. 앱 논리에서 사용자 요청의 암호화 여부를 확인해야 하는 경우 `X-Forwarded-Proto` 헤더를 검사합니다.

```php
if (isset($_SERVER['X-Forwarded-Proto']) && $_SERVER['X-Forwarded-Proto'] === 'https') {
  // Do something when HTTPS is used
}
```

인기 있는 웹 프레임워크를 사용하여 표준 앱 패턴의 `X-Forwarded-*` 정보에 액세스할 수 있습니다. [CodeIgniter](https://codeigniter.com/)에서, [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365)는 기본적으로 `X_FORWARDED_PROTO` 값을 확인합니다.

## <a name="customize-phpini-settings"></a>Php setup.ini 설정 사용자 지정

PHP 설치를 변경 해야 하는 경우 다음 단계에 따라 [php 지시문](https://www.php.net/manual/ini.list.php) 을 변경할 수 있습니다.

> [!NOTE]
> PHP 버전 및 현재 *php* 를 확인 하는 가장 좋은 방법은 앱에서 [phpinfo ()](https://php.net/manual/function.phpinfo.php) 를 호출 하는 것입니다.
>

### <a name="Customize-non-PHP_INI_SYSTEM directives"></a>사용자 지정-비 PHP_INI_SYSTEM 지시문

PHP_INI_USER, PHP_INI_PERDIR 및 PHP_INI_ALL 지시문을 사용자 지정 하려면 ( [PHP 지시문](https://www.php.net/manual/ini.list.php)참조) 앱의 루트 디렉터리에 *.htaccess* 파일을 추가 합니다.

*.Htaccess* 파일에서 `php_value <directive-name> <value>` 구문을 사용 하 여 지시문을 추가 합니다. 다음은 그 예입니다.

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

변경 내용으로 앱을 다시 배포 하 고 다시 시작 합니다. Kudu를 사용 하 여 배포 하는 경우 (예: [Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)사용) 배포 후 자동으로 다시 시작 됩니다.

*.Htaccess*를 사용 하는 대신 앱에서 [ini_set ()](https://www.php.net/manual/function.ini-set.php) 를 사용 하 여 이러한 비 PHP_INI_SYSTEM 지시문을 사용자 지정할 수 있습니다.

### <a name="customize-php_ini_system-directives"></a>PHP_INI_SYSTEM 지시문 사용자 지정

PHP_INI_SYSTEM 지시문을 사용자 지정 하려면 ( [PHP 지시문](https://www.php.net/manual/ini.list.php)참조) *. .htaccess* 접근 방식을 사용할 수 없습니다. App Service `PHP_INI_SCAN_DIR` 앱 설정을 사용 하 여 별도의 메커니즘을 제공 합니다.

먼저 [Cloud Shell](https://shell.azure.com) 에서 다음 명령을 실행 하 여 `PHP_INI_SCAN_DIR`이라는 앱 설정을 추가 합니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d`는 *php* 가 있는 기본 디렉터리입니다. `/home/site/ini`은 사용자 지정 *.ini* 파일을 추가할 사용자 지정 디렉터리입니다. `:`를 사용 하 여 값을 구분 합니다.

Linux 컨테이너 (`https://<app-name>.scm.azurewebsites.net/webssh/host`)를 사용 하 여 웹 SSH 세션으로 이동 합니다.

`/home/site`에서 `ini`라는 디렉터리를 만든 다음 사용자 지정 하려는 지시문을 사용 하 여 `/home/site/ini` 디렉터리에 .ini 파일을 만듭니다 (예: setup.ini *)* *.* *Php .ini* 파일에서 사용 하는 것과 동일한 구문을 사용 합니다. 

> [!TIP]
> App Service의 기본 제공 Linux 컨테이너에서 */home* 는 지속형 공유 저장소로 사용 됩니다. 
>

예를 들어 [expose_php](https://php.net/manual/ini.core.php#ini.expose-php) 의 값을 변경 하려면 다음 명령을 실행 합니다.

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

변경 내용을 적용 하려면 앱을 다시 시작 합니다.

## <a name="enable-php-extensions"></a>PHP 확장 사용

기본 제공 PHP 설치에는 가장 일반적으로 사용 되는 확장이 포함 되어 있습니다. [Php 지시문을 사용자 지정](#customize-php_ini_system-directives)하는 것과 같은 방법으로 추가 확장을 사용 하도록 설정할 수 있습니다.

> [!NOTE]
> PHP 버전 및 현재 *php* 를 확인 하는 가장 좋은 방법은 앱에서 [phpinfo ()](https://php.net/manual/function.phpinfo.php) 를 호출 하는 것입니다.
>

추가 확장을 사용하도록 설정하려면 다음 단계를 따릅니다.

`bin` 디렉터리를 앱의 루트 디렉터리에 추가 하 고 `.so` 확장 파일 (예: *mongodb.so*)을 저장 합니다. 확장이 Azure의 PHP 버전과 호환 되 고 VC9 및 nts (스레드로부터 안전 하지 않음) 호환 되는지 확인 합니다.

변경 내용을 배포 합니다.

[PHP_INI_SYSTEM 지시문 사용자 지정](#customize-php_ini_system-directives)의 단계에 따라 [확장](https://www.php.net/manual/ini.core.php#ini.extension) 또는 [zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension) 지시문을 사용 하 여 사용자 지정 *.ini* 파일에 확장을 추가 합니다.

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

변경 내용을 적용 하려면 앱을 다시 시작 합니다.

## <a name="access-diagnostic-logs"></a>진단 로그 액세스

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>브라우저에서 SSH 세션 열기

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>문제 해결

작동 하는 PHP 앱이 App Service에서 다르게 동작 하거나 오류가 발생 하는 경우 다음을 시도 합니다.

- [로그 스트림에 액세스](#access-diagnostic-logs)합니다.
- 프로덕션 모드에서 로컬로 앱을 테스트 합니다. App Service 프로덕션 모드에서 node.js 앱을 실행 하므로 프로젝트가 프로덕션 모드에서 로컬로 예상 대로 작동 하는지 확인 해야 합니다. 다음은 그 예입니다.
    - *작성기. json*에 따라 프로덕션 모드 (`require` `require-dev`)에 대해 서로 다른 패키지를 설치할 수 있습니다.
    - 특정 웹 프레임 워크는 프로덕션 모드에서 다른 방식으로 정적 파일을 배포할 수 있습니다.
    - 특정 웹 프레임 워크는 프로덕션 모드에서 실행 되는 경우 사용자 지정 시작 스크립트를 사용할 수 있습니다.
- 디버그 모드의 App Service에서 앱을 실행 합니다. 예를 들어, [Laravel](https://meanjs.org/)에서 [`APP_DEBUG` 앱 설정을 `true`으로 설정 ](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)하 여 프로덕션에서 디버그 메시지를 출력 하도록 앱을 구성할 수 있습니다.

### <a name="robots933456"></a>robots933456

컨테이너 로그에 다음 메시지가 표시 될 수 있습니다.

```
2019-04-08T14:07:56.641002476Z "-" - - [08/Apr/2019:14:07:56 +0000] "GET /robots933456.txt HTTP/1.1" 404 415 "-" "-"
```

이 메시지는 무시 해도 됩니다. `/robots933456.txt`는 컨테이너에서 요청을 처리할 수 있는지 여부를 확인 하는 데 사용 App Service는 더미 URL 경로입니다. 404 응답은 단순히 경로가 존재 하지 않는다는 것을 나타내지만 컨테이너가 정상 상태이 고 요청에 응답할 준비가 되었음을 App Service 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: MySQL을 사용 하는 PHP 앱](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [App Service Linux FAQ](app-service-linux-faq.md)
