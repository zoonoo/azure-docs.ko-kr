<properties
	pageTitle="PHP용 Azure SDK 다운로드"
	description="Azure SDK for PHP를 다운로드하여 설치하는 방법에 대해 알아봅니다."
	documentationCenter="php"
	services="app-service\web"
	authors="tfitzmac"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="article"
	ms.date="12/16/2015"
	ms.author="tomfitz"/>

#PHP용 Azure SDK 다운로드

## 개요

PHP용 Azure SDK에는 Azure용 PHP 응용 프로그램을 개발, 배포 및 관리할 수 있는 구성 요소가 포함되어 있습니다. 구체적으로 말해서 PHP용 Azure SDK에는 다음이 포함되어 있습니다.

* **Azure용 PHP 클라이언트 라이브러리**. 이러한 클래스 라이브러리는 Azure 기능(예: 데이터 관리 서비스 및 클라우드 서비스)에 액세스하기 위한 인터페이스를 제공합니다.  
* **Mac, Linux 및 Windows용 Azure 명령줄 인터페이스(Azure CLI)** Azure 웹 사이트 및 Azure 가상 컴퓨터와 같은 Azure 서비스를 배포 및 관리하기 위한 명령 집합입니다. Azure CLI는 Mac, Linux 및 Windows를 포함한 모든 플랫폼에서 작동합니다.
* **Azure PowerShell(Windows에만 해당)**. 클라우드 서비스 및 가상 컴퓨터와 같은 Azure 서비스를 배포 및 관리하기 위한 PowerShell cmdlet 집합입니다.
* **Azure 에뮬레이터(Windows에만 해당)**. 계산 및 저장소 에뮬레이터는 응용 프로그램을 로컬로 테스트할 수 있는 클라우드 서비스 및 데이터 관리 서비스의 로컬 에뮬레이터입니다. Azure 에뮬레이터는 Windows에서만 실행됩니다.

아래 섹션에서는 위에서 언급한 구성 요소를 다운로드하고 설치하는 방법에 대해 설명합니다.

이 항목의 지침은 [PHP][install-php]가 설치되어 있다고 가정합니다.

> [AZURE.NOTE]Azure용 PHP 클라이언트 라이브러리를 사용하려면 PHP 5.3 이상이 있어야 합니다.

##Azure용 PHP 클라이언트 라이브러리

Azure용 PHP 클라이언트 라이브러리는 운영 체제에서 Azure 기능(예: 데이터 관리 서비스 및 클라우드 서비스)에 액세스하기 위한 인터페이스를 제공합니다. 이러한 라이브러리는 작성기 또는 PEAR 패키지 관리자를 통하거나 수동으로 설치할 수 있습니다.

Azure용 PHP 클라이언트 라이브러리를 사용하는 방법에 대한 내용은 [Blob 서비스 사용 방법][blob-service], [테이블 서비스 사용 방법][table-service] 및 [큐 서비스 사용 방법][queue-service]을 참조하세요.

###작성기를 통해 설치

1. [Git를 설치][install-git]합니다.


	> [AZURE.NOTE]Windows에서는 PATH 환경 변수에도 Git 실행 파일을 추가해야 합니다.

2. 프로젝트 루트에 **composer.json**이라는 파일을 만들고 다음 코드를 추가합니다.

        {
            "repositories": [
                {
                    "type": "pear",
                    "url": "http://pear.php.net"
                }
            ],
            "require": {
                "pear-pear.php.net/mail_mime" : "*",
                "pear-pear.php.net/http_request2" : "*",
                "pear-pear.php.net/mail_mimedecode" : "*",
                "microsoft/windowsazure": "*"
            }
        }

3. 프로젝트 루트에 **[composer.phar][composer-phar]**을 다운로드합니다.

4. 명령 프롬프트를 열고 프로젝트 루트에서 이 파일을 실행합니다.

		php composer.phar install

###PEAR 패키지로 설치

Azure용 PHP 클라이언트 라이브러리를 PEAR 패키지로 설치하려면 다음 단계를 따르세요.

1. [PEAR를 설치][install-pear]합니다.
2. Azure PEAR 채널을 설정합니다.

		pear channel-discover pear.windowsazure.com
