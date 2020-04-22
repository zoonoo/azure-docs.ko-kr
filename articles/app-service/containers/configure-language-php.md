---
title: PHP 앱 구성
description: 앱에 대해 미리 빌드된 PHP 컨테이너를 구성하는 방법을 알아봅니다. 이 문서에서는 가장 일반적인 구성 작업을 보여줍니다.
ms.devlang: php
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 9e87466f810dc4ebf767c36ad74c358cbf6069e5
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758886"
---
# <a name="configure-a-linux-php-app-for-azure-app-service"></a>Azure 앱 서비스에 대한 Linux PHP 앱 구성

이 가이드에서는 Azure App Service의 웹 앱, 모바일 백 엔드 및 API 앱에 대해 기본 제공 PHP 런타임을 구성하는 방법을 보여 주었습니다.

이 가이드는 앱 서비스에서 기본 제공 Linux 컨테이너를 사용하는 PHP 개발자를 위한 주요 개념 및 지침을 제공합니다. Azure 앱 서비스를 사용한 적이 없는 경우 [먼저 MySQL 자습서를](tutorial-php-mysql-app.md) 사용하여 [PHP 빠른 시작](quickstart-php.md) 및 PHP를 따릅니다.

## <a name="show-php-version"></a>PHP 버전 표시

