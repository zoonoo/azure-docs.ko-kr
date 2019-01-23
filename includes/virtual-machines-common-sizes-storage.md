---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 07/06/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: b4de9efbe85d5ab497bccd1742df23ddc1b3af43
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54354662"
---
스토리지 최적화 VM 크기는 높은 디스크 처리량 및 IO를 제공하며 빅 데이터, SQL, NoSQL 데이터베이스, 데이터 웨어하우징 및 대형 트랜잭션 데이터베이스에 이상적입니다.  예를 들어 Cassandra, MongoDB, Cloudera 및 Redis가 있습니다. 이 문서에서는 각 최적화 크기에 대한 로컬 스토리지 처리량 및 네트워크 대역폭뿐 아니라 vCPU, 데이터 디스크 및 NIC 수에 대한 정보를 제공합니다.

Lsv2 시리즈는 처리량이 많고, 대기 시간이 짧으며, 모든 코어 부스트가 2.55GHz이고 최대 부스트가 3.0GHz인 [AMD EPYC&trade; 7551 프로세서](https://www.amd.com/en/products/epyc-7000-series)에서 실행되는 직접 매핑된 로컬 NVMe 스토리지가 특징입니다. Lsv2 시리즈 VM은 동시 다중 스레딩 구성을 통해 8~80vCPU 크기로 제공됩니다.  vCPU당 8GiB 메모리, 8vCPU당 1.92TB NVMe SSD M.2 디바이스 한 대가 있으며, L80s v2에서 최대 19.2TB(10x1.92TB)를 사용할 수 있습니다.

Ls 시리즈는 [Intel® Xeon® 프로세서 E5 v3 제품군](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html)을 사용하여 최대 32개의 vCPU를 제공합니다. Ls 시리즈는 G/GS 시리즈와 CPU 성능이 동일하며 vCPU당 8GiB 메모리가 제공됩니다.

> [!NOTE]
> Lsv2 시리즈 VM은 지속형 데이터 디스크를 사용하는 대신 VM에 직접 연결된 노드의 로컬 디스크를 사용하도록 최적화되었습니다.  이렇게 하면 워크로드에 대한 IOPS/처리량이 향상됩니다.  Lsv2 및 Ls 시리즈는 로컬 캐시를 만들어 지속형 데이터 디스크에서 달성할 수 있는 IOPS를 늘리는 작업을 지원하지 않습니다. 로컬 디스크의 처리량 및 IOPS가 높으면 단일 VM의 오류가 발생한 경우 지속성을 달성하기 위해 여러 VM 간에 데이터를 복제하는 Apache Cassandra 및 MongoDB와 같은 NoSQL 저장소에 Lsv2 및 Ls 시리즈 VM을 사용하는 것이 이상적입니다. 

## <a name="lsv2-series"></a>Lsv2 시리즈
ACU: 150-175

Premium Storage: 지원됨

Premium Storage 캐싱: 지원되지 않음

| 크기          | vCPU | 메모리(GiB) | 임시 디스크<sup>1</sup>(GiB) | NVMe 디스크 | NVMe 디스크 처리량<sup>2</sup>(IOPS/MBps 읽기) | 호스트 캐시 크기<sup>3</sup> | 최대 데이터 디스크 수 | 최대 NIC 수 / 예상 네트워크 대역폭(Mbps) | 
|---------------|-----------|-------------|--------------------------|----------------|---------------------------------------------------|-------------------------------------------|------------------------------|------------------------------| 
| Standard_L8s_v2   |  8 |  64 |  80 |  1x1.92TB  | 340,000/2,000 | 해당 없음 | 16 | 2/3,200  | 
| Standard_L16s_v2  | 16 | 128 | 160 |  2x1.92TB  | 680,000/4,500 | 해당 없음 | 32 | 4/6,400  | 
| Standard_L32s_v2  | 32 | 256 | 320 |  4x1.92TB  | 1.4M/9,000    | 해당 없음 | 32 | 8/12,800 | 
| Standard_L64s_v2  | 64 | 512 | 640 |  8x1.92TB  | 2.7M/18,000   | 해당 없음 | 32 | 8/25,600 |
| Standard_L80s_v2  | 80 | 640 | 800 | 10x1.92TB   | 3.4M/22,000   | 해당 없음 | 32 | 8 / 32,000 |
 
<sup>1</sup> Lsv2 시리즈 VM에는 OS 페이징/스왑 파일용 표준 SCSI 기반 임시 리소스 디스크가 있습니다(Windows의 D:, Linux의 /dev/sdb). 이 디스크는 모든 8 vCPU에 대해 80GiB 스토리지, 4,000 IOPS 및 80MBps 전송 속도를 제공합니다(예: Standard_L80s_v2는 40,000 IOPS 및 800MBPS에서 800GiB 제공). 이 경우 NVMe 드라이브는 완전히 애플리케이션 전용일 수 있습니다.

<sup>2</sup> Hyper-V NVMe Direct 기술은 게스트 VM 공간에 안전하게 매핑된 NVMe 드라이브에 대한 무제한 액세스를 제공합니다.  최상의 성능을 위해 Azure Marketplace에서 최신 WS2019 빌드나 Ubuntu 18.04 또는 16.04를 사용해야 합니다.  쓰기 성능은 IO 크기, 드라이브 로드 및 용량 사용률에 따라 달라집니다.

<sup>3</sup> Lsv2 시리즈 VM은 Lsv2 워크로드에 도움이 되지 않으므로 데이터 디스크에 대한 호스트 캐시를 제공하지 않습니다.  그러나 Lsv2 VM은 Azure의 임시 VM OS 디스크 옵션(최대 30GiB)을 수용할 수 있습니다. 



## <a name="ls-series"></a>Ls 시리즈
ACU: 180-240

Premium Storage:  지원됨

Premium Storage 캐싱:  지원되지 않음
 
| 크기          | vCPU | 메모리(GiB) | 임시 스토리지(GiB) | 최대 데이터 디스크 수 | 최대 임시 스토리지 처리량(IOPS/MBps) | 최대 캐시되지 않은 디스크 처리량(IOPS/MBps) | 최대 NIC 수 / 예상 네트워크 대역폭(Mbps) | 
|----------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4  | 32  | 678   | 16 | 20,000 / 200 | 5,000/125  | 2 / 4,000  | 
| Standard_L8s   | 8  | 64  | 1,388 | 32 | 40,000 / 400 | 10,000/250 | 4 / 8,000  | 
| Standard_L16s  | 16 | 128 | 2,807 | 64 | 80,000 / 800 | 20,000/500 | 8 / 16,000 | 
| Standard_L32s <sup>1</sup> | 32   | 256  | 5,630 | 64   | 160,000 / 1,600   | 40,000/1,000     | 8 / 20,000 | 
 

Ls 시리즈 VM에서 가능한 최대 디스크 처리량은 연결된 디스크 수, 크기 및 스트라이핑에 따라 제한될 수 있습니다. 자세한 내용은 [Premium Storage: Azure 가상 머신 워크로드를 위한 고성능 스토리지](../articles/virtual-machines/windows/premium-storage.md)를 참조하세요.

<sup>1</sup> 인스턴스는 단일 고객 전용의 하드웨어에 격리되어 있습니다.

## <a name="size-table-definitions"></a>크기 테이블 정의

- 저장소 용량 단위는 GiB(1024^3바이트) 단위로 표시됩니다. GB(1000^3바이트) 단위로 측정된 디스크를 GiB(1024^3바이트) 단위로 측정된 디스크와 비교할 때는 GiB 단위의 용량 수치가 더 작게 표시될 수 있음에 유의해야 합니다. 예를 들어 1023GiB = 1098.4GB입니다.
- 디스크 처리량은 IOPS(초당 입/출력 작업 수) 및 MBps로 측정되며, MBps = 10^6바이트/초입니다.
- VM에서 최상의 성능을 얻으려면 데이터 디스크 수를 vCPU 당 2개의 디스크로 제한해야 합니다.
- **예상 네트워크 대역폭**은 모든 NIC에서 모든 대상에 대해 [VM 유형별로 할당된 최대 집계 대역폭](../articles/virtual-network/virtual-machine-network-throughput.md)입니다. 상한값은 보장되지 않지만 원하는 애플리케이션에 적합한 VM 유형을 선택하기 위한 지침을 제공하기 위한 것입니다. 실제 네트워크 성능은 네트워크 정체, 애플리케이션 부하 및 네트워크 설정을 포함한 다양한 요인에 따라 달라집니다. 네트워크 처리량 최적화에 대한 내용은 [Windows 및 Linux에 대한 네트워크 처리량 최적화](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md)를 참조하세요. Linux 또는 Windows에 대한 예상 네트워크 성능을 달성하려면 특정 버전을 선택하거나 VM을 최적화해야 할 수도 있습니다. 자세한 내용은 [가상 머신 처리량을 안정적으로 테스트하는 방법](../articles/virtual-network/virtual-network-bandwidth-testing.md)을 참조하세요.
