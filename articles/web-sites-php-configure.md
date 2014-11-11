<properties title="How to Configure PHP in Azure Websites" pageTitle="How to Configure PHP in Azure Websites" metaKeywords="Azure, Azure Web Sites, configuration, PHP" description="Learn how to configure the default PHP installation or add a custom PHP installation in Azure Websites." services="Web Sites" documentationCenter="PHP" authors="cephalin" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin" />

# Azure 웹 사이트에서 PHP를 구성하는 방법

이 가이드에서는 Azure 웹 사이트에서 기본 제공 PHP 런타임을 구성하고 사용자 지정 PHP 런타임을 제공하며 Azure 웹 사이트에서 확장을 사용하도록 설정하는 방법을 설명합니다. Azure 웹 사이트를 사용하려면 [무료 평가판][무료 평가판]에 등록해야 합니다. 이 가이드를 최대한 활용하려면 먼저 Azure 웹 사이트에서 PHP 사이트를 만들어야 합니다. 자세한 내용은 [PHP Developer Center 자습서][PHP Developer Center 자습서]를 참조하세요. Azure 웹 사이트의 사이트 구성에 대한 자세한 내용은 [웹 사이트를 구성하는 방법][웹 사이트를 구성하는 방법]을 참조하세요.

## 목차

-   [Azure 웹 사이트란?][Azure 웹 사이트란?]
-   [방법: 기본 PHP 구성 변경][방법: 기본 PHP 구성 변경]
-   [방법: 기본 제공 PHP 런타임에서 확장 사용][방법: 기본 제공 PHP 런타임에서 확장 사용]
-   [방법: 사용자 지정 PHP 런타임 사용][방법: 사용자 지정 PHP 런타임 사용]
-   [다음 단계][다음 단계]

## <a name="WhatIs"></a>Azure 웹 사이트란?

Azure 웹 사이트를 사용하면 Azure에서 고확장성 웹 사이트를 구축할 수 있습니다. 소규모로 시작했다가 트래픽이 증가함에 따라 확장할 수 있는 고확장성 클라우드 환경에 빠르고 쉽게 사이트를 배포할 수 있습니다. Azure 웹 사이트는 사용자가 선택한 언어 및 오픈 소스 앱을 사용하고 Git, FTP 및 TFS를 통해 배포할 수 있도록 지원합니다. MySQL, SQL 데이터베이스, 캐싱, CDN, 저장소 등 기타 서비스를 쉽게 통합할 수 있습니다.

## <a name="ChangeBuiltInPHP"></a>방법: 기본 제공 PHP 구성 변경

기본적으로 PHP 5.4는 Azure 웹 사이트를 만들 때 설치하여 바로 사용할 수 있습니다. 사용 가능한 릴리스 버전, 관련 기본 구성 및 사용하도록 설정된 확장을 보는 최상의 방법은 [phpinfo()][phpinfo()] 함수를 호출하는 스크립트를 배포하는 것입니다.

PHP 5.5도 사용할 수 있지만 기본적으로 이는 사용하도록 설정되어 있지 않습니다. 사용하도록 설정하려면 다음 단계를 따르십시오.

1.  Azure 포털의 웹 사이트 대시보드로 이동하고 **구성**을 클릭합니다.

    ![웹 사이트 대시보드의 구성 탭][웹 사이트 대시보드의 구성 탭]

2.  PHP 5.5를 클릭합니다.

    ![PHP 버전 선택][PHP 버전 선택]

3.  페이지 맨 아래에서 **저장**을 클릭합니다.

    ![구성 설정 저장][구성 설정 저장]

기본 제공 PHP 런타임에 대해 아래 단계에 따라 시스템 수준 전용 지시문이 아닌 구성 옵션을 변경할 수 있습니다. 시스템 수준 전용 지시문에 대한 자세한 내용은 [php.ini 지시문 목록][php.ini 지시문 목록](영문)을 참조하십시오.

1.  [.user.ini][.user.ini] 파일을 루트 디렉터리에 추가합니다.
2.  `php.ini` 파일에 사용한 것과 동일한 구문을 사용하여 구성 설정을 `.user.ini` 파일에 추가합니다. 예를 들어 `display_errors` 설정을 on으로 전환하고 `upload_max_filesize` 설정을 10M로 설정하려면 `.user.ini` 파일에 다음 텍스트를 포함합니다.

        ; Example Settings
        display_errors=On
        upload_max_filesize=10M

3.  응용 프로그램을 배포합니다.
4.  웹 사이트를 다시 시작합니다. PHP가 `.user.ini` 파일을 읽는 빈도는 시스템 수준 설정인 `user_ini.cache_ttl` 설정(기본적으로 300초[5분])의 적용을 받으므로 웹 사이트를 다시 시작할 필요가 있습니다. 사이트를 다시 시작해야 PHP가 `.user.ini` 파일의 새 설정을 읽습니다.

`.user.ini` 파일을 사용하는 또 다른 방법은 스크립트에 [ini\_set()][ini\_set()] 함수를 사용하여 시스템 수준 지시문이 아닌 구성 옵션을 설정하는 것입니다.

## <a name="EnableExtDefaultPHP"></a>방법: 기본 PHP 런타임에서 확장 사용

이전 섹션에 언급된 것처럼 기본 PHP 버전, 관련 기본 구성 및 사용하도록 설정된 확장을 보는 최상의 방법은 [phpinfo()][phpinfo()]라는 스크립트를 배포하는 것입니다. 추가 확장을 사용하도록 설정하려면 아래 단계를 따르십시오.

