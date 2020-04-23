---
title: 2세대 VM에 대한 Azure 지원
description: 2세대 VM에 대한 Azure 지원 개요
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/11/2020
ms.author: jushiman
ms.openlocfilehash: 53806a1a627c7ae6bc3470aa387be0982df914fa
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082261"
---
# <a name="support-for-generation-2-vms-on-azure"></a>Azure에서 2세대 VM 지원

이제 Azure에서 2세대 가상 시스템(VM)에 대한 지원을 사용할 수 있습니다. 가상 컴퓨터를 만든 후에는 가상 컴퓨터의 생성을 변경할 수 없으므로 세대를 선택하기 전에 이 페이지의 고려 사항을 검토합니다.

2세대 VM은 1세대 VM에서 지원되지 않는 주요 기능을 지원합니다. 이러한 기능에는 증가된 메모리, 인텔 소프트웨어 가드 확장(인텔 SGX), 가상화된 영구 메모리(vPMEM)가 포함됩니다. 온-프레미스에서 실행되는 2세대 VM에는 아직 Azure에서 지원되지 않는 몇 가지 기능이 있습니다. 자세한 내용은 기능 [및 기능](#features-and-capabilities) 섹션을 참조하세요.

2세대 VM은 1세대 VM에서 사용하는 BIOS 기반 아키텍처대신 새로운 UEFI 기반 부팅 아키텍처를 사용합니다. 1세대 VM과 비교하여 2세대 VM은 부팅 및 설치 시간이 향상되었을 수 있습니다. 2세대 VM에 대한 개요와 1세대와 2세대의 차이점 중 일부에 대한 자세한 [내용은 Hyper-V에서 1세대 또는 2세대 가상 컴퓨터를 만들어야 합니까?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

## <a name="generation-2-vm-sizes"></a>2세대 VM 크기

1세대 VM은 Azure의 모든 VM 크기에서 지원됩니다(Mv2 시리즈 VM 제외). Azure는 이제 선택한 다음 VM 시리즈에 대한 2세대 지원을 제공합니다.

* [B 시리즈](https://docs.microsoft.com/azure/virtual-machines/windows/b-series-burstable)
* [DCsv2 시리즈](../dcv2-series.md)
* [DSv2 시리즈](../dv2-dsv2-series.md) 및 [Dsv3 시리즈](../dv3-dsv3-series.md)
* [Dasv4 시리즈](https://docs.microsoft.com/azure/virtual-machines/dav4-dasv4-series)
* [에스브3 시리즈](../ev3-esv3-series.md)
* [Easv4 시리즈](https://docs.microsoft.com/azure/virtual-machines/eav4-easv4-series)
* [Fsv2 시리즈](../fsv2-series.md)
* [GS 시리즈](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#gs-series)
* [HB 시리즈](../hb-series.md)
* [HC 시리즈](../hc-series.md)
* [Ls 시리즈](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#ls-series) 및 [Lsv2 시리즈](../lsv2-series.md)
* [Mv2 시리즈](../mv2-series.md)
* [NCv2 시리즈](../ncv2-series.md) 및 [NCv3 시리즈](../ncv3-series.md)
* [ND 시리즈](../nd-series.md)
* [NVv3 시리즈](../nvv3-series.md)

> [!NOTE]
> Mv2 시리즈는 2세대 VM 이미지만 사용할 수 있기 때문에 Mv2 시리즈 VM용 2세대 VM 이미지의 사용은 일반적으로 사용할 수 있습니다. Mv2 시리즈 VM에서 1세대 VM 이미지는 지원되지 않습니다. 

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Azure 마켓플레이스의 2세대 VM 이미지

2세대 VM은 다음 마켓플레이스 이미지를 지원합니다.

* Windows Server 2019, 2016, 2012 R2, 2012
* Windows 10 Pro, Windows 10 Enterprise
* 수지 리눅스 엔터프라이즈 서버 15 SP1
* 수지 리눅스 엔터프라이즈 서버 12 SP4
* 우분투 서버 16.04, 18.04, 19.04, 19.10 
* RHEL 8.1, 8.0, 7.7, 7.6, 7.5, 7.4, 7.0
* 센트 OS 8.1, 8.0, 7.7, 7.6, 7.5, 7.4
* 오라클 리눅스 7.7, 7.7-CI

## <a name="on-premises-vs-azure-generation-2-vms"></a>온-프레미스 vs. Azure 세대 2 VM

Azure는 현재 온-프레미스 Hyper-V가 2세대 VM에 지원하는 일부 기능을 지원하지 않습니다.

| 2세대 기능                | 온-프레미스 Hyper-V | Azure |
|-------------------------------------|---------------------|-------|
| 보안 부팅                         | :heavy_check_mark:  | :x:   |
| 보호된 VM                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| 가상화 기반 보안(VBS) | :heavy_check_mark:  | :x:   |
| VHDX 형식                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>기능 및 특성

### <a name="generation-1-vs-generation-2-features"></a>1세대 대 2 세대 기능

| 기능 | 1세대 | 2세대 |
|---------|--------------|--------------|
| 부팅             | PCAT                      | UEFI                               |
| 디스크 컨트롤러 | IDE                       | SCSI                               |
| VM 크기         | 모든 VM 크기 | 프리미엄 스토리지를 지원하는 VM만 |

### <a name="generation-1-vs-generation-2-capabilities"></a>1세대 대 2 세대 기능

| 기능 | 1세대 | 2세대 |
|------------|--------------|--------------|
| OS 디스크 > 2TB                    | :x:                | :heavy_check_mark: |
| 사용자 지정 디스크/이미지/스왑 OS         | :heavy_check_mark: | :heavy_check_mark: |
| 가상 시스템 스케일 세트 지원 | :heavy_check_mark: | :heavy_check_mark: |
| Azure Site Recovery               | :heavy_check_mark: | :heavy_check_mark: |
| 백업/복원                    | :heavy_check_mark: | :heavy_check_mark: |
| 공유 이미지 갤러리              | :heavy_check_mark: | :heavy_check_mark: |
| Azure Disk Encryption             | :heavy_check_mark: | :x:                |

## <a name="creating-a-generation-2-vm"></a>2세대 VM 만들기

### <a name="marketplace-image"></a>마켓플레이스 이미지

Azure 포털 또는 Azure CLI에서 UEFI 부팅을 지원하는 마켓플레이스 이미지에서 2세대 VM을 만들 수 있습니다.

#### <a name="azure-portal"></a>Azure portal

다음은 Azure 포털에서 세대 2(Gen2) VM을 만드는 단계입니다.

1. [https://portal.azure.com](https://portal.azure.com ) 에서 Azure Portal에 로그인합니다.
1. **리소스 만들기**를 선택합니다.
1. 왼쪽의 Azure 마켓플레이스에서 **모두 보기를** 클릭합니다.
1. Gen2를 지원하는 이미지를 선택합니다.
1. **만들기**를 클릭합니다.
1. **고급** 탭에서 **VM 생성** 섹션에서 Gen **2** 옵션을 선택합니다.
1. **기본** 탭에서 인스턴스 **세부 정보**에서 **크기로** 이동 하여 VM 크기 블레이드 **선택을 엽니다.**
1. [지원되는 세대 2 VM을](#generation-2-vm-sizes)선택합니다.
1. [Azure 포털 만들기 흐름을](quick-create-portal.md) 통해 VM 만들기를 완료합니다.

![1세대 또는 2세대 VM 선택](./media/generation-2/gen1-gen2-select.png)

#### <a name="powershell"></a>PowerShell

PowerShell을 사용하여 1세대 또는 2세대 SKU를 직접 참조하여 VM을 만들 수도 있습니다.

예를 들어 다음 PowerShell cmdlet을 사용하여 `WindowsServer` 오퍼의 SCO 목록을 가져옵니다.

```powershell
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```

또는 Azure CLI를 사용하여 **게시자**에 의해 나열된 사용 가능한 2세대 이미지를 볼 수 있습니다.

```azurecli
az vm image list --publisher Canonical --sku gen2 --output table --all
```

Windows Server 2012를 OS로 만드는 경우 다음과 같이 보이는 1세대(BIOS) 또는 2세대(UEFI) VM SKU를 선택합니다.

```powershell
2012-Datacenter
2012-datacenter-gensecond
```

지원되는 마켓플레이스 이미지의 현재 목록은 [기능 및 기능](#features-and-capabilities) 섹션을 참조하세요.

### <a name="managed-image-or-managed-disk"></a>관리되는 이미지 또는 관리 디스크

1세대 VM을 만드는 것과 동일한 방식으로 관리되는 이미지 또는 관리디스크에서 2세대 VM을 만들 수 있습니다.

### <a name="virtual-machine-scale-sets"></a>가상 머신 크기 집합

가상 시스템 규모 집합을 사용하여 생성 2 VM을 만들 수도 있습니다. Azure CLI에서 Azure 배율 집합을 사용하여 2세대 VM을 만듭니다.

## <a name="frequently-asked-questions"></a>자주 묻는 질문

* **모든 Azure 리전에서 2세대 VM을 사용할 수 있습니까?**  
    예. 그러나 모든 지역에서 모든 [세대 2 VM 크기를](#generation-2-vm-sizes) 사용할 수 있는 것은 아닙니다. 2세대 VM의 가용성은 VM 크기의 가용성에 따라 달라집니다.

* **1세대와 2세대 VM 사이에 가격 차이가 있습니까?**  
   아니요.

* **내 온 -프레미스 세대 2 VM에서 .vhd 파일이 있습니다. Azure에서 해당 .vhd 파일을 사용하여 2세대 VM을 만들 수 있습니까?**
  예. 세대 2 .vhd 파일을 Azure로 가져와서 2세대 VM을 만드는 데 사용할 수 있습니다. 다음 단계를 사용하여 다음 단계를 수행합니다.
    1. .vhd를 VM을 만들려는 동일한 지역의 저장소 계정에 업로드합니다.
    1. .vhd 파일에서 관리디스크를 만듭니다. 하이퍼-V 생성 속성을 V2로 설정합니다. 다음 PowerShell 명령은 관리 디스크를 만들 때 Hyper-V Generation 속성을 설정합니다.

        ```powershell
        $sourceUri = 'https://xyzstorage.blob.core.windows.net/vhd/abcd.vhd'. #<Provide location to your uploaded .vhd file>
        $osDiskName = 'gen2Diskfrmgenvhd'  #<Provide a name for your disk>
        $diskconfig = New-AzDiskConfig -Location '<location>' -DiskSizeGB 127 -AccountType Standard_LRS -OsType Windows -HyperVGeneration "V2" -SourceUri $sourceUri -CreateOption 'Import'
        New-AzDisk -DiskName $osDiskName -ResourceGroupName '<Your Resource Group>' -Disk $diskconfig
        ```

    1. 디스크를 사용할 수 있게 되면 이 디스크를 연결하여 VM을 만듭니다. 생성된 VM은 2세대 VM이 됩니다.
    2세대 VM을 만들 때 선택적으로 이 VM의 이미지를 일반화할 수 있습니다. 이미지를 일반화하여 여러 VM을 만들 수 있습니다.

* **OS 디스크 크기를 늘리는 방법은 무엇입니까?**  
  2TB보다 큰 OS 디스크는 2세대 VM에 새로 들어가지 않습니다. 기본적으로 OS 디스크는 2세대 VM의 경우 2TB보다 작습니다. 디스크 크기를 권장 최대 4TB까지 늘릴 수 있습니다. Azure CLI 또는 Azure 포털을 사용하여 OS 디스크 크기를 늘립니다. 디스크를 프로그래밍 방식으로 확장하는 방법에 대한 자세한 내용은 [디스크 크기 조정을](expand-os-disk.md)참조하십시오.

  Azure 포털에서 OS 디스크 크기를 늘리려면 다음을 수행합니다.

  1. Azure 포털에서 VM 속성 페이지로 이동합니다.
  1. VM을 종료하고 할당 해제하려면 **중지** 단추를 선택합니다.
  1. **디스크** 섹션에서 늘리려는 OS 디스크를 선택합니다.
  1. **디스크** 섹션에서 **구성을**선택하고 **크기를** 원하는 값으로 업데이트합니다.
  1. VM 속성 페이지로 돌아가VM **시작합니다.**
  
  2TB보다 큰 OS 디스크에 대한 경고가 표시될 수 있습니다. 경고는 2세대 VM에는 적용되지 않습니다. 그러나 4TB보다 큰 OS 디스크 크기는 *권장되지 않습니다.*

* **2세대 VM이 빠른 네트워킹을 지원합니까?**  
    예. 자세한 내용은 [빠른 네트워킹을 사용하여 VM 만들기를](../../virtual-network/create-vm-accelerated-networking-cli.md)참조하십시오.

* **VHDX는 2세대에서 지원되고 있습니까?**  
    아니요, 2세대 VM은 VHD만 지원합니다.

* **2세대 VM이 Azure 울트라 디스크 스토리지를 지원합니까?**  
    예.

* **VM을 1세대에서 2세대로 마이그레이션할 수 있습니까?**  
    아니요, VM을 만든 후에는 VM의 생성을 변경할 수 없습니다. VM 세대 간에 전환해야 하는 경우 다른 세대의 새 VM을 만듭니다.

* **Gen2 VM을 만들려고 할 때 크기 선택기에서 VM 크기를 사용하지 않는 이유는 무엇입니까?**

    이 문제는 다음을 수행하여 해결할 수 있습니다.

    1. **고급** 탭에서 **VM 생성** 속성이 **Gen 2로** 설정되어 있는지 확인합니다.
    1. [Gen2 VM을 지원하는 VM 크기를](#generation-2-vm-sizes)검색하고 있는지 확인합니다.

## <a name="next-steps"></a>다음 단계

* [하이퍼-V.](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

* 온-프레미스 시스템에서 Azure로 업로드할 [VHD를 준비하는](prepare-for-upload-vhd-image.md) 방법에 대해 알아봅니다.
