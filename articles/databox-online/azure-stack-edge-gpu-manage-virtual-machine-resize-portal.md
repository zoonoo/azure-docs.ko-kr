---
title: Azure Portal을 통해 Azure Stack Edge Pro GPU, Pro R, Mini R에서 VM 크기 조정
description: Azure Portal을 통해 Azure Stack Edge Pro GPU, Azure Stack Edge Pro R, Azure Stack Edge Mini R에서 실행되는 VM(가상 머신)의 크기를 조정하는 방법에 대해 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/30/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to resize VMs running on an Azure Stack Edge Pro device so that I can use it to run applications using Edge compute before sending it to Azure.
ms.openlocfilehash: bf2125a6e1d0b443202a036c52fdf845f79d11fa
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080016"
---
# <a name="use-the-azure-portal-to-resize-the-vms-on-your-azure-stack-edge-pro-gpu"></a>Azure Portal을 사용하여 Azure Stack Edge Pro GPU에서 VM의 크기를 조정합니다.

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 Azure Stack Edge Pro GPU 디바이스에 배포된 VM(가상 머신)의 크기를 조정하는 방법을 설명합니다.

       
## <a name="about-vm-sizing"></a>VM 크기 조정 정보

VM 크기에 따라 CPU, GPU, 메모리 등 VM에 사용할 수 있는 컴퓨팅 리소스의 양이 결정됩니다. 애플리케이션 워크로드에 적합한 VM 크기를 사용하여 가상 머신을 만들어야 합니다. 

모든 컴퓨터가 동일한 하드웨어에서 실행되는 경우에도 디스크에 액세스할 때는 컴퓨터 크기에 다른 제한이 적용됩니다. 이렇게 하면 VM 전체에서 전체 디스크 액세스를 관리할 수 있습니다. 워크로드가 증가할 경우 기존 가상 머신의 크기를 조정할 수도 있습니다.

자세한 내용은 [지원되는 디바이스용 VM 크기](azure-stack-edge-gpu-virtual-machine-sizes.md)를 참조하세요.


## <a name="prerequisites"></a>사전 요구 사항

Azure Portal을 통해 디바이스에서 실행되는 VM의 크기를 조정하기 전에 다음 사항을 확인합니다.

1. 디바이스에 하나 이상의 VM이 배포되어 있어야 합니다. 이 VM을 만들려면 [Azure Portal을 통해 Azure Stack Edge Pro에 VM 배포](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)의 지침을 참조하세요.

1. VM은 **중지됨** 상태여야 합니다. VM을 중지하려면 **가상 머신 > 개요** 로 이동하고 중지할 VM을 선택합니다. 개요 페이지에서 **중지** 를 선택한 후 확인 메시지가 표시되면 **예** 를 선택합니다. VM의 크기를 조정하기 전에 VM을 중지해야 합니다.

    ![개요 페이지에서 VM 중지](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/stop-vm-2.png)


## <a name="resize-a-vm"></a>VM 크기 조정

디바이스에 배포된 가상 머신의 크기를 조정하려면 다음 단계를 수행합니다. 

1. 중지한 가상 머신으로 이동한 후 **개요** 페이지로 이동합니다. **VM 크기(변경)** 를 선택합니다.
    
    ![개요 페이지에서 VM 크기 변경 선택](./media/azure-stack-edge-gpu-manage-virtual-machine-resize-portal/change-vm-size-1.png)

2. **VM 크기 변경** 블레이드의 명령 모음에서 **VM 크기** 를 선택하고 **변경** 을 선택합니다.

    ![새 VM 크기 선택](./media/azure-stack-edge-gpu-manage-virtual-machine-resize-portal/change-vm-size-2.png)

3. 가상 머신이 업데이트되고 있다는 알림이 표시됩니다. 가상 머신이 성공적으로 업데이트된 후에는 **개요** 페이지가 새로 고쳐지고 크기 조정된 VM이 표시됩니다.

    ![크기 조정된 VM ](./media/azure-stack-edge-gpu-manage-virtual-machine-resize-portal/change-vm-size-3.png)


## <a name="next-steps"></a>다음 단계

Azure Stack Edge Pro 디바이스에 가상 머신을 배포하는 방법을 알아보려면 [Azure Portal을 통해 가상 머신 배포](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)를 참조하세요.
