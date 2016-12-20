---
title: "Azure의 Linux 소개 | Microsoft Docs"
description: "Azure에서 Linux 가상 컴퓨터를 사용하는 방법에 대해 알아봅니다."
services: virtual-machines-linux
documentationcenter: python
author: szarkos
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: b13bf305-87bf-4df3-815e-e8f6337aa6ea
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: szark
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: dc61f74a10fed1ba9e3959326e0020cf2b4440ea


---
# <a name="introduction-to-linux-on-azure"></a>Azure의 Linux 소개
이 항목에서는 Azure 클라우드에서 Linux 가상 컴퓨터를 사용하는 몇 가지 측면을 간략하게 설명합니다. 갤러리의 기존 이미지를 사용하여 Linux 가상 컴퓨터 배포는 간단한 프로세스입니다.

## <a name="authentication-usernames-passwords-and-ssh-keys"></a>인증: 사용자 이름, 암호 및 SSH 키
Azure 클래식 포털을 사용하여 Linux 가상 컴퓨터를 만들 때, 사용자 이름, 암호 또는 SSH 공개 키가 요구됩니다. Azure에 Linux 가상 컴퓨터를 배포할 때 선택하는 사용자 이름에는 다음과 같은 제약이 있습니다. 가상 컴퓨터에 이미 존재하던 시스템 계정의 이름(UID <100)(예: 'root')은 허용되지 않습니다.

* [Linux를 실행하는 가상 컴퓨터 만들기](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.
* [Azure에서 Linux와 함께 SSH를 사용하는 방법](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요.

## <a name="obtaining-superuser-privileges-using-sudo"></a> `sudo`
Azure에서 가상 컴퓨터 인스턴스를 배포하는 동안 지정한 사용자 계정이 권한 있는 계정입니다. Azure Linux 에이전트에서 `sudo` 유틸리티를 사용하여 루트(superuser 계정)로 권한을 상승하도록 이 계정을 구성할 수 있습니다. 이 사용자 계정을 사용하여 로그인한 후 다음 명령 구문을 사용하여 루트로 명령을 실행할 수 있습니다.

    # sudo <COMMAND>

선택적으로 **sudo -s**를 사용하여 루트 셸을 얻을 수 있습니다.

* [Azure의 Linux 가상 컴퓨터에서 루트 권한 사용](virtual-machines-linux-use-root-privileges.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요.

## <a name="firewall-configuration"></a>방화벽 구성
Azure는 Azure 클래식에서 지정된 포트에 연결을 제한하는 인바운드 패킷 필터를 제공합니다. 기본적으로 허용되는 유일한 포트는 SSH입니다. Azure 클래식 포털에서 끝점을 구성하여 Linux 가상 컴퓨터의 추가 포트 액세스를 열 수 있습니다.

* [가상 컴퓨터에 끝점을 설정하는 방법](virtual-machines-windows-classic-setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)을 참조하세요.

Azure 갤러리의 Linux 이미지는 기본적으로 *iptables* 방화벽을 사용하도록 설정하지 않습니다. 원하는 경우 추가 필터링을 제공하도록 이 방화벽을 구성할 수 있습니다.

## <a name="hostname-changes"></a>호스트 이름 변경
Linux 이미지의 인스턴스를 처음 배포할 때 가상 컴퓨터의 호스트 이름을 지정해야 합니다. 가상 컴퓨터를 실행하면 이 호스트 이름이 플랫폼 DNS 서버에 게시되므로 서로 연결된 여러 가상 컴퓨터에서 호스트 이름을 사용하여 IP 주소를 조회할 수 있습니다.

가상 컴퓨터를 배포한 후 호스트 이름을 변경해야 하는 경우 다음 명령을 사용하세요.

    # sudo hostname <newname>

Azure Linux 에이전트에는 이 이름 변경을 자동으로 검색하여 이 변경 내용을 기억하도록 가상 컴퓨터를 적절히 구성하고 플랫폼 DNS 서버에 이 변경 내용을 게시하는 기능이 포함되어 있습니다.

* [Azure Linux 에이전트 사용자 가이드](virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="cloud-init"></a>Cloud-Init
**Ubuntu** 및 **CoreOS** 이미지는 cloud-init pn Azure를 활용하여 가상 컴퓨터를 부트스트랩하기 위한 추가 기능을 제공합니다.

* [사용자 지정 데이터를 삽입하는 방법](virtual-machines-windows-classic-inject-custom-data.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Microsoft Azure의 사용자 지정 데이터 및 Cloud-Init](https://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/)
* [Init 클라우드를 사용하여 Azure 스왑 파티션 만들기](https://wiki.ubuntu.com/AzureSwapPartitions)
* [Azure에서 CoreOS를 사용하는 방법](https://coreos.com/os/docs/latest/booting-on-azure.html)

## <a name="virtual-machine-image-capture"></a>가상 컴퓨터 이미지 캡처
Azure는 기존 가상 컴퓨터의 상태를 이미지로 캡처하는 기능을 제공합니다. 이 이미지는 이후에 추가 가상 컴퓨터 인스턴스를 배포하는 데 사용할 수 있습니다. Azure Linux 에이전트는 프로비전 프로세스 중 수행된 일부 사용자 지정을 롤백하는 데 사용할 수 있습니다. 아래 단계를 따르면 가상 컴퓨터를 이미지로 캡처할 수 있습니다.

1. **waagent -deprovision** 을 실행하여 사용자 지정 프로비전을 실행 취소합니다. 또는 선택적으로 프로비전 중 지정한 사용자 계정 및 연결된 모든 데이터를 삭제하려면 **waagent -deprovision+user** 를 실행합니다.
2. 가상 컴퓨터를 종료합니다.
3. Azure 클래식 포털에서 *캡쳐* 를 클릭하거나 PowerShell 또는 CLI 도구를 사용하여 가상 컴퓨터를 이미지로 캡쳐할 수 있습니다.
   
   * 참고: [Linux 가상 컴퓨터를 캡처하여 템플릿으로 사용하는 방법](virtual-machines-linux-classic-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

## <a name="attaching-disks"></a>디스크 연결
각 가상 컴퓨터에는 임시 로컬 *리소스 디스크* 가 연결되어 있습니다. 리소스 디스크의 데이터는 다시 부팅 후 지속되지 않을 수도 있으므로 가상 컴퓨터에서 실행되는 응용 프로그램 및 프로세스에서 **임시** 데이터 저장소에 사용되는 경우가 많습니다. 또한 운영 체제의 페이지 또는 스왑 파일을 저장하는 데 사용됩니다.

Linux에서 리소스 디스크는 일반적으로 Azure Linux 에이전트에 의해 관리되며 **/mnt/resource**(또는 Ubuntu 이미지의 **/mnt**)에 자동으로 탑재됩니다.

> [!NOTE]
> 리소스 디스크는 **임시** 디스크이며 VM의 프로비전을 해제할 때 비워질 수 있습니다.
> 
> 

Linux에서 데이터 디스크 이름은 커널에서 `/dev/sdc`로 지정될 수 있으며 사용자는 해당 리소스를 파티셔닝, 형식 지정 및 마운트해야 합니다. [데이터 디스크를 가상 컴퓨터에 연결하는 방법](virtual-machines-linux-classic-attach-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)에 대한 자습서의 단계를 다루었습니다.

* **참고 항목:** [Linux에서 소프트웨어 RAID 구성](virtual-machines-linux-configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) & [Azure에서 Linux VM에 대해 LVM 구성](virtual-machines-linux-configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)




<!--HONumber=Nov16_HO3-->


