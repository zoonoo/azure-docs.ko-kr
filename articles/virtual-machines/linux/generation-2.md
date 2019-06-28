---
title: 2 세대 Vm (미리 보기)에 대 한 azure 지원 | Microsoft Docs
description: 2 세대 Vm에 대 한 Azure 지원 개요
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
ms.date: 05/23/2019
ms.author: lahugh
ms.openlocfilehash: 352df275742c38307065252d2f65bb4253d78e5d
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67151267"
---
# <a name="support-for-generation-2-vms-preview-on-azure"></a>Azure에서 2 개의 Vm (미리 보기)을 생성에 대 한 지원

> [!IMPORTANT]
> Azure 2 세대 Vm에 대 한 지원은 현재 미리 보기로 제공에서 됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공 하 고 프로덕션 워크 로드에 대 한 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure 미리 보기에 대 한 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)합니다. 

2 세대 가상 컴퓨터 (Vm)에 대 한 지원이 Azure에서 미리 보기로 제공 됩니다. 따라서 고려 사항을 검토 합니다 페이지 세대를 선택 하기 전에 만든 후 가상 머신의 세대를 변경할 수 없습니다 하 합니다. 

2 세대 Vm 1 세대 Vm에서 지원 되지 않는 주요 기능을 지원 합니다. 이러한 기능 향상 된 메모리를 Intel 소프트웨어 Guard Extensions (Intel SGX) 등이 영구 메모리 (vPMEM)를 가상화 합니다. 2 세대 Vm에는 또한 Azure에서 아직 지원 되지 않는 일부 기능이 있으며 자세한 내용은 참조는 [기능과](#features-and-capabilities) 섹션입니다.

2 세대 Vm 1 Vm 세대에서 사용 된 BIOS 기반 아키텍처 대신 새 부트 UEFI 기반 아키텍처를 사용 합니다. 1 세대 Vm에 비해 2 세대 Vm 수를 개선 부팅 및 설치 시간이. 2 세대 Vm 개요 및 1 세대와 2 세대 간의 차이점 중 일부에 대 한 참조 [Hyper-v에 1 또는 2 세대 가상 컴퓨터를 만들 해야?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)합니다.

## <a name="generation-2-vm-sizes"></a>2 세대 VM 크기

1 세대 Vm은 Azure에서 모든 VM 크기에서 지원 됩니다. Azure는 이제 다음 선택한 VM 시리즈에 대 한 2 세대 지원 미리 보기를 제공합니다.

* [Dsv2 시리즈](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv2-series) 고 [Dsv3 시리즈](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv3-series-1)
* [Esv3-series](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#esv3-series)
* [Fsv2-series](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-compute#fsv2-series-1)
* [GS-series](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#gs-series)
* [Ls 시리즈](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#ls-series) 고 [Lsv2 시리즈](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-storage#lsv2-series)
* [Mv2 시리즈](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#mv2-series)

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Azure Marketplace에서 2 세대 VM 이미지

2 세대 Vm에는 다음 Marketplace 이미지를 지원합니다.

* Windows Server 2019 Datacenter
* Windows Server 2016 Datacenter
* Windows Server 2012 R2 Datacenter
* Windows Server 2012 Datacenter

## <a name="on-premises-vs-azure-generation-2-vms"></a>온-프레미스 vs입니다. Azure 2 세대 Vm

현재 azure는 온-프레미스 Hyper-v 지원 2 세대 Vm에 대 한 기능 중 일부를 지원 하지 않습니다.

| 2 세대 기능                | 온-프레미스 Hyper-v가 설치 | Azure |
|-------------------------------------|---------------------|-------|
| 보안 부팅                         | :heavy_check_mark:  | :x:   |
| 보호 된 VM                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| 가상화 기반 보안 (VBS) | :heavy_check_mark:  | :x:   |
| VHDX 형식                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>특징과 기능

### <a name="generation-1-vs-generation-2-features"></a>1 세대와 2 세대 기능 비교

| 기능 | 1세대 | 2 세대 |
|---------|--------------|--------------|
| 부팅             | PCAT                      | UEFI                               |
| 디스크 컨트롤러 | IDE                       | SCSI                               |
| VM 크기         | 모든 VM 크기 | Premium storage 지원 Vm만 |

### <a name="generation-1-vs-generation-2-capabilities"></a>1 세대와 2 세대 기능 비교

| 기능 | 1세대 | 2 세대 |
|------------|--------------|--------------|
| OS 디스크 > 2TB                    | :x:                        | :heavy_check_mark: |
| 사용자 지정 디스크/이미지/swap OS         | :heavy_check_mark:         | :heavy_check_mark: |
| 가상 머신 확장 집합 지원 | :heavy_check_mark:         | :heavy_check_mark: |
| ASR/backup                        | :heavy_check_mark:         | :x:                |
| 공유 이미지 갤러리              | :heavy_check_mark:         | :x:                |
| Azure Disk Encryption             | :heavy_check_mark:         | :x:                |

## <a name="creating-a-generation-2-vm"></a>만들기 2 세대 VM

### <a name="marketplace-image"></a>Marketplace 이미지

Azure portal 또는 Azure CLI에서 만들 수 있습니다 세대 2 Vm UEFI 부팅을 지 원하는 Marketplace 이미지에서.

`windowsserver-gen2preview` 제품 Windows 2 세대 이미지에 대해서만 포함 되어 있습니다. 이 패키징 1 세대 및 2 세대 이미지에 대 한 혼동을 방지할 수 있습니다. 2 세대를 만들려면 VM을 선택 **이미지** 에서 제공 하 고 VM을 만드는 표준 프로세스를 따릅니다.

현재 Marketplace는 다음 Windows 생성을 2 이미지 제공합니다.

* 2019-datacenter-gen2
* 2016-datacenter-gen2
* 2012-r2-datacenter-gen2
* 2012-datacenter-gen2

참조 된 [기능과](#features-and-capabilities) 지원 되는 Marketplace 이미지의 최신 목록에 대 한 섹션입니다.

### <a name="managed-image-or-managed-disk"></a>관리 되는 이미지 또는 관리 디스크

2 세대를 만들면 관리 되는 이미지 또는 1 세대 만든 동일한 방식으로 관리 되는 디스크에서 VM VM.

### <a name="virtual-machine-scale-sets"></a>가상 머신 크기 집합

작업을 가상 머신 확장 집합을 사용 하 여 2 개의 Vm 생성으로 만들 수도 있습니다. Azure CLI를 사용 하 여 Azure 확장 세대 2 Vm 만들기를 설정 합니다.

## <a name="frequently-asked-questions"></a>질문과 대답

* **모든 Azure 지역에서 사용할 수 있는 2 개의 Vm 세대를 인가요?**  
    예. 전부는 아니지만 [2 세대 VM 크기](#generation-2-vm-sizes) 모든 지역에서 사용할 수 있습니다. 세대 2 VM은 VM 크기의 가용성에 따라 달라 집니다의 가용성입니다.

* **사이 차이가 있는 가격은 1 세대와 2 개의 Vm?**  
    아니요.

* **OS 디스크 크기를 늘리려면 어떻게 해야 합니까?**  
  2TB 보다 큰 OS 디스크는 새로운 2 세대 Vm입니다. 기본적으로 OS 디스크는 2 세대 Vm에 대 한 2TB 보다 작습니다. 권장 되는 최대 4TB의 디스크 크기를 늘릴 수 있습니다. Azure CLI 또는 Azure portal을 사용 하 여 OS 디스크 크기를 늘립니다. 프로그래밍 방식으로 디스크를 확장 하는 방법에 대 한 정보를 참조 하세요 [디스크 크기 조정](expand-disks.md)합니다.

  Azure portal에서 OS 디스크 크기를 늘리려면:

  1. Azure portal에서 VM 속성 페이지로 이동 합니다.
  1. 종료 하 고 VM을 할당 취소를 선택 합니다 **중지** 단추입니다.
  1. 에 **디스크** 섹션을 증가 시킬 OS 디스크를 선택 합니다.
  1. 에 **디스크** 섹션에서 **구성**, 및 업데이트를 **크기** 원하는 값으로.
  1. VM 속성 페이지로 이동 하 고 **시작** VM.

  2TB 보다 큰 OS 디스크에 대 한 경고를 볼 수 있습니다. 경고는 2 세대 Vm에 적용 되지 않습니다. 그러나 4TB 보다 큰 OS 디스크 크기는 *권장 되지 않습니다.*

* **세대 2 Vm 지원 가속화 된 네트워킹?**  
    예. 자세한 내용은 [가속화 된 네트워킹을 사용 하 여 VM 만들기](../../virtual-network/create-vm-accelerated-networking-cli.md)합니다.

* **VHDX는 2 세대에서 지원 됩니까?**  
    아니요, 2 세대 Vm VHD만 지원합니다.

* **2 세대 Vm Azure Ultra 디스크 저장소를 지원 하나요?**  
    예.

* **마이그레이션할 수는 VM 1 세대에서 2 세대로?**  
    아니요, 만든 후 VM의 세대를 변경할 수 없습니다. VM 세대 사이 전환 해야 할 경우 다른 세대의 새 VM을 만듭니다.

## <a name="next-steps"></a>다음 단계

* 에 대 한 자세한 [hyper-v에서 2 세대 가상 컴퓨터](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)합니다.
