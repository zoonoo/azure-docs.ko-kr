---
title: Azure Portal을 통해 Azure Stack Edge Pro GPU, Pro R, Mini R에서 VM 디스크 관리
description: Azure Portal을 통해 Azure Stack Edge Pro GPU, Azure Stack Edge Pro R 및 Azure Stack Edge Mini R에 배포된 VM에서 데이터 디스크 추가 또는 분리하는 방법을 비롯한 디스크 관리 방법에 대해 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/30/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to manage disks on a VM running on an Azure Stack Edge Pro device so that I can use it to run applications using Edge compute before sending it to Azure.
ms.openlocfilehash: dff3f4bafdb35d89e8f1792c1aca68824a9bc685
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080048"
---
# <a name="use-the-azure-portal-to-manage-disks-on-the-vms-on-your-azure-stack-edge-pro-gpu"></a>Azure Portal을 사용하여 Azure Stack Edge Pro GPU에서 VM의 디스크 관리

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Portal을 사용하여 Azure Stack Edge Pro 디바이스에 배포된 VM(가상 머신)에 디스크를 프로비전할 수 있습니다. 디스크는 로컬 Azure Resource Manager를 통해 디바이스에 프로비전되고 디바이스 용량을 사용합니다. Azure Portal을 통해 디스크 추가, 디스크 분리 같은 작업을 수행할 수 있으며, 그러면 로컬 Azure Resource Manager가 호출되고 스토리지를 프로비전합니다. 

이 문서에서는 Azure Portal을 통해 기존 VM에 데이터 디스크를 추가하고, 데이터 디스크를 분리하고, 마지막으로 VM 자체의 크기를 조정하는 방법을 설명합니다. 

        
## <a name="about-disks-on-vms"></a>VM의 디스크 정보

VM에는 OS 디스크 및 데이터 디스크가 있을 수 있습니다. 디바이스에 배포된 모든 가상 머신에는 연결된 운영 체제 디스크가 하나 있습니다. 이 OS 디스크에는 VM이 만들어질 때 선택된 OS가 사전 설치되어 있습니다. 이 디스크에는 부팅 볼륨이 포함되어 있습니다.

> [!NOTE]
> 디바이스에서 VM의 OS 디스크 크기를 변경할 수는 없습니다. OS 디스크 크기는 선택한 VM 크기에 따라 결정됩니다. 


반면, 데이터 디스크는 디바이스에서 실행 중인 VM에 연결된 관리 디스크입니다. 데이터 디스크는 애플리케이션 데이터를 저장하는 데 사용됩니다. 데이터 디스크는 일반적으로 SCSI 드라이브입니다. VM 크기에 따라 VM에 연결할 수 있는 데이터 디스크 개수가 결정됩니다. 기본적으로 Premium Storage를 사용하여 디스크가 호스트됩니다.

디바이스에 배포된 VM에 임시 디스크가 포함 되는 경우도 있습니다. 이러한 임시 디스크는 애플리케이션 및 프로세스에 대한 단기 스토리지를 제공하며 페이지 또는 스왑 파일과 같은 데이터 저장에 사용됩니다. 임시 디스크의 데이터는 유지 관리 이벤트 또는 VM을 다시 배포할 때 손실될 수 있습니다. VM을 정상적으로 재부팅하는 동안 임시 디스크의 데이터는 유지됩니다. 


## <a name="prerequisites"></a>필수 조건

Azure Portal을 통해 디바이스에서 실행 중인 VM의 디스크 관리를 시작하기 전에 다음 사항을 확인하세요.


1. 활성화된 Azure Stack Edge Pro GPU 디바이스에 액세스할 수 있습니다. 또한 디바이스에서 컴퓨팅을 위한 네트워크 인터페이스를 사용하도록 설정했습니다. 이 작업은 VM의 해당 네트워크 인터페이스에 가상 스위치를 만듭니다. 
    1. 디바이스의 로컬 UI에서 **컴퓨팅** 으로 이동합니다. 가상 스위치를 만드는 데 사용할 네트워크 인터페이스를 선택합니다.

        > [!IMPORTANT] 
        > 컴퓨팅용 포트는 하나만 구성할 수 있습니다.

    1. 네트워크 인터페이스에서 컴퓨팅을 사용하도록 설정합니다. 이 네트워크 인터페이스에 해당하는 가상 스위치를 Azure Stack Edge Pro GPU가 만들고 관리합니다.

1. 디바이스에 하나 이상의 VM이 배포되어 있습니다. 이 VM을 만들려면 [Azure Portal을 통해 Azure Stack Edge Pro에 VM 배포](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)에 나와 있는 지침을 참조하세요.



## <a name="add-a-data-disk"></a>데이터 디스크 추가

디바이스에 배포된 가상 머신에 디스크를 추가하려면 다음 단계를 수행합니다. 

1. 데이터 디스크를 추가하려는 가상 머신으로 이동한 다음, **개요** 페이지로 이동합니다. **디스크** 를 선택합니다.
    
    ![개요 페이지에서 디스크 선택](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-1.png)

1. **디스크 블레이드** 의 **데이터 디스크** 에서 **새 디스크 만들기 및 연결** 을 선택합니다.

    ![새 디스크 만들기 및 연결](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-2.png)

