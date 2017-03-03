---
title: "고성능 Premium Storage 및 Azure VM 디스크 | Microsoft Docs"
description: "고성능 Premium Storage와 관리되지 않는 Azure VM 디스크 및 관리 Azure VM 디스크를 설명합니다. Azure DS 시리즈, DSv2 시리즈 및 GS 시리즈 VM은 프리미엄 저장소를 지원합니다."
services: storage
documentationcenter: 
author: ramankumarlive
manager: aungoo-msft
editor: tysonn
ms.assetid: e2a20625-6224-4187-8401-abadc8f1de91
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: ramankum
translationtype: Human Translation
ms.sourcegitcommit: 3a353bc874c1827f8a0fc85352894ad96cff16b5
ms.openlocfilehash: c9e43df37784999036c6cf250f27a808f79ebe2f
ms.lasthandoff: 02/10/2017


---
# <a name="high-performance-premium-storage-and-unmanaged-and-managed-azure-vm-disks"></a>고성능 Premium Storage와 관리되지 않는 Azure VM 디스크 및 관리 Azure VM 디스크
Microsoft Azure Premium Storage는 I/O 집약적인 워크로드를 실행하는 VM(가상 컴퓨터)에 대기 시간이 짧은 고성능 디스크를 지원합니다. Premium Storage를 사용하는 VM 디스크는 SSD(반도체 드라이브)에 데이터를 저장합니다. 이 디스크의 속도와 성능 혜택을 활용하여 응용 프로그램의 VM 디스크를 Azure 프리미엄 저장소로 마이그레이션할 수 있습니다.

Azure VM은 여러 개의 프리미엄 저장소 디스크 연결을 지원하기 때문에 응용 프로그램에 VM당 저장소를 64TB까지 확보할 수 있습니다. Premium Storage를 사용할 경우 읽기 작업의 대기 시간이 매우 짧은 상태에서 VM당 80,000 IOPS(초당 입/출력 작업 수) 및 VM당 디스크 처리량을 2000MB/초까지 달성할 수 있습니다.

Azure는 Premium Storage를 통해 Dynamics AX, Dynamics CRM, Exchange Server, SharePoint Farms 및 SAP Business Suite 등의 까다로운 엔터프라이즈 응용 프로그램을 클라우드로 완벽하게 리프트 앤 시프트하는 기능을 제공합니다. 일관된 고성능 및 짧은 대기 시간을 필요로 하는 SQL Server, Oracle, MongoDB, MySQL, Redis 등 다양한 성능 집약적 데이터베이스 워크로드를 Premium Storage에서 실행할 수 있습니다.

> [!NOTE]
> 응용 프로그램이 최고 성능을 낼 수 있도록 높은 IOPS가 필요한 모든 가상 컴퓨터 디스크를 Premium Storage로 마이그레이션하는 것이 좋습니다. 디스크에 높은 IOPS가 필요하지 않은 경우, 가상 컴퓨터 디스크 데이터를 SSD가 아닌 하드 디스크 드라이브(HDD)에 저자하는 표준 저장소를 사용하여 비용을 절약할 수 있습니다.
> 

Azure VM에 프리미엄 디스크를 만드는 방법은 두 가지입니다.

**관리되지 않는 디스크**: VM 디스크에 해당하는 VHD 파일을 저장하는 데 사용되는 저장소 계정을 관리하는 원래 방법입니다. VHD 파일은 저장소 계정에 페이지 Blob으로 저장됩니다. 

**[Azure Managed Disks](storage-managed-disks-overview.md)**: 이 기능은 VM 디스크에 사용되는 저장소 계정을 관리해 줍니다. 필요한 디스크의 유형(프리미엄 또는 표준)과 크기를 지정하면 Azure가 알아서 디스크를 만들고 관리해줍니다. 저장소 계정에 대한 확장성 한도 내에 머무르기 위해 다수의 저장소 계정에 디스크를 배치하려고 고심할 필요가 없습니다. Azure가 알아서 처리해 드립니다.

두 가지 유형의 디스크를 모두 사용할 수 있지만 많은 기능의 이점을 활용하려면 Managed Disks를 사용하는 것이 좋습니다.

