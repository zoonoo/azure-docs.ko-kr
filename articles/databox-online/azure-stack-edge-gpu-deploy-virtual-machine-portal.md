---
title: Azure Portal를 통해 Azure Stack Edge Pro에서 Vm을 배포 하는 방법
description: Azure Portal를 통해 Azure Stack Edge Pro에서 Vm을 만들고 관리 하는 방법을 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro device so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 6054e7e79acaa6abf304508221c63143b9d14a45
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102436535"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-the-azure-portal"></a>Azure Portal를 통해 Azure Stack Edge Pro GPU 장치에 Vm 배포

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Portal, 템플릿, Azure PowerShell cmdlet을 사용 하 고 Azure CLI/Python 스크립트를 통해 Azure Stack Edge 장치에서 Vm (가상 컴퓨터)을 만들고 관리할 수 있습니다. 이 문서에서는 Azure Portal를 사용 하 여 Azure Stack Edge 장치에서 VM을 만들고 관리 하는 방법을 설명 합니다. 

이 문서는 Azure Stack Edge Pro GPU, Azure Stack Edge Pro R 및 Azure Stack Edge 미니 R 장치에 적용 됩니다. 

> [!IMPORTANT] 
> 클라우드에서 장치에 배포 된 Vm을 관리 하는 사용자에 대해 다단계 인증을 사용 하도록 설정 하는 것이 좋습니다.
        
## <a name="vm-deployment-workflow"></a>VM 배포 워크플로

배포 워크플로에 대한 개략적인 요약은 다음과 같습니다.

1. Azure Stack Edge 장치에서 계산에 대 한 네트워크 인터페이스를 사용 하도록 설정 합니다. 그러면 지정 된 네트워크 인터페이스에 가상 스위치가 생성 됩니다.
1. Azure Portal에서 가상 컴퓨터의 클라우드 관리를 사용 하도록 설정 합니다.
1. Storage 탐색기를 사용 하 여 Azure Storage 계정에 VHD를 업로드 합니다. 
1. 업로드 된 VHD를 사용 하 여 VHD를 장치에 다운로드 하 고 VHD에서 VM 이미지를 만듭니다. 
1. 이전 단계에서 만든 리소스를 사용 합니다.
    1. 만든 VM 이미지입니다.
    1. Compute를 사용 하도록 설정한 네트워크 인터페이스와 연결 된 VSwitch입니다.
    1. VSwitch와 연결 된 서브넷입니다.

    그리고 다음 리소스를 인라인으로 생성 하거나 지정 합니다.
    1. Vm 이름, VM에 대해 지원 되는 VM 크기, 로그인 자격 증명을 선택 합니다. 
    1. 새 데이터 디스크를 만들거나 기존 데이터 디스크를 연결 합니다.
    1. VM에 대 한 정적 또는 동적 IP를 구성 합니다. 고정 IP를 제공 하는 경우 compute에 대해 사용 하도록 설정 된 네트워크 인터페이스의 서브넷 범위에서 사용 가능한 IP를 선택 합니다.

    위의 리소스를 사용 하 여 가상 컴퓨터를 만듭니다.


## <a name="prerequisites"></a>필수 구성 요소

Azure Portal를 통해 장치에서 Vm 만들기 및 관리를 시작 하기 전에 다음을 확인 합니다.