1.  `bin` 디렉터리를 루트 디렉터리에 추가합니다.
2.  `.dll` 확장 파일을 `bin` 디렉터리에 둡니다(예: `php_mongo.dll`). 확장이 기본 버전의 PHP(본 문서 게시 시점에는 PHP 5.4)와 호환되며 VC9 및 nts(non-thread-safe)와 호환 가능해야 합니다.
3.  응용 프로그램을 배포합니다.
4.  Azure 포털의 사이트 대시보드로 이동하고 **구성**을 클릭합니다.

    ![웹 사이트 대시보드의 구성 탭][웹 사이트 대시보드의 구성 탭]

5.  **앱 설정** 섹션에서 **PHP\_EXTENSIONS** 키 및 **bin\\your-ext-file** 값을 만듭니다. 여러 확장을 사용하도록 설정하려면 `.dll` 파일의 쉼표로 구분된 목록을 포함합니다.

    ![앱 설정의 확장 사용][앱 설정의 확장 사용]

6.  페이지 맨 아래에서 **저장**을 클릭합니다.

    ![구성 설정 저장][구성 설정 저장]

## <a name="UseCustomPHP"></a>방법: 사용자 지정 PHP 런타임 사용

Azure 웹 사이트는 기본 PHP 런타임 대신, 사용자가 PHP 스크립트를 실행하도록 제공한 PHP 런타임을 사용할 수 있습니다. 제공한 런타임은 또한 사용자가 제공한 `php.ini` 파일로 구성될 수 있습니다. Azure 웹 사이트에 사용자 지정 PHP 런타임을 사용하려면 아래 단계를 따르세요.

1.  non-thread-safe 및 VC9과 호환되는 버전의 Windows용 PHP를 받아야 합니다. 최신 Windows용 PHP 릴리스는 [][]<http://windows.php.net/download/></a>에서 찾을 수 있습니다. 기존 릴리스는 [][1]<http://windows.php.net/downloads/releases/archives/></a>에서 보관 파일로 찾을 수 있습니다.
2.  런타임에 대한 `php.ini` 파일을 수정합니다. 참고로, 시스템 수준 전용 지시문인 구성 설정은 Azure 웹 사이트에서 무시됩니다. 시스템 수준 전용 지시문에 대한 자세한 내용은 [php.ini 지시문 목록][php.ini 지시문 목록](영문)을 참조하십시오.
3.  경우에 따라 확장을 PHP 런타임에 추가하고 `php.ini` 파일에서 확장을 사용하도록 설정합니다.
4.  루트 디렉터리에 `bin` 디렉터리를 추가하고 PHP 런타임이 포함된 디렉터리를 해당 bin 디렉터리(예: `bin\php`)에 배치합니다.
5.  응용 프로그램을 배포합니다.
6.  Azure 포털의 사이트 대시보드로 이동하고 **구성**을 클릭합니다.

    ![웹 사이트 대시보드의 구성 탭][웹 사이트 대시보드의 구성 탭]

7.  **처리기 매핑** 섹션에서 `*.php`를 확장에 추가하고 경로를 `php-cgi.exe` 실행 파일에 추가합니다. PHP 런타임을 응용 프로그램의 루트에 있는 `bin` 디렉터리에 배치하면 경로는 `D:\home\site\wwwroot\bin\php\php-cgi.exe`가 됩니다.

    ![처리기 매핑에 처리기 지정][처리기 매핑에 처리기 지정]

8.  페이지 맨 아래에서 **저장**을 클릭합니다.

    ![구성 설정 저장][구성 설정 저장]

## <a name="NextSteps"></a>다음 단계

Azure 웹 사이트에서 PHP를 구성하는 방법을 배웠으므로 다음 링크를 따라 다른 작업을 더 수행하는 방법을 알아보세요.

-   [Azure에서 웹 사이트 구성, 모니터링 및 크기 조정(영문)][Azure에서 웹 사이트 구성, 모니터링 및 크기 조정(영문)]
-   [PHP용 Azure SDK 다운로드][PHP용 Azure SDK 다운로드]

  [무료 평가판]: https://www.windowsazure.com/ko-KR/pricing/free-trial/
  [PHP Developer Center 자습서]: https://www.windowsazure.com/ko-KR/develop/php/tutorials/
  [웹 사이트를 구성하는 방법]: https://www.windowsazure.com/ko-KR/manage/services/web-sites/how-to-configure-websites/
  [방법: 기본 PHP 구성 변경]: #ChangeBuiltInPHP
  [방법: 기본 제공 PHP 런타임에서 확장 사용]: #EnableExtDefaultPHP
  [방법: 사용자 지정 PHP 런타임 사용]: #UseCustomPHP
  [다음 단계]: #NextSteps
  [웹 사이트 대시보드의 구성 탭]: ./media/web-sites-php-configure/configure.png
  [PHP 버전 선택]: ./media/web-sites-php-configure/select-php-version.png
  [구성 설정 저장]: ./media/web-sites-php-configure/save-button.png
  [php.ini 지시문 목록]: http://www.php.net/manual/en/ini.list.php
  [.user.ini]: http://www.php.net/manual/en/configuration.file.per-user.php
  [앱 설정의 확장 사용]: ./media/web-sites-php-configure/app-settings.png
  []: http://windows.php.net/download/
  [1]: http://windows.php.net/downloads/releases/archives/
  [처리기 매핑에 처리기 지정]: ./media/web-sites-php-configure/handler-mappings.png
  [PHP용 Azure SDK 다운로드]: http://www.windowsazure.com/ko-KR/develop/php/common-tasks/download-php-sdk/
