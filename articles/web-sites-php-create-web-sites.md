<properties title="How to create a PHP website in Azure Websites" pageTitle="Azure 웹 사이트에서 PHP 웹 사이트를 만드는 방법" metaKeywords ="PHP Azure 웹 사이트" description="Azure 웹 사이트에서 PHP 웹 사이트를 만드는 방법에 대해 알아봅니다." documentationCenter="PHP" services="Web Sites" editor="mollybos" manager="wpickett" authors="tomfitz" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="10/21/2014" ms.author="tomfitz" />

#Azure 웹 사이트에서 PHP 웹 사이트를 만드는 방법

이 문서에서는[Azure 웹 사이트][waws]에서 [Azure 관리 포털], [Mac 및 Linux용 Azure 명령줄 도구][xplat-tools] 또는 [Azure PowerShell cmdlet][powershell-cmdlets]을 사용하여 PHP를 웹 사이트를 만드는 방법을 보여 줍니다.

일반적으로, PHP 웹 사이트를 만드는 작업은 Azure 웹 사이트에서 *임의의* 웹 사이트를 만드는 방법과 동일합니다. 기본적으로, PHP는 모든 웹 사이트에 대해 사용할 수 있습니다. PHP 구성(또는 사용자 지정된 PHP 런타임 제공)에 대한 자세한 내용은 [Azure 웹 사이트에서 PHP를 구성하는 방법][configure-php]을 참조하세요.

아래에 설명된 각 옵션은 공유 호스팅 환경에서 웹 사이트를 무료로 만드는 방법을 보여 줍니다. 그러나 CPU 및 대역폭 사용량에는 어느 정도 제한이 있습니다. 자세한 내용은 [Azure 웹 사이트 가격 책정][websites-pricing]을 참조하세요. 웹 사이트를 업그레이드하고 크기를 조정하는 방법에 대한 자세한 내용은 [웹 사이트 크기를 조정하는 방법][scale-websites]을 참조하세요.

