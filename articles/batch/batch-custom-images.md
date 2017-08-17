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
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 1248aeeaa159789b008eb003c2cd7babe0432919
ms.contentlocale: ko-kr
ms.lasthandoff: 08/09/2017

---

# <a name="use-a-custom-image-to-create-a-pool"></a>사용자 지정 이미지를 사용하여 풀 만들기

Azure Batch에서 풀을 만들 경우 풀에서 각 계산 노드에 대해 운영 체제 구성을 제공하는 가상 컴퓨터(VM) 이미지를 지정합니다. Azure Marketplace 이미지를 사용하거나 사용자가 준비한 사용자 지정 이미지를 제공하여 풀을 만들 수 있습니다. 사용자 지정 이미지를 제공할 경우 각 계산 노드가 프로비전되는 시점에서 운영 체제가 구성되는 방식을 제어할 수 있습니다. 사용자 지정 이미지에는 프로비전되는 즉시, 계산 노드에서 사용 가능한 응용 프로그램 및 참조 데이터도 포함할 수 있습니다.

사용자 지정 이미지를 사용하면 풀의 계산 노드가 Batch 워크로드를 실행할 준비를 하는 데 걸리는 시간을 절약할 수 있습니다. 프로비전된 후에는 항상 Azure Marketplace 이미지를 사용하고 각 계산 노드에 소프트웨어를 설치할 수 있지만, 이러한 접근 방식은 사용자 지정 이미지를 사용하는 것보다 효율성이 떨어질 수 있습니다. 큰 응용 프로그램을 설치하거나 많은 양의 데이터를 복사하거나 설치 프로세스 중 VM을 다시 부팅해야 한다면 사용자의 요구 사항에 맞게 구성된 사용자 지정 이미지를 사용하는 것을 고려하세요.  

## <a name="prerequisites"></a>필수 조건

