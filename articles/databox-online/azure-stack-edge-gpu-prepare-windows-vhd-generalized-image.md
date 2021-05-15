---
title: Azure Stack Edge Pro GPU 디바이스에 대한 일반화된 Windows VHD 이미지에서 VM 이미지 만들기
description: Windows VHD 또는 VHDX에서 시작하는 일반화된 이미지의 이미지를 VM으로 만드는 방법을 설명합니다. 이 일반화된 이미지를 사용하여 Azure Stack Edge Pro GPU 디바이스에 배포된 VM에 사용할 VM 이미지를 만듭니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/18/2021
ms.author: alkohli
ms.openlocfilehash: 978099accd57d15c750a27f77b2e220f569a2dd0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104962613"
---
# <a name="use-generalized-image-from-windows-vhd-to-create-a-vm-image-for-your-azure-stack-edge-pro-device"></a>Windows VHD에서 일반화된 이미지를 사용하여 Azure Stack Edge Pro 디바이스에 대한 VM 이미지 만들기

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack Edge Pro 디바이스에 VM을 배포하려면 VM을 만드는 데 사용할 수 있는 사용자 지정 VM 이미지를 만들 수 있어야 합니다. 이 문서에서는 일반화된 이미지를 만들기 위해 Windows VHD 또는 VHDX를 준비하는 데 필요한 단계를 설명합니다. 이 일반화된 이미지는 Azure Stack Edge Pro 디바이스에 대한 VM 이미지를 만드는 데 사용됩니다. 

## <a name="about-preparing-windows-vhd"></a>Windows VHD 준비 정보

Windows VHD 또는 VHDX를 사용하여 *일반화* 된 이미지 또는 *특수* 이미지를 만들 수 있습니다. 다음 표에는 *일반화* 된 이미지와 *특수* 이미지 간의 주요 차이점이 요약되어 있습니다.


|이미지 형식  |일반화  |특수화  |
|---------|---------|---------|
|대상     |모든 시스템에 배포         | 특정 시스템을 대상으로 함        |
|부팅 후 설정     | VM을 처음 부팅할 때 설정해야 합니다.          | 설정이 필요하지 않습니다. <br> 플랫폼에서 VM을 설정합니다.        |
|구성     |호스트 이름, 관리자-사용자 및 기타 VM별 설정이 필요합니다.         |완전히 미리 구성됩니다.         |
|사용 시기     |동일한 이미지에서 새 VM을 여러 개 만듭니다.         |특정 머신을 마이그레이션하거나 이전 백업에서 VM 복원 중입니다.         |


이 문서에서는 일반화된 이미지에서 배포하는 데 필요한 단계에 대해 설명합니다. 특수한 이미지에서 배포하려면 디바이스에 [특수 WINDOWS VHD 사용](azure-stack-edge-placeholder.md)을 참조하세요.

> [!IMPORTANT]
> 이 절차에서는 원본 VHD가 사용자 지정 구성 및 설정으로 구성된 경우를 다루지 않습니다. 예를 들어 사용자 지정 방화벽 규칙 또는 프록시 설정이 포함된 VHD를 일반화하려면 추가 작업이 필요할 수 있습니다. 이러한 추가 작업에 대한 자세한 내용은 [Azure에 업로드할 WINDOWS VHD 준비-Azure Virtual Machines](../virtual-machines/windows/prepare-for-upload-vhd-image.md)를 참조하세요.


## <a name="vm-image-workflow"></a>VM 이미지 워크플로

일반화된 이미지로 사용할 Windows VHD를 준비하는 개략적인 워크플로는 다음 단계를 수행합니다.

1. 원본 VHD 또는 VHDX를 고정 크기의 VHD로 변환합니다.
1. 고정 VHD를 사용하여 Hyper-V에서 VM을 만듭니다.
1. Hyper-V VM에 연결합니다.
1. *sysprep* 유틸리티를 사용하여 VHD를 일반화합니다. 
1. 일반화된 이미지를 Blob Storage에 복사합니다.
1. 일반화된 이미지를 사용하여 디바이스에서 VM을 배포합니다. 자세한 내용은 [Azure Portal를 통해 VM을 배포](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)하는 방법 또는 [PowerShell을 통해 VM을 배포](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)하는 방법을 참조하세요.


## <a name="prerequisites"></a>필수 구성 요소

Azure Stack Edge에서 일반화된 이미지로 사용할 Windows VHD를 준비하기 전에 다음을 확인합니다.

- 지원되는 버전의 Windows를 포함하는 VHD 또는 VHDX가 있습니다. Azure Stack Edge Pro에 대해 [지원되는 게스트 운영 체제]()를 참조하세요. 
- Hyper-V 관리자가 설치된 Windows 클라이언트에 액세스할 수 있습니다. 
- Azure Blob Storage 계정에 액세스하여 VHD를 준비한 후 저장할 수 있습니다.

## <a name="prepare-a-generalized-windows-image-from-vhd"></a>VHD에서 일반화된 Windows 이미지 준비

## <a name="convert-to-a-fixed-vhd"></a>고정 VHD로 변환 

디바이스의 경우 VM 이미지를 만들기 위한 고정 크기의 VHD가 필요합니다. 원본 Windows VHD 또는 VHDX를 고정 VHD로 변환해야 합니다. 다음 단계를 수행합니다.

1. 클라이언트 시스템에서 Hyper-V 관리자를 엽니다. **디스크 편집** 으로 이동.

    ![Hyper-V 관리자를 엽니다](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-1.png)

