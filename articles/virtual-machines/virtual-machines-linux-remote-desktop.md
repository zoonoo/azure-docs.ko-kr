<properties
	pageTitle="xrdp를 통해 원격 데스크톱을 사용하여 Microsoft Azure Linux VM에 연결"
	description="Microsoft Azure Linux VM에서 원격 데스크톱을 설치하고 구성하는 방법에 대해 알아봅니다."
	services="virtual-machines"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/31/2015"
	ms.author="mingzhan"/>


#xrdp를 통해 원격 데스크톱을 사용하여 Microsoft Azure Linux VM에 연결

##개요

RDP(원격 데스크톱 프로토콜)는 Windows에 사용되는 독점 프로토콜이지만 RDP를 사용하여 Linux VM에 원격으로 어떻게 연결합니까?

이 설명서에서 답을 줍니다! xrdp를 Microsoft Azure Linux VM(가상 컴퓨터)에 설치하고 구성하도록 도와주고 Windows 컴퓨터에서 원격 데스크톱으로 연결할 수 있습니다.

xrdp는 Windows 컴퓨터에서 원격 데스크톱으로 Linux 서버에 연결할 수 있도록 해주는 공개 소스 RDP 서버입니다. VNC(Virtual Network Computing)보다 더 강력한 성능을 구현합니다. VNC는 "JPEG" 품질이며 느린 동작인 반면 RDP는 신속하고 맑습니다.
 

> [AZURE.NOTE]Linux를 실행하는 Microsoft Azure VM이 있어야 합니다. Linux VM을 생성하고 설정하려면 [Azure Linux VM 자습서](virtual-machines-linux-tutorial.md)를 참조하세요.


##원격 데스크톱에 대한 끝점 만들기
이 문서에서는 원격 데스크톱에 대해 기본 끝점 3389를 사용합니다. 따라서 아래와 같은 Linux VM에 원격 데스크톱으로 3389 끝점을 설정합니다.


![이미지](./media/virtual-machines-linux-remote-desktop/no1.png)


VM에 대한 끝점을 설정하는 방법을 몰랐을 경우 [지침](virtual-machines-set-up-endpoints.md)을 참조하세요.


##Gnome 데스크톱 설치

putty를 통해 Linux VM에 연결하고 `Gnome Desktop`을(를) 설치합니다.

Red Hat 제품군 Linux의 경우 다음을 사용합니다.

	#sudo yum install gnome* "xorg*" -y

Debian 및 Ubuntu의 경우 다음을 사용합니다.

	#sudo apt-get update
	#sudo apt-get install ubuntu-desktop


OpenSUSE의 경우 다음을 사용합니다.

	#sudo zypper -y install gnome-session


##xrdp 설치

Red Hat 제품군 Linux의 경우 `yum`을(를) 통해 xrdp 패키지를 설치하기 위해 먼저 Linux VM에 EPEL 리포지토리를 설치해야 합니다.

	#sudo rpm -ivh http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
	#sudo yum -y install xrdp tigervnc-server tigervnc-server-module xterm

Debian 및 Ubuntu Linux의 경우 다음을 사용합니다.

	#sudo apt-get install xrdp


OpenSUSE의 경우 다음을 사용합니다.

> [AZURE.NOTE]아래 명령으로 사용 중인 버전으로 OpenSUSE 버전을 업데이트합니다. 아래는 `OpenSUSE 13.2`에 대한 예제 명령입니다.

	#sudo zypper in http://download.opensuse.org/repositories/X11:/RemoteDesktop/openSUSE_13.2/x86_64/xrdp-0.9.0git.1401423964-2.1.x86_64.rpm
    #sudo zypper install tigervnc xorg-x11-Xvnc xterm remmina-plugin-vnc


##xrdp를 시작하고 부팅에 xdrp 서비스를 설정합니다.

Red Hat 제품군 Linux의 경우 다음을 사용합니다.

	#sudo service xrdp start
	#sudo chkconfig xrdp on


OpenSUSE의 경우 다음을 사용합니다.

	#sudo systemctl start xrdp
	#sudo systemctl enable xrdp
 

##Red Hat 제품군 Linux를 사용하는 경우 iptables를 사용하지 않도록 설정 

사용:

	#sudo service iptables stop


##Ubuntu 12.04LTS보다 최신 Ubuntu 버전을 사용하는 경우 xfce 사용

현재 xrop는 Ubuntu 12.04LTS보다 최신 Ubuntu 버전에서 Gnome 데스크톱을 지원하지 않으므로 `xfce` 데스크톱을 대신 사용합니다.

`xfce`을(를) 설치하고 다음을 사용합니다.

    #sudo apt-get install xubuntu-desktop

그런 다음 `xfce`을(를) 활성화하고 다음을 사용합니다.
    
    #echo xfce4-session >~/.xsession

구성 파일 `/etc/xrdp/startwm.sh`을(를) 편집하고 다음을 사용합니다.

    #sudo vi /etc/xrdp/startwm.sh   

줄 `/etc/X11/Xsession` 앞에 줄 `xfce4-session`을(를) 추가합니다.

xrdp 서비스를 다시 시작하고 다음을 사용합니다.

    #sudo service xrdp restart


##Windows 컴퓨터에서 Linux VM 연결
Windows 컴퓨터에서 원격 데스크톱 클라이언트를 시작하고 Linux VM DNS 이름을 입력하거나 Azure 포털에서 VM의 `Dashboard`(으)로 이동하고 `Connect`을(를) 클릭하면 아래 로그인 창이 표시됩니다.

![이미지](./media/virtual-machines-linux-remote-desktop/no2.png)

Linux VM에 대해 `user` & `password`을(를) 사용하여 로그인하고 Microsoft Azure Linux VM에서 지금 바로 원격 데스크톱을 즐기세요!


##다음
xrdp 사용에 대한 자세한 내용은 [여기](http://www.xrdp.org/)를 참조할 수 있습니다.





 

<!---HONumber=September15_HO1-->