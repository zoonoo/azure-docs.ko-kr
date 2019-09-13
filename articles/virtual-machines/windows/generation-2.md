---
title: 2 세대 Vm에 대 한 Azure 지원 (미리 보기) | Microsoft Docs
description: 2 세대 Vm에 대 한 Azure 지원 개요
services: virtual-machines-windows
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/10/2019
ms.author: lahugh
ms.openlocfilehash: 5e342418dc6cc9ed0a3bbbfaad42801d5ffe9e9d
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70900250"
---
# <a name="support-for-generation-2-vms-preview-on-azure"></a>Azure에서 2 세대 Vm (미리 보기)에 대 한 지원

> [!IMPORTANT]
> 2 세대 Vm에 대 한 Azure 지원은 현재 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure 미리 보기의 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조 하세요.

이제 Azure의 미리 보기에서 2 세대 Vm (가상 머신)에 대 한 지원이 제공 됩니다. 가상 컴퓨터를 만든 후에는 가상 컴퓨터의 세대를 변경할 수 없으므로 세대를 선택 하기 전에이 페이지에 대 한 고려 사항을 검토 하세요. 

2 세대 Vm은 1 세대 Vm에서 지원 되지 않는 주요 기능을 지원 합니다. 이러한 기능에는 메모리 증가, intel SGX (Software Guard Extensions) 및 vPMEM (가상화 된 영구 메모리)가 포함 됩니다. 또한 2 세대 Vm에는 아직 Azure에서 지원 되지 않는 몇 가지 기능이 있습니다. 자세한 내용은 [기능 및](#features-and-capabilities) 기능 섹션을 참조 하세요.

2 세대 Vm은 1 세대 Vm에서 사용 하는 BIOS 기반 아키텍처가 아닌 새로운 UEFI 기반 부팅 아키텍처를 사용 합니다. 1 세대 Vm과 비교할 때 2 세대 Vm은 부팅 및 설치 시간을 향상 시킬 수 있습니다. 2 세대 Vm의 개요와 1 세대와 2 세대 간의 차이점에 대 한 개요는 [hyper-v에서 1 세대 또는 2 세대 가상 머신을 만들어야 하나요?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)를 참조 하세요.

## <a name="generation-2-vm-sizes"></a>2 세대 VM 크기

1 세대 Vm은 Azure의 모든 VM 크기에서 지원 됩니다. 이제 Azure는 다음과 같은 선택한 VM 시리즈에 대 한 미리 보기 2 세대 지원을 제공 합니다.

* [B 시리즈](https://docs.microsoft.com/azure/virtual-machines/windows/b-series-burstable)
* [DC 시리즈](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dc-series)
* [Dsv2 시리즈](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv2-series) 및 [Dsv3 시리즈](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)
* [Esv3 시리즈](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#esv3-series)
* [Fsv2 시리즈](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-compute#fsv2-series-1)
* [GS-series](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#gs-series)
* [HB-시리즈](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#hb-series)
* [HC 시리즈](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#hc-series)
* [Ls 시리즈](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#ls-series) 및 [Lsv2 시리즈](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-storage#lsv2-series)
* [Mv2 시리즈](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#mv2-series)
* [NCv2 시리즈](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#ncv2-series) 및 [NCv3 시리즈](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#ncv3-series)
* [ND 시리즈](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#nd-series)
* [NVv2 시리즈](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#nvv3-series--1)

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Azure Marketplace의 2 세대 VM 이미지

2 세대 Vm은 다음 Marketplace 이미지를 지원 합니다.

* Windows Server 2019 Datacenter
* Windows Server 2016 Datacenter
* Windows Server 2012 R2 Datacenter
* Windows Server 2012 Datacenter
* SUSE Linux Enterprise Server 15 SP1
* SUSE Linux Enterprise Server 12 SP4

## <a name="on-premises-vs-azure-generation-2-vms"></a>온-프레미스 및 Azure 2 세대 Vm

Azure는 현재 온-프레미스 Hyper-v에서 2 세대 Vm에 대해 지 원하는 기능 중 일부를 지원 하지 않습니다.

| 2 세대 기능                | 온-프레미스 Hyper-v | Azure |
|-------------------------------------|---------------------|-------|
| 보안 부팅                         | :heavy_check_mark:  | :x:   |
| 차폐 VM                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| VBS (가상화 기반 보안) | :heavy_check_mark:  | :x:   |
| VHDX 형식                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>특징과 기능

### <a name="generation-1-vs-generation-2-features"></a>1 세대와 2 세대의 기능 비교

| 기능 | 1세대 | 2 세대 |
|---------|--------------|--------------|
| 부팅             | PCAT                      | UEFI                               |
| 디스크 컨트롤러 | IDE                       | SCSI                               |
| VM 크기         | 모든 VM 크기 | Premium storage를 지 원하는 Vm만 |

### <a name="generation-1-vs-generation-2-capabilities"></a>1 세대와 2 세대의 기능 비교

| 기능 | 1세대 | 2 세대 |
|------------|--------------|--------------|
| OS 디스크 > 2tb                    | :x:                        | :heavy_check_mark: |
| 사용자 지정 디스크/이미지/스왑 OS         | :heavy_check_mark:         | :heavy_check_mark: |
| 가상 머신 확장 집합 지원 | :heavy_check_mark:         | :heavy_check_mark: |
| ASR/backup                        | :heavy_check_mark:         | :x:                |
| 공유 이미지 갤러리              | :heavy_check_mark:         | :x:                |
| Azure Disk Encryption             | :heavy_check_mark:         | :x:                |

## <a name="creating-a-generation-2-vm"></a>2 세대 VM 만들기

### <a name="marketplace-image"></a>Marketplace 이미지

Azure Portal 또는 Azure CLI에서는 UEFI 부팅을 지 원하는 Marketplace 이미지에서 2 세대 Vm을 만들 수 있습니다.

이 `windowsserver-gen2preview` 제품에는 Windows 2 세대 이미지만 포함 되어 있습니다. 이 패키징은 1 세대와 2 세대 이미지 간의 혼동을 방지 합니다. 2 세대 VM을 만들려면이 제품에서 **이미지** 를 선택 하 고 표준 프로세스에 따라 vm을 만듭니다.

현재 Marketplace는 다음과 같은 Windows 생성 2 이미지를 제공 합니다.

* 2019-datacenter-gen2
* 2016-datacenter-gen2
* 2012-r2-datacenter-gen2
* 2012-datacenter-gen2

지원 되는 Marketplace 이미지의 최신 목록은 [기능 및 기능](#features-and-capabilities) 섹션을 참조 하세요.

### <a name="managed-image-or-managed-disk"></a>관리 되는 이미지 또는 관리 디스크

1 세대 VM을 만드는 것과 같은 방법으로 관리 되는 이미지 또는 관리 디스크에서 2 세대 VM을 만들 수 있습니다.

### <a name="virtual-machine-scale-sets"></a>Virtual Machine Scale Sets

또한 가상 머신 확장 집합을 사용 하 여 2 세대 Vm을 만들 수 있습니다. Azure CLI에서 Azure 확장 집합을 사용 하 여 2 세대 Vm을 만듭니다.

## <a name="frequently-asked-questions"></a>질문과 대답

* **모든 Azure 지역에서 2 세대 Vm을 사용할 수 있나요?**  
    예. 하지만 모든 지역에서 [2 세대 VM 크기](#generation-2-vm-sizes) 를 사용할 수 있는 것은 아닙니다. 2 세대 VM의 가용성은 VM 크기의 가용성에 따라 달라 집니다.

* **1 세대 및 2 세대 Vm 간에 가격 차이가 있나요?**  
   아니요.

* **온-프레미스 2 세대 VM의 .vhd 파일이 있습니다. 이 .vhd 파일을 사용 하 여 Azure에서 2 세대 VM을 만들 수 있나요?**
  예, 2 세대 .vhd 파일을 Azure로 가져와서이를 사용 하 여 2 세대 VM을 만들 수 있습니다. 이렇게 하려면 다음 단계를 사용 합니다.
    1. VM을 만들려는 동일한 지역의 저장소 계정에 .vhd를 업로드 합니다.
    1. .Vhd 파일에서 관리 디스크를 만듭니다. HyperV 생성 속성을 V2로 설정 합니다. 다음 PowerShell 명령은 관리 디스크를 만들 때 HyperV 생성 속성을 설정 합니다.

        ```powershell
        $sourceUri = 'https://xyzstorage.blob.core.windows.net/vhd/abcd.vhd'. #<Provide location to your uploaded .vhd file>
        $osDiskName = 'gen2Diskfrmgenvhd'  #<Provide a name for your disk>
        $diskconfig = New-AzDiskConfig -Location '<location>' -DiskSizeGB 127 -AccountType Standard_LRS -OsType Windows -HyperVGeneration "V2" -SourceUri $sourceUri -CreateOption 'Import'
        New-AzDisk -DiskName $osDiskName -ResourceGroupName '<Your Resource Group>' -Disk $diskconfig
        ```

    1. 디스크를 사용할 수 있게 되 면이 디스크를 연결 하 여 VM을 만듭니다. 만든 VM은 2 세대 VM이 됩니다.
    2 세대 VM을 만들 때 필요에 따라이 VM의 이미지를 일반화할 수 있습니다. 이미지를 일반화 하 여 여러 Vm을 만드는 데 사용할 수 있습니다.

* **OS 디스크 크기를 늘릴 어떻게 할까요? 있나요?**  
  2tb 보다 큰 OS 디스크는 2 세대 Vm에 대 한 새로운 것입니다. 기본적으로 OS 디스크는 2 세대 Vm의 경우 2tb 보다 작습니다. 최대 4 TB까지 디스크 크기를 늘릴 수 있습니다. Azure CLI 또는 Azure Portal를 사용 하 여 OS 디스크 크기를 늘립니다. 디스크를 프로그래밍 방식으로 확장 하는 방법에 대 한 자세한 내용은 [디스크 크기 조정](expand-os-disk.md)을 참조 하세요.

  Azure Portal에서 OS 디스크 크기를 늘리려면 다음을 수행 하십시오.

  1. Azure Portal에서 VM 속성 페이지로 이동 합니다.
  1. VM을 종료 하 고 할당을 취소 하려면 **중지** 단추를 선택 합니다.
  1. **디스크** 섹션에서 증가 시킬 OS 디스크를 선택 합니다.
  1. **디스크** 섹션에서 **구성**을 선택 하 고 **크기** 를 원하는 값으로 업데이트 합니다.
  1. VM 속성 페이지로 돌아가서 VM을 **시작** 합니다.
  
  2tb 보다 큰 OS 디스크에 대 한 경고가 표시 될 수 있습니다. 이 경고는 2 세대 Vm에는 적용 되지 않습니다. 그러나 4 TB 보다 큰 OS 디스크 크기는 *권장 되지 않습니다.*

* **2 세대 Vm은 가속화 네트워킹을 지원 하나요?**  
    예. 자세한 내용은 [가속화 된 네트워킹을 사용 하 여 VM 만들기](../../virtual-network/create-vm-accelerated-networking-cli.md)를 참조 하세요.

* **VHDX는 2 세대에서 지원 되나요?**  
    아니요, 2 세대 Vm은 VHD만 지원 합니다.

* **2 세대 Vm은 Azure Ultra 디스크 저장소을 지원 하나요?**  
    예.

* **1 세대에서 2 세대로 VM을 마이그레이션할 수 있나요?**  
    아니요, VM을 만든 후에는 VM의 세대를 변경할 수 없습니다. VM 세대 간을 전환 해야 하는 경우 다른 세대의 새 VM을 만듭니다.

## <a name="next-steps"></a>다음 단계

* [Hyper-v의 2 세대 가상 컴퓨터](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)에 대해 알아봅니다.

* 온-프레미스 시스템에서 Azure로 업로드할 [VHD를 준비](prepare-for-upload-vhd-image.md) 하는 방법을 알아봅니다.