1. **시작하기 전에** 페이지에서 **다음>** 을 선택합니다.

1. **가상 하드 디스크 찾기** 페이지에서 변환할 원본 Windows VHD 또는 VHDX의 위치로 이동합니다. **다음>** 을 선택합니다.

    ![가상 하드 디스크 찾기 페이지](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-2.png)

1. **작업 선택** 페이지에서 **변환** 을 선택하고 **다음>** 을 선택합니다.

    ![작업 페이지 선택](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-3.png)

1. **디스크 형식 선택** 페이지에서 **VHD** 형식을 선택하고 **다음>** 을 선택합니다.

   ![디스크 형식 선택 페이지](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-4.png)


1. **디스크 유형 선택** 페이지에서 **고정 크기** 를 선택하고 **다음>** 를 선택합니다.

   ![디스크 유형 선택 페이지](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-5.png)


1. **디스크 구성** 페이지에서 위치로 이동하고 고정 크기의 VHD 디스크 이름을 지정합니다. **다음>** 을 선택합니다.

   ![디스크 구성 페이지](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-6.png)


1. 요약을 검토하고 **마침** 을 선택합니다. VHD 또는 VHDX 변환에는 몇 분이 걸립니다. 변환 시간은 원본 디스크의 크기에 따라 달라집니다. 

<!--
1. Run PowerShell on your Windows client.
1. Run the following command:

    ```powershell
    Convert-VHD -Path <source VHD path> -DestinationPath <destination-path.vhd> -VHDType Fixed 
    ```
-->
이 문서의 모든 후속 단계에 이 고정 VHD를 사용합니다.


## <a name="create-a-hyper-v-vm-from-fixed-vhd"></a>고정 VHD에서 Hyper-V VM 만들기

1. 범위 창에서 **Hyper-V 관리자** 의 시스템 노드를 마우스 오른쪽 단추로 클릭하여 상황에 맞는 메뉴를 연 다음 **신규** > **가상 머신** 을 선택합니다.

    ![범위 창에서 새 가상 머신 선택](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-1.png)

1. 새 가상 머신 마법사의 **시작하기 전에** 페이지에서 **다음** 을 선택합니다.

1. **이름 및 위치 지정** 페이지에서 가상 머신의 **이름** 및 **위치** 를 입력합니다. **다음** 을 선택합니다.

    ![VM의 이름 및 위치 지정](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-2.png)

1. **세대 지정** 페이지에서 .vhd 디바이스 이미지 유형으로 **세대 1** 를 선택하고 **다음** 을 선택합니다.    

    ![세대 지정](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-3.png)

1. 원하는 메모리와 네트워킹 구성을 할당합니다.

1. **가상 하드 디스크 연결** 페이지에서 **기존 가상 하드 디스크 사용** 을 선택하고 가상 이전에 만든 Windows 고정 VHD의 위치를 지정한 후 **다음** 을 선택합니다.

    ![가상 하드 디스크 연결 페이지](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-4.png)

1. **요약** 을 검토하고 **마침** 을 선택하여 가상 머신을 만듭니다.

가상 머신을 만드는 데 몇 분 정도 걸립니다.
     

## <a name="connect-to-the-hyper-v-vm"></a>Hyper-V VM에 연결합니다

VM은 클라이언트 시스템의 가상 머신 목록에 표시됩니다. 


1. VM을 선택하고 마우스 오른쪽 단추를 클릭한 다음 **시작** 을 선택합니다. 

    ![VM을 선택하고 시작합니다](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/connect-virtual-machine-2.png)

2. VM이 **실행 중** 으로 표시되는 것을 보여줍니다. VM을 선택하고 마우스 오른쪽 단추를 클릭한 다음 **연결** 을 선택합니다.

    ![VM에 연결](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/connect-virtual-machine-4.png)

VM에 연결한 후 컴퓨터 설치 마법사를 완료하고 VM에 로그인합니다.


## <a name="generalize-the-vhd"></a>VHD 일반화  

*Sysprep* 유틸리티를 사용하여 VHD를 일반화합니다. 

1. VM 내에서 명령 프롬프트를 엽니다.
1. 다음 명령을 실행하여 VHD를 일반화합니다. 

    ```
    c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm
    ```
    자세한 내용은 [Sysprep(시스템 준비) 개요](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)를 참조하세요.
1.  명령이 완료되면 VM이 종료됩니다. **VM을 다시 시작하지 마세요**.

## <a name="upload-the-vhd-to-azure-blob-storage"></a>Azure Blob Storage에 VHD를 업로드합니다

이제 VHD를 사용하여 Azure Stack Edge에서 일반화된 이미지를 만들 수 있습니다. 

1. Azure Blob Storage에 VHD를 업로드합니다. [Azure Storage Explorer를 사용하여 VHD 업로드](../devtest-labs/devtest-lab-upload-vhd-using-storage-explorer.md)의 자세한 지침을 참조하세요.
1. 업로드가 완료되면 업로드된 이미지를 사용하여 VM 이미지 및 VM을 만들 수 있습니다. 

<!-- this should be added to deploy VM articles - If you experience any issues creating VMs from your new image, you can use VM console access to help troubleshoot. For information on console access, see [link].-->



## <a name="next-steps"></a>다음 단계

배포의 특성에 따라 다음 절차 중 하나를 선택할 수 있습니다.

- [Azure Portal를 통해 일반화된 이미지에서 VM 배포](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
- [Azure PowerShell를 통해 일반화된 이미지에서 VM 배포](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)  
