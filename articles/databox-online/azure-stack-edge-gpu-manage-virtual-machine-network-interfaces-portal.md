---
title: Azure Portal을 통해 Azure Stack Edge Pro에서 VM 네트워크 인터페이스를 관리하는 방법
description: Azure Portal을 통해 Azure Stack Edge Pro GPU에 배포된 VM에서 네트워크 인터페이스를 관리하는 방법을 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/30/2021
ms.author: alkohli
ms.openlocfilehash: 3e709b04b4eac60e6cc0ba3e53eb77583162dfef
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078896"
---
# <a name="use-the-azure-portal-to-manage-network-interfaces-on-the-vms-on-your-azure-stack-edge-pro-gpu"></a>Azure Portal을 사용하여 Azure Stack Edge Pro GPU에서 VM의 네트워크 인터페이스 관리

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Portal, 템플릿, Azure PowerShell cmdlet을 사용하고 Azure CLI/Python 스크립트를 통해 Azure Stack Edge 디바이스에서 VM(가상 머신)을 만들고 관리할 수 있습니다. 이 문서에서는 Azure Portal을 사용하여 Azure Stack Edge 디바이스에서 실행되는 VM에서 네트워크 인터페이스를 관리하는 방법을 설명합니다. 

VM을 만드는 경우 만들 가상 네트워크 인터페이스를 하나 지정합니다. 가상 머신이 생성되면 하나 이상의 네트워크 인터페이스를 가상 머신에 추가할 수 있습니다. 기존 네트워크 인터페이스의 기본 네트워크 인터페이스 설정을 변경할 수도 있습니다.

이 문서에서는 기존 VM에 네트워크 인터페이스를 추가하고, IP 유형(고정 및 동적)과 같은 기존 설정을 변경하고, 마지막으로 기존 인터페이스를 제거하거나 분리하는 방법을 설명합니다. 

        
## <a name="about-network-interfaces-on-vms"></a>VM의 네트워크 인터페이스 정보

네트워크 인터페이스를 사용하면 Azure Stack Edge Pro 디바이스에서 실행되는 VM(가상 머신)을 Azure 및 온-프레미스 리소스와 통신할 수 있습니다. 디바이스에서 컴퓨팅 네트워크용 포트를 사용하도록 설정하면 해당 네트워크 인터페이스에 가상 스위치가 생성됩니다. 그런 다음, 이 가상 스위치를 사용하여 VM 또는 컨테이너화된 애플리케이션과 같은 컴퓨팅 워크로드를 디바이스에 배포합니다. 

사용하는 디바이스는 가상 스위치를 하나만 지원하지만 가상 네트워크 인터페이스는 여러 개의 가상 스위치를 지원합니다. VM의 각 네트워크 인터페이스에는 고정 또는 동적 IP 주소가 할당되어 있습니다. VM의 여러 네트워크 인터페이스에 IP 주소를 할당하면 VM에서 특정 기능을 사용할 수 있습니다. 예를 들어 VM은 단일 서버에서 여러 IP 주소 및 SSL 인증서를 사용하여 여러 웹 사이트 또는 서비스를 호스트할 수 있습니다. 디바이스의 VM은 방화벽 또는 부하 분산 장치와 같은 네트워크 가상 어플라이언스로 사용할 수 있습니다. <!--Is it possible to do that on ASE?-->

<!--There is a limit to how many virtual network interfaces can be created on the virtual switch on your device. See the Azure Stack Edge Pro limits article for details.--> 


## <a name="prerequisites"></a>사전 요구 사항

Azure Portal을 통해 디바이스에서 VM 관리를 시작하기 전에 다음 사항을 확인하세요.

1. 활성화된 Azure Stack Edge Pro GPU 디바이스에 액세스할 수 있습니다. 디바이스에서 컴퓨팅을 위한 네트워크 인터페이스를 사용하도록 설정했습니다. 이 작업은 VM의 해당 네트워크 인터페이스에 가상 스위치를 만듭니다. 
    1. 디바이스의 로컬 UI에서 **컴퓨팅** 페이지로 이동합니다. 가상 스위치를 만드는 데 사용할 네트워크 인터페이스를 선택합니다.

        > [!IMPORTANT] 
        > 컴퓨팅용 포트는 하나만 구성할 수 있습니다.

    1. 네트워크 인터페이스에서 컴퓨팅을 사용하도록 설정합니다. 이 네트워크 인터페이스에 해당하는 가상 스위치를 Azure Stack Edge Pro GPU가 만들고 관리합니다.

1. 디바이스에 하나 이상의 VM이 배포되어 있습니다. 이 VM을 만들려면 [Azure Portal을 통해 Azure Stack Edge Pro에 VM 배포](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)에 제공된 지침을 참조하세요.

