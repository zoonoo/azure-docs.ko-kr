<properties
	pageTitle="Azure CustomScript 확장을 사용하여 Linux 응용 프로그램 배포"
	description="Azure CustomScript 확장을 사용하여 Linux 가상 컴퓨터에서 응용 프로그램을 배포하는 방법에 대해 알아봅니다."
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	services=""
	authors="gbowerman"/>

<tags
	ms.service="virtual-machines"
	ms.workload="multiple"
	ms.tgt_pltfrm="linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="2/23/2015"
	ms.author="guybo"/>

#Linux용 Azure CustomScript 확장을 사용하여 LAMP 앱 배포#

Linux용 Azure CustomScript 확장을 사용하면 Python, Bash 등 VM에서 지원하는 모든 스크립팅 언어로 작성된 임의 코드를 실행하여 VM(가상 컴퓨터)을 사용자 지정할 수 있습니다. 이렇게 하면 매우 유동적으로 응용 프로그램을 여러 컴퓨터에 자동으로 배포할 수 있습니다.

Azure 포털, PowerShell 또는 Azure 플랫폼 간 명령줄 인터페이스(xplat-cli)를 사용하여 CustomScript 확장을 배포할 수 있습니다.

이 예제에서는 xplat-cli를 사용하여 간단한 LAMP 응용 프로그램을 Ubuntu에 배포하는 과정을 안내합니다.

## 필수 구성 요소

이 연습에서는 14.04를 실행하는 Azure VM 2대를 만듭니다. 해당 VM의 이름은 *script-vm* 및 *lamp-vm*으로 지정합니다. 실제 환경에서는 고유한 이름을 사용하세요. VM 중 하나에서는 CLI 명령을 실행하고 다른 하나에는 LAMP 앱을 배포합니다.

또한 Azure 저장소 계정과 해당 계정에 액세스하는 데 사용할 키도 필요합니다. 계정 및 키는 Azure 포털에서 다운로드할 수 있습니다.

Azure에서 Linux VM을 만들 때 도움이 필요하면 [Linux를 실행하는 가상 컴퓨터 만들기를 참조하세요](../virtual-machines-linux-tutorial/).

여기서 사용하는 구체적인 설치 명령은 Ubuntu를 사용한다고 가정하지만 지원되는 모든 배포판에 맞게 일반 단계를 조정할 수 있습니다.

 *script-vm* VM에는 xplat-cli가 설치되어 있어야 하며 Azure에 대한 정상적인 연결이 설정되어 있어야 합니다. 이와 같이 설정하는 과정에서 도움이 필요하면 [Azure 플랫폼 간 명령줄 인터페이스 설치 및 구성을 참조하세요](../xplat-cli/).

## 스크립트 업로드

이 예제에서 CustomScript 확장은 원격 VM에서 스크립트를 실행하여 LAMP 스택을 설치하고 PHP 페이지를 만듭니다. 어디서나 스크립트에 액세스할 수 있도록 Azure Blob으로 스크립트를 업로드합니다.

**스크립트**

이 스크립트는 Ubuntu에 LAMP 스택을 설치하고(MySQL 자동 설치를 설정하는 작업도 수행함) 간단한 PHP 파일을 쓴 다음 Apache를 시작합니다.

	#!/bin/bash
	# set up a silent install of MySQL
	dbpass="mySQLPassw0rd"

	export DEBIAN_FRONTEND=noninteractive
	echo mysql-server-5.6 mysql-server/root_password password $dbpass | debconf-set-selections
	echo mysql-server-5.6 mysql-server/root_password_again password $dbpass | debconf-set-selections

	# install the LAMP stack
	apt-get -y install apache2 mysql-server php5 php5-mysql  

	# write some PHP
	echo \<center\>\<h1\>My Demo App\</h1\>\<br/\>\</center\> > /var/www/html/phpinfo.php
	echo \<\?php phpinfo\(\)\; \?\> >> /var/www/html/phpinfo.php

	# restart Apache
	apachectl restart

**업로드**

스크립트를 *lamp_install.sh*와 같은 텍스트 파일로 저장한 다음 Azure 저장소에 업로드합니다. xplat-cli를 사용하면 이 작업을 쉽게 수행할 수 있습니다. 다음 예제에서는 "scripts"라는 저장소 컨테이너에 이 파일을 업로드합니다. 참고: 컨테이너가 없으면 먼저 만들어야 합니다.

    azure storage blob upload -a <yourStorageAccountName> -k <yourStorageKey> --container scripts ./install_lamp.sh

또한 Azure 저장소에서 스크립트를 다운로드하는 방법을 설명하는 JSON 파일도 만듭니다. 이 파일을 *public_config.json*으로 저장하고, "mystorage"는 저장소 계정 이름으로 바꿉니다.

    {fileUris":["https://mystorage.blob.core.windows.net/scripts/install_lamp.sh"], "commandToExecute":"sh install_lamp.sh" }


## 확장 배포

이제 xplat-cli를 사용하여 원격 VM에 Linux CustomScript 확장을 배포할 수 있습니다.

    azure vm extension set -c "./public_config.json" lamp-vm CustomScriptForLinux Microsoft.OSTCExtensions 1.*

그러면 *lamp-vm* VM에서 *lamp_install.sh* 스크립트가 다운로드 및 실행됩니다.

앱은 웹 서버를 포함하므로 원격 VM에서 HTTP 수신 대기 포트를 열어야 합니다.

    azure vm endpoint create -n Apache -o tcp lamp-vm 80 80

## 모니터링 및 문제 해결

원격 VM에서 로그 파일을 통해 사용자 지정 스크립트 실행 진행률을 확인할 수 있습니다.  *lamp-vm*에 대해 SSH를 실행하고 로그 파일의 끝부분을 확인합니다.

    cd /var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.3.0.0/
    tail -f extension.log

CustomScript 확장 실행이 완료되면 앞에서 만든 PHP 페이지를 찾아볼 수 있습니다. 이 예제에서 해당 페이지는 *http://lamp-vm.cloudapp.net/phpinfo.php*입니다.

## 추가 리소스

동일한 기본 단계를 사용하여 보다 복잡한 앱을 배포할 수 있습니다. 이 예제에서는 설치 스크립트를 Azure 저장소에 공용 Blob으로 저장했습니다. 이보다 안전한 옵션은 SAS([보안 액세스 서명](https://msdn.microsoft.com/library/azure/ee395415.aspx))를 사용하여 보안 Blob으로 설치 스크립트를 저장하는 것입니다.

아래에는 xplat-cli, Linux 및 CustomScript 확장에 대한 몇 가지 추가 리소스가 나와 있습니다.

[CustomScript 확장을 사용하여 Linux VM 사용자 지정 작업 자동화](http://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/)

[Azure Linux 확장(GitHub)](https://github.com/Azure/azure-linux-extensions)

[Azure의 오픈 소스 컴퓨팅 및 Linux](../virtual-machines-linux-opensource/)

<!--HONumber=47-->