1. **새 디스크 만들기** 블레이드에서 다음 매개 변수를 입력합니다.

    
    |필드  |설명  |
    |---------|---------|
    |속성     | 리소스 그룹의 고유한 이름입니다. 데이터 디스크를 만든 후에는 이름을 변경할 수 없습니다.     |
    |크기| 데이터 디스크의 크기입니다(GiB 단위). 데이터 디스크의 최대 크기는 선택한 VM 크기에 따라 결정됩니다. 디스크를 프로비저닝할 때는 디바이스의 실제 공간과 용량을 소비하는, 실행 중인 다른 VM 워크로드도 고려해야 합니다.  |         

    ![새 디스크 블레이드 만들기](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-3.png)

    **확인** 을 선택하고 계속합니다.

1. **개요** 페이지의 **디스크** 에 새 디스크에 해당하는 항목이 표시됩니다. 기본값을 그대로 적용하거나 디스크에 유효한 LUN(논리 단위 번호)을 할당하고 **저장** 을 선택합니다. LUN은 SCSI 디스크의 고유 식별자입니다. 자세한 내용은 [LUN이란?](../virtual-machines/linux/azure-to-guest-disk-mapping.md#what-is-a-lun)을 참조하세요.

    ![개요 페이지의 새 디스크](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-4.png)

1. 디스크 만들기가 진행 중이라는 알림이 표시됩니다. 디스크가 성공적으로 만들어지면 가상 머신이 업데이트됩니다. 

    ![디스크 만들기에 대한 알림](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-5.png)

1. **개요** 페이지로 돌아갑니다. 새로 만든 데이터 디스크가 표시되도록 디스크 목록이 업데이트됩니다.

    ![데이터 디스크의 업데이트된 목록](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-6.png)


## <a name="change-a-data-disk"></a>데이터 디스크 변경

디바이스에 배포된 가상 머신과 연결된 디스크를 변경하려면 다음 단계를 수행합니다.

1. 변경할 데이터 디스크가 있는 가상 머신으로 이동하고 **개요** 페이지로 이동합니다. **디스크** 를 선택합니다.

1. 데이터 디스크 목록에서 변경하려는 디스크를 선택합니다. 선택한 디스크의 맨 오른쪽에 있는 편집 아이콘(연필)을 선택합니다.  

    ![변경할 디스크 선택](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/edit-data-disk-1.png)

1. **디스크 변경** 블레이드에서는 디스크 크기만 변경할 수 있습니다. 디스크와 연결된 이름은 디스크 생성 후 변경할 수 없습니다. **크기** 를 변경하고 변경 내용을 저장합니다.

    ![데이터 디스크 크기 변경](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/edit-data-disk-2.png)

    > [!NOTE]
    > 데이터 디스크를 확장할 수만 있으며 축소할 수는 없습니다.

1. **개요** 페이지에서 업데이트된 디스크를 표시하도록 디스크 목록이 새로 고침됩니다.


## <a name="attach-an-existing-disk"></a>기존 디스크 연결

디바이스에 배포된 가상 머신에 기존 디스크를 연결하려면 다음 단계를 수행합니다.

1. 기존 디스크를 연결할 가상 머신으로 이동한 후 **개요** 페이지로 이동합니다. **디스크** 를 선택합니다.
    
    ![디스크 선택 ](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-1.png)

1. **디스크** 블레이드의 **데이터 디스크** 에서 **기존 디스크 연결** 을 선택합니다.

    ![기존 디스크 연결 선택](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/attach-existing-data-disk-1.png)

1. 기본 LUN을 적용하거나 유효한 LUN을 할당합니다. 드롭다운 목록에서 기존 데이터 디스크를 선택합니다. [저장]을 선택합니다.

    ![기존 디스크 선택](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/attach-existing-data-disk-2.png)

    **저장** 을 선택하고 계속합니다.

1. 가상 머신이 업데이트되었다는 알림이 표시됩니다. VM이 업데이트되면 **개요** 페이지로 다시 이동합니다. 페이지를 새로 고쳐 데이터 디스크 목록에서 새로 연결된 디스크를 확인합니다.

    ![개요 페이지에서 업데이트된 데이터 디스크 목록 보기](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-2.png)


## <a name="detach-a-data-disk"></a>데이터 디스크 분리

디바이스에 배포된 가상 머신과 연결된 데이터 디스크를 분리하거나 제거하려면 다음 단계를 수행합니다.

> [!NOTE]
> - VM이 실행되는 동안 데이터 디스크를 제거할 수 있습니다. VM에서 해당 디스크를 분리할 때 해당 디스크가 전혀 사용되고 있지 않아야 합니다.
> - 디스크를 분리해도 자동으로 삭제되지 않습니다.

1. 데이터 디스크를 분리할 가상 머신으로 이동하고 **개요** 페이지로 이동합니다. **디스크** 를 선택합니다.

    ![디스크 선택](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-1.png)

1. 디스크 목록에서 분리하려는 디스크를 선택합니다. 선택한 디스크의 맨 오른쪽에 있는 분리 아이콘(X 표)을 선택합니다. 선택한 항목이 분리됩니다. **저장** 을 선택합니다. 

    ![분리할 디스크 선택](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/detach-data-disk-1.png)

1. 디스크가 분리되면 가상 머신이 업데이트됩니다. 업데이트된 데이터 디스크 목록을 보려면 **개요** 페이지를 새로 고칩니다.

    ![저장 선택](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-2.png)


## <a name="next-steps"></a>다음 단계

Azure Stack Edge Pro 디바이스에 가상 머신을 배포하는 방법을 알아보려면 [Azure Portal을 통해 가상 머신 배포](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)를 참조하세요.