- **사용자 구독 풀 할당 모드로 만든 Batch 계정.** 가상 컴퓨터 풀을 프로비전하는 데 사용자 지정 이미지를 사용하려면 사용자 구독 [풀 할당 모드](batch-api-basics.md#pool-allocation-mode)로 Batch 계정을 만듭니다. 이 모드를 사용하면 해당 계정이 있는 구독에 Batch 풀이 할당됩니다. Batch 계정을 만들 때 풀 할당 모드를 설정하는 방법에 대한 자세한 내용은 [Batch를 사용하여 대규모 병렬 계산 솔루션 개발](batch-api-basics.md)의 [계정](batch-api-basics.md#account) 섹션을 참조하세요.

- **Azure Storage 계정.** 사용자 지정 이미지를 사용하여 가상 컴퓨터 구성 풀을 만들려면 사용자 지정 VHD 이미지를 저장하기 위한 표준 Azure Storage 계정이 하나 이상 있어야 합니다. 사용자 지정 이미지는 Blob으로 저장됩니다. 풀을 만들 때 사용자 지정 이미지를 참조하려면 [virtualMachineConfiguration](https://docs.microsoft.com/rest/api/batchservice/add-a-pool-to-an-account#bk_vmconf) 속성의 [osDisk](https://docs.microsoft.com/rest/api/batchservice/add-a-pool-to-an-account#bk_osdisk) 속성에 대해 사용자 지정 이미지 VHD Blob의 URI를 지정합니다.

    저장소 계정이 다음 조건을 충족하는지 확인합니다.   
    
    - 사용자 지정 이미지 VHD Blob이 있는 저장소 계정은 Batch 계정(사용자 구독)과 동일한 구독에 있어야 합니다.
    - 지정된 저장소 계정은 Batch 계정과 동일한 지역에 있어야 합니다.
    - 현재 표준 범용 저장소 계정만 지원됩니다. Azure 프리미엄 저장소는 나중에 지원됩니다.
    - 사용자 지정 VHD Blob이 여러 개 있는 단일 저장소 계정을 지정하거나 Blob 하나만 있는 저장소 계정을 여러 개 지정할 수 있습니다. 더 나은 성능을 얻으려면 저장소 계정을 여러 개 사용하는 것이 좋습니다.
    - 고유한 사용자 지정 이미지 VHD Blob마다 최대 40개의 Linux VM 인스턴스 또는 20개의 Windows VM 인스턴스를 지원할 수 있습니다. 더 많은 VM을 포함하는 풀을 만들려면 VHD Blob 복사본을 만들어야 합니다. 예를 들어 200개 Windows VM이 있는 풀의 경우 **osDisk** 속성에 고유한 10개의 VHD Blob을 지정해야 합니다.
    
## <a name="prepare-a-custom-image"></a>사용자 지정 이미지 준비

Batch와 함께 사용할 사용자 지정 이미지를 준비하려면 Linux 또는 Windows의 기존 설치를 일반화해야 합니다. 운영 체제 설치를 일반화하면 컴퓨터 특정 정보가 제거되며, 결과는 다른 컴퓨터 또는 VM에 설치할 수 있는 이미지입니다.  

Azure Marketplace 이미지를 사용자 지정 이미지에 대한 기본 이미지로 사용할 수 있습니다. 기본 이미지를 사용자 지정하려면 Azure VM을 만들고 응용 프로그램 또는 데이터를 추가하세요. 그런 다음 사용자 지정 이미지 역할을 하도록 VM을 일반화합니다.   

Azure VM에서 사용자 지정 Linux 이미지를 준비하는 방법에 대한 자세한 내용은 [가상 컴퓨터 또는 VHD의 이미지를 만드는 방법](../virtual-machines/linux/capture-image.md)을 참조하세요. 

Azure VM에서 사용자 지정 Windows 이미지 준비에 대한 자세한 내용은 [Azure PowerShell을 사용하여 사용자 지정 VM 이미지 만들기](../virtual-machines/windows/tutorial-custom-images.md) 및 [Sysprep(시스템 준비) 개요](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)를 참조하세요. 

> [!IMPORTANT]
> 사용자 지정 이미지를 준비할 때는 다음 사항에 유의해야 합니다.
> - Batch 풀을 프로비전하는 데 사용할 기본 OS 이미지에 사전 설치된 Azure 확장(사용자 지정 스크립트 확장)이 없는지 확인합니다. 이미지에 사전 설치된 확장이 있는 경우 Azure에서 VM 배포 시 문제가 발생할 수 있습니다.
> - Batch 노드 에이전트는 현재 기본 임시 드라이브를 예상하므로 사용자가 제공하는 기본 OS 이미지가 기본 임시 드라이브를 사용하는지 확인합니다.
>
>
    
## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>포털의 사용자 지정 이미지에서 풀 만들기

Azure Portal을 사용하여 사용자 지정 이미지에서 풀을 만들려면 다음을 수행합니다.

1. Azure Portal에서 Batch 계정으로 이동합니다.
2. **설정** 블레이드에서 **풀** 메뉴 항목을 선택합니다.
3. **풀** 블레이드에서 **추가** 명령을 선택합니다. 그러면 **풀 추가** 블레이드가 표시됩니다.
4. **이미지 유형** 드롭다운에서 **사용자 지정 이미지(Linux/Windows)**를 선택합니다. 포털에서 **사용자 지정 이미지** 선택기가 표시됩니다. 동일한 컨테이너에서 VHD를 하나 이상 선택하고 **선택** 단추를 클릭합니다. 
   향후 릴리스에는 다양한 저장소 계정 및 컨테이너에서 VHD를 지원할 예정입니다.
5. 사용자 지정 VHD에 대해 올바른 **게시자/제품/SKU**를 선택하고, 원하는 **캐싱** 모드를 선택한 다음, 풀에 대한 다른 모든 매개 변수를 입력합니다.
6. 풀이 사용자 지정 이미지를 기반으로 하는지 확인하려면 **풀** 블레이드의 리소스 요약 섹션에 있는 **운영 체제** 속성을 참조하세요. 이 속성의 값은 **사용자 지정 VM 이미지**입니다.
7. 풀과 연결된 모든 사용자 지정 VHD가 해당 풀의 **속성** 블레이드에 표시됩니다.
 
## <a name="next-steps"></a>다음 단계

- Batch에 대한 심층적인 개요는 [Batch를 사용하여 대규모 병렬 계산 솔루션 개발](batch-api-basics.md)을 참조하세요.
- Batch 계정을 만드는 방법에 대한 자세한 내용은 [Azure Portal에서 Batch 계정 만들기](batch-account-create-portal.md)를 참조하세요.