1. [1 단계: Azure Stack Edge pro 장치 구성](azure-stack-edge-j-series-connect-resource-manager.md#step-1-configure-azure-stack-edge-pro-device)에 설명 된 대로 Azure Stack edge pro 장치에서 네트워크 설정을 완료 했습니다.

    1. 계산을 위한 네트워크 인터페이스를 사용 하도록 설정 했습니다. 이 네트워크 인터페이스 IP는 VM 배포를 위한 가상 스위치를 만드는 데 사용됩니다. 장치의 로컬 UI에서 **Compute** 로 이동 합니다. 가상 스위치를 만드는 데 사용할 네트워크 인터페이스를 선택합니다.

        > [!IMPORTANT] 
        > 컴퓨팅용 포트는 하나만 구성할 수 있습니다.

    1. 네트워크 인터페이스에서 컴퓨팅을 사용하도록 설정합니다. 이 네트워크 인터페이스에 해당하는 가상 스위치를 Azure Stack Edge Pro가 만들고 관리합니다.

1. 만들려는 가상 컴퓨터의 VM 이미지를 만드는 데 사용 하는 Windows 또는 Linux VHD에 액세스할 수 있습니다.

## <a name="deploy-a-vm"></a>VM 배포

Azure Stack Edge 장치에서 가상 머신을 만들려면 다음 단계를 따르세요.

### <a name="add-a-vm-image"></a>VM 이미지 추가

1. Azure Storage 계정에 VHD를 업로드 합니다. [Azure Storage 탐색기를 사용 하 여 VHD 업로드](../devtest-labs/devtest-lab-upload-vhd-using-storage-explorer.md)의 단계를 따릅니다.

1. Azure Portal에서 Azure Stack Edge 장치에 대 한 Azure Stack Edge 리소스로 이동 합니다. **Edge compute > Virtual Machines** 로 이동 합니다.

    ![VM 이미지 1 추가](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-1.png)

1. **Virtual Machines** 를 선택 하 여 **개요** 페이지로 이동 합니다. 가상 컴퓨터 클라우드 관리를 **사용 하도록 설정** 합니다.
    ![VM 이미지 2 추가](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-2.png)

1. 첫 번째 단계는 VM 이미지를 추가 하는 것입니다. 이전 단계에서 저장소 계정에 이미 VHD를 업로드 했습니다. 이 VHD를 사용 하 여 VM 이미지를 만듭니다.

    저장소 계정에서 VHD를 다운로드 하 고 장치에 추가 하려면 **이미지 추가** 를 선택 합니다. 다운로드 프로세스는 VHD 크기와 다운로드에 사용할 수 있는 인터넷 대역폭에 따라 몇 분이 걸립니다. 

    ![VM 이미지 3 추가](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-3.png)

1. **이미지 추가** 블레이드에서 다음 매개 변수를 입력 합니다. **추가** 를 선택합니다.


    |매개 변수  |설명  |
    |---------|---------|
    |저장소 blob에서 다운로드    |VHD를 업로드 한 저장소 계정에서 저장소 blob의 위치로 이동 합니다.         |
    |다운로드 대상    | 가상 컴퓨터를 배포 하는 현재 장치에 자동으로 설정 됩니다.        |
    |다른 이름으로 이미지 저장      | 저장소 계정에 업로드 한 VHD에서 만든 VM 이미지의 이름입니다.        |
    |OS 유형     |VM 이미지를 만드는 데 사용 하는 VHD의 운영 체제로 Windows 또는 Linux에서 선택 합니다.         |
   

    ![VM 이미지 4 추가](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-6.png)

1. VHD를 다운로드 하 고 VM 이미지를 만듭니다. 이미지 만들기를 완료 하는 데 몇 분 정도 걸립니다. VM 이미지가 성공적으로 완료 되었다는 알림이 표시 됩니다.

    ![VM 이미지 5 추가](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-8.png)


1. VM 이미지가 성공적으로 만들어지면 **이미지** 블레이드의 이미지 목록에 추가 됩니다.
    ![VM 이미지 6 추가](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-9.png)

    배포 **블레이드가 업데이트 되어 배포** 상태를 표시 합니다.

    ![VM 이미지 7 추가](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-10.png)

    새로 추가 된 이미지는 **개요** 페이지에도 표시 됩니다.
    ![VM 이미지 8 추가](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-11.png)


### <a name="add-a-vm"></a>VM 추가

VM 이미지를 만든 후에 VM을 만들려면 다음 단계를 수행 합니다.

1. **개요** 페이지에서 **가상 머신 추가** 를 선택 합니다.

    ![VM 추가 1](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-1.png)

1. **기본 사항** 탭에서 다음 매개 변수를 입력 합니다.


    |매개 변수 |설명  |
    |---------|---------|
    |가상 머신 이름     |         |
    |이미지     | 장치에서 사용할 수 있는 VM 이미지를 선택 합니다.        |
    |크기     | [지원 되는 VM 크기](azure-stack-edge-gpu-virtual-machine-sizes.md)에서 선택 합니다.        |
    |사용자 이름     | 기본 사용자 이름 *azureuser* 를 사용 합니다.        |
    |인증 유형    | SSH 공개 키 또는 사용자 정의 암호를 선택 합니다.       |
    |암호     | 가상 컴퓨터에 로그인 하려면 암호를 입력 하십시오. 암호는 12 자 이상 이어야 하며 정의 된 [복잡성 요구 사항을](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)충족 해야 합니다.        |
    |암호 확인    | 암호를 다시 입력합니다.        |


    ![VM 추가 2](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-basics-1.png)

    완료되면 **다음: 디스크** 를 선택합니다.

1. **디스크** 탭에서 VM에 디스크를 연결 합니다. 
    
    1. **새 디스크를 만들어 연결** 하거나 **기존 디스크를 연결** 하도록 선택할 수 있습니다.

        ![VM 추가 3](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-1.png)

    1. **새 디스크 만들기 및 연결을** 선택 합니다. **새 디스크 만들기** 블레이드에서 디스크의 이름과 GiB 크기를 제공 합니다.

        ![VM 4 추가](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-2.png)

    1.  위의 단계를 반복 하 여 디스크를 더 추가 합니다. 디스크를 만든 후 **디스크 탭에** 표시 됩니다.

        ![VM 추가 5](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-3.png)

        **다음: 네트워킹** 을 선택합니다.

1. **네트워킹** 탭에서 VM에 대 한 네트워크 연결을 구성 합니다.

    
    |매개 변수  |설명 |
    |---------|---------|
    |가상 네트워크    | 네트워크 인터페이스에서 계산을 사용 하도록 설정한 경우 드롭다운 목록에서 Azure Stack Edge 장치에 만들어진 가상 스위치를 선택 합니다.    |
    |서브넷     | 이 필드는 compute를 사용 하도록 설정한 네트워크 인터페이스와 연결 된 서브넷으로 자동으로 채워집니다.         |
    |IP 주소     | VM에 대 한 정적 또는 동적 IP를 제공 합니다. 고정 IP는 지정 된 서브넷 범위에서 사용 가능한 사용 가능한 IP 여야 합니다.        |

    ![VM 추가 6](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-networking-1.png)

    완료되면 **다음: 검토 + 만들기** 를 선택합니다.

1. **검토 + 만들기** 탭에서 VM에 대 한 사양을 검토 하 고 **만들기** 를 선택 합니다.

    ![VM 추가 7](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-review-create-1.png)

1. VM 생성이 시작 되 고 최대 20 분이 걸릴 수 있습니다. **배포** 로 이동 하 여 VM 생성을 모니터링할 수 있습니다.

    ![VM 추가 8](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-deployments-page-1.png)

    
1. VM이 성공적으로 만들어지면 **개요** 페이지가 업데이트 되어 새 vm이 표시 됩니다.

    ![VM 추가 9](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-overview-page-1.png)

1. 새로 만든 VM을 선택 하 여 **가상 머신으로** 이동 합니다.

    ![VM 10 추가](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-page-1.png)

    VM을 선택 하 여 세부 정보를 확인 합니다. 

    ![VM 추가 11](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-details-1.png)

## <a name="connect-to-a-vm"></a>VM에 연결

Windows 또는 Linux VM을 만들었는지 여부에 따라 연결 단계가 다를 수 있습니다. Azure Portal를 통해 장치에 배포 된 Vm에 연결할 수 없습니다. Linux 또는 Windows VM에 연결 하려면 다음 단계를 수행 해야 합니다.

### <a name="connect-to-linux-vm"></a>Linux VM에 연결

Linux VM에 연결 하려면 다음 단계를 수행 합니다.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

### <a name="connect-to-windows-vm"></a>Windows VM에 연결

Windows VM에 연결 하려면 다음 단계를 수행 합니다.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]

## <a name="next-steps"></a>다음 단계

Azure Stack Edge Pro 장치를 관리 하는 방법에 대 한 자세한 내용은[로컬 웹 UI를 사용 하 여 Azure Stack Edge pro 관리](azure-stack-edge-manage-access-power-connectivity-mode.md)를 참조 하세요.
