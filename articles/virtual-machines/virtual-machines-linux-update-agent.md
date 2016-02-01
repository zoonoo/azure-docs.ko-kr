<properties
	pageTitle="GitHub에서 Azure Linux 에이전트 업데이트 | Microsoft Azure"
	description="Azure Linux VM의 Azure Linux 에이전트를 Github의 최신 버전으로 업데이트하는 방법을 알아봅니다."
	services="virtual-machines"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/14/2015"
	ms.author="mingzhan"/>


# VM의 Azure Linux 에이전트를 GitHub의 최신 버전으로 업데이트하는 방법

Azure Linux VM에서 [Azure Linux 에이전트](https://github.com/Azure/WALinuxAgent)를 업데이트하려면 다음 항목이 이미 있어야 합니다.

1. Azure에서 실행 중인 Linux vm
2. SSH를 사용하여 해당 Linux VM에 연결

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


> [AZURE.NOTE]Windows 컴퓨터에서 이 작업을 수행할 경우 Linux 컴퓨터에 Putty 및 SSH를 사용할 수 있습니다. 자세한 내용은 [Linux를 실행하는 가상 컴퓨터에 로그온하는 방법](virtual-machines-linux-how-to-log-on.md)을 참조하세요.

Azure 인증 Linux 배포판에서는 Azure Linux 에이전트 패키지를 해당 리포지토리에 저장하므로 가능하면 먼저 배포판 리포지토리에서 최신 버전을 확인하고 설치하세요.

Ubuntu의 경우 다음을 입력하면 됩니다.

    #sudo apt-get install walinuxagent

그리고 CentOS에서, 다음을 입력합니다.

    #sudo yum install waagent

Oracle Linux의 경우, `Addons`리포지토리가 사용하도록 설정되었는지 확인합니다. 파일 `/etc/yum.repo.d/public-yum-ol6.repo`(Oracle Linux 6) 또는 파일 `/etc/yum.repo.d/public-yum-ol7.repo`(Oracle Linux)를 편집하고 이 파일의 **[ol6\_addons]** 또는 **[ol7\_addons]** 아래에서 줄 `enabled=0`을 `enabled=1`로 변경합니다.

최신 버전의 Azure Linux 에이전트를 설치하려면 다음을 입력합니다.

    #sudo yum install WALinuxAgent

일반적으로는 이렇게만 하면 되지만 어떤 이유로든 https://github.com에서 직접 설치해야 하는 경우 다음 단계를 사용하세요.


## wget 설치

SSH를 사용하여 VM에 로그인합니다.

명령줄에 `#sudo yum install wget`를 입력하여 wget를 설치합니다. Redhat, CentOS, Oracle Linux 버전 6.4 및 6.5와 같이 기본적으로 이 방법으로 설치되지 않는 몇 가지 배포판이 있습니다.


## 최신 버전 다운로드

웹 페이지에서 [GitHub의 Azure Linux 에이전트 릴리스](https://github.com/Azure/WALinuxAgent/releases)를 열고 최신 버전 번호를 확인합니다. `#waagent --version`을 입력하면 현재 버전을 찾을 수 있습니다.

###버전 2.0.x의 경우 다음을 입력합니다.

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-[version]/waagent  

   예를 들어 다음 줄에서는 버전 2.0.14를 사용합니다.

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.14/waagent  

###2\.1.x 이상의 경우 다음을 입력합니다.

    #wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-[version].zip
    #unzip WALinuxAgent-[version].zip
    #cd WALinuxAgent-[version]

   예를 들어 다음 줄에서는 버전 2.1.0을 사용합니다.

    #wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-2.1.0.zip
    #unzip WALinuxAgent-2.1.0.zip  
    #cd WALinuxAgent-2.1.0

##Linux 에이전트 설치

###버전 2.0.x의 경우 다음을 사용합니다.

 waagent 실행 파일 만들기

    #chmod +x waagent

 /usr/sbin/에 새 실행 파일 복사

  대부분의 Linux에서는 다음을 사용합니다.

    #sudo cp waagent /usr/sbin

  CoreOS의 경우 다음을 사용합니다.

    #sudo cp waagent /usr/share/oem/bin/

  Azure Linux 에이전트를 새로 설치하는 경우 아래를 실행합니다.
 
    #sudo /usr/sbin/waagent -install -verbose

###버전 2.1.x의 경우 다음을 사용합니다.

`setuptools` 패키지를 먼저 설치해야 할 수도 있습니다. [여기](https://pypi.python.org/pypi/setuptools)를 참조하세요. 그런 다음 아래를 실행합니다.

    #sudo python setup.py install

## waagent 서비스 다시 시작

대부분의 linux 배포판:

    #sudo service waagent restart

Ubuntu의 경우 다음을 사용합니다.

    #sudo service walinuxagent restart

CoreOS의 경우 다음을 사용합니다.

    #sudo systemctl restart waagent

## Azure Linux 에이전트 버전 확인

    #waagent -version

CoreOS에서는 위의 명령이 작동하지 않을 수 있습니다.

Linux 에이전트 버전이 새 버전으로 업데이트된 것을 확인할 수 있습니다.

Azure Linux 에이전트에 대한 자세한 내용은 [Azure Linux 에이전트 추가 정보](https://github.com/Azure/WALinuxAgent)를 참조하세요.

<!---HONumber=AcomDC_0121_2016-->