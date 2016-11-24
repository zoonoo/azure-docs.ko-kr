---
title: "Azure Portal을 사용하여 Linux VM 만들기 | Microsoft Docs"
description: "Azure 포털을 사용하여 Linux VM을 만듭니다."
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: cc5dc395-dc54-4402-8804-2bb15aba8ea2
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: hero-article
ms.date: 10/28/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: 3ad64861bc4c3b0a938c75990fc516ef634943ef


---
# <a name="create-a-linux-vm-on-azure-using-the-portal"></a>포털을 사용하여 Azure에서 Linux VM 만들기
이 문서에서는 [Azure 포털](https://portal.azure.com/)을 사용하여 Linux 가상 컴퓨터를 만드는 방법을 보여 줍니다.

요구 사항은 다음과 같습니다.

* [Azure 계정](https://azure.microsoft.com/pricing/free-trial/)
* [SSH 공용 및 개인 키 파일](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="sign-in"></a>로그인
Azure 계정 ID를 사용하여 Azure 포털에 로그인하고 왼쪽 위에서 **+ 새로 만들기** 를 클릭합니다.

![screen1](../media/virtual-machines-linux-quick-create-portal/screen1.png)

## <a name="choose-vm"></a>VM 선택
**마켓플레이스**에서 **Virtual Machines**를 클릭한 다음 **추천 앱** 이미지 목록에서 **Ubuntu Server 14.04 LTS**를 클릭합니다.  맨 아래에서 배포 모델이 `Resource Manager` 인지 확인하고 **만들기**를 클릭합니다.

![screen2](../media/virtual-machines-linux-quick-create-portal/screen2.png)

## <a name="enter-vm-options"></a>VM 옵션 입력
**기본** 페이지에서 다음을 입력합니다.

* VM의 이름
* 관리 사용자의 사용자 이름
* 인증 유형이 **SSH 공개 키**
* 문자열로 SSH 공개 키( `~/.ssh/` 디렉터리에서)
* 새 리소스 그룹 이름 또는 기존 항목 선택

계속하려면 **확인**을 클릭하고 VM 크기를 선택합니다. 다음 스크린샷과 같이 표시됩니다.

![screen3](../media/virtual-machines-linux-quick-create-portal/screen3.png)

## <a name="choose-vm-size"></a>VM 크기 선택
Premium SSD에 Ubuntu를 설치하는 **DS1** 크기를 선택하고 **선택**을 클릭하여 설정을 구성합니다.

![screen4](../media/virtual-machines-linux-quick-create-portal/screen4.png)

## <a name="storage-and-network"></a>저장소 및 네트워크
**설정**에서 저장소와 네트워크에 대한 기본값을 그대로 두고 **확인**을 클릭하여 요약을 확인합니다.  DS1을 선택하여 디스크 유형을 프리미엄 SSD로 설정하면 **S** 는 SSD를 나타냅니다.

![screen5](../media/virtual-machines-linux-quick-create-portal/screen5.png)

## <a name="confirm-vm-settings-and-launch"></a>VM 설정 확인 및 시작
새로운 Ubuntu VM에 대한 설정을 확인하고 **확인**을 클릭합니다.

![screen6](../media/virtual-machines-linux-quick-create-portal/screen6.png)

## <a name="find-the-vm-nic"></a>VM NIC 찾기
포털 대시보드를 열고 **네트워크 인터페이스** 에서 NIC를 선택합니다.

![screen7](../media/virtual-machines-linux-quick-create-portal/screen7.png)

## <a name="find-the-public-ip"></a>공용 IP 찾기
NIC 설정 아래에서 공용 IP 주소 메뉴를 엽니다.

![screen8](../media/virtual-machines-linux-quick-create-portal/screen8.png)

## <a name="ssh-to-the-vm"></a>VM에 SSH
SSH 공개 키를 사용하여 공용 IP로 SSH합니다.  Mac 또는 Linux 워크스테이션에서 터미널에서 직접 SSH할 수 있습니다. Windows 워크스테이션에 있으면 PuTTY, MobaXTerm 또는 Cygwin을 사용하여 Linux에 SSH해야 합니다.  아직 없는 경우 Linux에 SSH하도록 Windows 워크스테이션을 준비하는 문서는 다음과 같습니다.

[Azure에서 Windows를 통해 SSH 키를 사용하는 방법](virtual-machines-linux-ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```
ssh -i ~/.ssh/azure_id_rsa ubuntu@13.91.99.206
```

## <a name="next-steps"></a>다음 단계
이제 Linux VM을 신속하게 만들었으므로 테스트 또는 데모를 위해 사용합니다. 이러한 문서 중 하나를 수행하여 인프라에 대해 사용자 지정된 Linux VM을 만들 수 있습니다.

* [템플릿을 사용하여 Azure에서 Linux VM 만들기](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [템플릿을 사용하여 Azure에서 SSH 보안 Linux VM 만들기](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure CLI를 사용하여 Linux VM 만들기](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)




<!--HONumber=Nov16_HO2-->


