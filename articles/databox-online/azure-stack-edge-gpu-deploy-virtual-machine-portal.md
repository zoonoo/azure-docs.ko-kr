---
title: Azure Portal을 통해 Azure Stack Edge Pro에 VM 배포하는 방법
description: Azure Portal를 통해 Azure Stack Edge Pro에서 VM을 만들고 관리하는 방법을 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/30/2021
ms.author: alkohli
ms.openlocfilehash: 139b543160b679ba063a0633f9091e7bc0ef1fc1
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106074852"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-the-azure-portal"></a>Azure Portal을 통해 Azure Stack Edge Pro GPU 디바이스에 VM 배포

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Portal, 템플릿, Azure PowerShell cmdlet을 사용하고 Azure CLI/Python 스크립트를 통해 Azure Stack Edge 디바이스에서 VM(가상 머신)을 만들고 관리할 수 있습니다. 문서에서는 Azure Portal을 사용하여 Azure Stack Edge Pro 디바이스에서 VM을 만들고 관리하는 방법을 설명합니다. 

해당 문서는 Azure Stack Edge Pro GPU, Azure Stack Edge Pro R, Azure Stack Edge Mini R 디바이스에만 적용됩니다. 

> [!IMPORTANT] 
> 클라우드에서 디바이스에 배포된 VM을 관리하는 사용자를 위해 다단계 인증을 사용하도록 설정하는 것이 좋습니다.
        
## <a name="vm-deployment-workflow"></a>VM 배포 워크플로

배포 워크플로에 대한 개략적인 요약은 다음과 같습니다.

1. Azure Stack Edge 디바이스에서 컴퓨팅용 네트워크 인터페이스를 사용하도록 설정합니다. 그러면 지정된 네트워크 인터페이스에 가상 스위치가 생성됩니다.
1. Azure Portal에서 가상 머신의 클라우드 관리를 사용하도록 설정합니다.
1. Storage Explorer를 사용하여 Azure Storage 계정에 VHD를 업로드합니다. 
1. 업로드된 VHD를 사용하여 VHD를 디바이스에 다운로드하고 VHD에서 VM 이미지를 만듭니다. 
1. 이전 단계에서 만든 리소스를 사용합니다.
    1. 만든 VM 이미지입니다.
    1. 컴퓨팅을 사용하도록 설정한 네트워크 인터페이스와 연결된 VSwitch입니다.
    1. VSwitch와 연결된 서브넷입니다.

    그리고 다음 리소스 인라인을 생성하거나 지정합니다.
    1. VM 이름, VM에 지원되는 VM 크기, 로그인 자격 증명을 선택합니다. 
    1. 새 데이터 디스크를 만들거나 기존 데이터 디스크를 연결합니다.
    1. VM의 고정 IP 또는 동적 IP를 구성합니다. 고정 IP를 제공하는 경우 컴퓨팅에 사용하도록 설정된 네트워크 인터페이스의 서브넷 범위에서 사용 가능한 IP를 선택합니다.

    위의 리소스를 사용하여 가상 머신을 만듭니다.


## <a name="prerequisites"></a>필수 구성 요소

Azure Portal를 통해 디바이스에서 VM 만들기 및 관리를 시작하기 전에 다음을 확인합니다.

