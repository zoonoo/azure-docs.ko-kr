---
title: Azure에서 2 세대 Vm (미리 보기) | Microsoft Docs
description: Azure 2 세대 Vm 개요
services: virtual-machines-linux
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/13/2019
ms.author: lahugh
ms.openlocfilehash: 777486a64464c29e9014fdc3dbf94957b2e93aa5
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65596336"
---
# <a name="generation-2-vms-preview-on-azure"></a>Azure에서 2 세대 Vm (미리 보기)

> [!IMPORTANT]
> 2 세대 Vm은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

2 세대 가상 컴퓨터 (Vm)에 대 한 지원이 Azure에서 공개 미리 보기로 제공 됩니다. 만든 후 가상 머신의 세대를 변경할 수 없습니다. 고려 사항을 검토 하는 권장 따라서 [여기](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) 세대를 선택 하기 전에이 페이지의 정보와 합니다.

2 세대 Vm 지원에 대 한 주요 기능 같은: 향상 된 메모리, Intel® Software Guard Extensions SGX (), 및 영구 가상 메모리 (vPMEM)는 1 세대 Vm에서 지원 되지 않습니다. 2 세대 Vm에 Azure에서 아직 지원 되지 않는 일부 기능이 있습니다. 자세한 내용은 참조는 [기능과](#features-and-capabilities) 섹션입니다. 

2 세대 Vm 새 부트 UEFI 기반 아키텍처 및 1 세대 Vm에서 사용 하는 BIOS 기반 아키텍처를 사용 합니다. 1 세대 Vm에 비해 2 세대 Vm 향상 시킬 수 있습니다 부팅 및 설치 시간이 있습니다. 2 세대 Vm 개요 및 1 세대 및 2 세대 간의 주요 차이점 중 일부에 대 한 참조 [Hyper-v에 1 또는 2 세대 가상 컴퓨터를 만들 해야?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)합니다.

## <a name="generation-2-vm-sizes"></a>2 세대 VM 크기

1 세대 Vm은 Azure에서 모든 VM 크기에서 지원 됩니다. Azure는 이제 공개 미리 보기에서 선택한 다음 VM 시리즈에 대 한 2 세대 지원을 제공 합니다.

* [Dsv2](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv2-series) 고 [Dsv3 시리즈](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv3-series-1)
* [Esv3-series](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#esv3-series)
* [Fsv2-series](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-compute#fsv2-series-1)
* [GS-series](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#gs-series)
* [Ls 시리즈](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#ls-series) 고 [Lsv2 시리즈](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-storage#lsv2-series)
* [Mv2 시리즈](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#mv2-series)

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Azure Marketplace에서 2 세대 VM 이미지

2 세대 Vm에는 다음 Azure Marketplace 이미지를 지원합니다.

* Windows server 2019 데이터 센터
* Windows server 2016 데이터 센터
* Windows server 2012 R2 Datacenter
* Windows server 2012 Datacenter

## <a name="on-premises-vs-azure-generation-2-vms"></a>온-프레미스 및 Azure 2 세대 Vm

Azure 지원 하지 않습니다 현재 온-프레미스 Hyper-v 지원 2 세대 Vm에 대 한 기능 중 일부입니다.

| 2 세대 기능                | 온-프레미스 Hyper-v가 설치 | Azure |
|-------------------------------------|---------------------|-------|
| 보안 부팅                         | :heavy_check_mark:  | :x:   |
| 보호 된 VM                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| 가상화 기반 보안 (VBS) | :heavy_check_mark:  | :x:   |
| VHDX 형식                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>특징과 기능

### <a name="generation-1-vs-generation-2-features"></a>1 세대 및 2 세대 기능

| 기능 | 1세대 | 2 세대 |
|---------|--------------|--------------|
| 부팅             | PCAT                      | UEFI                               |
| 디스크 컨트롤러 | IDE                       | SCSI                               |
| VM 크기         | 모든 VM 크기에서 사용할 수 있습니다. | Premium storage 지원 Vm에만 해당 |

### <a name="generation-1-vs-generation-2-capabilities"></a>1 세대 및 2 세대 기능

| 기능 | 1세대 | 2 세대 |
|------------|--------------|--------------|
| OS 디스크 > 2TB                    | :x:                        | :heavy_check_mark: |
| 사용자 지정 디스크/이미지/Swap OS         | :heavy_check_mark:         | :heavy_check_mark: |
| 가상 머신 확장 집합 지원 | :heavy_check_mark:         | :heavy_check_mark: |
| ASR/Backup                        | :heavy_check_mark:         | :x:                |
| 공유 이미지 갤러리              | :heavy_check_mark:         | :x:                |
| Azure 디스크 암호화             | :heavy_check_mark:         | :x:                |

## <a name="creating-a-generation-2-vm"></a>만들기 2 세대 VM

### <a name="marketplace-image"></a>Marketplace 이미지

Azure portal 또는 Azure CLI를 통해 (UEFI 부팅을 지원)는 marketplace 이미지에서 2 세대 Vm은 만들 수 있습니다.

`windowsserver-gen2preview` 제품 Windows 2 세대 이미지에 대해서만 포함 되어 있습니다. 이 1 세대 및 2 세대 이미지와 관련 하 여 혼동을 방지합니다. 세대 2 Vm을 만들려면 선택 **이미지** 에서 제공 하 고 표준 VM 만들기 프로세스를 따릅니다.

현재는 다음 Windows 세대 2 이미지 게시 된 Azure Marketplace에서:

* 2019-datacenter-gen2
* 2016-datacenter-gen2
* 2012-r2-datacenter-gen2
* 2012-datacenter-gen2

계속 2 세대를 지 원하는 추가 이미지를 추가 하는 대로 지원 되는 marketplace 이미지의 목록 기능 섹션을 참조 하세요.

### <a name="managed-image-or-managed-disk"></a>관리 되는 이미지 또는 관리 디스크

1 세대 만든 동일한 방식으로 관리 되는 이미지 또는 관리 되는 디스크에서 2 개의 Vm을 만들 수 있습니다 생성 VM.

### <a name="virtual-machine-scale-sets"></a>Virtual Machine Scale Sets

세대 가상 머신 확장 집합을 사용 하 여 2 개의 Vm 만들 수도 있습니다. Azure CLI를 통해 Azure 가상 머신 확장 집합을 사용 하 여 Vm이 2 세대를 만들 수 있습니다.

## <a name="frequently-asked-questions"></a>질문과 대답

* **2 세대 Vm 가속화 된 네트워킹을 지원 하나요?**  
    예, 2 세대 Vm이 지 원하는 [가속 네트워킹](../../virtual-network/create-vm-accelerated-networking-cli.md)합니다.

* **.Vhdx는 2 세대에서 지원 됩니까?**  
    아니요,만.vhd는 2 세대 Vm에서 지원 됩니다.

* **2 세대 Vm Ultra 반도체 드라이브 (SSD)를 지원 하나요?**  
    예, 2 세대 Vm Ultra SSD를 지원합니다.

* **2 세대 Vm에 1 세대에서 마이그레이션할 수 있습니까?**  
    아니요,이 만든 후 VM의 세대를 변경할 수 없습니다. VM 세대 사이 전환 해야 할 경우 다른 세대의 새 VM을 만드는 해야 합니다.

## <a name="next-steps"></a>다음 단계

* 에 대해 자세히 알아보세요 [hyper-v에서 2 세대 가상 컴퓨터](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)합니다.