1. VM은 **중지됨** 상태여야 합니다. VM을 중지하려면 **가상 머신 > 개요** 로 이동하고 중지할 VM을 선택합니다. VM 속성 페이지에서 **중지** 를 선택한 후 확인 메시지가 표시되면 **예** 를 선택합니다. 네트워크 인터페이스를 추가, 편집 또는 삭제하기 전에 VM을 중지해야 합니다.

    ![VM 속성 페이지에서 VM 중지](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/stop-vm-2.png)


## <a name="add-a-network-interface"></a>네트워크 인터페이스 추가

디바이스에 배포된 가상 머신에 네트워크 인터페이스를 추가하려면 다음 단계를 수행합니다. 

1. 중지한 가상 머신으로 이동한 후 **VM 속성** 페이지로 이동합니다. **네트워킹** 을 선택합니다.
    
    ![VM 속성 페이지에서 네트워킹 선택](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-1.png)

2. **네트워킹** 블레이드의 명령 모음에서 **+ 네트워크 인터페이스 추가** 를 선택합니다.

    ![네트워크 인터페이스 추가 선택](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-2.png)

3. **네트워크 인터페이스 추가** 블레이드에서 다음 매개 변수를 입력합니다.

    
    |열1  |Column2  |
    |---------|---------|
    |속성     | 리소스 그룹의 고유한 이름입니다. 네트워크 인터페이스를 만든 후에는 이름을 변경할 수 없습니다. 여러 네트워크 인터페이스를 쉽게 관리하려면 [명명 규칙](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming)에 제공된 제안 사항을 이용합니다.     |
    |가상 네트워크| 네트워크 인터페이스에서 컴퓨팅을 사용하도록 설정한 경우 디바이스에서 생성된 가상 스위치와 연결된 가상 네트워크입니다. 디바이스와 연결된 가상 네트워크는 하나만 있습니다.         |         
    |서브넷     | 선택한 가상 네트워크 내의 서브넷 이 필드는 컴퓨팅을 사용하도록 설정한 네트워크 인터페이스와 연결된 서브넷에 자동으로 채워집니다.         |       
    |IP 할당   | 네트워크 인터페이스의 고정 또는 동적 IP입니다. 고정 IP는 지정된 서브넷 범위에서 제공되는 사용 가능한 IP여야 합니다. 환경에 DHCP 서버가 있으면 동적을 선택합니다.        | 

    ![네트워크 인터페이스 블레이드 추가](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-3.png)

4. 네트워크 인터페이스 만들기가 진행 중이라는 알림이 표시됩니다.

    ![네트워크 인터페이스가 생성될 때 알림](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-4.png)

5.  네트워크 인터페이스가 생성되면 네트워크 인터페이스 목록이 새로 고쳐지고, 새로 생성된 인터페이스가 표시됩니다.

    ![업데이트된 네트워크 인터페이스 목록](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-5.png)


## <a name="edit-a-network-interface"></a>네트워크 인터페이스 편집

디바이스에 배포된 가상 머신에 연결된 네트워크 인터페이스를 편집하려면 다음 단계를 수행합니다.

1. 중지한 가상 머신으로 이동하고 **VM 속성** 페이지로 이동합니다. **네트워킹** 을 선택합니다.

1. 네트워크 인터페이스 목록에서 편집할 인터페이스를 선택합니다. 선택한 네트워크 인터페이스의 오른쪽 끝에 있는 편집 아이콘(연필)을 선택합니다.  

    ![편집할 네트워크 인터페이스 선택](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/edit-nic-1.png)

1. **네트워크 인터페이스 편집** 블레이드에서는 네트워크 인터페이스의 IP 할당만 변경할 수 있습니다. 이름, 가상 네트워크 및 네트워크 인터페이스와 연결된 서브넷은 만든 후에는 변경할 수 없습니다. **IP 할당** 을 고정으로 변경하고 변경 내용을 저장합니다.

    ![네트워크 인터페이스에 대한 IP 할당 변경](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/edit-nic-2.png)

1. 네트워크 인터페이스 목록이 새로 고쳐지고 업데이트된 네트워크 인터페이스가 표시됩니다.


## <a name="detach-a-network-interface"></a>네트워크 인터페이스 분리

디바이스에 배포된 가상 머신과 연결된 네트워크 인터페이스를 분리하거나 제거하려면 다음 단계를 수행합니다.

1. 중지한 가상 머신으로 이동하고 **VM 속성** 페이지로 이동합니다. **네트워킹** 을 선택합니다.

1. 네트워크 인터페이스 목록에서 편집할 인터페이스를 선택합니다. 선택한 네트워크 인터페이스의 오른쪽 끝에 있는 분리 아이콘(분리)을 선택합니다.  

    ![분리할 네트워크 인터페이스 선택](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/detach-nic-1.png)

1. 인터페이스가 완전히 분리되면 네트워크 인터페이스 목록이 새로 고쳐져 나머지 인터페이스가 표시됩니다.

## <a name="next-steps"></a>다음 단계

Azure Stack Edge Pro 디바이스에 가상 머신을 배포하는 방법을 알아보려면 [Azure Portal을 통해 가상 머신 배포](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)를 참조하세요.