현재 PHP 버전을 표시하려면 [클라우드 셸에서](https://shell.azure.com)다음 명령을 실행합니다.

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

지원되는 모든 PHP 버전을 표시하려면 클라우드 [셸에서](https://shell.azure.com)다음 명령을 실행합니다.

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

## <a name="set-php-version"></a>PHP 버전 설정

[클라우드 셸에서](https://shell.azure.com) 다음 명령을 실행하여 PHP 버전을 7.2로 설정합니다.

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

## <a name="customize-build-automation"></a>빌드 자동화 사용자 지정

빌드 자동화가 켜져 있는 Git 또는 zip 패키지를 사용하여 앱을 배포하는 경우 App Service는 다음 순서를 통해 자동화 단계를 빌드합니다.

1. 에 의해 지정된 `PRE_BUILD_SCRIPT_PATH`경우 사용자 지정 스크립트를 실행합니다.
1. `php composer.phar install`을 실행합니다.
1. 에 의해 지정된 `POST_BUILD_SCRIPT_PATH`경우 사용자 지정 스크립트를 실행합니다.

`PRE_BUILD_COMMAND`기본적으로 `POST_BUILD_COMMAND` 비어 있는 환경 변수입니다. 미리 빌드 명령을 실행하려면 `PRE_BUILD_COMMAND`을 정의합니다. 빌드 후 명령을 실행하려면 `POST_BUILD_COMMAND`을 정의합니다.

다음 예제에서는 쉼표로 구분된 일련의 명령으로 두 변수를 지정합니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

빌드 자동화를 사용자 지정하는 추가 환경 변수는 [Oryx 구성을](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)참조하십시오.

앱 서비스가 Linux에서 PHP 앱을 실행하고 빌드하는 방법에 대한 자세한 내용은 [Oryx 설명서: PHP 앱을 검색하고 빌드하는 방법을](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/php.md)참조하십시오.

## <a name="customize-start-up"></a>시작 사용자 지정

기본적으로 기본 제공 PHP 컨테이너는 아파치 서버를 실행합니다. 시동시 에 실행됩니다. `apache2ctl -D FOREGROUND"` 원하는 경우 [Cloud Shell에서](https://shell.azure.com)다음 명령을 실행하여 시작 시 다른 명령을 실행할 수 있습니다.

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

## <a name="access-environment-variables"></a>환경 변수 액세스

App Service에서, 앱 코드 외부에서 [앱 설정](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)을 지정할 수 있습니다. 그런 다음 표준 [getenv()](https://secure.php.net/manual/function.getenv.php) 패턴을 사용하여 액세스할 수 있습니다. 예를 들어 앱 설정 `DB_HOST`에 액세스하려면 다음 코드를 사용합니다.

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>사이트 루트 변경

선택한 웹 프레임워크는 하위 디렉터리를 사이트 루트로 사용할 수 있습니다. 예를 들어 [Laravel은](https://laravel.com/) `public/` 하위 디렉터리를 사이트 루트로 사용합니다.

앱 서비스의 기본 PHP 이미지는 아파치를 사용하며 앱의 사이트 루트를 사용자 지정할 수 없습니다. 이 제한을 해결하려면 다음 내용과 함께 리포지토리 루트에 *.htaccess* 파일을 추가합니다.

```
<IfModule mod_rewrite.c>
    RewriteEngine on

    RewriteRule ^(.*)$ /public/$1 [NC,L,QSA]
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

## <a name="customize-phpini-settings"></a>php.ini 설정 사용자 지정

PHP 설치를 변경해야 하는 경우 다음 단계에 따라 [php.ini 지시문을](https://www.php.net/manual/ini.list.php) 변경할 수 있습니다.

> [!NOTE]
> PHP 버전과 현재 *php.ini* 구성을 확인하는 가장 좋은 방법은 앱에서 [phpinfo()를](https://php.net/manual/function.phpinfo.php) 호출하는 것입니다.
>

### <a name="customize-non-php_ini_system-directives"></a><a name="Customize-non-PHP_INI_SYSTEM directives"></a>PHP_INI_SYSTEM 아닌 지시문 사용자 지정

PHP_INI_USER, PHP_INI_PERDIR 및 PHP_INI_ALL 지시문을 사용자 지정하려면 앱의 루트 디렉터리에 *.htaccess* 파일을 [추가합니다.](https://www.php.net/manual/ini.list.php)

*.htaccess* 파일에서 `php_value <directive-name> <value>` 구문을 사용하여 지시문을 추가합니다. 예를 들어:

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

변경 내용과 함께 앱을 다시 배포하고 다시 시작합니다. Kudu와 함께 배포하는 경우(예: [Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)사용) 배포 후 자동으로 다시 시작됩니다.

*.htaccess를*사용하는 대신 앱에서 [ini_set()를](https://www.php.net/manual/function.ini-set.php) 사용하여 이러한 PHP_INI_SYSTEM 아닌 지시문을 사용자 지정할 수 있습니다.

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>PHP_INI_SYSTEM 지시문 사용자 지정

PHP_INI_SYSTEM 지시문을 사용자 지정하려면 [(php.ini 지시문](https://www.php.net/manual/ini.list.php)참조) *.htaccess* 접근 방식을 사용할 수 없습니다. 앱 서비스는 앱 설정을 `PHP_INI_SCAN_DIR` 사용하는 별도의 메커니즘을 제공합니다.

먼저 [Cloud Shell에서](https://shell.azure.com) 다음 명령을 실행하여 다음 `PHP_INI_SCAN_DIR`을 응용 프로그램 설정을 추가합니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d`*는 php.ini가* 있는 기본 디렉토리입니다. `/home/site/ini`은 사용자 지정 *.ini* 파일을 추가하는 사용자 지정 디렉터리입니다. 값을 으로 구분합니다. `:`

Linux 컨테이너()를`https://<app-name>.scm.azurewebsites.net/webssh/host`사용하여 웹 SSH 세션으로 이동합니다.

호출된 `/home/site` `ini`디렉터리 만들기는 사용자 지정하려는 지시문으로 `/home/site/ini` 디렉터리(예: *settings.ini)에* *.ini* 파일을 만듭니다. *php.ini* 파일에서 사용하는 것과 동일한 구문을 사용합니다. 

> [!TIP]
> 앱 서비스의 기본 제공 Linux 컨테이너에서 */home은* 지속가능한 공유 저장소로 사용됩니다. 
>

예를 들어 [expose_php](https://php.net/manual/ini.core.php#ini.expose-php) 값을 변경하려면 다음 명령을 실행합니다.

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

변경 사항이 적용되면 앱을 다시 시작합니다.

## <a name="enable-php-extensions"></a>PHP 확장 사용

기본 제공 PHP 설치에는 가장 일반적으로 사용되는 확장이 포함되어 있습니다. [php.ini 지시문을 사용자 지정하는](#customize-php_ini_system-directives)것과 동일한 방식으로 추가 확장을 활성화할 수 있습니다.

> [!NOTE]
> PHP 버전과 현재 *php.ini* 구성을 확인하는 가장 좋은 방법은 앱에서 [phpinfo()를](https://php.net/manual/function.phpinfo.php) 호출하는 것입니다.
>

추가 확장을 사용하도록 설정하려면 다음 단계를 따릅니다.

앱의 `bin` 루트 디렉터리에 디렉터리를 추가하고 `.so` 확장 파일을 mongodb.so 넣습니다. *mongodb.so* 확장이 Azure의 PHP 버전과 호환되고 VC9 및 비스레드 안전(nts)과 호환되는지 확인합니다.

변경 사항을 배포합니다.

[PHP_INI_SYSTEM 지시문 사용자 정의의](#customize-php_ini_system-directives)단계를 수행, [확장또는](https://www.php.net/manual/ini.core.php#ini.extension) [zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension) 지시문과 사용자 지정 *.ini* 파일에 확장을 추가합니다.

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

변경 사항이 적용되면 앱을 다시 시작합니다.

## <a name="access-diagnostic-logs"></a>진단 로그 액세스

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>브라우저에서 SSH 세션 열기

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>문제 해결

작동하는 PHP 앱이 앱 서비스에서 다르게 작동하거나 오류가 있는 경우 다음을 시도해 보십시오.

- [로그 스트림에 액세스](#access-diagnostic-logs)합니다.
- 프로덕션 모드에서 로컬로 앱을 테스트합니다. 앱 서비스는 프로덕션 모드에서 Node.js 앱을 실행하므로 프로젝트가 프로덕션 모드에서 예상대로 작동하는지 확인해야 합니다. 예를 들어:
    - *composer.json에*따라 프로덕션`require` `require-dev`모드(대)에 대해 다른 패키지가 설치될 수 있습니다.
    - 특정 웹 프레임워크는 프로덕션 모드에서 정적 파일을 다르게 배포할 수 있습니다.
    - 프로덕션 모드에서 실행할 때 특정 웹 프레임워크는 사용자 지정 시작 스크립트를 사용할 수 있습니다.
- 디버그 모드에서 앱 서비스에서 앱을 실행합니다. 예를 들어 [Laravel에서](https://meanjs.org/)앱 설정을 로 설정하여 프로덕션 환경에서 디버그 메시지를 [출력하도록 `APP_DEBUG` 앱을 구성할 `true` ](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)수 있습니다.

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: MySQL을 사용하는 PHP 앱](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [App Service Linux FAQ](app-service-linux-faq.md)
