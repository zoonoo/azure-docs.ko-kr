<properties
	pageTitle="Azure 앱 서비스 웹 앱에서 PHP 구성"
	description="Azure 앱 서비스의 웹앱에서 기본 PHP 설치를 구성하거나 사용자 지정 PHP 설치를 추가하는 방법에 대해 알아봅니다."
	services="app-service"
	documentationCenter="php"
	authors="tfitzmac"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="article"
	ms.date="09/16/2015"
	ms.author="tomfitz"/>

#Azure 앱 서비스 웹 앱에서 PHP 구성

## 소개

이 가이드에서는 [Azure 앱 서비스](http://go.microsoft.com/fwlink/?LinkId=529714)에서 웹앱에 대한 기본 제공 PHP 런타임을 구성하고 사용자 지정 PHP 런타임을 제공하며 확장을 사용하도록 설정하는 방법을 보여 줍니다. 앱 서비스를 사용하려면 [무료 평가판]에 등록해야 합니다. 이 가이드를 최대한 활용하려면 먼저 앱 서비스에서 PHP 웹 앱을 만들어야 합니다.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## 방법: 기본 제공 PHP 버전 변경
기본적으로 PHP 5.4는 앱 서비스 웹 앱을 만들 때 설치하여 바로 사용할 수 있습니다. 사용 가능한 릴리스 버전, 관련 기본 구성 및 사용하도록 설정된 확장을 보는 최상의 방법은 [phpinfo()] 함수를 호출하는 스크립트를 배포하는 것입니다.

PHP 5.5 및 PHP 5.6 버전 사용할 수 있지만 기본적으로 이는 사용하도록 설정되어 있지 않습니다. PHP 버전을 업데이트하려면 다음 방법 중 하나를 따르세요.

### Azure 포털

1. [Azure 포털](https://portal.azure.com)에서 웹앱을 찾아 **설정** 단추를 클릭합니다.

	![웹앱 설정][settings-button]

2. **설정** 블레이드에서 **응용 프로그램 설정**을 선택하고 새 PHP 버전을 선택합니다.

    ![응용 프로그램 설정][application-settings]

3. **웹앱 설정** 블레이드의 위쪽에서 **저장** 단추를 클릭합니다.

	![구성 설정 저장][save-button]

### Azure PowerShell(Windows)

1. Windows PowerShell을 엽니다.
2. `Set-AzureWebsite -PhpVersion [5.4 | 5.5 | 5.6] -Name <site-name>`을 입력하고 Enter 키를 누릅니다.
3. PHP 버전이 설정되었습니다.

	![Azure PowerShell을 사용하여 PHP 버전 설정][SETPHPVERPS]
4. `Get-AzureWebiste -Name <site-name>`을 입력하여 이러한 설정을 확인한 후 Enter 키를 누릅니다.

	![Azure PowerShell을 사용하여 PHP 버전 확인][GETPHPVERPS]

### Azure 명령줄 인터페이스(Linux, Mac, Windows)

Azure 명령줄 인터페이스를 사용하려면 **Node.js**를 컴퓨터에 설치해야 합니다.

1. 터미널을 엽니다.
2. `azure site set --php-version [5.4 | 5.5] [site-name]`을 입력하고 Enter 키를 누릅니다.
3. PHP 버전이 설정되었습니다.

	![Azure 명령줄 인터페이스를 사용하여 PHP 버전 설정][SETPHPVERCLI]
4. `azure site show [site-name]`을 입력하여 이러한 설정을 확인한 후 Enter 키를 누릅니다.

	![Azure 명령줄 인터페이스를 사용하여 PHP 버전 확인][GETPHPVERCLI]

## 방법: 기본 제공 PHP 구성 변경

기본 제공 PHP 런타임에 대해 아래 단계에 따라 구성 옵션을 변경할 수 있습니다. php.ini 지시문에 대한 자세한 내용은 [php.ini 지시문 목록]을 참조하세요.

### PHP\_INI\_USER, PHP\_INI\_PERDIR, PHP\_INI\_ALL 구성 설정 변경

1. [.user.ini] 파일을 루트 디렉터리에 추가합니다.
2. `php.ini` 파일에 사용한 것과 동일한 구문을 사용하여 구성 설정을 `.user.ini` 파일에 추가합니다. 예를 들어 `display_errors` 설정을 켜고 `upload_max_filesize` 설정을 10M로 설정하려면 `.user.ini` 파일에 다음 텍스트를 포함합니다.

		; Example Settings
		display_errors=On
		upload_max_filesize=10M

3. 웹 앱에 배포합니다.
4. 웹 앱을 다시 시작합니다. PHP가 `.user.ini` 파일을 읽는 빈도는 시스템 수준 설정인 `user_ini.cache_ttl` 설정(기본적으로 300초[5분])의 적용을 받으므로 웹앱을 다시 시작해야 합니다. 웹앱을 다시 시작하면 PHP가 `.user.ini` 파일에서 새 설정을 읽습니다.

`.user.ini` 파일을 사용하는 대신 스크립트에서 [ini\_set()] 함수를 사용하여 시스템 수준 지시문이 아닌 구성 옵션을 설정할 수도 있습니다.

### PHP\_INI\_SYSTEM 구성 설정 변경

1. `PHP_INI_SCAN_DIR` 키 및 `d:\home\site\ini` 값으로 웹앱에 앱 설정을 추가합니다.
2. Kudu 콘솔(http://&lt;site-name&gt;.scm.azurewebsite.net)을 사용하여 `d:\home\site\ini` 디렉토리에서 `settings.ini` 파일을 만듭니다.
3. php.ini 파일에서 사용한 것과 동일한 구문을 사용하여 구성 설정을 `settings.ini` 파일에 추가합니다. 예를 들어 `curl.cainfo` 설정이 `*.crt` 파일을 가리키고 'wincache.maxfilesize' 설정을 512K로 설정하려면 `settings.ini` 파일에 다음 텍스트를 포함합니다.

		; Example Settings
		curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
		wincache.maxfilesize=512
4. 웹앱을 다시 시작하여 변경 내용을 로드합니다.

## 방법: 기본 PHP 런타임에서 확장 사용
이전 섹션에 언급된 것처럼 기본 PHP 버전, 관련 기본 구성 및 사용하도록 설정된 확장을 보는 최상의 방법은 [phpinfo()]라는 스크립트를 배포하는 것입니다. 추가 확장을 사용하도록 설정하려면 아래 단계를 따르세요.

### Ini 설정을 통해 구성

1. `ext` 디렉터리를 `d:\home\site` 디렉터리에 추가합니다.
2. `.dll` 확장 파일을 `ext` 디렉터리에 둡니다(예: `php_mongo.dll` `php_xdebug.dll`). 확장이 기본 버전의 PHP(본 문서 게시 시점에는 PHP 5.4)와 호환되며 VC9 및 nts(non-thread-safe)와 호환 가능해야 합니다.
3. `PHP_INI_SCAN_DIR` 키 및 `d:\home\site\ini` 값으로 웹앱에 앱 설정을 추가합니다.
4. `d:\home\site\ini`에 `extensions.ini`라는 `ini` 파일을 만듭니다.
5. php.ini 파일에서 사용한 것과 동일한 구문을 사용하여 구성 설정을 `extensions.ini` 파일에 추가합니다. 예를 들어 MongoDB 및 XDebug 확장을 사용하려면 `extensions.ini` 파일에 다음 텍스트를 포함합니다.

		; Enable Extensions
		extension=d:\home\site\ext\php_mongo.dll
		zend_extension=d:\home\site\ext\php_xdebug.dll
6. 웹앱을 다시 시작하여 변경 내용을 로드합니다.

### 앱 설정을 통해 구성

1. `bin` 디렉터리를 루트 디렉터리에 추가합니다.
2. `.dll` 확장 파일을 `bin` 디렉터리에 둡니다(예: `php_mongo.dll`). 확장이 기본 버전의 PHP(본 문서 게시 시점에는 PHP 5.4)와 호환되며 VC9 및 nts(non-thread-safe)와 호환 가능해야 합니다.
3. 웹 앱에 배포합니다.
4. Azure 포털에서 웹앱을 찾아 **설정** 단추를 클릭합니다.

	![웹앱 설정][settings-button]

5. **설정** 블레이드에서 **응용 프로그램 설정**을 선택하고 **앱 설정** 섹션으로 스크롤합니다.
6. **앱 설정** 섹션에서 **PHP\_EXTENSIONS** 키를 만듭니다. 이 키의 값은 웹 사이트 루트 **bin\\your-ext-file**에 상대적인 경로입니다.

	![앱 설정의 확장 사용][php-extensions]

7. **웹앱 설정** 블레이드의 위쪽에서 **저장** 단추를 클릭합니다.

	![구성 설정 저장][save-button]

**PHP\_ZENDEXTENSIONS** 키를 통해 Zend 확장도 지원됩니다. 여러 확장을 사용하려면 앱 설정 값에 `.dll` 파일의 쉼표로 구분된 목록을 포함합니다.


## 방법: 사용자 지정 PHP 런타임 사용
앱 서비스 웹 앱은 기본 PHP 런타임 대신, 사용자가 PHP 스크립트를 실행하도록 제공한 PHP 런타임을 사용할 수 있습니다. 이러한 런타임은 사용자가 제공한 `php.ini` 파일로 구성될 수 있습니다. 웹 앱에 사용자 지정 PHP 런타임을 사용하려면 아래 단계를 따르세요.

1. 스레드로부터 안전하지 않은 VC9 또는 VC11과 호환되는 버전의 Windows용 PHP를 받아야 합니다. 최신 Windows용 PHP 릴리스는 [http://windows.php.net/download/]에서 확인할 수 있습니다. 이전 릴리스는 보관 파일 [http://windows.php.net/downloads/releases/archives/]에서 확인할 수 있습니다.
2. 런타임에 맞게 `php.ini` 파일을 수정합니다. 참고로, 시스템 수준 전용 지시문인 구성 설정은 웹 앱에서 무시됩니다. 시스템 수준 전용 지시문에 대한 자세한 내용은 [php.ini 지시문 목록](영문)을 참조하세요.
3. 경우에 따라 확장을 PHP 런타임에 추가하고 `php.ini` 파일에서 확장을 사용하도록 설정합니다.
4. 루트 디렉터리에 `bin` 디렉터리를 추가하고 PHP 런타임이 포함된 디렉터리(예: `bin\php`)를 배치합니다.
5. 웹 앱에 배포합니다.
4. Azure 포털에서 웹앱을 찾아 **설정** 단추를 클릭합니다.

	![웹앱 설정][settings-button]

7. **설정** 블레이드에서 **응용 프로그램 설정**을 선택하고 **처리기 매핑** 섹션으로 스크롤합니다. `*.php`를 확장 필드에 추가하고 경로를 `php-cgi.exe` 실행 파일에 추가합니다. PHP 런타임을 응용 프로그램의 루트에 있는 `bin` 디렉터리에 배치하면 경로는 `D:\home\site\wwwroot\bin\php\php-cgi.exe`가 됩니다.

	![처리기 매핑에 처리기 지정][handler-mappings]

8. **웹앱 설정** 블레이드의 위쪽에서 **저장** 단추를 클릭합니다.

	![구성 설정 저장][save-button]

## 다음 단계

자세한 내용은 [PHP 개발자 센터](/develop/php/)를 참조하세요.

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 웹 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

## 변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)을 참조하세요.

[무료 평가판]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo()]: http://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[php.ini 지시문 목록]: http://www.php.net/manual/en/ini.list.php
[.user.ini]: http://www.php.net/manual/en/configuration.file.per-user.php
[ini\_set()]: http://www.php.net/manual/en/function.ini-set.php
[application-settings]: ./media/web-sites-php-configure/application-settings.png
[settings-button]: ./media/web-sites-php-configure/settings-button.png
[save-button]: ./media/web-sites-php-configure/save-button.png
[php-extensions]: ./media/web-sites-php-configure/php-extensions.png
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[http://windows.php.net/download/]: http://windows.php.net/download/
[http://windows.php.net/downloads/releases/archives/]: http://windows.php.net/downloads/releases/archives/
[SETPHPVERCLI]: ./media/web-sites-php-configure/ChangePHPVersion-XPlatCLI.png
[GETPHPVERCLI]: ./media/web-sites-php-configure/ShowPHPVersion-XplatCLI.png
[SETPHPVERPS]: ./media/web-sites-php-configure/ChangePHPVersion-PS.png
[GETPHPVERPS]: ./media/web-sites-php-configure/ShowPHPVersion-PS.png
 

<!---HONumber=AcomDC_1203_2015-->