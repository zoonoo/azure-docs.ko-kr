---
title: "사용자 지정 이미지에서 Azure Batch 풀 프로비전| Microsoft Docs"
description: "사용자 지정 이미지에서 Batch 풀을 만들어 애플리케이션에 대해 사용자가 필요한 소프트웨어 및 데이터가 들어 있는 계산 노드를 프로비전할 수 있습니다. 사용자 지정 이미지는 Batch 워크로드를 실행하도록 계산 노드를 구성하는 효율적인 방법입니다."
services: batch
author: tamram
manager: timlt
ms.service: batch
ms.topic: article
ms.date: 08/07/2017
ms.author: tamram
ms.openlocfilehash: 0816c464b6b52747148cc42a55445048901e7595
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="use-a-custom-image-to-create-a-pool-of-virtual-machines"></a>사용자 지정 이미지를 사용하여 가상 컴퓨터 풀 만들기

Azure Batch에서 가상 컴퓨터 풀을 만들 경우 풀에서 각 계산 노드에 대해 운영 체제를 제공하는 가상 컴퓨터(VM) 이미지를 지정합니다. Azure Marketplace 이미지를 사용하거나 사용자가 준비한 사용자 지정 VHD 이미지를 제공하여 가상 컴퓨터 풀을 만들 수 있습니다. 사용자 지정 이미지를 제공할 경우 각 계산 노드가 프로비전되는 시점에서 운영 체제가 구성되는 방식을 제어할 수 있습니다. 사용자 지정 이미지에는 프로비전되는 즉시, 계산 노드에서 사용 가능한 응용 프로그램 및 참조 데이터도 포함할 수 있습니다.

사용자 지정 이미지를 사용하면 풀의 계산 노드가 Batch 워크로드를 실행할 준비를 하는 데 걸리는 시간을 절약할 수 있습니다. 프로비전된 후에는 항상 Azure Marketplace 이미지를 사용하고 각 계산 노드에 소프트웨어를 설치할 수 있지만, 이러한 접근 방식은 사용자 지정 이미지를 사용하는 것보다 효율성이 떨어질 수 있습니다. 

사용자 시나리오에 맞게 구성된 사용자 지정 이미지를 사용해야 하는 몇 가지 이유는 다음과 같습니다.

- **OS(운영 체제) 구성** 사용자 지정 이미지에서 운영 체제의 모든 특별한 구성을 수행할 수 있습니다. 
- **대규모 응용 프로그램 설치.** 사용자 지정 이미지에 응용 프로그램을 설치하는 것이 프로비전 후 각 계산 노드에서 설치하는 것보다 더 효율적입니다.
- **많은 양의 데이터 복사.** 데이터를 사용자 지정 이미지에 복사하는 경우 각 계산 노드에 복사하는 것보다는 한 번만 복사하여 시간과 대역폭을 절감해야 합니다.
- **설치 과정 중에 VM 다시 부팅.** VM을 다시 부팅하는 것은 특히 여러 계산 노드가 있는 경우 시간이 많이 소요되는 과정일 수 있습니다.

## <a name="prerequisites"></a>필수 조건