##목차
* [Azure 관리 포털을 사용하여 웹 사이트 만들기](#portal)
* [Mac 및 Linux용 Azure 명령줄 도구를 사용하여 웹 사이트 만들기](#XplatTools)
* [Azure PowerShell cmdlet을 사용하여 웹 사이트 만들기](#PowerShell)

<h2><a name="portal"></a>Azure 관리 포털을 사용하여 PHP 웹 사이트 만들기</h2>

Azure 관리 포털에서 웹 사이트를 만들 때 3가지 옵션인 **빠른 생성**, **데이터베이스를 사용하여 만들기** 및 **갤러리에서**를 사용할 수 있습니다. 아래 지침에서는 **빠른 생성** 옵션을 사용합니다. 다른 두 옵션에 대한 자세한 내용은 [PHP-MySQL Azure 웹 사이트 만들기 및 Git를 사용하여 배포][website-mysql-git] 및 [Azure의 갤러리에서 WordPress 웹 사이트 만들기][wordpress-gallery]를 참조하세요.

Azure 관리 포털을 사용하여 PHP 웹 사이트를 만들려면 다음을 수행하세요.

1. [Azure 관리 포털]에 로그인합니다.
1. 페이지 맨 아래에 있는 **새로 만들기**를 클릭하고 **계산**, **웹 사이트**, **빠른 생성**을 차례로 클릭합니다. 웹 사이트의 **URL**을 입력하고 웹 사이트의 **지역**을 선택합니다. 마지막으로 **웹 사이트 만들기**를 클릭합니다.

	![Select Quick Create web site](./media/web-sites-php-create-web-sites/select-quickcreate-website.png)

<h2><a name="XplatTools"></a>Mac 및 Linux용 Azure 명령줄 도구를 사용하여 PHP 웹 사이트 만들기</h2>

Mac 및 Linux용 Azure 명령줄 도구를 사용하여 PHP 웹 사이트 만들려면 다음을 수행하세요.

1. 다음 링크의 지침을 따라서 Azure 명령줄 도구를 설치합니다. [Mac 및 Linux용 Azure 명령줄 도구를 설치하는 방법](/ko-kr/develop/php/how-to-guides/command-line-tools/#Download).

1. 다음 링크의 지침을 따라서 게시 설정 파일을 다운로드하여 가져옵니다. [게시 설정을 다운로드 및 가져오는 방법](/ko-kr/develop/php/how-to-guides/command-line-tools/#Account).

1. 명령 프롬프트에서 다음 명령을 실행합니다.

		azure site create MySiteName

새로 만든 웹 사이트의 URL은 `http://MySiteName.azurewebsites.net`입니다.  
 
다음 옵션 중 하나와 함께 'azure site create' 명령을 실행할 수 있습니다.

* `--location [location name]`. 이 옵션을 사용하면 웹 사이트가 만들어지는 데이터 센터의 위치를 지정할 수 있습니다(예: "미국 서부"). 이 옵션을 생략하면 위치를 선택하라는 메시지가 표시됩니다.
* `--hostname [custom host name]`. 이 옵션을 사용하면 웹 사이트에 대한 사용자 지정 호스트 이름을 지정할 수 있습니다.
* `--git`. 이 옵션을 통해 로컬 응용 프로그램 디렉터리와 웹 사이트의 데이터 센터 둘 다에 git 리포지토리를 만들면 git를 사용하여 웹 사이트에 게시할 수 있습니다. 로컬 폴더가 이미 git 리포지토리인 경우 이 명령은 기존 리포지토리에 새 원격을 추가하며 웹 사이트 데이터 센터의 리포지토리를 가리킵니다.

추가 옵션에 대한 자세한 내용은 [Azure 웹 사이트를 만들고 관리하는 방법](/ko-kr/develop/php/how-to-guides/command-line-tools/#WebSites)을 참조하세요.

<h2><a name="PowerShell"></a>Azure PowerShell cmdlet을 사용하여 PHP 웹 사이트 만들기</h2>

Azure PowerShell cmdlet을 사용하여 PHP 웹 사이트를 만들려면 다음을 수행하세요.

1. 다음 링크의 지침을 따라서 Azure PowerShell cmdlet을 설치합니다. [Azure PowerShell 시작](/ko-kr/develop/php/how-to-guides/powershell-cmdlets/#GetStarted).

1. 다음 링크의 지침을 따라서 게시 설정 파일을 다운로드하여 가져옵니다. [방법: 게시 설정 가져오기](/ko-kr/develop/php/how-to-guides/powershell-cmdlets/#ImportPubSettings)

1. PowerShell 명령 프롬프트를 열고 다음 명령을 실행합니다.

		New-AzureWebSite MySiteName

새로 만든 웹 사이트의 URL은 `http://MySiteName.azurewebsites.net`입니다.  
 
다음 옵션 중 하나와 함께 'New-AzureWebSite' 명령을 실행할 수 있습니다.

* `-Location [location name]`. 이 옵션을 사용하면 웹 사이트가 만들어지는 데이터 센터의 위치를 지정할 수 있습니다(예: "미국 서부"). 이 옵션을 생략하면 위치를 선택하라는 메시지가 표시됩니다.
* `-Hostname [custom host name]`. 이 옵션을 사용하면 웹 사이트에 대한 사용자 지정 호스트 이름을 지정할 수 있습니다.
* `-Git`. 이 옵션을 통해 로컬 응용 프로그램 디렉터리와 웹 사이트의 데이터 센터 둘 다에 git 리포지토리를 만들면 git를 사용하여 웹 사이트에 게시할 수 있습니다. 로컬 폴더가 이미 git 리포지토리인 경우 이 명령은 기존 리포지토리에 새 원격을 추가하며 웹 사이트 데이터 센터의 리포지토리를 가리킵니다.

추가 옵션에 대한 자세한 내용은 [방법: Azure 웹 사이트 만들기 및 관리](/ ko-kr/develop/php/how-to-guides/powershell-cmdlets/#WebSite)를 참조하세요.

<h2><a name="NextSteps"></a>다음 단계</h2>

Azure 웹 사이트에서 PHP 웹 사이트를 만들었으므로, 이제 사이트를 관리, 구성 및 모니터링하고, 사이트에 배포하고, 사이트 크기를 조정할 수 있습니다. 자세한 내용은 다음 링크를 참조하세요.

* [웹 사이트를 구성하는 방법](/ko-kr/manage/services/web-sites/how-to-configure-websites/)
* [Azure 웹 사이트에서 PHP를 구성하는 방법][configure-php]
* [웹 사이트를 관리하는 방법](/ko-kr/manage/services/web-sites/how-to-manage-websites/)
* [웹 사이트를 모니터링하는 방법](/ko-kr/manage/services/web-sites/how-to-monitor-websites/)
* [웹 사이트 크기를 조정하는 방법](/ko-kr/manage/services/web-sites/how-to-scale-websites/)
* [Git로 게시](/ko-kr/develop/php/common-tasks/publishing-with-git/)

광범위한 자습서는 [PHP 개발자 센터 - 자습서](/ko-kr/develop/php/tutorials/) 페이지를 참조하세요.

[waws]: /ko-kr/manage/services/web-sites/
[Azure Management Portal]: http://manage.windowsazure.com/
[xplat-tools]: /ko-kr/develop/php/how-to-guides/command-line-tools/
[powershell-cmdlets]: /ko-kr/develop/php/how-to-guides/powershell-cmdlets/
[configure-php]: /ko-kr/develop/php/common-tasks/configure-php-web-site/
[website-mysql-git]: /ko-kr/develop/php/tutorials/website-w-mysql-and-git/
[wordpress-gallery]: /ko-kr/develop/php/tutorials/website-from-gallery/
[websites-pricing]: http://www.windowsazure.com/ko-kr/pricing/details/#header-1
[scale-websites]: /ko-kr/manage/services/web-sites/how-to-scale-websites/
