---
title: "Azure에서 Debian Linux VHD 준비 | Microsoft Docs"
description: "Azure에서 배포할 Debian 7 및 8 VHD 파일을 만드는 방법에 대해 알아봅니다."
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: a6de7a7c-cc70-44e7-aed0-2ae6884d401a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: szark
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 93d30941af689e19e48c9ee204867c96348ecc5f
ms.lasthandoff: 03/27/2017


---
# <a name="prepare-a-debian-vhd-for-azure"></a>Azure용 Debian VHD 준비
## <a name="prerequisites"></a>필수 조건
이 섹션에서는 [Debian 웹 사이트](https://www.debian.org/distrib/) 에서 다운로드한 .iso 파일에서 Debian Linux 운영 체제를 가상 하드 디스크에 설치했다고 가정합니다. .vhd 파일을 만드는 여러 도구가 있으며 Hyper-V가 한 가지 예입니다. Hyper-V 사용에 대한 자세한 내용은 [Hyper-V 역할 설치 및 가상 컴퓨터 구성](https://technet.microsoft.com/library/hh846766.aspx)을 참조하세요.

## <a name="installation-notes"></a>설치 참고 사항
* Azure용 Linux를 준비하는 방법에 대한 추가 팁은 [일반 Linux 설치 참고 사항](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) 을 참조하세요.
* 새 VHDX 형식은 Azure에서 지원되지 않습니다. Hyper-V 관리자 또는 **convert-vhd** cmdlet을 사용하여 디스크를 VHD 형식으로 변환할 수 있습니다.
* Linux 시스템 설치 시에는 LVM(설치 기본값인 경우가 많음)이 아닌 표준 파티션을 사용하는 것이 좋습니다. 이렇게 하면 특히 문제 해결을 위해 OS 디스크를 다른 VM에 연결해야 하는 경우 복제된 VM과 LVM 이름이 충돌하지 않도록 방지합니다. 원하는 경우에는 데이터 디스크에서 [LVM](virtual-machines-linux-configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 또는 [RAID](virtual-machines-linux-configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 사용할 수 있습니다.
* OS 디스크에 스왑 파티션을 구성하지 마세요. 임시 리소스 디스크에서 스왑 파일을 만들도록 Azure Linux 에이전트를 구성할 수 있습니다. 여기에 대한 자세한 내용은 아래 단계에서 확인할 수 있습니다.
* 모든 VHD 크기는 1MB의 배수여야 합니다.

## <a name="use-azure-manage-to-create-debian-vhds"></a>Azure-Manage를 사용하여 Debian VHD를 만듭니다.
[credativ](http://www.credativ.com/)의 [azure-manage](https://github.com/credativ/azure-manage) 스크립트처럼 Azure용 Debian VHD를 생성하는 데 사용할 수 있는 도구가 있습니다. 권장되는 방법과 이미지를 처음부터 새로 만드는 방법을 비교한 것입니다. 예를 들어 Debian 8 VHD를 만들기 위해 다음 명령을 실행하여 azure-manage(및 종속성)를 다운로드하고 azure_build_image 스크립트를 실행합니다.

    # sudo apt-get update
    # sudo apt-get install git qemu-utils mbr kpartx debootstrap

    # sudo apt-get install python3-pip python3-dateutil python3-cryptography
    # sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
    # git clone https://github.com/credativ/azure-manage.git
    # cd azure-manage
    # sudo pip3 install .

    # sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section


## <a name="manually-prepare-a-debian-vhd"></a>Debian VHD 수동 준비
1. Hyper-V 관리자에서 가상 컴퓨터를 선택합니다.
2. **연결** 을 클릭하여 가상 컴퓨터의 콘솔 창을 엽니다.
3. ISO 파일에 대해 VM을 설정한 경우 `/etc/apt/source.list`에서 **deb cdrom**에 대한 줄을 주석으로 처리합니다.
4. 다음과 같이 `/etc/default/grub` 파일을 편집하고 **GRUB_CMDLINE_LINUX** 매개 변수를 수정하여 Azure에 대한 추가 커널 매개 변수를 포함시킵니다.
   
        GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200 earlyprintk=ttyS0,115200 rootdelay=30"
5. grub을 다시 빌드하고 다음을 실행합니다.
   
        # sudo update-grub
6. Debian의 Azure 리포지토리를 Debian 7 또는 8에 대한 /etc/apt/sources.list에 추가합니다.
   
    **Debian 7.x "Wheezy"**
   
        deb http://debian-archive.trafficmanager.net/debian wheezy-backports main
        deb-src http://debian-archive.trafficmanager.net/debian wheezy-backports main
        deb http://debian-archive.trafficmanager.net/debian-azure wheezy main
        deb-src http://debian-archive.trafficmanager.net/debian-azure wheezy main

    **Debian 8.x "Jessie"**

        deb http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb http://debian-archive.trafficmanager.net/debian-azure jessie main
        deb-src http://debian-archive.trafficmanager.net/debian-azure jessie main


1. Azure Linux 에이전트를 설치합니다.
   
        # sudo apt-get update
        # sudo apt-get install waagent
2. Debian 7은 wheezy-backports 리포지토리에서 3.16 기반 커널을 실행하는 데 필요합니다. 먼저 다음과 같은 내용으로 /etc/apt/preferences.d/linux.pref라는 파일을 만듭니다.
   
        Package: linux-image-amd64 initramfs-tools
        Pin: release n=wheezy-backports
        Pin-Priority: 500
   
    그런 다음 "sudo apt-get install linux-image-amd64"를 실행하여 새 커널을 만듭니다.
3. 가상 컴퓨터의 프로비전을 해제하고 Azure에서 프로비전할 가상 컴퓨터를 준비하고 다음 명령을 실행합니다.
   
        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout
4. Hyper-V 관리자에서 **작업** -> 종료를 클릭합니다. 이제 Linux VHD를 Azure에 업로드할 수 있습니다.

## <a name="next-steps"></a>다음 단계
이제 Debian 가상 하드 디스크를 사용하여 Azure에서 새 가상 컴퓨터를 만들 준비가 되었습니다. .vhd 파일을 Azure에 처음으로 업로드하는 경우 [Linux 운영 체제를 포함하는 가상 하드 디스크 만들기 및 업로드](linux/classic/create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)에서 2단계 및 3단계를 참조하세요.


