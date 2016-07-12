<properties
	pageTitle="Linux VM에 대한 원격 데스크톱 | Microsoft Azure"
	description="원격 데스크톱을 설치 및 구성하여 Microsoft Azure Linux VM에 연결하는 방법을 알아봅니다."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/01/2016"
	ms.author="mingzhan"/>


#원격 데스크톱을 사용하여 Microsoft Azure Linux VM에 연결

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


##개요

RDP(원격 데스크톱 프로토콜)는 Windows에 사용되는 독점 프로토콜입니다. 어떻게 RDP를 사용하여 Linux VM에 원격으로 연결할 수 있을까요?

이 설명서에서 답을 줍니다! Microsoft Azure Linux VM에 xrdp를 설치 및 구성하는 데 도움이 되며, Windows 컴퓨터에서 원격 데스크톱을 사용하여 연결할 수 있습니다. 이 지침에서는 Ubuntu 또는 OpenSUSE를 실행하는 Linux VM을 예로 사용합니다.

xrdp는 Windows 컴퓨터에서 원격 데스크톱으로 Linux 서버에 연결할 수 있도록 해주는 공개 소스 RDP 서버입니다. VNC(Virtual Network Computing)보다 더 강력한 성능을 구현합니다. VNC는 "JPEG" 품질이며 느린 동작인 반면 RDP는 신속하고 맑습니다.


> [AZURE.NOTE] Linux를 실행하는 Microsoft Azure VM이 있어야 합니다. Linux VM을 만들고 설정하려면 [Azure Linux VM 자습서](virtual-machines-linux-classic-createportal.md)를 참조하세요.


##원격 데스크톱에 대한 끝점 만들기
이 문서에서는 원격 데스크톱에 대해 기본 끝점 3389를 사용합니다. 따라서 아래와 같은 Linux VM에 원격 데스크톱으로 3389 끝점을 설정합니다.


![이미지](./media/virtual-machines-linux-classic-remote-desktop/no1.png)


VM에 대한 끝점을 설정하는 방법을 모르는 경우 [지침](virtual-machines-linux-classic-setup-endpoints.md)을 참조하세요.


##Gnome 데스크톱 설치

putty를 통해 Linux VM에 연결하고 `Gnome Desktop`을 설치합니다.

Ubuntu의 경우 다음을 사용합니다.

	#sudo apt-get update
	#sudo apt-get install ubuntu-desktop


OpenSUSE의 경우 다음을 사용합니다.

	#sudo zypper install gnome-session

##xrdp 설치

Ubuntu의 경우 다음을 사용합니다.

	#sudo apt-get install xrdp

OpenSUSE의 경우 다음을 사용합니다.

> [AZURE.NOTE] 아래 명령을 통해 OpenSUSE 버전을 사용 중인 버전으로 업데이트합니다. 아래는 `OpenSUSE 13.2`에 대한 예제 명령입니다.

	#sudo zypper in http://download.opensuse.org/repositories/X11:/RemoteDesktop/openSUSE_13.2/x86_64/xrdp-0.9.0git.1401423964-2.1.x86_64.rpm
    #sudo zypper install tigervnc xorg-x11-Xvnc xterm remmina-plugin-vnc


##xrdp를 시작하고 부팅에 xdrp 서비스를 설정합니다.

OpenSUSE의 경우 다음을 사용합니다.

	#sudo systemctl start xrdp
	#sudo systemctl enable xrdp

Ubuntu의 경우 설치 후 부팅 시 자동으로 xrdp가 시작되어 사용됩니다.

##Ubuntu 12.04LTS보다 최신 Ubuntu 버전을 사용하는 경우 xfce 사용

현재 xrdp는 Ubuntu 12.04LTS보다 최신 Ubuntu 버전에서 Gnome 데스크톱을 지원하지 않으므로 `xfce` 데스크톱을 대신 사용합니다.

다음을 사용하여 `xfce`를 설치합니다.

    #sudo apt-get install xubuntu-desktop

다음을 사용하여 `xfce`를 사용하도록 설정합니다.

    #echo xfce4-session >~/.xsession

다음을 사용하여 구성 파일 `/etc/xrdp/startwm.sh`를 편집합니다.

    #sudo vi /etc/xrdp/startwm.sh   

`/etc/X11/Xsession` 줄 앞에 `xfce4-session` 줄을 추가합니다.

xrdp 서비스를 다시 시작하고 다음을 사용합니다.

    #sudo service xrdp restart


##Windows 컴퓨터에서 Linux VM 연결
Windows 컴퓨터에서 원격 데스크톱 클라이언트를 시작하고 Linux VM DNS 이름을 입력하거나, Azure 클래식 포털에서 VM의 `Dashboard`로 이동하고 `Connect`를 클릭하여 Linux VM을 연결하면 아래 로그인 창이 표시됩니다.

![이미지](./media/virtual-machines-linux-classic-remote-desktop/no2.png)

Linux VM의 `user` 및 `password`를 사용하여 로그인한 다음 Microsoft Azure Linux VM에서 지금 바로 원격 데스크톱을 즐기세요!


##다음
xrdp를 사용하는 방법에 대한 자세한 내용은 [여기](http://www.xrdp.org/)를 참조할 수 있습니다.

<!---HONumber=AcomDC_0629_2016-->