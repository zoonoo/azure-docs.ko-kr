---
title: Azure Stack Edge Pro GPU 디바이스용 VM 이미지 만들기
description: Azure Stack Edge Pro GPU 디바이스에서 사용할 Linux 또는 Windows VM 이미지를 만드는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 09/04/2020
ms.author: alkohli
ms.openlocfilehash: 745d0df07b6d0d01acf0d564df8c242e16f3f56d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90890996"
---
# <a name="create-custom-vm-images-for-your-azure-stack-edge-pro-device"></a>Azure Stack Edge Pro 디바이스용 사용자 지정 VM 이미지 만들기

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Azure Stack Edge Pro 디바이스에 VM을 배포하려면 VM을 만드는 데 사용할 수 있는 사용자 지정 VM 이미지를 만들 수 있어야 합니다. 이 문서에서는 Azure Stack Edge Pro 디바이스에 VM을 배포하는 데 사용할 수 있는 Linux 또는 Windows VM 사용자 지정 이미지를 만드는 데 필요한 단계를 설명합니다.

## <a name="vm-image-workflow"></a>VM 이미지 워크플로

이 워크플로에서는 Azure에서 가상 머신을 만들고, VM을 사용자 지정하고 일반화한 다음, 이 VM에 해당하는 VHD를 다운로드해야 합니다. Azure Stack Edge Pro에 일반화된 VHD가 업로드되고, 이 VHD에서 관리 디스크가 생성되고, 관리 디스크에서 이미지가 생성되고, 마지막으로 이 이미지에서 VM이 생성됩니다.   

자세한 내용은 [Azure PowerShell을 사용하여 Azure Stack Edge Pro 디바이스에 VM 배포](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md)를 참조하세요.


## <a name="create-a-windows-custom-vm-image"></a>Windows 사용자 지정 VM 이미지 만들기

다음 단계를 수행하여 Windows VM 이미지를 만듭니다.

1. Windows Virtual Machine을 만듭니다. 자세한 내용은 [자습서: Azure PowerShell을 사용하여 Windows VM 만들기 및 관리](../virtual-machines/windows/tutorial-manage-vm.md)를 참조하세요.

2. 기존 OS 디스크를 다운로드합니다.

    - [VHD 다운로드](../virtual-machines/windows/download-vhd.md)의 단계를 수행합니다.

    - 이전 절차에 설명된 내용 대신 `sysprep` 명령을 사용합니다.
    
        `c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm`
   
       [Sysprep(시스템 준비) 개요](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)를 참조할 수도 있습니다.

이제 이 VHD를 사용하여 Azure Stack Edge Pro 디바이스에서 VM을 만들고 배포합니다.

## <a name="create-a-linux-custom-vm-image"></a>Linux 사용자 지정 VM 이미지 만들기

다음 단계를 수행하여 Linux VM 이미지를 만듭니다.

1. Linux Virtual Machine 만들기. 자세한 내용은 [자습서: Azure CLI로 Linux VM 만들기 및 관리](../virtual-machines/linux/tutorial-manage-vm.md)를 참조하세요.

1. VM의 프로비전을 해제합니다. Azure VM 에이전트를 사용하여 머신별 파일 및 데이터를 삭제합니다. 원본 Linux VM에서 `-deprovision+user` 매개 변수와 함께 `waagent` 명령을 사용합니다. 자세한 내용은 [Azure Linux 에이전트 이해 및 사용](../virtual-machines/extensions/agent-linux.md)을 참조하세요.

    1. SSH 클라이언트를 사용하여 Linux VM에 연결합니다.
    2. SSH 창에서 다음 명령을 입력합니다.
       
        ```bash
        sudo waagent -deprovision+user
        ```
       > [!NOTE]
       > 이미지로 캡처하려는 VM에서만 이 명령을 실행합니다. 이 명령이 이미지에서 중요한 정보가 모두 지워졌다거나 재배포에 적합하다는 것을 보장하지는 않습니다. 또한 `+user` 매개 변수는 마지막 프로비전된 사용자 계정을 제거합니다. VM에 사용자 계정 자격 증명을 유지하려면 `-deprovision`만 사용합니다.
     
    3. 계속하려면 **y**를 입력합니다. `-force` 매개 변수를 추가하여 이 확인 단계를 피할 수 있습니다.
    4. 명령이 완료되면 **종료**를 입력하여 SSH 클라이언트를 닫습니다.  이 시점에서 VM은 계속 실행되고 있습니다.


1. [기존 OS 디스크를 다운로드합니다](../virtual-machines/linux/download-vhd.md).

이제 이 VHD를 사용하여 Azure Stack Edge Pro 디바이스에서 VM을 만들고 배포합니다. 다음과 같은 두 가지 Azure Marketplace 이미지를 사용하여 Linux 사용자 지정 이미지를 만들 수 있습니다.

|항목 이름  |설명  |게시자  |
|---------|---------|---------|
|[Ubuntu Server](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.ubuntuserver) |Ubuntu Server는 클라우드 환경에 세계적으로 가장 많이 사용되는 Linux입니다.|Canonical|
|[Debian 8 "Jessie"](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.debian) |Debian GNU/Linux는 가장 많이 사용되는 Linux 배포 중 하나입니다.     |credativ|

사용 가능한 Azure Marketplace 이미지의 전체 목록은(현재 테스트되지 않음) [Azure Stack Hub에 사용할 수 있는 Azure Marketplace 항목](https://docs.microsoft.com/azure-stack/operator/azure-stack-marketplace-azure-items?view=azs-1910)을 참조하세요.


## <a name="next-steps"></a>다음 단계

[Azure Stack Edge Pro 디바이스에 VM을 배포](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md)합니다.