3. PEAR 패키지를 설치합니다.

		pear install pear.windowsazure.com/WindowsAzure-0.4.0

설치를 완료한 후 응용 프로그램에서 클래스 라이브러리를 참조할 수 있습니다.

###수동으로 설치

Azure용 PHP 클라이언트 라이브러리를 수동으로 다운로드하고 설치하려면 다음 단계를 따르세요.

1. [GitHub][php-sdk-github]에서 라이브러리가 포함된 .zip 보관 파일을 다운로드합니다. 또는 리포지토리를 분기하여 로컬 컴퓨터에 복제합니다. 두 번째 옵션을 사용하려면 GitHub 계정이 필요하며 Git가 로컬에 설치되어 있어야 합니다.

	> [AZURE.NOTE]Azure용 PHP 클라이언트 라이브러리는 [HTTP\_Request2](http://pear.php.net/package/HTTP_Request2), [Mail\_mime](http://pear.php.net/package/Mail_mime) 및 [Mail\_mimeDecode](http://pear.php.net/package/Mail_mimeDecode) PEAR 패키지에 종속됩니다. 이 종속성을 해결하는 권장 방법은 [PEAR 패키지 관리자](http://pear.php.net/manual/en/installation.php)(영문)를 사용하여 이러한 패키지를 설치하는 것입니다.

2. 다운로드한 보관 파일의 `WindowsAzure` 디렉터리를 응용 프로그램 디렉터리 구조에 복사하고 응용 프로그램에서 클래스를 참조합니다.

##Azure PowerShell 및 Azure 에뮬레이터

Azure PowerShell는 Azure 서비스(예: 클라우드 서비스 및 가상 컴퓨터)를 배포 및 관리하기 위한 PowerShell cmdlet 집합입니다. Azure 에뮬레이터는 응용 프로그램을 로컬로 테스트할 수 있는 클라우드 서비스 및 데이터 관리 서비스의 에뮬레이터입니다. 이러한 구성 요소는 Windows에서만 지원됩니다.

Azure PowerShell 및 Azure 에뮬레이터는 [Microsoft 웹 플랫폼 설치 관리자][download-wpi]를 사용하여 설치하는 것이 좋습니다. PHP, SQL Server, PHP용 Microsoft Drivers for SQL Server, WebMatrix와 같은 다른 개발 구성 요소를 설치하도록 선택할 수도 있습니다.

Azure PowerShell 사용 방법에 대한 내용은 [Azure PowerShell 사용 방법][powershell-tools]을 참조하세요.

##Azure CLI

Azure CLI는 Azure 웹 사이트 및 Azure 가상 컴퓨터와 같은 Azure 서비스를 배포 및 관리하기 위한 명령 집합입니다. Azure CLI를 설치하는 방법에 대한 자세한 내용은 [Azure CLI 설치](xplat-cli-install.md)를 참조하세요.

## 다음 단계

자세한 내용은 [PHP 개발자 센터](/develop/php/)를 참조하세요.


[install-php]: http://www.php.net/manual/en/install.php
[composer-github]: https://github.com/composer/composer
[composer-phar]: http://getcomposer.org/composer.phar
[pear-net]: http://pear.php.net/
[http-request2-package]: http://pear.php.net/package/HTTP_Request2
[mail-mimedecode-package]: http://pear.php.net/package/Mail_mimeDecode
[mail-mime-package]: http://pear.php.net/package/Mail_mime
[install-pear]: http://pear.php.net/manual/en/installation.getting.php
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[download-wpi]: http://go.microsoft.com/fwlink/?LinkId=253447
[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[blob-service]: http://go.microsoft.com/fwlink/?LinkId=252714
[table-service]: http://go.microsoft.com/fwlink/?LinkId=252715
[queue-service]: http://go.microsoft.com/fwlink/?LinkId=252716
[azure cli]: http://go.microsoft.com/fwlink/?LinkId=252717
[powershell-tools]: http://go.microsoft.com/fwlink/?LinkId=252718
[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git

<!---HONumber=AcomDC_1217_2015-->