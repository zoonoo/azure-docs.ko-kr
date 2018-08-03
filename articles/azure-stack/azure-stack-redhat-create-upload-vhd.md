---
title: 만들기 및 Azure Stack에서 사용 하기 위해 Red Hat Enterprise Linux VHD 업로드 | Microsoft Docs
description: RedHat Linux 운영 체제가 포함된 Azure VHD(가상 하드 디스크)를 만들고 업로드하는 방법에 대해 알아봅니다.
services: azure-stack
documentationcenter: ''
author: JeffGoldner
manager: BradleyB
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: jeffgo
ms.openlocfilehash: 5af8380accc23a62baf04b842430e692fdff3692
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39443555"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure-stack"></a>Azure Stack에 대 한 Red Hat 기반 가상 머신 준비

이 문서에서는 Azure Stack에서 사용 하 여 Red Hat Enterprise Linux (RHEL) 가상 컴퓨터를 준비 하는 방법을 배웁니다. 이 문서에 설명 되어 있는 RHEL의 버전은 7.1 +입니다. 이 문서에서 다룰 준비에 대한 하이퍼바이저는 Hyper-V, KVM(커널 기반 가상 머신) 및 VMware입니다.

Red Hat Enterprise Linux 지원 정보를 참조 하세요 [Red Hat 및 Azure Stack: Frequently Asked Questions](https://access.redhat.com/articles/3413531)합니다.

## <a name="prepare-a-red-hat-based-virtual-machine-from-hyper-v-manager"></a>Hyper-V 관리자에서 Red Hat 기반 가상 머신 준비

이 섹션에서는 가정 이미 Red Hat 웹 사이트에서 ISO 파일이 가상 하드 디스크 (VHD)에 RHEL 이미지를 설치 합니다. Hyper-v 관리자를 사용 하 여 운영 체제 이미지를 설치 하는 방법에 대 한 자세한 내용은 참조 하세요. [Hyper-v 역할 설치 및 가상 머신 구성](http://technet.microsoft.com/library/hh846766.aspx)합니다.

### <a name="rhel-installation-notes"></a>RHEL 설치 참고 사항

* Azure Stack에 VHDX 형식을 지원 하지 않습니다. Azure는 고정 VHD만 지원합니다. Hyper-V 관리자를 사용하여 디스크를 VHD 형식으로 변환하거나, convert-vhd cmdlet을 사용할 수 있습니다. VirtualBox를 사용하는 경우 디스크를 만들 때 기본 동적 할다 옵션과 달리 **고정 크기**를 선택합니다.
* Azure Stack에는 1 세대 가상 머신만 지원합니다. VHDX에서 VHD 파일 형식으로, 동적 확장에서 고정된 크기의 디스크로 1세대 가상 컴퓨터를 변환할 수 있습니다. 가상 머신의 세대는 변경할 수 없습니다. 자세한 내용은 [Hyper-V에 1 또는 2세대 가상 머신을 만들어야 하나요?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)를 참조하세요.
* VHD에 허용되는 최대 크기는 1,023GB입니다.
* Linux 운영 체제를 설치하는 경우 설치 기본값인 경우가 많은 LVM(논리 볼륨 관리자)이 아닌 표준 파티션을 사용하는 것이 좋습니다. 이 방법은 특히 문제 해결에 대 한 다른 동일한 가상 컴퓨터에 운영 체제 디스크를 연결 해야 하는 경우 복제 된 가상 머신과 LVM 이름이 충돌을 방지 합니다.
* UDF(범용 디스크 형식) 파일 시스템을 탑재하기 위한 커널 지원이 필요합니다. 게스트에 연결 된 UDF 형식의 미디어 처음 부팅 시 Linux 가상 머신을 프로 비전 구성을 전달 합니다. Azure Linux 에이전트는 해당 구성을 읽고 가상 머신을 프로 비전을 위해 UDF 파일 시스템을 탑재 해야 합니다.
* 운영 체제 디스크에서는 스왑 파티션을 구성하지 마세요. 임시 리소스 디스크에서 스왑 파일을 만들도록 Linux 에이전트를 구성할 수 있습니다. 다음 단계에서에 대 한 자세한 정보를 찾을 수 있습니다.
* Azure의 모든 Vhd는 가상 크기가 1MB로 정렬 있어야 합니다. 원시 디스크에서 VHD로 변환할 때 원시 디스크 크기가 변환 전에 1MB의 배수 인지 확인 해야 합니다. 자세한 내용은 아래 단계에서 찾을 수 있습니다.
* Azure Stack에서 cloud-init를 지원 하지 않습니다. 지원 되는 Windows Azure Linux 에이전트 (WALA)의 버전을 사용 하 여 VM은 구성 해야 합니다.

### <a name="prepare-a-rhel-7-virtual-machine-from-hyper-v-manager"></a>Hyper-V 관리자에서 RHEL 7 가상 머신 준비

1. Hyper-V 관리자에서 가상 머신을 선택합니다.

1. **연결** 을 클릭하여 가상 머신의 콘솔 창을 엽니다.

1. 파일 `/etc/sysconfig/network`를 만들거나 편집하고 다음 텍스트를 추가합니다.

    ```sh
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. 만들기 또는 편집을 `/etc/sysconfig/network-scripts/ifcfg-eth0` 파일 및 필요에 따라 다음 텍스트를 추가 합니다.

    ```sh
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

1. 다음 명령을 실행 하 여 네트워크 서비스 부팅 시 시작 해야 합니다.

    ```sh
    # sudo systemctl enable network
    ```

1. RHEL 리포지토리에서 패키지 설치를 사용하도록 다음 명령을 실행하여 Red Hat 구독을 등록합니다.

    ```sh
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Azure용 커널 매개 변수를 추가로 포함하려면 grub 구성에서 커널 부팅 줄을 수정합니다. 이렇게 수정 하려면 엽니다 `/etc/default/grub` 텍스트 편집기에서 수정 하 고는 `GRUB_CMDLINE_LINUX` 매개 변수입니다. 예: 

    ```sh
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   이렇게 하면 모든 콘솔 메시지가 첫 번째 직렬 포트로 도움이 될 수 있습니다 Azure 보내집니다는 문제를 디버깅 하는 지원 합니다. 이 구성은 NIC에 대한 새 RHEL 7 명명 규칙도 해제합니다. 

   모든 로그를 직렬 포트로 보내려는 클라우드 환경에서는 그래픽 및 자동 부팅 기능이 효율적이지 않습니다. 원할 경우 `crashkernel` 옵션은 구성된 상태로 둘 수 있습니다. 이 매개 변수는 가상 머신의 사용 가능한 메모리 양을 128MB 이상 줄입니다. 이 방식은 좀 더 작은 가상 머신 크기에서는 문제가 될 수도 있습니다. 다음 매개 변수를 제거 하는 것이 좋습니다.

    ```sh
    rhgb quiet crashkernel=auto
    ```

1. `/etc/default/grub`편집을 완료한 후에 다음 명령을 실행하여 grub 구성을 다시 빌드합니다.

    ```sh
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. SSH 서버가 설치되어 부팅 시 시작되도록 구성되어 있는지 확인합니다. 이것이 일반적으로 기본값입니다. 다음 줄을 포함하도록 `/etc/ssh/sshd_config` 을 수정합니다.

    ```sh
    ClientAliveInterval 180
    ```

1. WALinuxAgent 패키지 `WALinuxAgent-<version>`은 Red Hat 기타 리포지토리에 푸시되었습니다. 다음 명령을 실행하여 기타 리포지토리를 사용합니다.

    ```sh
    # subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. 다음 명령을 실행하여 Azure Linux 에이전트를 설치합니다.

    ```sh
    # sudo yum install WALinuxAgent
    # sudo systemctl enable waagent.service
    ```

1. 운영 체제 디스크에 스왑 공간을 만들지 마세요.

    Azure Linux 에이전트는 Azure에서 가상 머신을 프로비전한 후에 가상 머신에 연결된 로컬 리소스 디스크를 사용하여 자동으로 스왑 공간을 구성할 수 있습니다. 로컬 리소스 디스크는 임시 디스크 및 가상 컴퓨터 프로 비전을 해제 하는 때 비워질 수 있습니다. Azure Linux 에이전트를 설치한 후에(이전 단계 참조) `/etc/waagent.conf`에서 다음 매개 변수를 적절하게 수정합니다.

    ```sh
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    # NOTE: set this to whatever you need it to be.
    ```

1. 구독에 대한 등록을 해제하려면 다음 명령을 실행합니다.

    ```sh
    # sudo subscription-manager unregister
    ```

1. 엔터프라이즈 인증 기관을 사용 하 여 배포 된 시스템을 사용 하는 경우 RHEL 가상 머신에 Azure Stack 루트 인증서를 신뢰 하지 않게 됩니다. 신뢰할 수 있는 루트 저장소에 배치 해야 합니다. 참조 [신뢰할 수 있는 추가 루트 인증서를 서버](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html)합니다.

1. 다음 명령을 실행하여 가상 머신의 프로비전을 해제하고 Azure에서 프로비전할 준비를 합니다.

    ```sh
    # sudo waagent -force -deprovision
    # export HISTSIZE=0
    # logout
    ```

1. Hyper-V 관리자에서 **작업** > **종료**를 클릭합니다.

1. CONVERT-VHD PowerShell 명령 또는 Hyper-v 관리자 "편집 디스크" 기능을 사용 하 여 VHD 고정 크기 VHD를 변환 합니다. 이제 Linux VHD를 Azure에 업로드할 수 있습니다.

## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>KVM에서 RedHat 기반 가상 머신 준비

1. Red Hat 웹 사이트에서 RHEL 7의 KVM 이미지를 다운로드합니다. 이 절차에서는 RHEL 7을 예제로 사용합니다.

1. 루트 암호를 설정합니다.

    암호화된 암호를 생성하고 명령 출력을 복사합니다.

    ```sh
    # openssl passwd -1 changeme
    ```

   guestfish 루트 암호를 설정합니다.

    ```sh
    # guestfish --rw -a <image-name>
    > <fs> run
    > <fs> list-filesystems
    > <fs> mount /dev/sda1 /
    > <fs> vi /etc/shadow
    > <fs> exit
    ```

   루트 사용자의 두 번째 필드를 “!!”에서 암호화된 암호로 변경합니다.

1. qcow2 이미지에서 KVM에 가상 머신을 만듭니다. 디스크 형식을 **qcow2**로 설정하고 가상 네트워크 인터페이스 장치 모델을 **virtio**로 설정합니다. 그 후 가상 머신을 시작하고 루트로 로그인합니다.

1. 파일 `/etc/sysconfig/network`를 만들거나 편집하고 다음 텍스트를 추가합니다.

    ```sh
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. 파일 `/etc/sysconfig/network-scripts/ifcfg-eth0`를 만들거나 편집하고 다음 텍스트를 추가합니다.

    ```sh
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

1. 다음 명령을 실행 하 여 네트워크 서비스 부팅 시 시작 해야 합니다.

    ```sh
    # sudo systemctl enable network
    ```

1. RHEL 리포지토리에서 패키지 설치를 사용하도록 다음 명령을 실행하여 Red Hat 구독을 등록합니다.

    ```sh
    # subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Azure용 커널 매개 변수를 추가로 포함하려면 grub 구성에서 커널 부팅 줄을 수정합니다. 이 구성을 수행 하려면 엽니다 `/etc/default/grub` 텍스트 편집기에서 수정 된 `GRUB_CMDLINE_LINUX` 매개 변수. 예: 

    ```sh
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   이 명령은 모든 콘솔 메시지를 첫 번째 직렬 포트로 전송하므로 Azure 지원에서 문제를 디버깅하는 데에도 도움이 될 수 있습니다. 이 명령은 해제 새 RHEL 7 명명 규칙도 Nic에 대 한

   그래픽 및 자동 부팅 로그를 직렬 포트로 보내는 클라우드 환경에서 유용 합니다. 원할 경우 `crashkernel` 옵션은 구성된 상태로 둘 수 있습니다. 이 매개 변수는 더 작은 가상 머신 크기에 문제가 발생할 수도 있습니다 128MB 이상 가상 컴퓨터에서 사용 가능한 메모리의 양을 줄입니다. 다음 매개 변수를 제거 하는 것이 좋습니다.

    ```sh
    rhgb quiet crashkernel=auto
    ```

1. `/etc/default/grub`편집을 완료한 후에 다음 명령을 실행하여 grub 구성을 다시 빌드합니다.

    ```sh
    # grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Hyper-V 모듈을 initramfs에 추가합니다.

    `/etc/dracut.conf` 을 편집하고 콘텐츠를 추가합니다.

    ```sh
    add_drivers+="hv_vmbus hv_netvsc hv_storvsc"
    ```

    Initramfs를 다시 빌드합니다.

    ```sh
    # dracut -f -v
    ```

1. Cloud-Init을 제거합니다.

    ```sh
    # yum remove cloud-init
    ```

1. SSH 서버가 설치되어 부팅 시 시작되도록 구성되어 있는지 확인합니다.

    ```sh
    # systemctl enable sshd
    ```

    다음 줄을 포함하도록 /etc/ssh/sshd_config를 수정합니다.

    ```sh
    PasswordAuthentication yes
    ClientAliveInterval 180
    ```

1. WALinuxAgent 패키지 `WALinuxAgent-<version>`은 Red Hat 기타 리포지토리에 푸시되었습니다. 다음 명령을 실행하여 기타 리포지토리를 사용합니다.

    ```sh
    # subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. 다음 명령을 실행하여 Azure Linux 에이전트를 설치합니다.

    ```sh
    # yum install WALinuxAgent
    ```

    waagent 서비스를 사용하도록 설정합니다.

    ```sh
    # systemctl enable waagent.service
    ```

1. 운영 체제 디스크에 스왑 공간을 만들지 마세요.

    Azure Linux 에이전트는 Azure에서 가상 머신을 프로비전한 후에 가상 머신에 연결된 로컬 리소스 디스크를 사용하여 자동으로 스왑 공간을 구성할 수 있습니다. 로컬 리소스 디스크는 임시 디스크 및 가상 컴퓨터 프로 비전을 해제 하는 때 비워질 수 있습니다. Azure Linux 에이전트를 설치한 후에(이전 단계 참조) `/etc/waagent.conf`에서 다음 매개 변수를 적절하게 수정합니다.

    ```sh
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    # NOTE: set this to whatever you need it to be.
    ```

1. 다음 명령을 실행하여 (필요한 경우) 구독에 대한 등록을 해제합니다.

    ```sh
    # subscription-manager unregister
    ```

1. 엔터프라이즈 인증 기관을 사용 하 여 배포 된 시스템을 사용 하는 경우 RHEL 가상 머신에 Azure Stack 루트 인증서를 신뢰 하지 않게 됩니다. 신뢰할 수 있는 루트 저장소에 배치 해야 합니다. 참조 [신뢰할 수 있는 추가 루트 인증서를 서버](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html)합니다.

1. 다음 명령을 실행하여 가상 머신의 프로비전을 해제하고 Azure에서 프로비전할 준비를 합니다.

    ```sh
    # sudo waagent -force -deprovision
    # export HISTSIZE=0
    # logout
    ```

1. KVM의 가상 머신을 종료합니다.

1. qcow2 이미지를 VHD 형식으로 변환합니다.

    > [!NOTE]
    > VHD 형식이 잘못 지정되는 qemu-img 버전 >=2.2.1에 알려진 버그가 있습니다. 이 문제는 QEMU 2.6에서 해결되었습니다. qemu-img 2.2.0 이하 버전을 사용하거나 2.6 이상으로 업데이트하는 것이 좋습니다. 참조: https://bugs.launchpad.net/qemu/+bug/1490611

    우선 이미지를 원시 형식으로 변환합니다.

    ```sh
    # qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw
    ```

    원시 이미지의 크기가 1MB로 정렬되는지 확인합니다. 그렇지 않은 경우 1MB에 맞게 크기를 반올림합니다.

    ```sh
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-7.4.raw $rounded_size
    ```

    원시 디스크를 고정 크기 VHD로 변환합니다.

    ```sh
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

    또는, qemu 버전 **2.6 +** 는 `force_size` 옵션을 포함합니다.

    ```sh
    # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="prepare-a-red-hat-based-virtual-machine-from-vmware"></a>VMware에서 RedHat 기반 가상 머신 준비

이 섹션은 VMWare에 RHEL 가상 머신이 이미 설치되어 있다고 가정합니다. VMWare에서 운영 체제를 설치하는 자세한 방법은 [VMWare 게스트 운영 체제 설치 가이드](http://partnerweb.vmware.com/GOSIG/home.html)를 참조하세요.

* Linux 운영 체제를 설치하는 경우 LVM(설치 기본값인 경우가 많음)이 아닌 표준 파티션을 사용하는 것이 좋습니다. 이 운영 체제 디스크 문제 해결에 대 한 다른 가상 컴퓨터에 연결 해야 하는 경우에 특히 복제 된 가상 머신과 LVM 이름이 충돌을 피할 수 있습니다. 원하는 경우에는 데이터 디스크에서 LVM 또는 RAID를 사용할 수 있습니다.
* 운영 체제 디스크에서는 스왑 파티션을 구성하지 마세요. Linux 에이전트를 구성하여 임시 리소스 디스크에서 스왑 파일을 만들 수 있습니다. 여기에 대한 자세한 내용은 아래 단계에서 찾을 수 있습니다.
* 가상 하드 디스크를 만들 때 **가상 디스크를 단일 파일로 저장**을 선택합니다.

### <a name="prepare-a-rhel-7-virtual-machine-from-vmware"></a>VMWare에서 RHEL 7 가상 머신 준비

1. 파일 `/etc/sysconfig/network`를 만들거나 편집하고 다음 텍스트를 추가합니다.

    ```sh
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. 파일 `/etc/sysconfig/network-scripts/ifcfg-eth0`를 만들거나 편집하고 다음 텍스트를 추가합니다.

    ```sh
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

1. 다음 명령을 실행하여 부팅 시 네트워크 서비스가 시작되도록 합니다.

    ```sh
    # sudo chkconfig network on
    ```

1. RHEL 리포지토리에서 패키지 설치를 사용하도록 다음 명령을 실행하여 Red Hat 구독을 등록합니다.

    ```sh
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Azure용 커널 매개 변수를 추가로 포함하려면 grub 구성에서 커널 부팅 줄을 수정합니다. 이렇게 수정 하려면 엽니다 `/etc/default/grub` 텍스트 편집기에서 수정 하 고는 `GRUB_CMDLINE_LINUX` 매개 변수입니다. 예: 

    ```sh
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

    이 구성은 모든 콘솔 메시지를 첫 번째 직렬 포트로 전송하므로 Azure 지원에서 문제를 디버깅하는 데에도 도움이 될 수 있습니다. NIC에 대한 새 RHEL 7 명명 규칙도 해제합니다. 그 밖에 다음 매개 변수를 제거하는 것이 좋습니다.

    ```sh
    rhgb quiet crashkernel=auto
    ```

    모든 로그를 직렬 포트로 보내려는 클라우드 환경에서는 그래픽 및 자동 부팅 기능이 효율적이지 않습니다. 원할 경우 `crashkernel` 옵션은 구성된 상태로 둘 수 있습니다. 이 매개 변수는 가상 컴퓨터의 사용 가능한 메모리 양을 128MB 이상 줄입니다. 이 방식은 좀 더 작은 가상 컴퓨터 크기에서는 문제가 될 수도 있습니다.

1. `/etc/default/grub`편집을 완료한 후에 다음 명령을 실행하여 grub 구성을 다시 빌드합니다.

    ```sh
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Hyper-V 모듈을 initramfs에 추가합니다.

    `/etc/dracut.conf`를 편집하고 콘텐츠를 추가합니다.

    ```sh
    add_drivers+="hv_vmbus hv_netvsc hv_storvsc"
    ```

    Initramfs를 다시 빌드합니다.

    ```sh
    # dracut -f -v
    ```

1. SSH 서버가 설치되어 부팅 시 시작되도록 구성되어 있는지 확인합니다. 이 설정이 일반적으로 기본값입니다. 다음 줄을 포함하도록 `/etc/ssh/sshd_config` 을 수정합니다.

    ```sh
    ClientAliveInterval 180
    ```

1. WALinuxAgent 패키지 `WALinuxAgent-<version>`은 Red Hat 기타 리포지토리에 푸시되었습니다. 다음 명령을 실행하여 기타 리포지토리를 사용합니다.

    ```sh
    # subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. 다음 명령을 실행하여 Azure Linux 에이전트를 설치합니다.

    ```sh
    # sudo yum install WALinuxAgent
    # sudo systemctl enable waagent.service
    ```

1. 운영 체제 디스크에 스왑 공간을 만들지 마세요.

    Azure Linux 에이전트는 Azure에서 가상 컴퓨터를 프로비전한 후에 가상 컴퓨터에 연결된 로컬 리소스 디스크를 사용하여 자동으로 스왑 공간을 구성할 수 있습니다. 로컬 리소스 디스크는 임시 디스크이며 가상 머신의 프로비전을 해제할 때 비워질 수 있습니다. Azure Linux 에이전트를 설치한 후에(이전 단계 참조) `/etc/waagent.conf`에서 다음 매개 변수를 적절하게 수정합니다.

    ```sh
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    # NOTE: set this to whatever you need it to be.
    ```

1. 구독에 대한 등록을 해제하려면 다음 명령을 실행합니다.

    ```sh
    # sudo subscription-manager unregister
    ```

1. 엔터프라이즈 인증 기관을 사용 하 여 배포 된 시스템을 사용 하는 경우 RHEL 가상 머신에 Azure Stack 루트 인증서를 신뢰 하지 않게 됩니다. 신뢰할 수 있는 루트 저장소에 배치 해야 합니다. 참조 [신뢰할 수 있는 추가 루트 인증서를 서버](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html)합니다.

1. 다음 명령을 실행하여 가상 머신의 프로비전을 해제하고 Azure에서 프로비전할 준비를 합니다.

    ```sh
    # sudo waagent -force -deprovision
    # export HISTSIZE=0
    # logout
    ```

1. 가상 머신을 종료하고 VMDK 파일을 VHD 형식으로 변환합니다.

    > [!NOTE]
    > VHD 형식이 잘못 지정되는 qemu-img 버전 >=2.2.1에 알려진 버그가 있습니다. 이 문제는 QEMU 2.6에서 해결되었습니다. qemu-img 2.2.0 이하 버전을 사용하거나 2.6 이상으로 업데이트하는 것이 좋습니다. 참조: <https://bugs.launchpad.net/qemu/+bug/1490611.>

    우선 이미지를 원시 형식으로 변환합니다.

    ```sh
    # qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw
    ```

    원시 이미지의 크기가 1MB로 정렬되는지 확인합니다. 그렇지 않은 경우 1MB에 맞게 크기를 반올림합니다.

    ```sh
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-7.4.raw $rounded_size
    ```

    원시 디스크를 고정 크기 VHD로 변환합니다.

    ```sh
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

    또는, qemu 버전 **2.6 +** 는 `force_size` 옵션을 포함합니다.

    ```sh
    # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="prepare-a-red-hat-based-virtual-machine-from-an-iso-by-using-a-kickstart-file-automatically"></a>자동으로 kickstart 파일을 사용하여 ISO에서 Red Hat 기반 가상 머신 준비

1. 다음 콘텐츠를 포함하는 kickstart 파일을 만들고 저장합니다. Kickstart 설치에 대한 자세한 내용은 [Kickstart 설치 가이드](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html)를 참조하세요.

    ```sh
    # Kickstart for provisioning a RHEL 7 Azure VM

    # System authorization information
    auth --enableshadow --passalgo=sha512

    # Use graphical install
    text

    # Do not run the Setup Agent on first boot
    firstboot --disable

    # Keyboard layouts
    keyboard --vckeymap=us --xlayouts='us'

    # System language
    lang en_US.UTF-8

    # Network information
    network  --bootproto=dhcp

    # Root password
    rootpw --plaintext "to_be_disabled"

    # System services
    services --enabled="sshd,waagent,NetworkManager"

    # System timezone
    timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

    # Partition clearing information
    clearpart --all --initlabel

    # Clear the MBR
    zerombr

    # Disk partitioning information
    part /boot --fstype="xfs" --size=500
    part / --fstyp="xfs" --size=1 --grow --asprimary

    # System bootloader configuration
    bootloader --location=mbr

    # Firewall configuration
    firewall --disabled

    # Enable SELinux
    selinux --enforcing

    # Don't configure X
    skipx

    # Power down the machine after install
    poweroff

    %packages
    @base
    @console-internet
    chrony
    sudo
    parted
    -dracut-config-rescue

    %end

    %post --log=/var/log/anaconda/post-install.log

    #!/bin/bash

    # Register Red Hat Subscription
    subscription-manager register --username=XXX --password=XXX --auto-attach --force

    # Install latest repo update
    yum update -y

    # Enable extras repo
    subscription-manager repos --enable=rhel-7-server-extras-rpms

    # Install WALinuxAgent
    yum install -y WALinuxAgent

    # Unregister Red Hat subscription
    subscription-manager unregister

    # Enable waaagent at boot-up
    systemctl enable waagent

    # Disable the root account
    usermod root -p '!!'

    # Configure swap in WALinuxAgent
    sed -i 's/^\(ResourceDisk\.EnableSwap\)=[Nn]$/\1=y/g' /etc/waagent.conf
    sed -i 's/^\(ResourceDisk\.SwapSizeMB\)=[0-9]*$/\1=2048/g' /etc/waagent.conf

    # Set the cmdline
    sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

    # Enable SSH keepalive
    sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

    # Build the grub cfg
    grub2-mkconfig -o /boot/grub2/grub.cfg

    # Configure network
    cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    EOF

    # Deprovision and prepare for Azure
    waagent -force -deprovision

    %end
    ```

1. 설치 시스템에서 액세스할 수 있는 위치에 kickstart 파일을 배치합니다.

1. Hyper-V 관리자에서 새 가상 머신을 만듭니다. **가상 하드 디스크 연결** 페이지에서 **나중에 가상 하드 디스크 연결**을 선택하고 새 Virtual Machine 마법사를 완료합니다.

1. 가상 머신 설정을 엽니다.

    a. 새 가상 하드 디스크를 가상 머신에 연결합니다. **VHD 형식** 및 **고정된 크기**를 선택하도록 합니다.

    나. 설치 ISO를 DVD 드라이브에 연결합니다.

    다. CD에서 부팅하도록 BIOS를 설정합니다.

1. 가상 머신을 시작합니다. 설치 가이드가 나타나면 **Tab** 키를 눌러서 부팅 옵션을 구성합니다.

1. 부팅 옵션 마지막에 `inst.ks=<the location of the kickstart file>` 을 입력하고 **Enter**키를 누릅니다.

1. 설치가 완료될 때까지 기다립니다. 완료 되 면 가상 머신은 자동으로 종료 됩니다. 이제 Linux VHD를 Azure에 업로드할 수 있습니다.

## <a name="known-issues"></a>알려진 문제

### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>Hyper-V 드라이버가 Hyper-V 하이퍼바이저가 아닌 프로그램을 사용하는 경우 초기 RAM 디스크에 포함될 수 없습니다.

경우에 따라 Linux 설치 관리자는 Hyper-V 환경에서 실행 중임을 감지하지 않는 한 초기 RAM 디스크(initrd 또는 initramfs)에 Hyper-V용 드라이버를 포함하지 않을 수 있습니다.

다른 가상화 시스템(예: Virtualbox, Xen 등)을 사용하여 Linux 이미지를 준비할 경우 초기 RAM 디스크에서 최소한 hv_vmbus 및 hv_storvsc 커널 모듈을 사용할 수 있도록 initrd를 다시 작성해야 할 수 있습니다. 이는 적어도 업스트림 Red Hat 배포를 기반으로 하는 시스템의 알려진 문제입니다.

이 문제를 해결하려면 Hyper-V 모듈을 initramfs에 추가하고 다시 빌드합니다.

`/etc/dracut.conf`를 편집하고 다음 내용을 추가합니다.

```sh
add_drivers+="hv_vmbus hv_netvsc hv_storvsc"
```

Initramfs를 다시 빌드합니다.

```sh
# dracut -f -v
```

자세한 내용은 [initramfs 다시 작성](https://access.redhat.com/solutions/1958)합니다.

## <a name="next-steps"></a>다음 단계

이제 Azure Stack에서 새 가상 컴퓨터를 만드는 Red Hat Enterprise Linux 가상 하드 디스크를 사용할 준비가 되었습니다. 처음으로 Azure Stack에 VHD 파일을 업로드 하는 경우 참조 [Marketplace 도구 키트를 사용 하 여 만들고 marketplace 항목 게시](azure-stack-marketplace-publisher.md)합니다.

Red Hat Enterprise Linux를 실행 하는 하이퍼바이저에 대 한 자세한 내용은 참조 하세요. [Red Hat 웹 사이트](https://access.redhat.com/certified-hypervisors)합니다.
