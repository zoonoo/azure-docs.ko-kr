---
title: 오라클 리눅스 VHD 생성 및 업로드
description: Oracle Linux 운영 체제가 포함된 Azure VHD(가상 하드 디스크)를 만들고 업로드하는 방법에 대해 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: gbowerman
manager: gwallace
editor: tysonn
tags: azure-service-management,azure-resource-manager
ms.assetid: dd96f771-26eb-4391-9a89-8c8b6d691822
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 12/10/2019
ms.author: guybo
ms.openlocfilehash: 784d6c01125a9fd6ec291f32e989e4b22e7607af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066572"
---
# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Azure용 Oracle Linux 가상 머신 준비

이 문서에서는 가상 하드 디스크에 Oracle Linux 운영 체제를 이미 설치했다고 가정합니다. .vhd 파일을 만드는 여러 도구가 있습니다(예: Hyper-V와 같은 가상화 솔루션). 지침은 [Hyper-V 역할 설치 및 가상 컴퓨터 구성을](https://technet.microsoft.com/library/hh846766.aspx)참조하십시오.

## <a name="oracle-linux-installation-notes"></a>Oracle Linux 설치 참고 사항
* Azure용 Linux를 준비하는 방법에 대한 추가 팁은 [일반 Linux 설치 참고 사항](create-upload-generic.md#general-linux-installation-notes) 을 참조하세요.
* 하이퍼-V 및 Azure는 Ununable 엔터프라이즈 커널(UEK) 또는 Red Hat 호환 커널을 통해 오라클 Linux를 지원합니다.
* Oracle의 UEK2는 필수 드라이버를 포함하지 않으므로 Hyper-V 및 Azure에서 지원되지 않습니다.
* VHDX 형식은 Azure에서 지원되지 않습니다. **고정된 VHD**만 지원됩니다.  Hyper-V 관리자 또는 convert-vhd cmdlet을 사용하여 디스크를 VHD 형식으로 변환할 수 있습니다.
* Linux 시스템 설치 시에는 LVM(설치 기본값인 경우가 많음)이 아닌 표준 파티션을 사용하는 것이 좋습니다. 이렇게 하면 특히 문제 해결을 위해 OS 디스크를 다른 VM에 연결해야 하는 경우 복제된 VM과 LVM 이름이 충돌하지 않도록 방지합니다. 원하는 경우 데이터 디스크에 [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 또는 [RAID를](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 사용할 수 있습니다.
* 2.6.37 이전 버전의 Linux 커널은 더 큰 VM 크기의 Hyper-V에서 NUMA를 지원하지 않습니다. 이 문제는 주로 업스트림 Red Hat 2.6.32 커널을 사용하여 이전 배포판에 영향을 미치며 오라클 Linux 6.6 이상에서 수정되었습니다.
* OS 디스크에 스왑 파티션을 구성하지 마세요. 임시 리소스 디스크에서 스왑 파일을 만들도록 Linux 에이전트를 구성할 수 있습니다.  여기에 대한 자세한 내용은 아래 단계에서 확인할 수 있습니다.
* Azure의 모든 VHD는 가상 크기가 1MB 단위로 조정되어야 합니다. 원시 디스크에서 VHD로 변환할 때 변환하기 전에 원시 디스크 크기가 1MB의 배수인지 확인해야 합니다. 자세한 내용은 [Linux 설치 참고 사항](create-upload-generic.md#general-linux-installation-notes)을 참조하세요.
* `Addons` 리포지토리가 사용되도록 설정되었는지 확인합니다. 파일(오라클 `/etc/yum.repos.d/public-yum-ol6.repo`리눅스 `/etc/yum.repos.d/public-yum-ol7.repo`6) 또는 (오라클 `enabled=0` `enabled=1` 리눅스 7) 편집하고 이 파일의 **[ol6_addons]** 또는 **[ol7_addons]** 아래에서 줄을 변경합니다.

## <a name="oracle-linux-64-and-later"></a>오라클 리눅스 6.4 이상
가상 머신을 Azure에서 실행하려면 운영 체제에서 특정 구성 단계를 완료해야 합니다.

1. Hyper-V 관리자의 가운데 창에서 가상 머신을 선택합니다.
2. **연결**을 클릭하여 가상 컴퓨터 창을 엽니다.
3. 다음 명령을 실행하여 NetworkManager를 제거합니다.
   
        # sudo rpm -e --nodeps NetworkManager
   
    **참고:** 패키지가 아직 설치되어 있지 않은 경우 이 명령이 실패하고 오류 메시지가 표시됩니다. 예상된 동작입니다.
4. 다음 텍스트가 포함된 **network** in the `/etc/sysconfig/` 디렉터리에 만듭니다.
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
5. 다음 텍스트가 포함된 **ifcfg-eth0** in the `/etc/sysconfig/network-scripts/` 디렉터리에 만듭니다.
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
6. 이더넷 인터페이스에 대한 정적 규칙을 생성하지 않도록 방지하는 udev 규칙을 수정합니다. 이러한 규칙은 Microsoft Azure 또는 Hyper-V에서 가상 머신을 복제하는 경우 문제를 발생시킬 수 있습니다.
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
7. 다음 명령을 실행하여 부팅 시 네트워크 서비스가 시작되도록 합니다.
   
        # chkconfig network on
8. 다음 명령을 실행하여 python-pyasn1을 설치합니다.
   
        # sudo yum install python-pyasn1
9. Azure용 커널 매개 변수를 추가로 포함하려면 grub 구성에서 커널 부팅 줄을 수정합니다. 텍스트 편집기에서 "/boot/grub/menu.lst"를 열고 커널에 다음 매개 변수가 포함되어 있는지 확인하려면 다음을 수행하십시오.
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
   이렇게 하면 모든 콘솔 메시지가 첫 번째 직렬 포트로 전송되므로 Azure 지원에서 문제를 디버깅하는 데 도움이 될 수 있습니다.
   
   위의 작업을 수행하는 동시에 다음 매개 변수도 *제거* 하는 것이 좋습니다.
   
        rhgb quiet crashkernel=auto
   
   모든 로그를 직렬 포트로 보내려는 클라우드 환경에서는 그래픽 및 자동 부팅 기능이 효율적이지 않습니다.
   
   원하는 경우에는 `crashkernel` 옵션을 구성한 상태로 유지할 수도 있지만 이 매개 변수를 사용하는 경우 VM에서 사용 가능한 메모리의 양이 128MB 이상 감소하므로 VM 크기가 작은 경우 문제가 될 수 있습니다.
10. SSH 서버가 설치되어 부팅 시 시작되도록 구성되어 있는지 확인합니다.  보통 SSH 서버는 기본적으로 이와 같이 구성되어 있습니다.
11. 다음 명령을 실행하여 Azure Linux 에이전트를 설치합니다. 최신 버전은 2.0.15입니다.
    
        # sudo yum install WALinuxAgent
    
    WALinuxAgent 패키지를 설치하면 NetworkManager 및 NetworkManager-gnome 패키지가 2단계에서 설명된 대로 아직 제거되지 않은 경우 이러한 패키지를 제거합니다.
12. OS 디스크에 스왑 공간을 만들지 마십시오.
    
    Azure Linux 에이전트는 Azure에서 프로비전한 후 VM에 연결된 로컬 리소스 디스크를 사용하여 자동으로 스왑 공간을 구성할 수 있습니다. 로컬 리소스 디스크는 *임시* 디스크이며 VM프로비저닝이 해제될 때 비워질 수 있습니다. Azure Linux 에이전트를 설치한 후(이전 단계 참조) /etc/waagent.conf에서 다음 매개 변수를 적절하게 수정합니다.
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
13. 다음 명령을 실행하여 가상 머신의 프로비전을 해제하고 Azure에서 프로비전할 준비를 합니다.
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
14. 하이퍼 V 관리자에서 **작업 -> 종료를** 클릭합니다. 이제 Linux VHD를 Azure에 업로드할 수 있습니다.

---
## <a name="oracle-linux-70-and-later"></a>오라클 리눅스 7.0 이상
**Oracle Linux 7의 변경 내용**

Azure용으로 Oracle Linux 7 가상 컴퓨터를 준비하는 작업은 Oracle Linux 6과 매우 비슷하지만 다음과 같은 몇 가지 중요한 차이점이 있습니다.

* Azure는 깨지지 않는 엔터프라이즈 커널(UEK) 또는 Red Hat 호환 커널을 통해 오라클 Linux를 지원합니다. UEK오라클 리눅스는 권장됩니다.
* NetworkManager 패키지가 더 이상 Azure Linux 에이전트와 충돌하지 않습니다. 이 패키지는 기본적으로 설치되며 제거하지 않는 것이 좋습니다.
* 이제 GRUB2가 기본 부팅 로더로 사용되므로 커널 매개 변수를 편집하는 절차가 변경되었습니다(아래 참조).
* 이제 XFS가 기본 파일 시스템입니다. 원하는 경우에는 ext4 파일 시스템도 계속 사용할 수 있습니다.

**구성 단계**

1. Hyper-V 관리자에서 가상 머신을 선택합니다.
2. **연결** 을 클릭하여 가상 머신의 콘솔 창을 엽니다.
3. 다음 텍스트가 포함된 **network** in the `/etc/sysconfig/` 디렉터리에 만듭니다.
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
4. 다음 텍스트가 포함된 **ifcfg-eth0** in the `/etc/sysconfig/network-scripts/` 디렉터리에 만듭니다.
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
5. 이더넷 인터페이스에 대한 정적 규칙을 생성하지 않도록 방지하는 udev 규칙을 수정합니다. 이러한 규칙은 Microsoft Azure 또는 Hyper-V에서 가상 머신을 복제하는 경우 문제를 발생시킬 수 있습니다.
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
6. 다음 명령을 실행하여 부팅 시 네트워크 서비스가 시작되도록 합니다.
   
        # sudo chkconfig network on
7. 다음 명령을 실행하여 python-pyasn1 패키지를 설치합니다.
   
        # sudo yum install python-pyasn1
8. 다음 명령을 실행하여 현재 yum 메타데이터를 지우고 모든 업데이트를 설치합니다.
   
        # sudo yum clean all
        # sudo yum -y update
9. Azure용 커널 매개 변수를 추가로 포함하려면 grub 구성에서 커널 부팅 줄을 수정합니다. 이렇게 하려면 텍스트 편집기에서 "/etc/default/grub"를 열고 `GRUB_CMDLINE_LINUX` 매개 변수를 편집합니다. 예를 들면 다음과 같습니다.
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   이렇게 하면 모든 콘솔 메시지가 첫 번째 직렬 포트로 전송되므로 Azure 지원에서 문제를 디버깅하는 데에도 도움이 될 수 있습니다. 또한 오라클 리눅스 7에서 PC에 대 한 명명 규칙을 해제 하 고 깨지지 않는 엔터프라이즈 커널. 위의 작업을 수행하는 동시에 다음 매개 변수도 *제거* 하는 것이 좋습니다.
   
       rhgb quiet crashkernel=auto
   
   모든 로그를 직렬 포트로 보내려는 클라우드 환경에서는 그래픽 및 자동 부팅 기능이 효율적이지 않습니다.
   
   원하는 경우에는 `crashkernel` 옵션을 구성한 상태로 유지할 수도 있지만 이 매개 변수를 사용하는 경우 VM에서 사용 가능한 메모리의 양이 128MB 이상 감소하므로 VM 크기가 작은 경우 문제가 될 수 있습니다.
10. 위의 설명에 따라 "/etc/default/grub" 편집을 완료한 후에는 다음 명령을 실행하여 grub 구성을 다시 빌드합니다.
    
        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
11. SSH 서버가 설치되어 부팅 시 시작되도록 구성되어 있는지 확인합니다.  보통 SSH 서버는 기본적으로 이와 같이 구성되어 있습니다.
12. 다음 명령을 실행하여 Azure Linux 에이전트를 설치합니다.
    
        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent
13. OS 디스크에 스왑 공간을 만들지 마십시오.
    
    Azure Linux 에이전트는 Azure에서 프로비전한 후 VM에 연결된 로컬 리소스 디스크를 사용하여 자동으로 스왑 공간을 구성할 수 있습니다. 로컬 리소스 디스크는 *임시* 디스크이며 VM프로비저닝이 해제될 때 비워질 수 있습니다. Azure Linux 에이전트를 설치한 후(이전 단계 참조) /etc/waagent.conf에서 다음 매개 변수를 적절하게 수정합니다.
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
14. 다음 명령을 실행하여 가상 머신의 프로비전을 해제하고 Azure에서 프로비전할 준비를 합니다.
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
15. 하이퍼 V 관리자에서 **작업 -> 종료를** 클릭합니다. 이제 Linux VHD를 Azure에 업로드할 수 있습니다.

## <a name="next-steps"></a>다음 단계
이제 Oracle Linux .vhd를 사용하여 Azure에서 새 가상 머신을 만들 준비가 되었습니다. .vhd 파일을 Azure에 처음 업로드하는 경우 [사용자 지정 디스크에서 Linux VM 만들기](upload-vhd.md#option-1-upload-a-vhd)를 참조하세요.