Azure Premium Storage를 시작하려면 [무료로 시작 하기](https://azure.microsoft.com/pricing/free-trial/)를 방문하세요. 

기존 VM을 Premium Storage로 마이그레이션하는 내용은 [기존 Windows VM을 Managed Disks로 마이그레이션](../virtual-machines/virtual-machines-windows-convert-unmanaged-to-managed-disks.md) 또는 [기존 Azure Linux VM을 Managed Disks로 마이그레이션](../virtual-machines/virtual-machines-linux-convert-unmanaged-to-managed-disks.md)을 참조하세요.

> [!NOTE]
> Premium Storage는 현재 대부분의 지역에서 지원됩니다. 사용 가능 지역 목록은 [지역별 Azure 서비스](https://azure.microsoft.com/regions/#services)에서 크기-시리즈 VM(DS, DSV2, Fs 및 GS)이 지원되는 지역을 살펴보면 찾을 수 있습니다.
> 

## <a name="premium-storage-features"></a>Premium Storage 기능

Premium Storage 기능 중 일부를 살펴보겠습니다.

**Premium Storage 디스크**: Azure Premium Storage는 특정 크기-시리즈 VM(DS, DSv2, GS 및 Fs 시리즈)에 연결할 수 있는 VM 디스크를 지원합니다. P10(128GiB), P20(512GiB), P30(1024GiB)의 세 가지 디스크 크기 중에 선택할 수 있으며, 각각 성능 사양이 다릅니다. 응용 프로그램 요구 사항에 따라 이러한 디스크 중 하나 이상을 VM에 연결할 수 있습니다. 사양에 대한 자세한 내용은 [프리미엄 저장소 확장성 및 성능 목표 ](#premium-storage-scalability-and-performance-targets) 에 대한 다음 섹션에서 설명하겠습니다.

**프리미엄 페이지 Blob**: Premium Storage는 VM의 관리되지 않는 영구적인 디스크를 저장하는 데 사용되는 페이지 Blob을 지원합니다. 표준 저장소와 달리 Premium Storage는 블록 Blob, 추가 Blob, 파일, 테이블 또는 큐를 지원하지 않습니다.
프리미엄 저장소 계정에 있는 모든 개체는 페이지 Blob이 되고 지원되는 프로비전 크기 중 하나에 맞춰집니다. 이 때문에 프리미엄 저장소 계정은 매우 작은 Blob을 저장하기에는 적합하지 않습니다.

**Premium Storage 계정**: Premium Storage를 사용하려면 관리되지 않는 디스크에 대한 프리미엄 저장소 계정을 만들어야 합니다. [Azure Portal](https://portal.azure.com)을 사용하려는 경우 "프리미엄" 성능 계층 및 "LRS(로컬 중복 저장소)"를 복제 옵션으로 지정하여 프리미엄 저장소 계정을 만들 수 있습니다. 또한 [Storage REST API](/rest/api/storageservices/fileservices/Azure-Storage-Services-REST-API-Reference) 버전 2014-02-14 이상, [Service Management REST API](http://msdn.microsoft.com/library/azure/ee460799.aspx) 버전 2014-10-01 이상(클래식 배포), [Azure Storage 리소스 공급자 REST API 참조](/rest/api/storagerp)(Resource Manager 배포) 및 [Azure PowerShell](../powershell-install-configure.md) 버전 0.8.10 이상을 사용하여 유형을 “Premium_LRS”로 지정하고 프리미엄 저장소 계정을 만들 수도 있습니다. [프리미엄 저장소 확장성 및 성능 목표](#premium-storage-scalability-and-performance-targets.md)에 대한 다음 섹션에서 프리미엄 저장소 계정 제한에 대해 자세히 알아보세요.

**프리미엄 로컬 중복 저장소**: 프리미엄 저장소 계정은 복제 옵션으로 LRS(로컬 중복 저장소)만 지원하며 즉, 단일 지역 내에 데이터 복사본&3;개를 유지합니다. 프리미엄 저장소 사용 시 지역에서 복제와 관련된 고려 사항은 이 문서의 [Blob 스냅숏 생성 및 복사](#snapshots-and-copy-blob) 를 참조하세요.

Azure는 저장소 계정을 관리되지 않는 디스크의 컨테이너로 사용합니다. 관리되지 않는 디스크로 Azure DS, DSv2, GS 또는 Fs VM을 만들고 프리미엄 저장소 계정을 선택하는 경우 운영 체제와 데이터 디스크가 해당 저장소 계정에 저장됩니다.

## <a name="premium-storage-supported-vms"></a>Premium Storage 지원 VM
Premium Storage는 DS 시리즈, DSv2 시리즈, GS 시리즈 및 Fs 시리즈 VM을 지원합니다. 이러한 VM에는 표준 및 프리미엄 저장소 디스크를 모두 사용할 수 있습니다. Premium Storage와 호환되지 않는 VM 시리즈에서는 프리미엄 저장소 디스크를 사용할 수 없습니다.

사용 가능한 Azure VM 유형 및 크기에 대한 자세한 내용은 [가상 컴퓨터의 크기](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요. Linux VM용 VM 유형 및 크기에 대한 자세한 내용은 [가상 컴퓨터의 크기](../virtual-machines/virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

다음은 DS, DSv2, GS 및 Fs 시리즈 VM의 일부 기능입니다.

**클라우드 서비스**: DS 시리즈 VM만 포함하는 클라우드 서비스에 DS 시리즈 VM을 추가할 수 있습니다. DS 시리즈가 아닌 VM을 포함하는 기존 클라우드 서비스에 DS 시리즈 VM을 추가하지 마세요. DS 시리즈 VM만 실행하는 새 클라우드 서비스에 기존 VHD를 마이그레이션할 수 있습니다. DS 시리즈 VM을 호스트하는 새 클라우드 서비스에 동일한 VIP(가상 IP 주소)를 유지하려는 경우, [예약된 IP 주소](../virtual-network/virtual-networks-instance-level-public-ip.md)를 사용합니다. GS 시리즈 VM은 G 시리즈 VM만을 실행 중인 기존 클라우드 서비스에 추가될 수 있습니다.

**운영 체제 디스크**: Premium Storage에서 실행될 수 있는 VM은 프리미엄 또는 표준 운영 체제(OS) 디스크를 사용하도록 구성할 수 있습니다. 최상의 환경을 위해 Premium Storage 기반 OS 디스크를 사용하는 것이 좋습니다.

**데이터 디스크**: Premium Storage에서 실행되는 동일한 VM에 프리미엄 및 표준 디스크를 모두 사용할 수 있습니다. Premium Storage를 사용하면 VM을 프로비전하고 VM에 여러 영구 데이터 디스크를 연결할 수 있습니다. 필요한 경우, 볼륨의 성능과 용량을 늘리도록 디스크에 걸쳐 스트라이핑 할 수 있습니다.

> [!NOTE]
> [저장소 공간](http://technet.microsoft.com/library/hh831739.aspx)을 사용하여 프리미엄 저장소 데이터 디스크를 스트라이프하는 경우, 사용되는 각 디스크에 대해 하나의 열로 구성해야 합니다. 그렇지 않은 경우, 디스크에서의 고르지 못한 트래픽 분배로 스트라이프 볼륨의 전반적인 성능이 예상보다 저하될 수 있습니다. 기본적으로 서버 관리자 사용자 인터페이스(UI)를 사용하면 최대 8개의 디스크를 열로 설정할 수 있습니다. 디스크가 9개 이상인 경우, PowerShell을 사용하여 볼륨을 만들고 열 수를 수동으로 지정해야 합니다. 그렇지 않은 경우 서버 관리자 UI는 더 많은 디스크가 있더라도 8개의 열을 사용하여 계속합니다. 예를 들어 단일 스트라이프 세트에 32개의 디스크가 있다면 32개의 열을 지정해야 합니다. *New-VirtualDisk* PowerShell cmdlet의 [NumberOfColumns](http://technet.microsoft.com/library/hh848643.aspx) 매개 변수를 사용하여 가상 디스크에서 사용되는 열 수를 지정할 수 있습니다. 자세한 내용은 [저장소 공간 개요](http://technet.microsoft.com/library/hh831739.aspx) 및 [저장소 공간 질문과 대답](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx)을 참조하세요.
> 

**캐시**: Premium Storage를 지원하는 크기 시리즈의 VM에는 기초를 이루는 프리미엄 저장소 디스크 성능을 능가하는 높은 수준의 처리량 및 대기 시간을 얻을 수 있는 고유의 캐싱 기능이 있습니다. 프리미엄 저장소 디스크의 디스크 캐싱 정책은 ReadOnly, ReadWrite 또는 None으로 구성할 수 있습니다. 기본 디스크 캐싱 정책은 모든 프리미엄 데이터 디스크에 대해서는 ReadOnly이고 운영 체제 디스크에 대해서는 ReadWrite입니다. 최적의 응용 프로그램 성능을 얻으려면 올바른 구성 설정을 사용해야 합니다. 예를 들어 SQL Server 데이터 파일처럼 읽기 작업이 많거나 읽기 전용인 데이터 디스크의 경우에는 디스크 캐싱 정책을 "ReadOnly"로 설정하세요. SQL Server 로그 파일처럼 쓰기 작업이 많거나 쓰기 전용인 데이터 디스크의 경우에는 디스크 캐싱 정책을 "None"으로 설정하세요. [프리미엄 저장소를 사용한 성능을 위한 디자인](storage-premium-storage-performance.md)에서 프리미엄 저장소로 디자인을 최적화하는 자세한 방법을 알아보세요.

**분석**: Premium Storage의 디스크를 사용하여 VM 성능을 분석하려면 [Azure Portal](https://portal.azure.com)에서 VM 진단을 활성화하세요. 자세한 내용은 [Azure 진단 확장을 사용한 Microsoft Azure 가상 컴퓨터 모니터링](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/)을 참조하세요. 디스크 성능을 보려면 Windows VM용 [Windows 성능 모니터](https://technet.microsoft.com/library/cc749249.aspx) 및 Linux VM용 [IOSTAT](http://linux.die.net/man/1/iostat)와 같은 운영 체제 기반 도구를 사용하세요.

**VM 규모 한도 및 성능**: 각 Premium Storage 지원 VM 크기에는 규모 한도가 있으며 IOPS, 대역폭 및 VM당 연결할 수 있는 디스크 수에 대한 성능 사양이 있습니다. VM에 프리미엄 저장소 디스크를 사용하는 경우 VM에서 디스크 트래픽을 운용하기에 충분한 IOPS 및 대역폭을 사용할 수 있어야 합니다.
예를 들어, STANDARD_DS1 VM에는 프리미엄 저장소 디스크 트래픽에 사용할 수 있는 32MB/초의 전용 대역폭이 있습니다. P10 프리미엄 저장소 디스크는 100MB/초의 대역폭을 제공할 수 있습니다. 이 VM에 P10 프리미엄 저장소 디스크가 연결되어 있는 경우 대역폭을 32MB/초까지만 올릴 수 있고 P10 디스크가 제공할 수 있는 100MB/초까지는 올릴 수 없습니다.

현재 DS 시리즈에서 가장 큰 VM은 Standard_DS15_v2이며 모든 디스크에 최대 960MB/초를 제공할 수 있습니다. GS 시리즈에서 가장 큰 VM은 Standard_GS5이며 모든 디스크에 최대 2000MB/초를 제공할 수 있습니다.
이러한 한도는 디스크 트래픽에만 해당되며 캐시 적중 수 및 네트워크 트래픽은 포함되지 않습니다. VM 네트워크 트래픽에 사용할 수 있는 별도의 대역폭이 있으며, 프리미엄 저장소 디스크 전용 대역폭과는 다릅니다.

Premium Storage 지원 VM에 대한 최대 IOPS 및 처리량(대역폭)에 대한 최신 정보는 [Windows VM 크기](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 또는 [Linux VM 크기](../virtual-machines/virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

프리미엄 저장소 디스크와 해당 IOPS 및 처리량 한도에 대한 자세한 내용은 이 문서에서 [프리미엄 저장소 확장성 및 성능 목표](#premium-storage-scalability-and-performance-targets) 섹션의 테이블을 참조하세요.

## <a name="premium-storage-scalability-and-performance-targets"></a>Premium Storage 확장성 및 성능 목표
이 섹션에서는 프리미엄 저장소 사용 시 고려해야 하는 모든 확장성 및 성능 목표에 설명합니다.

프리미엄 저장소 계정에는 다음과 같은 확장성 목표가 있습니다.

| 총 계정 용량 | 로컬 중복 저장소 계정의 총 대역폭 |
| --- | --- | 
| 디스크 용량: 35TB <br>스냅숏 용량: 10TB | 인바운드+아웃바운드에 대해 초당 최대 50기가비트 |

* 인바운드는 저장소 계정으로 전송되는 모든 데이터(요청)를 가리킵니다.
* 아웃바운드는 저장소 계정에서 수신되는 모든 데이터(응답)를 가리킵니다.

자세한 내용은 [Azure 저장소 확장성 및 성능 목표](storage-scalability-targets.md)를 참조하세요.

관리되지 않는 디스크에 프리미엄 저장소 계정을 사용하는 경우 응용 프로그램이 단일 저장소 계정의 확장성 목표를 초과하면 Managed Disks로 마이그레이션하는 것이 좋습니다. Managed Disks로 마이그레이션하지 않으려면 응용 프로그램이 다수의 저장소 계정을 사용하도록 빌드하고 이 저장소 계정의 데이터를 분할합니다. 예를 들어 51TB(테라바이트) 디스크를 다수의 VM에 연결하려는 경우에는 단일 프리미엄 저장소 계정의 한도가 35TB이므로 두 개의 저장소 계정에 분산합니다. 단일 프리미엄 저장소 계정에 35TB를 넘는 프로비전된 디스크가 없도록 해야 합니다.

### <a name="premium-storage-disk-limits"></a>프리미엄 저장소 디스크 한도
프리미엄 저장소 디스크를 프로비전할 때 디스크의 크기가 최대 IOPS 및 처리량(대역폭)을 결정합니다. 프리미엄 저장소 디스크의 유형은 P10, P20 및 P30 이렇게 세 가지입니다. 각 디스크에는 다음 표에 지정된 대로 특정 IOPS 및 처리량 한도가 있습니다.

|프리미엄 저장소 디스크 유형 | P10 | P20 | P30 |
| --- | --- | --- | --- |
| 디스크 크기 | 128GiB | 512GiB | 1024GiB(1TB) |
| 디스크당 IOPS | 500 | 2300 | 5000 |
디스크당 처리량 | 100MB/초 | 150MB/초 | 200MB/초 |

> [!NOTE]
> 앞서 이 문서의 [Premium Storage 지원 VM](#premium-storage-supported-vms) 섹션에서 설명한 대로 VM에서 디스크 트래픽을 운용하기에 충분한 대역폭을 사용할 수 있는지 확인해야 합니다. 충분하지 않은 경우, 디스크 처리량 및 IOPS가 이전 표에서 언급된 디스크 한도보다는 VM 한도에 기반한 값보다 더 낮게 제한됩니다.  
> 
> 

다음은 프리미엄 저장소 확장성 및 성능 목표에 대해 알고 계셔야 하는 중요한 사항입니다.

* **프로비전된 용량 및 성능**: 프리미엄 저장소 디스크를 프로비전하면 표준 저장소와 달리, 해당 디스크의 용량, IOPS 및 처리량이 보장됩니다. 예를 들어 P30 디스크를 만들면 Azure에서 해당 디스크에 저장소 용량 1024GB, IOPS 5000, 처리량 200MB/초가 프로비전됩니다. 응용 프로그램에서 용량 및 성능의 전체 또는 일부를 사용할 수 있습니다.

* **디스크 크기**: 디스크 크기(올림)는 테이블에 지정되어 있는 가장 가까운 프리미엄 저장소 디스크 옵션에 매핑합니다. 예를 들어 100GiB 크기의 디스크는 P10 옵션으로 분류되어 IOPS는 500까지 처리량은 100MB/초까지 수행될 수 있습니다. 마찬가지로 400GiB 크기의 디스크는 P20으로 분류되어 IOPS는 2300까지 처리량은 150MB/초까지 수행될 수 있습니다.
  
> [!NOTE]
> 기존 디스크의 크기를 쉽게 확대할 수 있습니다. 예를 들어, 30GB 크기의 디스크를 128GB 또는 1TB로 확대할 수 있습니다. 용량이 더 필요하거나 IOPS 및 처리량이 더 필요한 경우 P20 디스크를 P30 디스크로 전환할 수 있습니다. 
> 
 
* **IO 크기**: I/O(입/출력) 단위 크기는 256KB입니다. 전송되는 데이터가 256KB 미만일 경우 하나의 I/O 단위로 간주됩니다. 더 큰 I/O 크기는 256KB 크기의 여러 I/O로 계산됩니다. 예를 들어 1100KB I/O는 I/O 단위 5개로 계산됩니다.

* **처리량**: 처리량 한도에는 디스크에 쓰기뿐 아니라 캐시에서 제공되지 않은 디스크에서 읽기도 포함됩니다. 예를 들어 P10 디스크는 디스크당 처리량이 100MB/초입니다. 다음은 P10 디스크의 유효한 처리량에 대한 예입니다.

| Max Throughput per P10 disk | Non-cache Reads from disk | Non-cache Writes to disk |
| --- | --- | --- |
| 100 MB per sec | 100 MB per sec | 0 |
| 100 MB per sec | 0 | 100 MB per sec |
| 100 MB per sec | 60 MB per sec | 40 MB per sec |

* **캐시 적중 수**: 캐시 적중 수는 디스크에 할당된 IOPS 또는 처리량의 제한을 받지 않습니다. 예를 들어 Premium Storage에서 지원되는 VM에 ReadOnly 캐시 설정이 있는 데이터 디스크를 사용하는 경우 캐시에서 제공되는 읽기는 디스크의 IOPS 및 처리 용량이 적용되지 않습니다. 따라서 워크로드가 대부분 읽기인 경우 디스크에서 상당히 높은 처리량을 얻을 수 있습니다. 캐시에는 VM 크기에 기반한 VM 수준에서 별도의 IOPS 및 처리량 한도가 적용됩니다. DS 시리즈 VM은 대략 캐시 및 로컬 SSD IO에 대해 코어당 4000 IOPS 및 33MB/초여야 합니다. GS 시리즈 VM의 캐시 및 로컬 SSD IO에 대한 제한은 코어당 5000 IOPS 및 50MB/초입니다. 

## <a name="throttling"></a>제한
응용 프로그램의 IOPS 또는 처리량이 프리미엄 저장소 디스크에 할당된 한도를 초과하는 경우 또는 VM의 모든 디스크의 디스크 트래픽이 VM에서 사용할 수 있는 디스크 대역폭 한도를 초과하는 경우 제한이 발생할 수 있습니다. 제한을 방지하려면 프로비전한 디스크의 확장성과 성능 목표 및 VM에서 사용할 수 있는 디스크 대역폭을 기반으로 디스크에 대해 보류 중인 I/O 요청 수를 제한하는 것이 좋습니다.  

응용 프로그램이 제한을 방지하도록 설계된 경우 가장 낮은 대기 시간을 얻을 수 있습니다. 반면, 디스크에 대해 보류 중인 I/O 요청 수가 너무 작으면 응용 프로그램이 디스크에서 사용할 수 있는 최대 IOPS 및 처리량 수준을 활용할 수 없습니다.

다음 예제에서는 제한 수준을 계산하는 방법을 보여 줍니다. 모든 계산은 256KB의 I/O 단위 크기를 기반으로 합니다.

### <a name="example-1"></a>예제 1:
응용 프로그램이 P10 디스크에서 초당 16KB 크기의 495개 I/O를 수행했습니다. 이는 IOPS(초당 495 I/O 단위)로 계산됩니다. 동일한 초에 2MB I/O를 시도하면 총 I/O 단위 수는 495+8이 됩니다. 이는 I/O 단위 크기가 256KB일 경우 2MB I/O에서 2048KB/256KB = 8개 I/O 단위가 나오기 때문입니다. 495+8의 합계가 디스크 한도인 500개 IOPS를 초과하므로 제한이 발생합니다.

### <a name="example-2"></a>예 2:
응용 프로그램이 P10 디스크에서 256KB 크기의 400개 I/O를 수행했습니다. 소비되는 총 대역폭은 (400*256)/1024=100MB/초입니다. P10 디스크의 처리량 제한은 초당 100MB입니다. 응용 프로그램이 해당 초에 추가 I/O를 수행하려고 하면 할당된 한도를 초과하므로 제한됩니다.

### <a name="example-3"></a>예 3:
연결된 두 P30 디스크와 DS4 VM이 있습니다. 각 P30 디스크는 처리량이 초당 200MB입니다. 하지만 DS4 VM은 초당 256MB의 총 디스크 대역폭 용량입니다. 따라서 동시에 이 DS4 VM에서 최대 처리량으로 연결된 디스크를 운용할 수 없습니다. 이를 해결하려면 한 디스크에서는 초당 200MB, 다른 디스크에서는 초당 56MB의 트래픽을 유지할 수 있습니다. 디스크 트래픽의 합계는 초당 256MB 이상으로 이동하며 해당 트래픽이 제한됩니다.

> [!NOTE]
> 디스크 트래픽이 대체로 작은 I/O 크기로 구성된 경우 응용 프로그램이 처리량 한도 전에 IOPS 한도에 도달할 가능성이 큽니다. 반면, 디스크 트래픽이 대체로 큰 I/O 크기로 구성된 경우에는 응용 프로그램이 IOPS 한도 대신 처리량 한도에 도달할 가능성이 큽니다. 최적 I/O 크기를 사용하고 디스크에 대해 보류 중인 I/O 요청 수를 제한하여 응용 프로그램의 IOPS 및 처리량을 최대화할 수 있습니다.
> 

프리미엄 저장소를 사용한 고성능 설계에 대한 자세한 내용은 [프리미엄 저장소를 사용한 성능을 위한 디자인](storage-premium-storage-performance.md)을 참조하세요.

## <a name="snapshots-and-copy-blob"></a>스냅숏 및 Blob 복사

Storage 서비스에서 VHD 파일은 페이지 Blob입니다. 페이지 Blob의 스냅숏을 만들어서 다른 위치(예: 다른 저장소 계정)에 복사할 수 있습니다.

### <a name="unmanaged-disks"></a>관리되지 않는 디스크

관리되지 않는 프리미엄 디스크에 대한 [증분 스냅숏](storage-incremental-snapshots.md)은 표준 저장소에서 스냅숏을 사용하는 것과 같은 방식으로 만들 수 있습니다. 프리미엄 저장소는 복제 옵션으로 LRS(로컬 중복 저장소)만 지원하므로 스냅숏을 만든 후 지역 중복 표준 저장소 계정에 이러한 스냅숏을 복사하는 것이 좋습니다. 자세한 내용은 [Azure 저장소 중복 옵션](storage-redundancy.md)을 참조하세요.

디스크가 VM에 연결되어 있으면 특정 API 작업이 디스크에 허용되지 않습니다. 예를 들어 디스크가 VM에 연결되어 있는 한 해당 Blob에 대해 [Blob 복사](/rest/api/storageservices/fileservices/Copy-Blob) 작업을 수행할 수 없습니다. 대신, 먼저 [Blob 스냅숏 생성](/rest/api/storageservices/fileservices/Snapshot-Blob) REST API 메서드를 사용하여 해당 Blob의 스냅숏을 만든 후 스냅숏의 [Blob 복사](/rest/api/storageservices/fileservices/Copy-Blob)를 수행하여 연결된 디스크를 복사합니다. 또는 디스크를 분리한 후 필요한 작업을 수행할 수 있습니다.

프리미엄 저장소 Blob 스냅숏에 다음 한도가 적용됩니다.

| 프리미엄 저장소 제한 | 값 |
| --- | --- |
| 최대 Blob당 스냅숏 수 | 100 |
| 스냅숏의 저장소 계정 용량(스냅숏에만 데이터를 포함하고 기본 Blob에는 데이터를 포함하지 않음) | 10TB |
| 최소 연속 스냅숏 사이의 시간 | 10분 |

스냅숏의 지역 중복 복사본을 유지하려면 AzCopy 또는 Blob 복사를 사용하여 프리미엄 저장소 계정에서 지역 중복 표준 저장소 계정으로 스냅숏을 복사할 수 있습니다. 자세한 내용은 [AzCopy 명령줄 유틸리티로 데이터 전송](storage-use-azcopy.md) 및 [Blob 복사](/rest/api/storageservices/fileservices/Copy-Blob)를 참조하세요.

프리미엄 저장소 계정에서 페이지 Blob에 대한 REST 작업을 수행하는 방법에 대한 자세한 내용은 [Azure Premium Storage로 Blob Service 작업 사용](http://go.microsoft.com/fwlink/?LinkId=521969)을 참조하세요.

### <a name="managed-disks"></a>Managed Disks

관리 디스크에 대한 스냅숏은 관리 디스크의 읽기 전용 복사본이며 표준 관리 디스크로 저장됩니다. [증분 스냅숏](storage-incremental-snapshots.md)은 현재 Managed Disks에 지원되지 않지만 향후 지원될 예정입니다. 관리 디스크에 대한 스냅숏을 만드는 방법을 알아보려면 [Windows에서 관리 스냅숏을 사용하여 Azure Managed Disk로 저장된 VHD 복사본 만들기](../virtual-machines/virtual-machines-windows-snapshot-copy-managed-disk.md) 또는 [Linux에서 관리 스냅숏을 사용하여 Azure Managed Disk로 저장된 VHD 복사본 만들기](../virtual-machines/linux/virtual-machines-linux-snapshot-copy-managed-disk.md)를 참조하세요.

관리 디스크가 VM에 연결되어 있으면 특정 API 작업이 디스크에 허용되지 않습니다. 예를 들어 디스크가 VM에 연결되어 있는 동안 공유 액세스 서명(SAS)을 생성하여 복사 작업을 수행할 수 없습니다. 대신 디스크 스냅숏을 먼저 만든 다음 스냅샷 복사를 수행합니다. 또는 디스크를 분리한 다음 공유 액세스 서명(SAS)을 생성하여 복사 작업을 수행할 수 있습니다.


## <a name="using-linux-vms-with-premium-storage"></a>프리미엄 저장소와 Linux VM 사용
Premium Storage에서 Linux VM을 구성하려면 아래의 중요 지침을 참조하세요.

* 캐시 설정이 “ReadOnly” 또는 “None”으로 설정된 모든 프리미엄 저장소 디스크에 대해서는 Premium Storage의 확장성 목표를 달성하기 위해 파일 시스템을 탑재하는 동안 “barrier”를 사용하지 않도록 설정해야 합니다. 프리미엄 저장소 디스크에 쓰기는 이러한 캐시 설정에 대해 지속되기 때문에 이 시나리오에 대한 barrier가 필요하지 않습니다. 쓰기 요청이 성공적으로 완료되면 데이터는 영구 저장소에 작성됩니다. “barrier”를 사용하지 않도록 설정하는 방법은 다음 메서드 중에서 파일 시스템에 대한 항목을 선택하여 사용하십시오.
  
* **reiserFS**를 사용하는 경우 탑재 옵션 “barrier=none”을 사용하여 장벽을 사용하지 않도록 설정(장벽 사용의 경우 “barrier=flush” 사용)
* **ext3/ext4**를 사용하는 경우 탑재 옵션 “barrier=0”을 사용하여 장벽을 사용하지 않도록 설정(장벽 사용의 경우 “barrier=1” 사용)
* **XFS**를 사용하는 경우 탑재 옵션 “nobarrier”를 사용하여 장벽을 사용하지 않도록 설정(장벽 사용의 경우 “barrier” 사용)
* “ReadWrite”으로 캐시가 설정된 프리미엄 저장소 디스크의 경우 쓰기 지속성을 위해 barrier가 설정되어야 합니다.
* 볼륨 레이블의 경우 VM을 다시 부팅한 후 유지하려면 디스크에 UUID 참조로 /etc/fstab을 업데이트해야 합니다. [관리 디스크를 Linux VM에 추가](../virtual-machines/virtual-machines-linux-add-disk.md)를 참조하세요.

다음은 프리미엄 저장소로 유효성을 검사한 Linux 배포판입니다. 프리미엄 저장소 사용 시 더 나은 성능 및 확장성을 위해 이러한 버전 이상으로 VM을 업그레이드하는 것이 좋습니다. 또한 버전 중 일부는 Microsoft Azure용 최신 LIS(Linux 통합 서비스) v4.0이 필요합니다. 다운로드 및 설치를 위해 아래 제공된 링크를 따르십시오. 추가 유효성 검사가 완료되면 목록에 대한 자세한 이미지 추가가 계속됩니다. 유효성 검사는 이러한 이미지에 따라 다른 성능을 보여주었으며 해당 이미지의 워크로드 특성 및 설정에 따라서도 달라집니다. 다른 종류의 워크로드에 대해 서로 다른 이미지가 조정됩니다.

| 배포 | 버전 | 지원되는 커널 | 세부 정보 |
| --- | --- | --- | --- |
| Ubuntu | 12.04 | 3.2.0-75.110+ | Ubuntu-12_04_5-LTS-amd64-server-20150119-en-us-30GB |
| Ubuntu | 14.04 | 3.13.0-44.73+ | Ubuntu-14_04_1-LTS-amd64-server-20150123-en-us-30GB |
| Debian | 7.x, 8.x | 3.16.7-ckt4-1+ | &nbsp; |
| SUSE | SLES 12| 3.12.36-38.1+| suse-sles-12-priority-v20150213 <br> suse-sles-12-v20150213 |
| SUSE | SLES 11 SP4 | 3.0.101-0.63.1+ | &nbsp; |
| CoreOS | 584.0.0+| 3.18.4+ | CoreOS 584.0.0 |
| CentOS | 6.5, 6.6, 6.7, 7.0 | &nbsp; | [LIS4 필요](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *아래 참조* |
| CentOS | 7.1+ | 3.10.0-229.1.2.el7+ | [LIS4 권장](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *아래 참조* |
| RHEL | 6.8+, 7.2+ | &nbsp; | &nbsp; |
| Oracle | 6.0+, 7.2+ | &nbsp; | UEK4 또는 RHCK |
| Oracle | 7.0-7.1 | &nbsp; | UEK4 또는 RHCK w/[LIS 4.1+](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |
| Oracle | 6.4-6.7 | &nbsp; | UEK4 또는 RHCK w/[LIS 4.1+](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |


### <a name="lis-drivers-for-openlogic-centos"></a>Openlogic CentOS용 LIS 드라이버

OpenLogic CentOS VM을 실행하는 고객은 다음 명령을 실행하여 최신 드라이버를 설치해야 합니다.

```
sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
sudo yum install microsoft-hyper-v
```

새 드라이버를 활성화하려면 다시 부팅해야 합니다.

## <a name="pricing-and-billing"></a>가격 책정 및 대금 청구

프리미엄 저장소를 사용하는 경우 다음과 같은 청구 고려 사항이 적용됩니다.

* 프리미엄 저장소 디스크/Blob 크기
* 프리미엄 저장소 스냅숏
* 아웃바운드 데이터 전송

**프리미엄 저장소 디스크/Blob 크기**: 프리미엄 저장소 디스크/Blob에 대한 청구는 프로비전된 디스크/Blob 크기에 따라 달라집니다. Azure는 [Premium Storage 사용 시 확장성 및 성능 목표](#premium-storage-scalability-and-performance-targets) 섹션의 테이블에 지정된 대로 프로비전된 크기(올림)를 가장 가까운 프리미엄 저장소 디스크 옵션에 매핑합니다. 각 디스크는 지원되는 프로비전된 크기 중 하나로 매핑되며 이에 따라 요금이 청구됩니다. 프로비전된 디스크에 대한 청구는 프리미엄 저장소 제품의 월별 가격을 사용하여 시간당 비례합니다. 예를 들어 P10 디스크를 프로비전하고 20시간 후 삭제한 경우 20시간에 비례하여 P10 제품에 대해 청구됩니다. 이는 디스크에 기록되는 실제 데이터 양이나 사용한 IOPS/처리량에 관계없이 적용됩니다.

**프리미엄 관리되지 않는 디스크 스냅숏**: 프리미엄 관리되지 않는 디스크의 스냅숏은 스냅숏에 사용된 추가 용량이 청구됩니다. 스냅숏에 대한 자세한 내용은 [Blob의 스냅숏 만들기](/rest/api/storageservices/fileservices/Snapshot-Blob)를 참조하세요.

**프리미엄 관리 디스크 스냅숏**: 관리 디스크의 스냅숏은 디스크의 읽기 전용 복사본이며 표준 관리 디스크로 저장됩니다. 스냅숏 비용은 표준 관리 디스크와 동일하게 책정됩니다. 예를 들어 128GB 프리미엄 관리 디스크의 스냅숏을 만드는 경우 스냅숏 비용은 128GB 표준 관리 디스크와 동일합니다.  

**아웃바운드 데이터 전송**: [아웃바운드 데이터 전송](https://azure.microsoft.com/pricing/details/data-transfers/) (Azure 데이터 센터에서 데이터 전송) 시 대역폭 사용에 대해 청구가 발생합니다.

Premium Storage에 대한 가격 책정, Premium Storage 지원 VM 및 Managed Disks에 대해 자세히 알아보려면 다음을 참조하세요.

* [Azure 저장소 가격](https://azure.microsoft.com/pricing/details/storage/)
* [가상 컴퓨터 가격](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Managed Disks 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks/)

## <a name="azure-backup-service-support"></a>Azure Backup 서비스 지원 

관리되지 않는 디스크가 포함된 가상 컴퓨터는 Azure Backup을 사용하여 백업할 수 있습니다. [자세한 내용](../backup/backup-azure-vms-first-look-arm.md).

Azure Backup 서비스를 Managed Disks와 함께 사용하면 시간 기반 백업, 손쉬운 VM 복원 및 백업 보존 정책을 사용하여 백업 작업을 만들 수도 있습니다. 자세한 내용은 [Managed Disks로 VM에 Azure Backup 서비스 사용](../backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup)을 참조하세요. 

## <a name="next-steps"></a>다음 단계
Azure 프리미엄 저장소에 대한 자세한 내용은 다음 문서를 참조하세요.

### <a name="design-and-implement-with-azure-premium-storage"></a>Azure 프리미엄 저장소를 사용하여 디자인 및 구현
* [프리미엄 저장소를 사용한 성능을 위한 디자인](storage-premium-storage-performance.md)
* [Azure 프리미엄 저장소와 함께 Blob 서비스 작업 사용](http://go.microsoft.com/fwlink/?LinkId=521969)

### <a name="operational-guidance"></a>운영 가이드
* [Azure 프리미엄 저장소로 마이그레이션](storage-migration-to-premium-storage.md)

### <a name="blog-posts"></a>블로그 게시물
* [일반적으로 제공되는 Azure 프리미엄 저장소](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/)
* [GS 시리즈 발표: 공용 클라우드의 최대 VM에 프리미엄 저장소 지원 추가](https://azure.microsoft.com/blog/azure-has-the-most-powerful-vms-in-the-public-cloud/)
