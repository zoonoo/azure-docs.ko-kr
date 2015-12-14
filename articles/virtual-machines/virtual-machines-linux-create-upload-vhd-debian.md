<properties
	pageTitle="Debian Linux VHD 준비 | Microsoft Azure"
	description="Azure에서 배포할 Debian 7 및 8 VHD 파일을 만드는 방법에 대해 알아봅니다."
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
	ms.date="12/01/2015"
	ms.author="mingzhan"/>




#Azure용 Debian VHD 준비

##필수 조건
이 섹션에서는 [Debian 웹 사이트](https://www.debian.org/distrib/)에서 다운로드한 .iso 파일에서 Debian Linux 운영 체제를 가상 하드 디스크에 설치했다고 가정합니다. .vhd 파일을 만드는 여러 도구가 있으며 Hyper-V가 한 가지 예입니다. Hyper-V 사용에 대한 자세한 내용은 [Hyper-V 역할 설치 및 가상 컴퓨터 구성](https://technet.microsoft.com/library/hh846766.aspx)을 참조하세요.


## 설치 참고 사항

- 새 VHDX 형식은 Azure에서 지원되지 않습니다. Hyper-V 관리자 또는 **convert-vhd** cmdlet을 사용하여 디스크를 VHD 형식으로 변환할 수 있습니다.
- Linux 시스템 설치 시에는 LVM(설치 기본값인 경우가 많음)이 아닌 표준 파티션을 사용하는 것이 좋습니다. 이렇게 하면 특히 문제 해결을 위해 OS 디스크를 다른 VM에 연결해야 하는 경우 복제된 VM과 LVM 이름이 충돌하지 않도록 방지합니다. 원하는 경우에는 데이터 디스크에서 LVM 또는 RAID를 사용할 수 있습니다.
- OS 디스크에 스왑 파티션을 구성하지 마세요. 임시 리소스 디스크에서 스왑 파일을 만들도록 Azure Linux 에이전트를 구성할 수 있습니다. 여기에 대한 자세한 내용은 아래 단계에서 확인할 수 있습니다.
- 모든 VHD 크기는 1MB의 배수여야 합니다.


##Debian 7.x 및 8.x

1. Hyper-V 관리자에서 가상 컴퓨터를 선택합니다.

2. **연결**을 클릭하여 가상 컴퓨터의 콘솔 창을 엽니다.

3. ISO 파일에 대해 VM을 설정한 경우 `/etc/apt/source.list`에서 **deb cdrom**에 대한 줄을 주석으로 처리합니다.

4. 다음과 같이 `/etc/default/grub` 파일을 편집하고 **GRUB\_CMDLINE\_LINUX** 매개 변수를 수정하여 Azure에 대한 추가 커널 매개 변수를 포함시킵니다.

        GRUB_CMDLINE_LINUX="console=ttyS0 earlyprintk=ttyS0 rootdelay=300"

5. grub을 다시 빌드하고 다음을 실행합니다.

        # sudo update-grub 

6. Azure Linux 에이전트에 대한 종속성 패키지를 설치합니다.

        # apt-get install -y git parted

7.	[참고 자료](virtual-machines-linux-update-agent.md)를 사용하여 Github에서 Azure Linux 에이전트를 설치하고 2.0.14 버전을 선택합니다.

			# wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.14/waagent
			# chmod +x waagent
			# cp waagent /usr/sbin
			# /usr/sbin/waagent -install -verbose

8. 가상 컴퓨터의 프로비전을 해제하고 Azure에서 프로비전할 가상 컴퓨터를 준비하고 다음 명령을 실행합니다.

        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout
 
9. Hyper-V 관리자에서 **작업** -> 종료를 클릭합니다. 이제 Linux VHD를 Azure에 업로드할 수 있습니다.

##Credativ 스크립트를 사용하여 Debian VHD 만들기

Debian VHD를 자동으로 빌드하는 데 도움이 되는 스크립트가 Credativ 웹 사이트에서 제공됩니다. [여기](https://gitlab.credativ.com/de/azure-manage)에서 다운로드하여 Linux VM에 설치하면 됩니다. Debian VHD(예: Debian 7)를 만들려면 다음을 실행합니다.

        # azure_build_image --option release=wheezy --option image_prefix=lilidebian7 --option image_size_gb=30 SECTION

이 스크립트를 사용할 때 문제가 있는 경우 문제를 Credativ([여기](https://gitlab.credativ.com/groups/de/issues))에 제출합니다.

## 다음 단계

이제 Debian.vhd를 사용하여 새 Azure 가상 컴퓨터를 만들 준비가 되었습니다.

<!---HONumber=AcomDC_1203_2015-->