<properties
	pageTitle="Azure 앱 서비스 웹 앱에서 PHP 구성 "
	description="Azure 앱 서비스의 웹 앱에서 기본 PHP 설치를 구성하거나 사용자 지정 PHP 설치를 추가하는 방법에 대해 알아봅니다."
	services="app-service\web"
	documentationCenter="php"
	authors="tfitzmac"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="article"
	ms.date="03/24/2015"
	ms.author="tomfitz"/>

#Azure 앱 서비스 웹 앱에서 PHP 구성  

## 소개

이 가이드에서는 [Azure 앱 서비스](http://go.microsoft.com/fwlink/?LinkId=529714)의 웹 앱에서 기본 제공 PHP 런타임을 구성하고 사용자 지정 PHP 런타임을 제공하며 확장을 사용하도록 설정하는 방법을 설명합니다. 앱 서비스를 사용하려면 [무료 평가판]에 등록해야 합니다. 이 가이드를 최대한 활용하려면 먼저 앱 서비스에서 PHP 웹 앱을 만들어야 합니다.

## 방법: 기본 제공 PHP 구성 변경
기본적으로 PHP 5.4는 앱 서비스 웹 앱을 만들 때 설치하여 바로 사용할 수 있습니다. 사용 가능한 릴리스 버전, 관련 기본 구성 및 사용하도록 설정된 확장을 보는 최상의 방법은 [phpinfo()] 함수를 호출하는 스크립트를 배포하는 것입니다.

PHP 5.5 및 PHP 5.6 버전 사용할 수 있지만 기본적으로 이는 사용하도록 설정되어 있지 않습니다. PHP 버전을 업데이트하려면 아래 단계를 따르세요.

1. [Azure 포털](http://go.microsoft.com/fwlink/?LinkId=529715)에서 웹 앱을 찾고 **설정** 단추를 클릭합니다.

	![웹 앱 설정][settings-button]

2. **설정** 블레이드에서 **응용 프로그램 설정**을 선택하고 새 PHP 버전을 선택합니다.

    ![응용 프로그램 설정][application-settings]

3. **웹 앱 설정** 블레이드에서 **저장** 단추를 클릭합니다.

	![구성 설정 저장][save-button]

기본 제공 PHP 런타임에 대해 아래 단계에 따라 시스템 수준 전용 지시문이 아닌 구성 옵션을 변경할 수 있습니다. 시스템 수준 전용 지시문에 대한 자세한 내용은 [php.ini 지시문 목록]을 참조하세요.

1. [.user.ini] 파일을 루트 디렉터리에 추가합니다.
2.  `php.ini` 파일에 사용한 것과 동일한 구문을 사용하여 구성 설정을  `.user.ini` 파일에 추가합니다. 예를 들어  `display_errors` 설정을 on으로 전환하고  `upload_max_filesize` 설정을 10M로 설정하려면  `.user.ini` 파일에 다음 텍스트를 포함합니다.

		; Example Settings
		display_errors=On
		upload_max_filesize=10M

3. 웹 앱에 배포합니다.
4. 웹 앱을 다시 시작합니다. PHP가  `.user.ini` 파일을 읽는 빈도는 시스템 수준 설정인  `user_ini.cache_ttl` 설정(기본적으로 300초[5분])의 적용을 받으므로 웹 사이트를 다시 시작할 필요가 있습니다. 웹 앱을 다시 시작해야 PHP가  `.user.ini` 파일의 새 설정을 읽습니다.

 `.user.ini` 파일을 사용하는 또 다른 방법은 스크립트에 [ini_set()] 함수를 사용하여 시스템 수준 지시문이 아닌 구성 옵션을 설정하는 것입니다.

## 방법: 기본 PHP 런타임에서 확장 사용
이전 섹션에 언급된 것처럼 기본 PHP 버전, 관련 기본 구성 및 사용하도록 설정된 확장을 보는 최상의 방법은 [phpinfo()]라는 스크립트를 배포하는 것입니다. 추가 확장을 사용하도록 설정하려면 아래 단계를 따르세요.

1.  `bin` 디렉터리를 루트 디렉터리에 추가합니다.
2.  `.dll` 확장 파일을  `bin` 디렉터리에 둡니다(예:  `php_mongo.dll`). 확장이 기본 버전의 PHP(본 문서 게시 시점에는 PHP 5.4)와 호환되며 VC9 및 nts(non-thread-safe)와 호환 가능해야 합니다.
3. 웹 앱에 배포합니다.
4. Azure 포털에서 웹 앱을 찾고 **설정** 단추를 클릭합니다.

	![Web App Settings][settings-button]

5. **설정** 블레이드에서 **응용 프로그램 설정**을 선택하고 **앱 설정** 섹션으로 스크롤합니다.
6. **앱 설정** 섹션에서 **PHP_EXTENSIONS** 키를 만듭니다. 이 키의 값은 웹 사이트 루트: **bin\your-ext-file**과 관련된 경로여야 합니다..

	![Enable extension in app settings][php-extensions]

7. **웹 앱 설정** 블레이드에서 **저장** 단추를 클릭합니다.

	![Save configuration settings][save-button]

Zend 확장은 **PHP_ZENDEXTENSIONS** 키 사용도 지원합니다. 여러 확장을 사용하려면 앱 설정 값에  `.dll` 파일의 쉼표로 구분된 목록을 포함합니다.

## 방법: 사용자 지정 PHP 런타임 사용
앱 서비스 웹 앱은 기본 PHP 런타임 대신, 사용자가 PHP 스크립트를 실행하도록 제공한 PHP 런타임을 사용할 수 있습니다. 제공한 런타임은 또한 사용자가 제공한  `php.ini` 파일로 구성될 수 있습니다. 웹 앱에 사용자 지정 PHP 런타임을 사용하려면 아래 단계를 따르세요.

1. non-thread-safe 및 VC9과 호환되는 버전의 Windows용 PHP를 받아야 합니다. 최신 Windows용 PHP 릴리스는 [http://windows.php.net/download/]에서 찾을 수 있습니다. 기존 릴리스는 [http://windows.php.net/downloads/releases/archives/]에서 보관 파일로 찾을 수 있습니다.
2. 런타임에 대한  `php.ini` 파일을 수정합니다. 참고로, 시스템 수준 전용 지시문인 구성 설정은 웹 앱에서 무시됩니다. 시스템 수준 전용 지시문에 대한 자세한 내용은 [php.ini 지시문 목록]을 참조하세요.
3. 경우에 따라 확장을 PHP 런타임에 추가하고  `php.ini` 파일에서 확장을 사용하도록 설정합니다.
4. 루트 디렉터리에  `bin` 디렉터리를 추가하고 PHP 런타임이 포함된 디렉터리(예:  `bin\php`)를 배치합니다.
5. 웹 앱에 배포합니다.
4. Azure 포털에서 웹 앱을 찾고 **설정** 단추를 클릭합니다.

	![Web App Settings][settings-button]

7. **설정** 블레이드에서 **응용 프로그램 설정**을 선택하고 **처리기 매핑** 섹션으로 스크롤합니다. `*.php`를 확장 필드에 추가하고 경로를  `php-cgi.exe` 실행 파일에 추가합니다. PHP 런타임을 응용 프로그램의 루트에 있는  `bin` 디렉터리에 배치하면 경로는  `D:\home\site\wwwroot\bin\php\php-cgi.exe`가 됩니다.

	![Specify handler in hander mappings][handler-mappings]

8. **웹 앱 설정** 블레이드에서 **저장** 단추를 클릭합니다.

	![Save configuration settings][save-button]

>[AZURE.NOTE] Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 사용](http://go.microsoft.com/fwlink/?LinkId=523751)으로 이동합니다. 앱 서비스에서는 단기 시작 웹 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

## 변경 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 가이드는 다음을 참조: [Azure 앱 서비스 및 기존 Azure 서비스에 대한 영향](http://go.microsoft.com/fwlink/?LinkId=529714)
* 이전 포털에서 새 포털로의 변경에 대한 가이드는 다음을 참조: [미리 보기 포털 탐색에 대한 참조](http://go.microsoft.com/fwlink/?LinkId=529715)

[무료 평가판]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo()]: http://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[php.ini 지시문 목록]: http://www.php.net/manual/en/ini.list.php
[.user.ini]: http://www.php.net/manual/en/configuration.file.per-user.php
[ini_set()]: http://www.php.net/manual/en/function.ini-set.php
[application-settings]: ./media/web-sites-php-configure/application-settings.png
[settings-button]: ./media/web-sites-php-configure/settings-button.png
[save-button]: ./media/web-sites-php-configure/save-button.png
[php-extensions]: ./media/web-sites-php-configure/php-extensions.png
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[http://windows.php.net/download/]: http://windows.php.net/download/
[http://windows.php.net/downloads/releases/archives/]: http://windows.php.net/downloads/releases/archives/

<!--HONumber=49-->