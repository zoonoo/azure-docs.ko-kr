<properties
	pageTitle="Azure 앱 서비스에서 PHP 웹 앱 만들기"
	description="Azure 앱 서비스.에서 PHP 웹 앱을 만드는 방법에 대해 알아봅니다"
	documentationCenter="php"
	services="app-service\web"
	editor="mollybos"
	manager="wpickett"
	authors="tfitzmac"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="hero-article"
	ms.date="04/29/2015"
	ms.author="tomfitz"/>

# Azure 앱 서비스에서 PHP 웹 앱 만들기

## 개요
이 문서에서는 [Azure Preview 포털](https://portal.azure.com), [Azure CLI][Azure CLI] 또는 [Azure PowerShell cmdlets][powershell-cmdlets]을 사용하여 [Azure 앱 서비스]에서 PHP를 웹앱을 만드는 방법을 보여 줍니다.

일반적으로, PHP 웹앱을 만드는 작업은 Azure 앱 서비스에서 *모든* 웹앱을 만드는 방법과 동일합니다. 기본적으로, PHP는 모든 웹 앱에 대해 사용할 수 있습니다. PHP 구성(또는 사용자 지정된 PHP 런타임 제공)에 대한 자세한 내용은 [Azure 앱 서비스 웹앱에서 PHP 구성]을 참조하세요.

아래에 설명된 각 옵션은 공유 호스팅 환경에서 웹 앱을 무료로 만드는 방법을 보여 줍니다. 그러나 CPU 및 대역폭 사용량에는 어느 정도 제한이 있습니다. 자세한 내용은 [앱 서비스 가격]을 참조하세요. 앱 서비스에서 웹앱을 업그레이드하고 크기 조정하는 방법에 대한 자세한 내용은 [Azure 앱 서비스에서 웹앱 크기 조정]을 참조하세요.

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 웹앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

## Azure Preview 포털을 사용하여 PHP 웹앱 만들기

Azure Preview 포털에서 PHP 웹앱을 만들 때 다음과 같은 3가지 옵션이 있습니다.

- **빠른 생성** - [방법: Azure Preview 포털을 사용하여 웹앱을 만드는 방법](../web-sites-create-deploy.md#createawebsiteportal) 참조
- **데이터베이스와 함께 만들기** - [Azure 앱 서비스에서 PHP-MySQL 웹앱 만들기 및 Git를 사용하여 배포] 참조
- **마켓플레이스에서** - [Azure 앱 서비스에서 WordPress 웹앱 만들기] 참조

## Azure CLI를 사용하여 PHP 웹앱 만들기

Azure CLI를 사용하여 PHP 웹앱을 만들려면 다음을 수행하세요.

1. [Azure CLI를 설치하는 방법](../xplat-cli.md#install)의 지침에 따라 Azure CLI를 설치합니다.

1. [게시 설정을 다운로드 및 가져오는 방법](../xplat-cli.md#configure)의 지침을 따라서 게시 설정 파일을 다운로드하여 가져옵니다.

1. 명령 프롬프트에서 다음 명령을 실행합니다.

		azure site create MyWebAppName

새로 만들어진 웹앱의 URL은 `http://MyWebAppName.azurewebsites.net`이 됩니다.

다음 옵션 중 하나와 함께 `azure site create` 명령을 실행할 수 있습니다.

* `--location [location name]`. 이 옵션을 사용하면 웹앱이 만들어지는 데이터 센터의 위치를 지정할 수 있습니다(예: "미국 서부"). 이 옵션을 생략하면 위치를 선택하라는 메시지가 표시됩니다.
* `--hostname [custom host name]`. 이 옵션을 사용하면 웹 앱에 대한 사용자 지정 호스트 이름을 지정할 수 있습니다.
* `--git`. 이 옵션을 통해 로컬 응용 프로그램 디렉터리와 웹 앱의 데이터 센터 둘 다에 git 리포지토리를 만들면 git를 사용하여 웹 앱에 게시할 수 있습니다. 로컬 폴더가 이미 git 리포지토리인 경우 이 명령은 기존 리포지토리에 새 원격을 추가하며 웹 앱 데이터 센터의 리포지토리를 가리킵니다.

## Azure PowerShell cmdlet을 사용하여 PHP 웹 앱 만들기

Azure PowerShell cmdlet을 사용하여 PHP 웹 앱을 만들려면 다음을 수행하십시오.

1. [Azure PowerShell 시작](/develop/php/how-to-guides/powershell-cmdlets/#GetStarted)의 지침을 따라서 Azure PowerShell cmdlet을 설치합니다.

1. [방법: 게시 설정 가져오기](/develop/php/how-to-guides/powershell-cmdlets/#ImportPubSettings)의 지침을 따라서 게시 설정 파일을 다운로드하여 가져옵니다.

1. PowerShell 명령 프롬프트를 열고 다음 명령을 실행합니다.

		New-AzureWebsite MyWebAppName

새로 만들어진 웹앱의 URL은 `http://MyWebAppName.azurewebsites.net`이 됩니다.

다음 옵션 중 하나와 함께 `New-AzureWebsite` 명령을 실행할 수 있습니다.

* `-Location [location name]`. 이 옵션을 사용하면 웹앱이 만들어지는 데이터 센터의 위치를 지정할 수 있습니다(예: "미국 서부"). 이 옵션을 생략하면 위치를 선택하라는 메시지가 표시됩니다.
* `-Hostname [custom host name]`. 이 옵션을 사용하면 웹 앱에 대한 사용자 지정 호스트 이름을 지정할 수 있습니다.
* `-Git`. 이 옵션을 통해 로컬 응용 프로그램 디렉터리와 웹 앱의 데이터 센터 둘 다에 git 리포지토리를 만들면 git를 사용하여 웹 앱에 게시할 수 있습니다. 로컬 폴더가 이미 git 리포지토리인 경우 이 명령은 기존 리포지토리에 새 원격을 추가하며 웹 앱 데이터 센터의 리포지토리를 가리킵니다.

## 다음 단계

Azure 앱 서비스에서 PHP 웹 앱을 만들었으므로, 이제 앱을 관리, 구성 및 모니터링하고, 배포하고, 앱 크기를 조정할 수 있습니다. 자세한 내용은 다음 링크를 참조하세요.

* [Azure 앱 서비스에서 웹앱 구성](web-sites-configure.md)
* [Azure 앱 서비스 웹앱에서 PHP 구성]
* [Azure 포털을 사용하여 웹앱 관리](web-sites-manage.md)
* [Azure 앱 서비스에서 웹앱 모니터링](web-sites-monitor.md)
* [Azure 앱 서비스에서 웹 앱 크기 조정]
* [Azure 앱 서비스에서 GIT를 사용하여 연속 배포](web-sites-publish-source-control.md)

종단 간 자습서에 대해서는 [PHP Developer Center - 자습서](/develop/php/tutorials/) 페이지를 참조하십시오.

## 변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)을 참조하세요.
* 포털에서 미리 보기 포털로의 변경에 대한 지침은 [미리 보기 포털 탐색에 대한 참조](http://go.microsoft.com/fwlink/?LinkId=529715)를 참조하세요.

[Azure 앱 서비스]: http://go.microsoft.com/fwlink/?LinkId=529714
[Azure Portal]: http://go.microsoft.com/fwlink/?LinkId=529715
[Azure CLI]: ../xplat-cli.md
[powershell-cmdlets]: ../powershell-install-configure.md
[Azure 앱 서비스 웹앱에서 PHP 구성]: web-sites-php-configure.md
[Azure 앱 서비스에서 PHP-MySQL 웹앱 만들기 및 Git를 사용하여 배포]: web-sites-php-mysql-deploy-use-git.md
[Azure 앱 서비스에서 WordPress 웹앱 만들기]: web-sites-php-web-site-gallery.md
[앱 서비스 가격]: /pricing/details/app-service/
[Azure 앱 서비스에서 웹 앱 크기 조정]: web-sites-scale.md
[Azure 앱 서비스에서 웹앱 크기 조정]: web-sites-scale.md
 

<!---HONumber=62-->