1. [1단계: Azure Stack Edge Pro 디바이스 구성](./azure-stack-edge-gpu-connect-resource-manager.md#step-1-configure-azure-stack-edge-pro-device)의 설명대로 Azure Stack Edge Pro 디바이스에서 네트워크 설정을 완료했습니다.

    1. 컴퓨팅에 네트워크 인터페이스를 사용하도록 설정했습니다. 이 네트워크 인터페이스 IP는 VM 배포를 위한 가상 스위치를 만드는 데 사용됩니다. 디바이스의 로컬 UI에서 **컴퓨팅** 페이지로 이동합니다. 가상 스위치를 만드는 데 사용할 네트워크 인터페이스를 선택합니다.

        > [!IMPORTANT] 
        > 컴퓨팅용 포트는 하나만 구성할 수 있습니다.

    1. 네트워크 인터페이스에서 컴퓨팅을 사용하도록 설정합니다. 이 네트워크 인터페이스에 해당하는 가상 스위치를 Azure Stack Edge Pro가 만들고 관리합니다.

1. 만들려는 가상 머신의 VM 이미지를 만드는 데 사용하는 Windows 또는 Linux VHD에 액세스할 수 있습니다.

## <a name="deploy-a-vm"></a>VM 배포

Azure Stack Edge 디바이스에서 가상 머신을 만들려면 다음 단계를 따르세요.

### <a name="add-a-vm-image"></a>VM 이미지 추가

1. Azure Storage 계정에 VHD 업로드. [Azure Storage Explorer를 사용하여 VHD 업로드](../devtest-labs/devtest-lab-upload-vhd-using-storage-explorer.md)의 단계를 따릅니다.

1. Azure Portal에서 Azure Stack Edge 디바이스를 위한 Azure Stack Edge 리소스로 이동합니다. Go to **Edge 컴퓨팅 > Virtual Machines** 로 이동합니다.

    ![VM 이미지 1 추가](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-1.png)

1. **Virtual Machines** 를 선택하여 **개요** 페이지로 이동합니다. 가상 머신 클라우드 관리를 **사용하도록 설정** 합니다.
    ![VM 이미지 2 추가](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-2.png)

1. 첫 번째 단계는 VM 이미지를 추가하는 것입니다. 이전 단계에서 스토리지 계정에 이미 VHD를 업로드했습니다. 이 VHD를 사용하여 VM 이미지를 만듭니다.

    스토리지 계정에서 VHD를 다운로드하고 디바이스에 추가하려면 **이미지 추가** 를 선택합니다. 다운로드 프로세스는 VHD 크기와 다운로드에 사용할 수 있는 인터넷 대역폭에 따라 몇 분이 걸립니다. 

    ![VM 이미지 3 추가](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-3.png)

1. **이미지 추가** 블레이드에서 다음 매개 변수를 입력합니다. **추가** 를 선택합니다.


    |매개 변수  |설명  |
    |---------|---------|
    |스토리지 BLOB에서 다운로드    |VHD를 업로드한 스토리지 계정에서 스토리지 BLOB의 위치로 이동합니다.         |
    |다운로드 위치:    | 가상 머신을 배포하는 현재 디바이스에 자동으로 설정됩니다.        |
    |다른 이름으로 이미지 저장      | 스토리지 계정에 업로드한 VHD에서 만든 VM 이미지의 이름입니다.        |
    |OS 유형     |Windows 또는 Linux를 VM 이미지를 만드는 데 사용하는 VHD의 운영 체제로 선택합니다.         |
   

    ![VM 이미지 4 추가](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-6.png)

1. VHD를 다운로드하고 VM 이미지를 만듭니다. 이미지를 만드는 데는 몇 분 정도 걸립니다. VM 이미지가 성공적으로 완료되었다는 알림이 표시됩니다.

    ![VM 이미지 5 추가](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-8.png)


1. VM 이미지가 성공적으로 만들어지면 **이미지** 블레이드의 이미지 목록에 추가됩니다.
    ![VM 이미지 6 추가](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-9.png)

    **배포** 블레이드가 업데이트되어 배포 상태를 표시합니다.

    ![VM 이미지 7 추가](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-10.png)

    새로 추가된 이미지는 **개요** 페이지에도 표시됩니다.
    ![VM 이미지 8 추가](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-11.png)


### <a name="add-a-vm"></a>VM 추가

VM 이미지를 만든 후에 VM을 만들려면 다음 단계를 수행합니다.

1. **개요** 페이지에서 **가상 머신 추가** 를 선택합니다.

    ![VM 1 추가](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-1.png)

1. **기본 사항** 블레이드에서 다음 매개 변수를 입력합니다.


    |매개 변수 |설명  |
    |---------|---------|
    |가상 머신 이름     |         |
    |Edge 리소스 그룹     | VM과 연결된 모든 리소스를 위한 새 리소스 그룹을 만듭니다.        |
    |이미지     | 디바이스에서 사용할 수 있는 VM 이미지를 선택합니다.        |
    |크기     | [지원되는 VM 크기](azure-stack-edge-gpu-virtual-machine-sizes.md)에서 선택합니다.        |
    |사용자 이름     | 관리자의 기본 사용자 이름 *azureuser* 를 사용하여 VM에 로그인합니다.        |
    |인증 유형    | SSH 퍼블릭 키 또는 사용자 정의 암호를 선택합니다.       |
    |암호     | 가상 머신에 로그인하려면 암호를 입력하세요. 암호는 12자 이상이어야 하며 정의된 [복잡성 요구 사항](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)을 충족해야 합니다.        |
    |암호 확인    | 암호를 다시 입력합니다.        |


    ![VM 2 추가](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-basics-1.png)

    완료되면 **다음: 디스크** 를 선택합니다.

1. **디스크** 탭에서 VM에 디스크를 연결합니다. 
    
    1. **새 디스크 만들기 및 연결** 또는 **기존 디스크 연결** 을 선택할 수 있습니다.

        ![VM 3 추가](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-1.png)

    1. **새 디스크 만들기 및 연결** 을 선택합니다. **새 디스크 만들기** 블레이드에서 디스크의 이름과 크기(GiB)를 제공합니다.

        ![VM 4 추가](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-2.png)

    1.  위 단계를 반복하여 디스크를 더 추가합니다. 디스크를 만들면 **디스크**  탭에 표시됩니다. **다음: 네트워킹** 을 선택합니다.

1. **네트워킹** 탭에서 VM을 위한 네트워크 연결을 구성합니다.

    
    |매개 변수  |설명 |
    |---------|---------|
    |가상 네트워크    | 네트워크 인터페이스에서 컴퓨팅을 사용하도록 설정한 경우 드롭다운 목록에서 Azure Stack Edge 디바이스에 만들어진 가상 스위치를 선택합니다.    |
    |서브넷     | 이 필드는 컴퓨팅을 사용하도록 설정한 네트워크 인터페이스와 연결된 서브넷으로 자동으로 채워집니다.         |
    |IP 주소     | VM의 고정 IP 또는 동적 IP를 제공합니다. 고정 IP는 지정된 서브넷 범위에서 사용 가능한 여유 IP여야 합니다.        |

    ![VM 6 추가](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-networking-1.png)

    완료되면 **다음: 검토 + 만들기** 를 선택합니다.

1. **고급** 탭에서 사용자 지정 데이터 또는 cloud-init를 지정하여 VM을 사용자 지정할 수 있습니다. 

    cloud-init를 사용하여 첫 번째 부팅에서 VM을 사용자 지정할 수 있습니다. cloud-init를 사용하여 패키지를 설치하고 파일을 쓰거나, 사용자 및 보안을 구성할 수 있습니다. 초기 부팅 프로세스 중에 cloud-init가 실행되면 구성을 적용하기 위한 추가 단계가 필요하지 않습니다. cloud-init에 관한 자세한 내용은 [cloud-init 개요](../virtual-machines/linux/tutorial-automate-vm-deployment.md#cloud-init-overview)를 참조하세요.

    ![VM 7 추가](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-advanced-1.png)    

1. **검토 + 만들기** 탭에서 VM 사양을 검토하고 **만들기** 를 선택합니다.

    ![VM 8 추가](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-review-create-1.png)

1. VM 생성이 시작되고 최대 20분이 걸릴 수 있습니다. **배포** 로 이동하여 VM 생성을 모니터링할 수 있습니다.

    ![VM 9 추가](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-deployments-page-1.png)

    
1. VM이 성공적으로 만들어지면 **개요** 페이지가 업데이트되어 새 VM이 표시됩니다.

    ![VM 10 추가](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-overview-page-1.png)

1. 새로 만든 VM을 선택하여 **가상 머신** 으로 이동합니다.

    ![VM 11 추가](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-page-1.png)

    VM을 선택하여 세부 정보를 확인합니다. 

    ![VM 12 추가](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-details-1.png)

## <a name="connect-to-a-vm"></a>VM에 연결

Windows 또는 Linux VM을 만들었는지 여부에 따라 연결 단계가 다를 수 있습니다. Azure Portal를 통해 디바이스에 배포된 VM에 연결할 수 없습니다. Linux 또는 Windows VM에 연결하려면 다음 단계를 수행해야 합니다.

### <a name="connect-to-linux-vm"></a>Linux VM에 연결

Linux VM에 연결하려면 다음 단계를 수행합니다.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

### <a name="connect-to-windows-vm"></a>Windows VM에 연결

Windows VM에 연결하려면 다음 단계를 수행합니다.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]

## <a name="next-steps"></a>다음 단계

Azure Stack Edge Pro 디바이스를 관리하는 방법에 관한 자세한 내용은[로컬 웹 UI를 사용하여 Azure Stack Edge pro 관리](azure-stack-edge-manage-access-power-connectivity-mode.md)를 참조하세요.