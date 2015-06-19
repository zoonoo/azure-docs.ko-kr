<properties 
	pageTitle="Azure에서 Ubuntu Linux VHD 만들기 및 업로드" 
	description="Ubuntu Linux 운영 체제가 포함된 Azure VHD(가상 하드 디스크)를 만들고 업로드하는 방법에 대해 알아봅니다." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="szarkos" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/15/2015" 
	ms.author="szarkos"/>


# Azure용 Ubuntu 가상 컴퓨터 준비

##필수 조건##

이 문서에서는 가상 하드 디스크에 Ubuntu Linux 운영 체제를 이미 설치했다고 가정합니다. .vhd 파일을 만드는 여러 도구가 있습니다(예: Hyper-V와 같은 가상화 솔루션). 자세한 내용은 [Hyper-V 역할 설치 및 가상 시스템 구성](http://technet.microsoft.com/library/hh846766.aspx)을 참조하십시오.

**Ubuntu 설치 참고 사항**

- 새 VHDX 형식은 Azure에서 지원되지 않습니다. Hyper-V 관리자 또는 convert-vhd cmdlet을 사용하여 디스크를 VHD 형식으로 변환할 수 있습니다.

- Linux 시스템 설치 시에는 LVM(설치 기본값인 경우가 많음)이 아닌 표준 파티션을 사용하는 것이 좋습니다. 이렇게 하면 특히 문제 해결을 위해 OS 디스크를 다른 VM에 연결해야 하는 경우 복제된 VM과 LVM 이름이 충돌하지 않도록 방지합니다. 원하는 경우에는 데이터 디스크에서 LVM 또는 [RAID](virtual-machines-linux-configure-raid.md)를 사용할 수 있습니다.

- OS 디스크에 스왑 파티션을 구성하지 마세요. 임시 리소스 디스크에서 스왑 파일을 만들도록 Linux 에이전트를 구성할 수 있습니다. 여기에 대한 자세한 내용은 아래 단계에서 확인할 수 있습니다.

- 모든 VHD 크기는 1MB의 배수여야 합니다.


## <a id="ubuntu"> </a>Ubuntu 12.04 이상 ##

1. Hyper-V 관리자의 가운데 창에서 가상 컴퓨터를 선택합니다.

2. **연결**을 클릭하여 가상 컴퓨터 창을 엽니다.

3.	Ubuntu의 Azure 리포지토리를 사용하도록 이미지의 현재 리포지토리를 바꿉니다. 단계는 Ubuntu 버전에 따라 약간씩 다릅니다.

	/etc/apt/sources.list를 편집하기 전에 백업을 만드는 것이 좋습니다.

		# sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

	Ubuntu 12.04:

		# sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
		# sudo apt-add-repository 'http://archive.canonical.com/ubuntu precise-backports main'
		# sudo apt-get update

	Ubuntu 12.10:

		# sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
		# sudo apt-add-repository 'http://archive.canonical.com/ubuntu quantal-backports main'
		# sudo apt-get update

	Ubuntu 14+:

		# sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
		# sudo apt-get update

4. 다음 명령을 실행하여 최신 커널로 운영 체제를 업데이트합니다.

	Ubuntu 12.04:

		# sudo apt-get update
		# sudo apt-get install hv-kvp-daemon-init linux-backports-modules-hv-precise-virtual
		(recommended) sudo apt-get dist-upgrade

		# sudo reboot

	Ubuntu 12.10:

		# sudo apt-get update
		# sudo apt-get install hv-kvp-daemon-init linux-backports-modules-hv-quantal-virtual
		(recommended) sudo apt-get dist-upgrade

		# sudo reboot
	
	Ubuntu 14+:

		# sudo apt-get update
		# sudo apt-get install hv-kvp-daemon-init
		(recommended) sudo apt-get dist-upgrade

		# sudo reboot

5.	(선택 사항) Ubuntu 시스템에서 오류가 발생하여 다시 부팅되는 경우 grub 부팅 프롬프트에서 사용자 입력을 대기하므로 시스템이 정상적으로 부팅되지 않는 경우가 많습니다. 이를 예방하려면 다음 단계를 완료하십시오.

	a) /etc/grub.d/00_header 파일을 엽니다.

	b) 함수**make_timeout()**에서 **if ["\${recordfail}" = 1 ]; then**을 검색합니다.

	c) 해당 줄 아래 문을 **set timeout=5**로 변경합니다.

	d) 'sudo update-grub'을 실행합니다.

6. Azure용 커널 매개 변수를 추가로 포함하려면 Grub의 커널 부팅 줄을 수정합니다. 이 작업을 수행하려면 "/etc/default/grub"을 텍스트 편집기에서 열고 `GRUB_CMDLINE_LINUX_DEFAULT` 변수를 찾거나 필요한 경우 추가하여 다음 매개 변수가 포함되도록 편집합니다.

		GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"

	이 파일을 저장하고 닫은 다음 '`sudo update-grub`'을 실행합니다. 이렇게 하면 모든 콘솔 메시지가 첫 번째 직렬 포트로 전송되므로 Azure 기술 지원에서 문제를 디버깅하는 데 도움이 될 수 있습니다.

8.	SSH 서버가 설치되어 부팅 시 시작되도록 구성되어 있는지 확인합니다. 보통 SSH 서버는 기본적으로 이와 같이 구성되어 있습니다.

9.	Azure Linux 에이전트를 설치합니다.

		# sudo apt-get update
		# sudo apt-get install walinuxagent

	`walinuxagent` 패키지를 설치하면 `NetworkManager` 및 `NetworkManager-gnome` 패키지가 설치되어 있는 경우 이러한 패키지를 제거합니다.

10.	다음 명령을 실행하여 가상 컴퓨터의 프로비전을 해제하고 Azure에서 프로비전할 준비를 합니다.

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

11. Hyper-V 관리자에서 **작업 -> 종료**를 클릭합니다. 이제 Linux VHD를 Azure에 업로드할 수 있습니다.

<!---HONumber=58--> 