<properties
pageTitle="Azure용 Oracle Linux 가상 컴퓨터 준비 | Microsoft Azure"
description="Microsoft Azure에서 Linux를 실행하는 Oracle 가상 컴퓨터의 구성 과정을 단계별로 설명합니다."
services="virtual-machines"
authors="bbenz"
documentationCenter="virtual-machines"
tags="azure-service-management,azure-resource-manager"
/>

<tags
ms.service="virtual-machines"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="vm-linux"
ms.workload="infrastructure-services"
ms.date="06/22/2015"
ms.author="bbenz" />

# Azure용 Oracle Linux 가상 컴퓨터 준비

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


-   [Azure용 Oracle Linux 6.4 이상 가상 컴퓨터 준비](virtual-machines-linux-create-upload-vhd-oracle.md)

-   [Azure용 Oracle Linux 7.0 이상 가상 컴퓨터 준비](virtual-machines-linux-create-upload-vhd-oracle.md)

## 필수 조건
이 문서에서는 가상 하드 디스크에 Oracle Linux 운영 체제를 이미 설치했다고 가정합니다. .vhd 파일을 만드는 여러 도구가 있습니다(예: Hyper-V와 같은 가상화 솔루션). 자세한 내용은 [Hyper-V 설치 및 가상 컴퓨터 만들기](http://technet.microsoft.com/library/hh846766.aspx)를 참조하세요.

**Oracle Linux 설치 참고 사항**

- Oracle Red Hat 호환 커널 및 해당 UEK3(Unbreakable Enterprise Kernel)은 모두 Hyper-V 및 Azure에서 지원됩니다. 최상의 결과를 얻으려면 Oracle Linux VHD를 준비할 때 최신 커널로 업데이트하세요.

- Oracle의 UEK2는 필수 드라이버를 포함하지 않으므로 Hyper-V 및 Azure에서 지원되지 않습니다.

- 새 VHDX 형식은 Azure에서 지원되지 않습니다. Hyper-V 관리자 또는 convert-vhd cmdlet을 사용하여 디스크를 VHD 형식으로 변환할 수 있습니다.

- Linux 시스템 설치 시 LVM(설치 기본값인 경우가 많음)이 아닌 표준 파티션을 사용하는 것이 좋습니다. 이렇게 하면 특히 문제 해결을 위해 OS 디스크를 다른 VM에 연결해야 하는 경우 복제된 VM과 LVM 이름이 충돌하지 않도록 방지합니다. 원하는 경우에는 데이터 디스크에서 LVM 또는 [RAID](virtual-machines-linux-configure-raid.md)를 사용할 수 있습니다.

- 2\.6.37보다 낮은 Linux 커널 버전의 버그 때문에 더 큰 VM 크기에서는 NUMA가 지원되지 않습니다. 이 문제는 주로 업스트림 Red Hat 2.6.32 커널을 사용하는 분산에 영향을 줍니다. Azure Linux 에이전트(waagent)를 수동으로 설치하면 Linux 커널의 GRUB 구성에서 NUMA가 자동으로 사용하지 않도록 설정됩니다. 여기에 대한 자세한 내용은 아래 단계에서 확인할 수 있습니다.

- OS 디스크에 스왑 파티션을 구성하지 마세요. 임시 리소스 디스크에서 스왑 파일을 만들도록 Linux 에이전트를 구성할 수 있습니다. 여기에 대한 자세한 내용은 아래 단계에서 확인할 수 있습니다.

- 모든 VHD 크기는 1MB의 배수여야 합니다.

- `Addons` 리포지토리가 사용하도록 설정되었는지 확인합니다. 파일 `/etc/yum.repo.d/public-yum-ol6.repo`(Oracle Linux 6) 또는 파일 `/etc/yum.repo.d/public-yum-ol7.repo`(Oracle Linux)를 편집하고 이 파일의 **[ol6\_addons]** 또는 **[ol7\_addons]** 아래에서 줄 `enabled=0`을 `enabled=1`로 변경하도록 선택합니다.


## Oracle Linux 6.4 이상
가상 컴퓨터를 Azure에서 실행하려면 운영 체제에서 특정 구성 단계를 완료해야 합니다.

1. Hyper-V 관리자의 가운데 창에서 가상 컴퓨터를 선택합니다.

2. **연결**을 클릭하여 가상 컴퓨터 창을 엽니다.

3. 다음 명령을 실행하여 NetworkManager를 제거합니다.

		# sudo rpm -e --nodeps NetworkManager

	>[AZURE.NOTE] 패키지가 아직 설치되어 있지 않은 경우 이 명령이 실패하고 오류 메시지가 표시됩니다. 예상된 동작입니다.

4. 다음 텍스트가 포함된 **network**파일을 /etc/sysconfig/ 디렉터리에 만듭니다.

	`NETWORKING=yes` `HOSTNAME=localhost.localdomain`

5.  다음 텍스트가 포함된 **ifcfg-eth0** 파일을 /etc/sysconfig/network-scripts/ 디렉터리에 만듭니다.

		DEVICE=eth0
		ONBOOT=yes
		BOOTPROTO=dhcp
			TYPE=Ethernet
			USERCTL=no
			PEERDNS=yes
		IPV6INIT=no

6.  이더넷 인터페이스에 대한 정적 규칙을 생성하지 않도록 방지하는 udev 규칙을 이동(또는 제거)합니다. 이러한 규칙은 Azure 또는 Hyper-V에서 가상 컴퓨터를 복제하는 경우 문제를 발생시킵니다.

		# sudo mkdir -m 0700 /var/lib/waagent
		# sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/ 2>/dev/null
		# sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/ 2>/dev/null

7.  다음 명령을 실행하여 부팅 시 네트워크 서비스가 시작되도록 합니다.

		# chkconfig network on

8.  다음 명령을 실행하여 python-pyasn1을 설치합니다.

		# sudo yum install python-pyasn1

9.  Azure용 커널 매개 변수를 추가로 포함하려면 grub 구성에서 커널 부팅 줄을 수정합니다. 이 작업을 수행하려면 "/boot/grub/menu.lst"를 텍스트 편집기에서 열고 다음 매개 변수가 기본 커널에 포함되어 있는지 확인합니다.

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

	이렇게 하면 모든 콘솔 메시지가 첫 번째 직렬 포트로 전송되므로 Azure 지원에서 문제를 디버깅하는 데에도 도움이 될 수 있습니다. 이 경우 Oracle Red Hat 호환 커널의 버그로 인해 NUMA가 사용하지 않도록 설정됩니다.

	위의 작업을 수행하는 동시에 다음 매개 변수를 *제거*하는 것이 좋습니다.

		rhgb quiet crashkernel=auto

	모든 로그를 직렬 포트로 보내려는 클라우드 환경에서는 그래픽 및 자동 부팅 기능이 효율적이지 않습니다.

	필요에 따라 `crashkernel` 옵션을 구성된 상태로 유지할 수도 있지만 이 매개 변수를 사용하는 경우 VM에서 사용 가능한 메모리의 양이 128MB 이상 감소하므로 VM 크기가 작은 경우 문제가 될 수 있습니다.

10.  SSH 서버가 설치되어 부팅 시 시작되도록 구성되어 있는지 확인합니다. 보통 SSH 서버는 기본적으로 이와 같이 구성되어 있습니다.

11.  다음 명령을 실행하여 Azure Linux 에이전트를 설치합니다.

		# sudo yum install WALinuxAgent

	WALinuxAgent 패키지를 설치하면 NetworkManager 및 NetworkManager-gnome 패키지가 2단계에서 설명된 대로 아직 제거되지 않은 경우 이러한 패키지를 제거합니다.

12.  OS 디스크에 스왑 공간을 만들지 마십시오.

	Azure Linux 에이전트는 Azure에서 프로비전한 후 VM에 연결된 로컬 리소스 디스크를 사용하여 자동으로 스왑 공간을 구성할 수 있습니다. 로컬 리소스 디스크는 *임시* 디스크이며 VM의 프로비전을 해제할 때 비워질 수 있습니다. Azure Linux 에이전트를 설치한 후(이전 단계 참조) /etc/waagent.conf에서 다음 매개 변수를 적절하게 수정합니다.

		ResourceDisk.Format=y

		ResourceDisk.Filesystem=ext4

		ResourceDisk.MountPoint=/mnt/resource

		ResourceDisk.EnableSwap=y

		ResourceDisk.SwapSizeMB=2048 ## NOTE: set this to whatever you need it to be.

13.  다음 명령을 실행하여 가상 컴퓨터의 프로비전을 해제하고 Azure에서 프로비전할 준비를 합니다.

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

14.  Hyper-V 관리자에서 **작업 -> 종료**를 클릭합니다. 이제 Linux VHD를 Azure에 업로드할 수 있습니다.

## Oracle Linux 7.0
**Oracle Linux 7의 변경 내용**

Azure용으로 Oracle Linux 7 가상 컴퓨터를 준비하는 작업은 Oracle Linux 6에 대한 프로세스와 매우 비슷하지만 다음과 같은 몇 가지 중요한 차이점이 있습니다.

-   Red Hat 호환 커널과 Oracle의 UEK3은 모두 Azure에서 지원됩니다. UEK3 커널을 사용하는 것이 좋습니다.

-   NetworkManager 패키지가 더 이상 Azure Linux 에이전트와 충돌하지 않습니다. 이 패키지는 기본적으로 설치되며 제거하지 않는 것이 좋습니다.

-   이제 GRUB2가 기본 부팅 로더로 사용되므로 커널 매개 변수를 편집하는 절차가 변경되었습니다(아래 참조).

-   이제 XFS가 기본 파일 시스템입니다. 원하는 경우에는 ext4 파일 시스템도 계속 사용할 수 있습니다.

**구성 단계**

1.  Hyper-V 관리자에서 가상 컴퓨터를 선택합니다.

2.  **연결**을 클릭하여 가상 컴퓨터의 콘솔 창을 엽니다.

3.  다음 텍스트가 포함된 **network**파일을 /etc/sysconfig/ 디렉터리에 만듭니다.

		NETWORKING=yes
		HOSTNAME=localhost.localdomain

4.  다음 텍스트가 포함된 **ifcfg-eth0**이라는 파일을 /etc/sysconfig/network-scripts/ 디렉터리에 만듭니다.

		DEVICE=eth0
		ONBOOT=yes
		BOOTPROTO=dhcp
		TYPE=Ethernet
		USERCTL=no
			PEERDNS=yes
		IPV6INIT=no

5.  이더넷 인터페이스에 대한 정적 규칙을 생성하지 않도록 방지하는 udev 규칙을 이동(또는 제거)합니다. 이러한 규칙은 Microsoft Azure 또는 Hyper-V에서 가상 컴퓨터를 복제하는 경우 문제를 발생시킵니다.

		# sudo mkdir -m 0700 /var/lib/waagent
		# sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/ 2>/dev/null
		# sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/ 2>/dev/null

6.  다음 명령을 실행하여 부팅 시 네트워크 서비스가 시작되도록 합니다.

		# sudo chkconfig network on

7.  다음 명령을 실행하여 python-pyasn1 패키지를 설치합니다.

		# sudo yum install python-pyasn1

8.  다음 명령을 실행하여 현재 yum 메타데이터를 지우고 모든 업데이트를 설치합니다.

		# sudo yum clean all
		# sudo yum -y update

9.  Azure용 커널 매개 변수를 추가로 포함하려면 grub 구성에서 커널 부팅 줄을 수정합니다. 이렇게 하려면 텍스트 편집기에서 "/etc/default/grub"를 열고 GRUB\_CMDLINE\_LINUX 매개 변수를 편집합니다. 예를 들면 다음과 같습니다.

		GRUB\_CMDLINE\_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"

	이렇게 하면 모든 콘솔 메시지가 첫 번째 직렬 포트로 전송되므로 Azure 지원에서 문제를 디버깅하는 데에도 도움이 될 수 있습니다. 위의 작업을 수행하는 동시에 다음 매개 변수를 *제거*하는 것이 좋습니다.

		rhgb quiet crashkernel=auto

	모든 로그를 직렬 포트로 보내려는 클라우드 환경에서는 그래픽 및 자동 부팅 기능이 효율적이지 않습니다.

	필요에 따라 `crashkernel` 옵션을 구성된 상태로 유지할 수도 있지만 이 매개 변수를 사용하는 경우 VM에서 사용 가능한 메모리의 양이 128MB 이상 감소하므로 VM 크기가 작은 경우 문제가 될 수 있습니다.

10.  "/etc/default/grub" 편집을 완료한 후에는 다음 명령을 실행하여 grub 구성을 다시 빌드합니다.

		# sudo grub2-mkconfig -o /boot/grub2/grub.cfg

11.  SSH 서버가 설치되어 부팅 시 시작되도록 구성되어 있는지 확인합니다. 보통 SSH 서버는 기본적으로 이와 같이 구성되어 있습니다.

12.  다음 명령을 실행하여 Azure Linux 에이전트를 설치합니다.

		# sudo yum install WALinuxAgent

13.  OS 디스크에 스왑 공간을 만들지 마십시오.

	Azure Linux 에이전트는 Azure에서 프로비전한 후 VM에 연결된 로컬 리소스 디스크를 사용하여 자동으로 스왑 공간을 구성할 수 있습니다. 로컬 리소스 디스크는 *임시* 디스크이며 VM의 프로비전을 해제할 때 비워질 수 있습니다. Azure Linux 에이전트를 설치한 후(이전 단계 참조) /etc/waagent.conf에서 다음 매개 변수를 적절하게 수정합니다.

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048 ## NOTE: Set this to whatever you need it to be.

14.  다음 명령을 실행하여 가상 컴퓨터의 프로비전을 해제하고 Azure에서 프로비전할 준비를 합니다.

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

15.  Hyper-V 관리자에서 **작업 -> 종료**를 클릭합니다. 이제 Linux VHD를 Azure에 업로드할 수 있습니다.

<!---HONumber=AcomDC_0211_2016-->