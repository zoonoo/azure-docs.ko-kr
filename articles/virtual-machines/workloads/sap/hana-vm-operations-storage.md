---
title: Azure 가상 머신 저장소 구성 SAP HANA | Microsoft Docs
description: SAP HANA 배포 된 VM에 대 한 저장소 권장 사항입니다.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/27/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 26994c3488feb5f2c1522960ba4d2664bdbc80f4
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707467"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>SAP HANA Azure 가상 머신 스토리지 구성

Azure는 SAP HANA를 실행 하는 Azure Vm에 적합 한 다양 한 유형의 저장소를 제공 합니다. **SAP HANA 인증 된 Azure storage 유형은** 다음과 같은 SAP HANA 배포 목록에 대해 고려할 수 있습니다. 

- Azure 프리미엄 SSD  
- [Ultra disk](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

이러한 디스크 유형에 대 한 자세한 내용은 [디스크 유형 선택](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types) 문서를 참조 하세요.

Azure는 Azure Standard 및 Premium Storage에서 VHD를 위한 두 가지 배포 방법을 제공합니다. 전반적인 시나리오에서 가능하다면 [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) 배포를 활용하세요. 

IOPS 및 스토리지 처리량에서 스토리지 유형 및 해당 SLA의 목록을 보려면 [관리 디스크에 대한 Azure 설명서](https://azure.microsoft.com/pricing/details/managed-disks/)를 참조하세요.

서로 다른 저장소 형식에 대 한 최소 SAP HANA의 인증 된 조건은 다음과 같습니다. 

- Azure/hana/log는 Azure [쓰기 가속기](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)를 사용 하 여 캐시 해야 합니다. 프리미엄 SSD /Hana/data 볼륨은 Azure 쓰기 가속기 또는 Ultra disk 없이 프리미엄 SSD에 배치 될 수 있습니다.
- 최소/hana/log 볼륨에 대 한 Azure Ultra disk. /Hana/data 볼륨은 Azure 쓰기 가속기 없이 프리미엄 SSD에 배치 하거나 울트라 디스크를 더 빠르게 다시 시작 하기 위해 사용할 수 있습니다.
- /Hana/log **및** /hana/data.에 대 한 Azure NetApp Files 위에 있는 **NFS v 4.1** 볼륨 /Hana/shared 볼륨은 NFS v3 또는 NFS v 4.1 프로토콜을 사용할 수 있습니다. NFS v 4.1 프로토콜은/hana/data 및/hana/log 볼륨에 필수입니다.

일부 저장소 유형을 결합할 수 있습니다. 예를 들어 Premium Storage에/hana/data를 배치 하 고, 필요한 짧은 대기 시간을 얻기 위해/hana/log를 울트라 디스크 저장소에 배치할 수 있습니다. 그러나/hana/data 용 ANF를 기반으로 하는 NFS v 4.1 볼륨을 사용 하는 경우/hana/log 용 ANF를 기반으로 하는 다른 NFS v 4.1 볼륨을 사용 해야 합니다. 볼륨 중 하나 (예:/hana/data)와 Azure Premium Storage 또는 다른 볼륨에 대 한 Ultra disk (예:/hana/log)에 대해 ANF 위에 NFS를 사용 하는 것은 **지원 되지 않습니다**.

온-프레미스 환경의 경우 i/o 하위 시스템 및 해당 기능에 대 한 관심이 거의 없습니다. SAP HANA에 대한 최소 스토리지 요구 사항을 충족하는 것은 어플라이언스 공급업체의 몫이었기 때문입니다. Azure 인프라를 직접 구축할 때 이러한 SAP 발급 요구 사항 중 일부를 알고 있어야 합니다. SAP에서 필요한 최소 처리량 특성 중 일부는 다음을 수행 해야 합니다.

- 1mb i/o 크기를 사용 하 여 250 m b/초의 **/hana/log** 에서 읽기/쓰기를 사용 하도록 설정
- 16MB 및 64MB I/O 크기의 **/hana/data**에 대해 최소 400MB/초의 읽기 작업을 사용하도록 설정합니다.
- 16MB 및 64MB I/O 크기의 **/hana/data**에 대해 최소 250MB/초의 쓰기 작업을 사용하도록 설정합니다.

낮은 스토리지 대기 시간이 DBMS 시스템에 중요하므로 SAP HANA와 같이 DBMS의 경우에도 데이터를 메모리 내에 유지합니다. 스토리지에서 중요한 경로는 일반적으로 DBMS 시스템의 트랜잭션 로그 쓰기입니다. 그러나 쓰기 저장점 또는 충돌 복구 후 메모리 내 데이터 로드와 같은 작업도 중요할 수 있습니다. 따라서 **/hana/data** 및 **/hana/log** 볼륨에는 Azure Premium 디스크를 활용 하는 것이 **필수** 입니다. SAP에서의 필요에 따라 **/hana/log** 및 **/hana/data**의 최소 처리량을 달성하기 위해 여러 Azure Premium Storage 디스크에 대한 MDADM 또는 LVM을 사용하여 RAID 0을 빌드해야 합니다. 또한 **/hana/data** 및 **/hana/log** 볼륨으로 RAID 볼륨을 사용합니다. 

**권장 사항: RAID 0의 스트라이프 크기는 다음을 사용 하는 것이 좋습니다.**

- **/hana/data**에 대해 64KB 또는 128KB
- **/hana/log**에 대해 32KB

> [!NOTE]
> Azure Premium과 Standard 스토리지는 세 개의 VHD 이미지를 유지하므로 RAID 볼륨을 사용하여 모든 중복 수준을 구성할 필요가 없습니다. RAID 볼륨의 사용법은 순수하게 충분한 I/O 처리량을 제공하는 볼륨을 구성하는 것입니다.

RAID 아래의 Azure VHD 수 누적은 IOPS 및 스토리지 처리량 측면의 누적입니다. 따라서 3 x P30 Azure Premium Storage 디스크에 대해 RAID 0을 설정하는 경우 IOPS의 세 배 및 단일 Azure Premium Storage P30 디스크의 스토리지 처리량의 세 배를 제공해야 합니다.

VM의 크기를 조정하거나 결정할 때도 전체 VM I/O 처리량을 고려하세요. 전체 VM 스토리지 처리량은 [메모리 최적화 가상 머신 크기](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory) 문서에 설명되어 있습니다.

## <a name="linux-io-scheduler-mode"></a>Linux I/O 스케줄러 모드
Linux에는 몇 가지 다른 I/O 일정 예약 모드가 있습니다. Linux 공급 업체 및 SAP를 통한 일반적인 권장 사항은 **cfq** 모드에서 **noop** 모드로 디스크 볼륨에 대 한 i/o scheduler 모드를 다시 구성 하는 것입니다. 자세한 내용은 [SAP Note #1984787](https://launchpad.support.sap.com/#/notes/1984787)에서 참조 하세요. 


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Azure M 시리즈 가상 머신에 대 한 Premium Storage 및 Azure 쓰기 가속기를 사용 하는 솔루션
Azure 쓰기 가속기는 Azure M 시리즈 Vm에만 사용할 수 있는 기능입니다. 이름에서 알 수 있듯이 이 기능은 Azure Premium Storage에 대한 쓰기의 I/O 대기 시간을 향상시키기 위한 것입니다. SAP HANA의 경우 Write Accelerator는 **/hana/log** 볼륨에 대해서만 사용해야 합니다. 따라서 **/hana/data** 및 **/hana/log** 는 **/Hana/log** 볼륨을 지 원하는 Azure 쓰기 가속기 별도의 볼륨입니다. 

> [!IMPORTANT]
> Azure Premium Storage을 사용 하는 경우 Azure [쓰기 가속기](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) 또는/hana/log 볼륨의 사용은 필수입니다. 쓰기 가속기는 premium Storage 및 M 시리즈 및 Mv2 시리즈 Vm에만 사용할 수 있습니다.

아래 캐싱 권장 사항은 다음과 같이 나열된 SAP HANA에 대한 I/O 특성을 가정하고 있습니다.

- HANA 데이터 파일에 대한 읽기 워크로드가 거의 없습니다. 예외는 HANA 인스턴스를 다시 시작한 후 또는 HANA에 데이터를 로드할 때 발생하는 대규모 I/O입니다. 데이터 파일에 대한 대규모 I/O의 또 다른 사례는 HANA 데이터베이스 백업일 수 있습니다. 대부분의 경우 모든 데이터 파일 볼륨을 완전히 읽어야 하므로 결과적으로 읽기 캐싱은 의미가 없습니다.
- HANA 저장점 및 HANA 크래시 복구를 기반으로 데이터 파일에 대한 쓰기가 갑작스럽게 발생합니다. 저장점 쓰기는 비동기이며 사용자 트랜잭션을 유지하지 않습니다. 크래시 복구 중에 데이터 쓰기는 시스템이 다시 빠르게 응답하기 위해 성능이 중요합니다. 그러나 크래시 복구는 예외적인 상황이어야 합니다.
- HANA 다시 실행 파일에는 읽기가 거의 없습니다. 트랜잭션 로그 백업, 크래시 복구를 수행하는 경우 또는 HANA 인스턴스를 다시 시작하는 단계의 대규모 I/O는 예외입니다.  
- SAP HANA 다시 실행 로그 파일에 대한 기본 부하는 쓰기입니다. 워크로드의 특성에 따라 4KB 정도의 작은 I/O가 있거나, 다른 경우에 1MB 이상의 I/O 크기가 있을 수 있습니다. SAP HANA 다시 실행 로그에 대한 쓰기 대기 시간은 성능이 중요합니다.
- 모든 쓰기는 신뢰할 수 있는 방식으로 디스크에서 유지되어야 합니다.

**권장 사항: 이러한 관찰 된 i/o SAP HANA 패턴의 결과로 Azure Premium Storage를 사용 하는 다른 볼륨에 대 한 캐싱은 다음과 같이 설정 해야 합니다.**

- **/hana/data** - 캐싱 없음
- **/hana/log** -캐싱 안 함-읽기 캐싱 없이 쓰기 가속기를 사용 하도록 설정 해야 하는 M-Mv2 시리즈에 대 한 예외입니다. 
- **/hana/shared** - 읽기 캐싱

### <a name="production-recommended-storage-solution"></a>프로덕션 권장 저장소 솔루션

> [!IMPORTANT]
> Azure M 시리즈 가상 머신에 대한 SAP HANA 인증은 **/hana/log** 볼륨에 대해 Azure Write Accelerator를 독점적으로 사용하는 것입니다. 결과적으로, Azure M 시리즈 가상 머신에 프로덕션 시나리오 SAP HANA 배포는 **/hana/log** 볼륨에 대해 Azure Write Accelerator를 사용하여 구성되어야 합니다.  

> [!NOTE]
> 프로덕션 시나리오의 경우 특정 VM 유형이 [IAAS에 대한 SAP 설명서](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)에서 SAP별 SAP HANA에 대해 지원되는지 여부를 확인합니다.

권장 사항은이 문서의 앞부분에서 설명한 대로 SAP 최소 요구 사항을 초과 하는 경우가 많습니다. 나열 된 권장 사항은 SAP에서 권장 하는 크기와 다양 한 VM 유형에 서 제공 하는 최대 저장소 처리량의 손상입니다.

**권장 사항: 프로덕션 시나리오에 권장 되는 구성은 다음과 같습니다.**

| VM SKU | RAM | 최대 VM I/O<br /> 처리량 | /hana/data | /hana/log | /hana/shared | /root 볼륨 | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192GiB | 500MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256GiB | 500MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512GiB | 1000MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64S | 1000GiB | 1000MB/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64MS | 1750GiB | 1000MB/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128S | 2000GiB | 2000MB/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 2 x P40 |
| M128MS | 3800GiB | 2000MB/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P40 |
| M208s_v2 | 2850 GiB | 1000MB/s | 4 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P40 |
| M208ms_v2 | 5700 GiB | 1000MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416s_v2 | 5700 GiB | 2000MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416ms_v2 | 11400 GiB | 2000MB/s | 8 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P50 |

제안 된 다른 볼륨의 저장소 처리량이 실행 하려는 작업을 충족 하는지 확인 합니다. 작업에 **/hana/data** 및 **/hana/log**에 대한 더 높은 볼륨이 필요한 경우 Azure Premium Storage VHD의 수를 증가시켜야 합니다. 나열 된 것 보다 더 많은 Vhd를 사용 하 여 볼륨의 크기를 조정 하면 Azure 가상 머신 유형 제한 내에서 IOPS 및 i/o 처리량이 늘어납니다.

Azure Write Accelerator는 [Azure 관리 디스크](https://azure.microsoft.com/services/managed-disks/)와만 함께 작동합니다. 따라서 **/hana/log** 볼륨을 형성하는 Azure Premium Storage 디스크는 관리 디스크로 배포되어야 합니다.

Azure Write Accelerator에서 지원할 수 있는 VM당 Azure Premium Storage VHD 수는 제한됩니다. 이 제한은 현재 다음과 같습니다.

- M128xx 및 M416xx VM에 대 한 16 개의 Vhd
- M64xx 및 M208xx VM에 대 한 Vhd 8 개
- M32xx VM당 4개 VHD

Azure Write Accelerator를 활성화하는 방법에 대한 자세한 지침은 [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) 문서에서 확인할 수 있습니다.

Azure Write Accelerator에 대한 세부 정보 및 제한 사항은 동일한 설명서에서 찾을 수 있습니다.

**권장 사항:/hana/log 볼륨을 형성 하는 디스크에 대 한 쓰기 가속기를 사용 해야 합니다.**


### <a name="cost-conscious-azure-storage-configuration"></a>비용 인식 Azure Storage 구성
다음 표에서는 고객이 Azure Vm에서 SAP HANA를 호스트 하는 데 사용 하는 VM 유형의 구성을 보여 줍니다. SAP HANA에 대 한 모든 최소 저장소 조건을 충족 하지 못할 수도 있고 SAP에서 SAP HANA 공식적으로 지원 되지 않는 일부 VM 유형도 있을 수 있습니다. 하지만 지금까지 해당 VM은 비-프로덕션 시나리오에 대해 제대로 수행되는 것처럼 보였습니다. **/Hana/data** 및 **/hana/log** 은 하나의 볼륨으로 결합 됩니다. 결과적으로 Azure 쓰기 가속기의 사용은 IOPS 측면에서 제한이 될 수 있습니다.

> [!NOTE]
> Sap 지원 시나리오의 경우 [IAAS에 대 한 sap 설명서](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)에서 특정 VM 유형이 sap에서 SAP HANA 지원 되는지 확인 합니다.

> [!NOTE]
> 비용에 민감한 솔루션에 대 한 이전 권장 사항을 변경 하는 것은 [azure 표준 HDD 디스크](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) 에서 더 나은 성능과 더 안정적인 [azure 표준 SSD 디스크로](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd) 이동 하는 것입니다.

권장 사항은이 문서의 앞부분에서 설명한 대로 SAP 최소 요구 사항을 초과 하는 경우가 많습니다. 나열 된 권장 사항은 SAP에서 권장 하는 크기와 다양 한 VM 유형에 서 제공 하는 최대 저장소 처리량의 손상입니다.

| VM SKU | RAM | 최대 VM I/O<br /> 처리량 | /hana/data 및 /hana/log<br /> (LVM 또는 MDADM으로 스트라이프됨) | /hana/shared | /root 볼륨 | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112GiB | 768MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E16v3 | 128GiB | 384MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E32v3 | 256GiB | 768MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| E64v3 | 432GiB | 1200 m b/초 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| GS5 | 448GiB | 2000 m b/초 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| M32ts | 192GiB | 500MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M32ls | 256GiB | 500MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M64ls | 512GiB | 1000 m b/초 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 |1 x E30 |
| M64S | 1000 GiB | 1000 m b/초 | 2 x P30 | 1 x E30 | 1 x E6 | 1 x E6 |2 x E30 |
| M64MS | 1750 GiB | 1000 m b/초 | 3 x P30 | 1 x E30 | 1 x E6 | 1 x E6 | 3 x E30 |
| M128S | 2000 GiB | 2000 m b/초 |3 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E40 |
| M128MS | 3800 GiB | 2000 m b/초 | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E50 |
| M208s_v2 | 2850 GiB | 1000 m b/초 | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 |  3 x E40 |
| M208ms_v2 | 5700 GiB | 1000 m b/초 | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416s_v2 | 5700 GiB | 2000 m b/초 | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416ms_v2 | 11400 GiB | 2000 m b/초 | 8 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E50 |


3 x P20을 사용하는 작은 VM 유형에 권장되는 디스크는 [SAP TDI Storage Whitepaper](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)(SAP TDI 스토리지 백서)에 따른 공간 권장 사항과 관련된 볼륨 크기를 초과합니다. 하지만 이 표에 표시된 항목은 SAP HANA에 필요한 충분한 디스크 처리량을 제공하도록 선택되었습니다. 메모리 볼륨의 두 배인 백업을 유지하기 위해 크기 지정된 **/hana/backup** 볼륨을 변경해야 하는 경우 자유롭게 조정할 수 있습니다.   
제안 된 다른 볼륨의 저장소 처리량이 실행 하려는 작업을 충족 하는지 확인 합니다. 작업에 **/hana/data** 및 **/hana/log**에 대한 더 높은 볼륨이 필요한 경우 Azure Premium Storage VHD의 수를 증가시켜야 합니다. 나열 된 것 보다 더 많은 Vhd를 사용 하 여 볼륨의 크기를 조정 하면 Azure 가상 머신 유형 제한 내에서 IOPS 및 i/o 처리량이 늘어납니다. 

> [!NOTE]
> 위의 구성은 Azure Premium Storage와 Azure Standard Storage의 혼합을 사용하지 않기 때문에 [Azure 가상 머신 단일 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/)의 이점을 얻지 못합니다. 그러나 비용을 최적화하기 위해 선택 영역이 선택되었습니다. VM 구성을 Azure 단일 VM SLA와 호환되도록 하려면 Azure Standard Storage(Sxx)로 나열된 위의 모든 디스크에 대한 Premium Storage를 선택해야 합니다.


> [!NOTE]
> 명시된 디스크 구성 권장 사항은 SAP가 인프라 공급자에 대해 표현한 최소 요구 사항을 대상으로 합니다. 실제 고객 배치 및 워크로드 시나리오에서 이러한 권장 사항이 여전히 충분한 기능을 제공하지 못하는 상황이 발생했습니다. 이는 고객이 HANA를 다시 시작한 후 또는 백업 구성에서 스토리지에 대해 더 높은 대역폭을 요구한 경우 데이터를 빠르게 재로드하도록 요구한 상황일 수 있습니다. 다른 사례로는 특정 워크로드에 대해 5000 IOPS가 충분하지 않은 **/hana/log**가 포함됐습니다. 따라서 이러한 권장 사항을 시작으로 워크로드의 요구 사항에 따라 적절히 조정합니다.
>  

## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>SAP HANA에 대 한 Azure Ultra disk 저장소 구성
Microsoft는 [Azure Ultra disk](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk)라는 새로운 azure storage 유형을 롤아웃 하는 과정을 진행 중입니다. 지금까지 제공 되는 Azure storage와 Ultra disk의 중요 한 차이점은 디스크 기능이 디스크 크기에 더 이상 바인딩되지 않는다는 것입니다. 고객은 Ultra disk에 대해 다음과 같은 기능을 정의할 수 있습니다.

- 4 GiB에서 65536 GiB 까지의 디스크 크기
- 100 IOPS에서 160K IOPS 까지의 IOPS 범위 (최대값은 VM 형식에 따라 다름)
- 300 m b/초에서 2000 m b/초의 저장소 처리량

Ultra disk를 사용 하면 크기, IOPS 및 디스크 처리량 범위를 충족 하는 단일 디스크를 정의할 수 있습니다. IOPS 및 저장소 처리량 요구 사항을 충족 하는 볼륨을 구성 하기 위해 Azure Premium Storage 위에 LVM 또는 MDADM과 같은 논리 볼륨 관리자를 사용 하는 대신 Ultra disk와 Premium Storage 간에 구성 조합을 실행할 수 있습니다. 결과적으로 Ultra disk의 사용량을/hana/data 및/hana/log 볼륨의 성능으로 제한 하 고 Azure를 사용 하 여 다른 볼륨을 처리할 수 있습니다 Premium Storage

울트라 디스크의 다른 이점은 Premium Storage에 비해 더 나은 읽기 대기 시간이 될 수 있습니다. 더 빠른 읽기 대기 시간은 HANA 시작 시간을 줄이고 데이터를 메모리에 로드 하는 데 필요한 경우에 이점이 있습니다. HANA가 저장점을 작성 하는 경우에도 Ultra disk storage의 이점을 느낄 수 있습니다. /Hana/data에 대 한 Premium Storage 디스크는 일반적으로 캐시 쓰기 가속기 되지 않으므로,/hana/data Premium Storage에 대 한 쓰기 대기 시간이 Ultra disk와 비교 하 여 더 높습니다. Ultra disk로 쓰기 저장 점이 Ultra disk에서 더 나은 성능을 기대할 수 있습니다.

> [!IMPORTANT]
> Ultra disk는 아직 모든 Azure 지역에 존재 하지 않으며 아래 나열 된 모든 VM 유형을 아직 지원 하지 않습니다. Ultra disk를 사용할 수 있고 지원 되는 VM 제품군에 대 한 자세한 내용은 [Azure에서 사용할 수 있는 디스크 유형](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk)문서를 참조 하세요.

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>순수한 Ultra disk 구성을 사용 하는 프로덕션 권장 저장소 솔루션
이 구성에서는/hana/data 및/hana/log 볼륨을 별도로 유지 합니다. 권장 값은 sap [TDI Storage 백서](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)에서 권장 하는 대로 SAP HANA 및 저장소 구성에 대 한 VM 유형을 인증 해야 하는 kpi에서 파생 됩니다.

권장 사항은이 문서의 앞부분에서 설명한 대로 SAP 최소 요구 사항을 초과 하는 경우가 많습니다. 나열 된 권장 사항은 SAP에서 권장 하는 크기와 다양 한 VM 유형에 서 제공 하는 최대 저장소 처리량의 손상입니다.

| VM SKU | RAM | 최대 VM I/O<br /> 처리량 | /hana/data 볼륨 | /hana/data i/o 처리량 | /hana/data IOPS | /hana/log 볼륨 | /hana/log i/o 처리량 | /hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E64s_v3 | 432GiB | 1200 m b/초 | 600GB | 700 MBps | 7,500 | 512 GB | 500 MBps  | 2,000 |
| M32ts | 192GiB | 500MB/s | 250GB | 400 MBps | 7,500 | 256GB | 250 MBps  | 2,000 |
| M32ls | 256GiB | 500MB/s | 300GB | 400 MBps | 7,500 | 256GB | 250 MBps  | 2,000 |
| M64ls | 512GiB | 1000 m b/초 | 600GB | 600 MBps | 7,500 | 512 GB | 400 MBps  | 2,500 |
| M64S | 1000 GiB | 1000 m b/초 |  1200 GB | 600 MBps | 7,500 | 512 GB | 400 MBps  | 2,500 |
| M64MS | 1750 GiB | 1000 m b/초 | 2100 GB | 600 MBps | 7,500 | 512 GB | 400 MBps  | 2,500 |
| M128S | 2000 GiB | 2000 m b/초 |2400 GB | 1200 MBps |9,000 | 512 GB | 800 MBps  | 3,000 | 
| M128MS | 3800 GiB | 2000 m b/초 | 4800 GB | 1200 MBps |9,000 | 512 GB | 800 MBps  | 3,000 | 
| M208s_v2 | 2850 GiB | 1000 m b/초 | 3500 GB | 1000 MBps | 9,000 | 512 GB | 400 MBps  | 2,500 | 
| M208ms_v2 | 5700 GiB | 1000 m b/초 | 7200 GB | 1000 MBps | 9,000 | 512 GB | 400 MBps  | 2,500 | 
| M416s_v2 | 5700 GiB | 2000 m b/초 | 7200 GB | 1500 MBps | 9,000 | 512 GB | 800 MBps  | 3,000 | 
| M416ms_v2 | 11400 GiB | 2000 m b/초 | 14400 GB | 1500 MBps | 9,000 | 512 GB | 800 MBps  | 3,000 |   

나열 되는 값은 시작 점으로 사용 되며 실제 수요에 대해 평가 해야 합니다. Azure Ultra disk의 장점은 VM을 종료 하거나 시스템에 적용 되는 워크 로드를 중지 하지 않고도 IOPS 및 처리량에 대 한 값을 적용할 수 있다는 것입니다.   

> [!NOTE]
> 지금까지 Ultra disk storage를 사용 하는 저장소 스냅숏을 사용할 수 없습니다. Azure Backup 서비스를 사용 하 여 VM 스냅숏의 사용을 차단 합니다.

### <a name="cost-conscious-storage-solution-with-pure-ultra-disk-configuration"></a>순수 Ultra 디스크 구성을 사용 하는 비용에 민감한 저장소 솔루션
이 구성에서는/hana/data 및/hana/log 볼륨이 동일한 디스크에 있습니다. 권장 값은 sap [TDI Storage 백서](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)에서 권장 하는 대로 SAP HANA 및 저장소 구성에 대 한 VM 유형을 인증 해야 하는 kpi에서 파생 됩니다. 

권장 사항은이 문서의 앞부분에서 설명한 대로 SAP 최소 요구 사항을 초과 하는 경우가 많습니다. 나열 된 권장 사항은 SAP에서 권장 하는 크기와 다양 한 VM 유형에 서 제공 하는 최대 저장소 처리량의 손상입니다.

| VM SKU | RAM | 최대 VM I/O<br /> 처리량 | /Hana/data 및/log의 볼륨 | /hana/data 및 log i/o 처리량 | /hana/data 및 로그 IOPS |
| --- | --- | --- | --- | --- | --- |
| E64s_v3 | 432GiB | 1200 m b/초 | 1200 GB | 1200 MBps | 9500 | 
| M32ts | 192GiB | 500MB/s | 512 GB | 400 MBps | 9500 | 
| M32ls | 256GiB | 500MB/s | 600GB | 400 MBps | 9500 | 
| M64ls | 512GiB | 1000 m b/초 | 1100 GB | 900 MBps | 10000 | 
| M64S | 1000 GiB | 1000 m b/초 |  1700 GB | 900 MBps | 10000 | 
| M64MS | 1750 GiB | 1000 m b/초 | 2600 GB | 900 MBps | 10000 | 
| M128S | 2000 GiB | 2000 m b/초 |2900 GB | 1800 MBps |12,000 | 
| M128MS | 3800 GiB | 2000 m b/초 | 5300 GB | 1800 MBps |12,000 |  
| M208s_v2 | 2850 GiB | 1000 m b/초 | 4000 GB | 900 MBps | 10000 |  
| M208ms_v2 | 5700 GiB | 1000 m b/초 | 7700 GB | 900 MBps | 10000 | 
| M416s_v2 | 5700 GiB | 2000 m b/초 | 7700 GB | 1, 800MBps | 12,000 |  
| M416ms_v2 | 11400 GiB | 2000 m b/초 | 15000 GB | 1800 MBps | 12,000 |    

나열 되는 값은 시작 점으로 사용 되며 실제 수요에 대해 평가 해야 합니다. Azure Ultra disk의 장점은 VM을 종료 하거나 시스템에 적용 되는 워크 로드를 중지 하지 않고도 IOPS 및 처리량에 대 한 값을 적용할 수 있다는 것입니다.  

## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>Azure NetApp Files의 NFS v 4.1 볼륨
Azure NetApp Files은/hana/shared,/hana/data 및/hana/log 볼륨에 사용할 수 있는 네이티브 NFS 공유를 제공 합니다. /Hana/data 및/hana/log 볼륨에 대해 ANF 기반 NFS 공유를 사용 하려면 v 4.1 NFS 프로토콜을 사용 해야 합니다. NFS 프로토콜 v3은 ANF의 공유를 기반으로 하는 경우/hana/data 및/hana/log 볼륨의 사용에 대해 지원 되지 않습니다. 

> [!IMPORTANT]
> Azure NetApp Files에서 구현 된 NFS v3 프로토콜은/hana/data 및/hana/log에 사용할 수 **없습니다** . NFS 4.1 사용은 기능 관점에서/hana/data 및/hana/log 볼륨에 대해 필수입니다. /Hana/shared 볼륨의 경우 기능 관점에서 NFS v3 또는 NFS v 4.1 프로토콜을 사용할 수 있습니다.

### <a name="important-considerations"></a>중요 고려 사항
SAP Netweaver 및 SAP HANA에 대 한 Azure NetApp Files 고려 하는 경우 다음과 같은 중요 한 사항을 고려해 야 합니다.

- 최소 용량 풀은 4 TiB입니다.  
- 최소 볼륨 크기는 100 GiB
- Azure NetApp Files 및 Azure NetApp Files 볼륨이 탑재 되는 모든 가상 머신은 동일한 Azure Virtual Network 또는 동일한 지역의 [피어 링 가상 네트워크](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) 에 있어야 합니다.  
- 선택한 가상 네트워크에는 Azure NetApp Files으로 위임 된 서브넷이 있어야 합니다.
- Azure NetApp 볼륨의 처리량은 [Azure NetApp Files의 서비스 수준](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)에 설명 된 대로 볼륨 할당량 및 서비스 수준의 기능입니다. HANA Azure NetApp 볼륨의 크기를 조정할 때 결과 처리량이 HANA 시스템 요구 사항을 충족 하는지 확인 합니다.  
- Azure NetApp Files에서 [내보내기 정책을](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)제공 합니다. 허용 되는 클라이언트, 액세스 유형 (읽기 & 쓰기, 읽기 전용 등)을 제어할 수 있습니다. 
- Azure NetApp Files 기능이 아직 영역을 인식 하지 않습니다. 현재 Azure NetApp Files 기능은 Azure 지역의 모든 가용성 영역에 배포 되지 않습니다. 일부 Azure 지역에서 잠재적 대기 시간 영향을 염두에 두어야 합니다.  
- 짧은 대기 시간을 위해 Azure NetApp storage와 가까운 곳에 가상 머신을 배포 하는 것이 중요 합니다. 
- <b>Sid</b>Adm의 사용자 id와 가상 컴퓨터의 `sapsys`에 대 한 그룹 id는 Azure NetApp Files의 구성과 일치 해야 합니다. 

> [!IMPORTANT]
> SAP HANA 워크 로드의 경우 짧은 대기 시간이 중요 합니다. Microsoft 담당자와 협력 하 여 가상 머신과 Azure NetApp Files 볼륨이 근접 하 게 배포 되었는지 확인 합니다.  

> [!IMPORTANT]
> <b>Sid</b>Adm의 사용자 ID와 가상 컴퓨터와 Azure netapp 구성 간의 `sapsys`에 대 한 그룹 id가 일치 하지 않는 경우 가상 컴퓨터에 탑재 된 Azure netapp 볼륨의 파일에 대 한 권한이 `nobody`표시 됩니다. [새 시스템을](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) Azure NetApp Files에 온 보 딩 할 때 <b>sid</b>ADM에 올바른 사용자 ID와 `sapsys`에 대 한 그룹 id를 지정 해야 합니다.

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Azure NetApp Files HANA 데이터베이스 크기 조정

Azure NetApp 볼륨의 처리량은 [Azure NetApp Files의 서비스 수준](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)에 설명 된 대로 볼륨 크기 및 서비스 수준의 기능입니다. 

Azure에서 SAP 용 인프라를 설계할 때 다음의 최소 처리량 특성으로 변환 되는 SAP의 최소 저장소 처리량 요구 사항에 대해 알고 있어야 합니다.

- 1mb i/o 크기를 사용 하 여 250 m b/초의/hana/log에서 읽기/쓰기를 사용 하도록 설정  
- 16mb 및 64 MB i/o 크기에 대해/hana/data에 대해 최소 400 m b/초의 읽기 작업을 사용 하도록 설정 합니다.  
- 16mb 및 64 MB i/o 크기를 사용 하 여/hana/data에 대해 최소 250 m b/초의 쓰기 작업을 사용 하도록 설정 합니다.  

볼륨 할당량의 1 TiB [처리량 제한은](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) 다음과 같습니다. Azure NetApp Files
- Premium Storage 계층-64 MiB/s  
- Ultra Storage 계층-128 MiB/s  

데이터 및 로그에 대 한 SAP 최소 처리량 요구 사항을 충족 하 고 `/hana/shared`에 대 한 지침에 따라 권장 크기는 다음과 같습니다.

| 볼륨 | 크기<br /> Premium Storage 계층 | 크기<br /> Ultra Storage 계층 | 지원 되는 NFS 프로토콜 |
| --- | --- | --- |
| /hana/log | 4TiB | 2 TiB | v 4.1 |
| /hana/data | 6.3 TiB | 3.2 TiB | v 4.1 |
| /hana/shared | 작업자 노드당 최대 (512gb, 1xRAM) | 작업자 노드당 최대 (512gb, 1xRAM) | v3 또는 v 4.1 |


> [!NOTE]
> 여기에 명시 된 Azure NetApp Files 크기 조정 권장 사항은 SAP가 인프라 공급자를 중심으로 하는 최소 요구 사항을 충족 하도록 대상으로 지정 됩니다. 실제 고객 배포 및 워크 로드 시나리오에서는이로 충분 하지 않을 수 있습니다. 이러한 권장 사항을 시작 점으로 사용 하 고 특정 워크 로드의 요구 사항에 따라 조정 합니다.  

따라서 Ultra disk storage에 대해 나열 된 것 처럼 ANF 볼륨에 대해 비슷한 처리량을 배포 하는 것을 고려할 수 있습니다. 또한 Ultra disk 테이블에서 수행 하는 것과 다른 VM Sku의 볼륨에 대해 나열 된 크기의 크기도 고려해 야 합니다.

> [!TIP]
> 볼륨을 `unmount` 하거나 가상 컴퓨터를 중지 하거나 SAP HANA를 중지 하지 않고도 Azure NetApp Files 볼륨의 크기를 동적으로 변경할 수 있습니다. 이를 통해 응용 프로그램에 예상 된 처리량과 예측할 수 없는 처리량 요구를 모두 충족할 수 있습니다.

ANF에서 호스트 되는 NFS v 4.1 볼륨을 사용 하 여 SAP HANA 스케일 아웃 구성을 배포 하는 방법에 대 한 설명서는 [SUSE Linux Enterprise Server Azure NetApp Files를 사용 하 여 Azure vm에서 대기 노드 SAP HANA 스케일 아웃으로](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)게시 됩니다.


## <a name="next-steps"></a>다음 단계
자세한 내용은

- [Azure virtual machines에 대 한 고가용성 가이드를 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)합니다.