- **사용자 구독 풀 할당 모드로 만든 Batch 계정.** 이 모드를 사용하면 해당 계정이 있는 구독에 Batch 풀이 할당됩니다. 자세한 내용은 [Batch를 사용하여 대규모 병렬 계산 솔루션 개발](batch-api-basics.md)의 [계정](batch-api-basics.md#account) 섹션을 참조하세요.

- **Azure Storage 계정.** 사용자 지정 이미지를 사용하여 가상 컴퓨터 풀을 만들려면 동일한 구독 및 지역에 표준의 범용 Azure Storage 계정이 있어야 합니다. Azure VM에서 사용자 지정 이미지를 만드는 경우 VM의 OS 디스크가 상주하는 저장소 계정에 이미지를 복사하고 별도의 저장소 계정을 만들 필요가 없습니다. 
    
## <a name="prepare-a-custom-image"></a>사용자 지정 이미지 준비

Batch와 함께 사용할 사용자 지정 이미지를 준비하려면 Linux 또는 Windows의 기존 설치를 일반화해야 합니다. 운영 체제 설치를 일반화하면 컴퓨터 특정 정보가 제거되며, 결과는 다른 컴퓨터 또는 VM에 설치할 수 있는 이미지입니다.  

> [!IMPORTANT]
> Batch에서는 현재 풀을 프로비전하는 데 Azure 관리 이미지 사용을 지원하지 않습니다. 풀을 프로비전하는 데 사용하는 사용자 지정 이미지는 Azure Storage에 저장해야 합니다. 
>
> 사용자 지정 이미지를 준비할 때는 다음 사항에 유의해야 합니다.
> - Batch 풀을 프로비전하는 데 사용할 기본 OS 이미지에 사전 설치된 Azure 확장(사용자 지정 스크립트 확장)이 없는지 확인합니다. 이미지에 사전 설치된 확장이 있는 경우 Azure에서 VM 배포 시 문제가 발생할 수 있습니다.
> - Batch 노드 에이전트는 현재 기본 임시 드라이브를 예상하므로 사용자가 제공하는 기본 OS 이미지가 기본 임시 드라이브를 사용하는지 확인합니다.
>
>

모든 기존의 준비된 Windows 또는 Linux 이미지를 사용자 지정 이미지로 사용할 수 있습니다. 예를 들어 로컬 이미지를 사용하려면 [AzCopy](../storage/storage-use-azcopy.md) 또는 다른 업로드 도구를 사용하여 이미지를 Batch 계정과 동일한 구독 및 지역에 있는 Azure Storage 계정에 업로드합니다.

새로운 또는 기존 Azure VM에서 사용자 지정 이미지를 준비할 수도 있습니다. 새 VM을 만드는 경우 Azure Marketplace 이미지를 사용자 지정 이미지에 대한 기본 이미지로 사용한 후 사용자 지정할 수 있습니다. 기본 이미지를 사용자 지정하려면 Azure VM을 만들고 응용 프로그램 또는 데이터를 추가하세요. 그런 다음 사용자 지정 이미지 역할을 하도록 VM을 일반화하고 Azure Storage에 저장합니다. 

Azure VM에서 사용자 지정 이미지를 준비하려면 다음 단계를 수행합니다.

1. Azure Marketplace 이미지에서 **관리되지 않는** Azure VM을 만듭니다. Azure Marketplace에는 [Windows](../virtual-machines/windows/quick-create-portal.md) 및 [Linux](../virtual-machines/linux/quick-create-portal.md) 모두에 대한 이미지가 포함됩니다.
    
    VM 만들기 프로세스의 3단계에서 **저장소: Managed Disks 사용** 옵션으로 **아니요**를 선택했는지 확인합니다. 또한 Azure에서 저장소 계정에 사용자 지정 이미지를 저장하므로 VM의 OS 디스크의 저장소 이름을 기록해 둡니다.

    ![관리되지 않는 VM 만들기 및 저장소 계정 이름 기록](media/batch-custom-images/vm-create-storage.png)
 
2. VM을 만드는 과정을 완료하고 Azure에서 할당할 때까지 기다립니다. 다음은 Azure Portal에서 실행 상태의 VM을 보여 주는 이미지입니다.

    ![마켓플레이스 이미지에서 VM 만들기](media/batch-custom-images/vm-status-running.png)

3. VM이 실행되면 RDP(Windows용) 또는 SSH(Linux용)를 통해 연결합니다. 필요한 소프트웨어를 설치하거나 원하는 데이터를 복사한 후 VM을 일반화합니다. [VM 일반화](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sa-copy-generalized.md#generalize-the-vm)에 설명된 단계를 따릅니다. 
   
4. 단계에 따라 [Azure PowerShell에 로그인](../virtual-machines/windows/sa-copy-generalized.md#log-in-to-azure-powershell)합니다. Azure PowerShell을 설치하려면 [Azure PowerShell 개요](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.2.0)를 참조하세요. 

5. 다음으로, 단계에 따라 [VM의 할당을 취소하고 상태를 일반화됨으로 설정](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sa-copy-generalized#deallocate-the-vm-and-set-the-state-to-generalized)합니다. 

    Azure Portal에서 VM의 할당 취소를 확인합니다.

    ![VM의 할당 취소 확인](media/batch-custom-images/vm-status-deallocated.png)

6.  [Save-AzureRmVMImage](https://docs.microsoft.com/powershell/module/azurerm.compute/save-azurermvmimage) PowerShell cmdlet을 사용하여 Azure Storage에 VM 이미지를 만들고 저장합니다. [이미지 만들기](../virtual-machines/windows/sa-copy-generalized.md#create-the-image)에 설명된 지침을 따릅니다.
    
    VM 이미지는 이 절차의 1단계에서와 같이 VM을 만들 때 만든 Azure Storage 계정에 저장됩니다. Save-AzureRmVMImage cmdlet은 이미지를 해당 저장소 계정의 **system** 컨테이너에 저장합니다. `-DestinationContainername` 매개 변수 이름은 **system** 컨테이너 내에서 가상 디렉터리 이름을 지정합니다. `-VHDNamePrefix` 매개 변수는 Blob 이름에 대한 접두사를 지정합니다. 이 접두사는 하이픈으로 Blob 이름 앞에 추가됩니다. 

    예를 들어 다음 매개 변수로 Save-AzureRmVMImage를 호출한다고 가정합니다.  

        Save-AzureRmVMImage -ResourceGroupName sample-resource-group -Name vm-custom-image -DestinationContainerName batchimages -VHDNamePrefix custom -Path C:\Temp\Images\vm-custom-image.json

    결과 이미지는 여기에 표시된 위치와 Blob 이름에 저장됩니다.

    ![system 컨테이너에 저장된 VHD의 위치](media/batch-custom-images/vhd-in-vm-storage-account.png)

    > [!NOTE]
    > Azure 관리되지 않는 VM은 다양한 용도로 여러 저장소 계정을 만듭니다. VM을 만들 때 OS 디스크에 대한 저장소 컨테이너의 이름을 기록해 두지 않았다면 **system** 컨테이너를 포함하는 관련 저장소 계정을 찾습니다. **system** 컨테이너를 탐색하여 **Save-AzureRmVMImage** 명령에 대해 지정된 값을 사용하여 사용자 지정 이미지를 찾습니다.

## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>포털의 사용자 지정 이미지에서 풀 만들기

사용자 지정 이미지를 저장했고 해당 위치를 알고 있으면 해당 이미지에서 Batch 풀을 만들 수 있습니다. Azure Portal에서 풀을 만들려면 다음 단계를 수행합니다.

1. Azure Portal에서 Batch 계정으로 이동합니다. 이 계정은 사용자 지정 이미지가 포함된 저장소 계정과 동일한 구독 및 지역에 있어야 합니다. 
2. 왼쪽의 **설정** 창에서 **풀** 메뉴 항목을 선택합니다.
3. **풀** 창에서 **추가** 명령을 선택합니다.
4. **풀 추가** 창의 **이미지 형식** 드롭다운에서 **사용자 지정 이미지(Linux/Windows)**를 선택합니다. 포털에서 **사용자 지정 이미지** 선택기가 표시됩니다. 사용자 지정 이미지가 있는 저장소 계정으로 이동하고 컨테이너에서 원하는 VHD를 선택합니다. 
5. 사용자 지정 VHD에 대해 올바른 **게시자/제품/Sku**를 선택합니다.
6. **노드 크기**, **대상 전용 노드** 및 **낮은 우선 순위 노드** 등 나머지 필수 설정과 선택 사항인 설정을 원하는 대로 지정합니다.

    예를 들어 Microsoft Windows Server Datacenter 2016 사용자 지정 이미지에 대 해서는 아래 표시된 것처럼 **풀 추가** 창이 나타납니다.

    ![사용자 지정 Windows 이미지에서 풀 추가](media/batch-custom-images/add-pool-custom-image.png)
  
기존 풀이 사용자 지정 이미지를 기반으로 하는지 확인하려면 **풀** 창의 리소스 요약 섹션에 있는 **운영 체제** 속성을 참조하세요. 사용자 지정 이미지에서 풀을 만든 경우 **사용자 지정 VM 이미지**로 설정합니다.

풀과 연결된 모든 사용자 지정 VHD가 해당 풀의 **속성** 창에 표시됩니다.
 
## <a name="next-steps"></a>다음 단계

- Batch에 대한 심층적인 개요는 [Batch를 사용하여 대규모 병렬 계산 솔루션 개발](batch-api-basics.md)을 참조하세요.
- Batch 계정을 만드는 방법에 대한 자세한 내용은 [Azure Portal에서 Batch 계정 만들기](batch-account-create-portal.md)를 참조하세요.