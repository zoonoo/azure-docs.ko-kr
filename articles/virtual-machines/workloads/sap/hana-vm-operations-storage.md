---
title: SAP HANA Azure 가상 머신 스토리지 구성 | Microsoft Docs
description: SAP HANA가 배포된 VM에 대한 스토리지 권장 사항입니다.
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
ms.date: 05/19/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aa3096f43952c047620b310412b27c434fc5fb06
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682594"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>SAP HANA Azure 가상 머신 스토리지 구성

Azure에서는 SAP HANA를 실행하는 Azure VM에 적합한 다양한 스토리지 유형을 제공합니다. SAP HANA 배포에 고려할 수 있는 **SAP HANA 인증 Azure 스토리지 유형**은 다음과 같습니다. 

- Azure 프리미엄 SSD  
- [Ultra Disk](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

이러한 디스크 유형에 대한 자세한 내용은 [디스크 유형 선택](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types) 문서를 참조하세요.

Azure는 Azure Standard 및 Premium Storage에서 VHD를 위한 두 가지 배포 방법을 제공합니다. 전반적인 시나리오에서 가능하다면 [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) 배포를 활용하세요. 

IOPS 및 스토리지 처리량에서 스토리지 유형 및 해당 SLA의 목록을 보려면 [관리 디스크에 대한 Azure 설명서](https://azure.microsoft.com/pricing/details/managed-disks/)를 참조하세요.

> [!IMPORTANT]
> 선택한 Azure 스토리지 유형과는 독립적으로 해당 스토리지에 사용되는 파일 시스템을 SAP에서 특정 운영 체제 및 DBMS에 대해 지원해야 합니다. [SAP Support Note #405827](https://launchpad.support.sap.com/#/notes/405827)에는 SAP HANA를 포함하여 다양한 운영 체제 및 데이터베이스에 대해 지원되는 파일 시스템이 나와 있습니다. 이는 SAP HANA가 어떤 작업이든 읽기 및 쓰기를 위해 액세스할 수 모든 볼륨에 적용됩니다. 특히 SAP HANA용 Azure에서 NFS를 사용하는 경우 이 문서의 뒷부분에서 설명한 대로 NFS 버전의 추가 제한 사항이 적용됩니다. 


다양한 스토리지 유형에 대한 최소 SAP HANA 인증 조건은 다음과 같습니다. 

- Azure 프리미엄 SSD - /hana/log는 Azure [쓰기 가속기](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)를 사용하여 캐시해야 합니다. /hana/data 볼륨은 Azure 쓰기 가속기 없이 프리미엄 SSD에 배치하거나 Ultra Disk에 배치할 수 있습니다.
- /hana/log 볼륨에 최소한 Azure Ultra Disk를 사용합니다. /hana/data 볼륨은 Azure 쓰기 가속기 없이 프리미엄 SSD에 배치하거나 다시 시작 시간을 단축하기 위해 Ultra Disk에 배치할 수 있습니다.
- /hana/log **및** /hana/data에 Azure NetApp Files를 기반으로 하는 **NFS v4.1** 볼륨을 사용합니다. /hana/shared 볼륨은 NFS v3 또는 NFS v 4.1 프로토콜을 사용할 수 있습니다. /hana/data 및 /hana/log 볼륨에는 NFS v 4.1 프로토콜이 필수입니다.

일부 스토리지 유형은 결합할 수 있습니다. 예를 들어 Premium Storage에 /hana/data를 배치하고 필요한 짧은 대기 시간을 확보하기 위해 Ultra Disk 스토리지에 /hana/log를 배치할 수 있습니다. 그러나 /hana/data에 ANF를 기반으로 하는 NFS v4.1 볼륨을 사용하는 경우 /hana/log에는 ANF를 기반으로 하는 또 하나의 NFS v4.1 볼륨을 사용해야 합니다. 볼륨 중 하나(예: /hana/data)에 ANF를 기반으로 하는 NFS를 사용하고 다른 볼륨(예: /hana/log)에는 Azure Premium Storage 또는 Ultra Disk를 사용하는 것은 **지원되지 않습니다**.

온-프레미스 환경의 경우 I/O 하위 시스템 및 해당 성능은 신경 쓸 필요가 거의 없었습니다. SAP HANA에 대한 최소 스토리지 요구 사항을 충족하는 것은 어플라이언스 공급업체의 몫이었기 때문입니다. 자체적으로 Azure 인프라를 빌드하는 동안에는 그러한 SAP 발행 요구 사항 몇 가지에 대해 알아야 합니다. SAP에서 요구하는 최소 처리량 특성 중 일부 때문에 다음이 필요합니다.

- 1MB I/O 크기의 **/hana/log**에 대해 250MB/초의 읽기/쓰기 작업을 사용하도록 설정합니다.
- 16MB 및 64MB I/O 크기의 **/hana/data**에 대해 최소 400MB/초의 읽기 작업을 사용하도록 설정합니다.
- 16MB 및 64MB I/O 크기의 **/hana/data**에 대해 최소 250MB/초의 쓰기 작업을 사용하도록 설정합니다.

낮은 스토리지 대기 시간이 DBMS 시스템에 중요하므로 SAP HANA와 같이 DBMS의 경우에도 데이터를 메모리 내에 유지합니다. 스토리지에서 중요한 경로는 일반적으로 DBMS 시스템의 트랜잭션 로그 쓰기입니다. 그러나 쓰기 저장점 또는 충돌 복구 후 메모리 내 데이터 로드와 같은 작업도 중요할 수 있습니다. 따라서 **/hana/data** 및 **/hana/log** 볼륨에는 Azure Premium 디스크를 활용하는 것이 **필수**입니다. SAP에서의 필요에 따라 **/hana/log** 및 **/hana/data**의 최소 처리량을 달성하기 위해 여러 Azure Premium Storage 디스크에 대한 MDADM 또는 LVM을 사용하여 RAID 0을 빌드해야 합니다. 또한 **/hana/data** 및 **/hana/log** 볼륨으로 RAID 볼륨을 사용합니다. 

> [!IMPORTANT]
>3개의 SAP HANA 파일 시스템 /data, /log 및 /shared는 기본 또는 루트 볼륨 그룹에 포함되지 않아야 합니다.  일반적으로 /data, /log 및 /shared에 개별 볼륨 그룹을 만드는 Linux 공급 업체 지침을 따르는 것이 좋습니다.

**권장 사항: RAID 0에 대한 스트라이프 크기로 다음을 사용하는 것이 좋습니다.**

- **/hana/data**에 대해 256KB
- **/hana/log**에 대해 32KB

> [!IMPORTANT]
> /hana/data에 대한 스트라이프 크기가 64KB 또는 128KB를 요구하는 이전 권장 사항에서 최신 Linux 버전을 사용하는 고객 환경에 따라 256KB로 변경되었습니다. 256KB 크기는 약간 더 나은 성능을 제공합니다.

> [!NOTE]
> Azure Premium과 Standard 스토리지는 세 개의 VHD 이미지를 유지하므로 RAID 볼륨을 사용하여 모든 중복 수준을 구성할 필요가 없습니다. RAID 볼륨의 사용법은 순수하게 충분한 I/O 처리량을 제공하는 볼륨을 구성하는 것입니다.

RAID 아래의 Azure VHD 수 누적은 IOPS 및 스토리지 처리량 측면의 누적입니다. 따라서 3 x P30 Azure Premium Storage 디스크에 대해 RAID 0을 설정하는 경우 IOPS의 세 배 및 단일 Azure Premium Storage P30 디스크의 스토리지 처리량의 세 배를 제공해야 합니다.

VM의 크기를 조정하거나 결정할 때도 전체 VM I/O 처리량을 고려하세요. 전체 VM 스토리지 처리량은 [메모리 최적화 가상 머신 크기](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory) 문서에 설명되어 있습니다.

## <a name="linux-io-scheduler-mode"></a>Linux I/O 스케줄러 모드
Linux에는 몇 가지 다른 I/O 일정 예약 모드가 있습니다. Linux 공급 업체 및 SAP를 통한 일반적인 권장 사항은 디스크 볼륨에 대한 I/O 스케줄러 모드를 **mq-deadline** 또는 **kyber** 모드에서 **noop**(non-multiqueue) 또는 **없음**(multiqueue) 모드로 다시 구성하는 것입니다. 자세한 내용은 [SAP Note #1984787](https://launchpad.support.sap.com/#/notes/1984787)에서 참조했습니다. 


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Azure M 시리즈 가상 머신에 Premium Storage 및 Azure 쓰기 가속기를 사용하는 솔루션
Azure 쓰기 가속기는 Azure M 시리즈 VM 전용 기능입니다. 이름에서 알 수 있듯이 이 기능은 Azure Premium Storage에 대한 쓰기의 I/O 대기 시간을 향상시키기 위한 것입니다. SAP HANA의 경우 Write Accelerator는 **/hana/log** 볼륨에 대해서만 사용해야 합니다. 따라서 **/hana/data**와 **/hana/log**는 별개의 볼륨입니다(Azure 쓰기 가속기가 **/hana/log** 볼륨만 지원하기 때문). 

> [!IMPORTANT]
> Azure Premium Storage를 사용하는 경우 Azure [쓰기 가속기](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) 또는/hana/log 볼륨 사용이 필수입니다. 쓰기 가속기는 Premium Storage와 M 시리즈 및 Mv2 시리즈 VM에만 사용할 수 있습니다.

아래 캐싱 권장 사항은 다음과 같이 나열된 SAP HANA에 대한 I/O 특성을 가정하고 있습니다.

- HANA 데이터 파일에 대한 읽기 워크로드가 거의 없습니다. 예외는 HANA 인스턴스를 다시 시작한 후 또는 HANA에 데이터를 로드할 때 발생하는 대규모 I/O입니다. 데이터 파일에 대한 대규모 I/O의 또 다른 사례는 HANA 데이터베이스 백업일 수 있습니다. 대부분의 경우 모든 데이터 파일 볼륨을 완전히 읽어야 하므로 결과적으로 읽기 캐싱은 의미가 없습니다.
- HANA 저장점 및 HANA 크래시 복구를 기반으로 데이터 파일에 대한 쓰기가 갑작스럽게 발생합니다. 저장점 쓰기는 비동기이며 사용자 트랜잭션을 유지하지 않습니다. 크래시 복구 중에 데이터 쓰기는 시스템이 다시 빠르게 응답하기 위해 성능이 중요합니다. 그러나 크래시 복구는 예외적인 상황이어야 합니다.
- HANA 다시 실행 파일에는 읽기가 거의 없습니다. 트랜잭션 로그 백업, 크래시 복구를 수행하는 경우 또는 HANA 인스턴스를 다시 시작하는 단계의 대규모 I/O는 예외입니다.  
- SAP HANA 다시 실행 로그 파일에 대한 기본 부하는 쓰기입니다. 워크로드의 특성에 따라 4KB 정도의 작은 I/O가 있거나, 다른 경우에 1MB 이상의 I/O 크기가 있을 수 있습니다. SAP HANA 다시 실행 로그에 대한 쓰기 대기 시간은 성능이 중요합니다.
- 모든 쓰기는 신뢰할 수 있는 방식으로 디스크에서 유지되어야 합니다.

**권장 사항: SAP HANA에서 관찰한 이러한 I/O 패턴의 결과로 Azure Premium Storage를 사용하여 서로 다른 볼륨에 대한 캐싱은 다음과 같이 설정됩니다.**

- **/hana/data** - 캐싱 없음
- **/hana/log** - 캐싱 없음 - 읽기 캐싱 없이 쓰기 가속기를 사용하도록 설정해야 하는 M/Mv2 시리즈는 예외입니다. 
- **/hana/shared** - 읽기 캐싱

### <a name="production-recommended-storage-solution"></a>프로덕션 권장 스토리지 솔루션

> [!IMPORTANT]
> Azure M 시리즈 가상 머신에 대한 SAP HANA 인증은 **/hana/log** 볼륨에 대해 Azure Write Accelerator를 독점적으로 사용하는 것입니다. 결과적으로, Azure M 시리즈 가상 머신에 프로덕션 시나리오 SAP HANA 배포는 **/hana/log** 볼륨에 대해 Azure Write Accelerator를 사용하여 구성되어야 합니다.  

> [!NOTE]
> 프로덕션 시나리오의 경우 특정 VM 유형이 [IAAS에 대한 SAP 설명서](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)에서 SAP별 SAP HANA에 대해 지원되는지 여부를 확인합니다.

권장 사항은 이 문서의 앞부분에서 설명한 대로 SAP 최소 요구 사항을 초과하는 경우가 많습니다. 나열된 권장 사항은 SAP에서 권장하는 크기와 다양한 VM 유형이 제공하는 최대 스토리지 처리량 간의 절충입니다.

**권장 사항: 프로덕션 시나리오에 권장되는 구성은 다음과 같습니다.**

| VM SKU | RAM | 최대 VM I/O<br /> 처리량 | /hana/data | /hana/log | /hana/shared | /root 볼륨 | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192GiB | 500MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256GiB | 500MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512GiB | 1000MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1000GiB | 1000MB/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750GiB | 1000MB/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000GiB | 2000MB/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 2 x P40 |
| M128ms | 3800GiB | 2000MB/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P40 |
| M208s_v2 | 2850GiB | 1000MB/s | 4 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P40 |
| M208ms_v2 | 5700GiB | 1000MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416s_v2 | 5700GiB | 2000MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416ms_v2 | 11400GiB | 2000MB/s | 8 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P50 |

제안된 다른 볼륨에 대한 스토리지 처리량이 실행하려는 워크로드를 충족하는지 여부를 확인합니다. 작업에 **/hana/data** 및 **/hana/log**에 대한 더 높은 볼륨이 필요한 경우 Azure Premium Storage VHD의 수를 증가시켜야 합니다. 나열된 것보다 더 많은 VHD로 볼륨을 크기 조정하면 Azure 가상 머신 유형의 한도 내 IOPS 및 I/O 처리량이 증가합니다.

Azure Write Accelerator는 [Azure 관리 디스크](https://azure.microsoft.com/services/managed-disks/)와만 함께 작동합니다. 따라서 **/hana/log** 볼륨을 형성하는 Azure Premium Storage 디스크는 관리 디스크로 배포되어야 합니다.

Azure Write Accelerator에서 지원할 수 있는 VM당 Azure Premium Storage VHD 수는 제한됩니다. 이 제한은 현재 다음과 같습니다.

- M128xx 및 M416xx VM당 16개 VHD
- M64xx 및 M208xx VM당 8개 VHD
- M32xx VM당 4개 VHD

Azure Write Accelerator를 활성화하는 방법에 대한 자세한 지침은 [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) 문서에서 확인할 수 있습니다.

Azure Write Accelerator에 대한 세부 정보 및 제한 사항은 동일한 설명서에서 찾을 수 있습니다.

**권장 사항: /hana/log 볼륨을 형성하는 디스크에 쓰기 가속기를 사용해야 합니다.**


### <a name="cost-conscious-azure-storage-configuration"></a>비용 인식 Azure Storage 구성
다음 표에서는 고객이 Azure VM에서 SAP HANA를 호스트하는 데에도 사용하는 VM 유형의 구성을 보여줍니다. SAP HANA에 대한 모든 최소 스토리지 조건을 충족하지 못할 수 있거나 SAP에서 공식적으로 SAP HANA를 지원받지 않는 일부 VM 유형이 있을 수 있습니다. 하지만 지금까지 해당 VM은 비-프로덕션 시나리오에 대해 제대로 수행되는 것처럼 보였습니다. **/hana/data**와 **/hana/log**는 한 볼륨으로 결합됩니다. 결과적으로 Azure 쓰기 가속기 사용은 IOPS 측면에서 제약이 될 수 있습니다.

> [!NOTE]
> SAP 지원 시나리오의 경우 특정 VM 유형이 [IAAS에 대한 SAP 설명서](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)에서 SAP별 SAP HANA에 대해 지원되는지 여부를 확인합니다.

> [!NOTE]
> 비용 인식 솔루션에 대한 이전 권장 사항에서 변경된 사항은 [Azure 표준 HDD 디스크](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd)에서 성능 및 안정성이 더 우수한 [Azure 표준 SSD 디스크](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd)로의 이동입니다.

권장 사항은 이 문서의 앞부분에서 설명한 대로 SAP 최소 요구 사항을 초과하는 경우가 많습니다. 나열된 권장 사항은 SAP에서 권장하는 크기와 다양한 VM 유형이 제공하는 최대 스토리지 처리량 간의 절충입니다.

| VM SKU | RAM | 최대 VM I/O<br /> 처리량 | /hana/data 및 /hana/log<br /> (LVM 또는 MDADM으로 스트라이프됨) | /hana/shared | /root 볼륨 | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112GiB | 768MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E16v3 | 128GiB | 384MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E32v3 | 256GiB | 768MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| E64v3 | 432GiB | 1,200MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| GS5 | 448GiB | 2,000MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| M32ts | 192GiB | 500MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M32ls | 256GiB | 500MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M64ls | 512GiB | 1,000MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 |1 x E30 |
| M64s | 1,000GiB | 1,000MB/s | 2 x P30 | 1 x E30 | 1 x E6 | 1 x E6 |2 x E30 |
| M64ms | 1,750GiB | 1,000MB/s | 3 x P30 | 1 x E30 | 1 x E6 | 1 x E6 | 3 x E30 |
| M128s | 2,000GiB | 2,000MB/s |3 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E40 |
| M128ms | 3,800GiB | 2,000MB/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E50 |
| M208s_v2 | 2,850GiB | 1,000MB/s | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 |  3 x E40 |
| M208ms_v2 | 5,700GiB | 1,000MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416s_v2 | 5,700GiB | 2,000MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416ms_v2 | 11400GiB | 2,000MB/s | 8 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E50 |


3 x P20을 사용하는 작은 VM 유형에 권장되는 디스크는 [SAP TDI Storage Whitepaper](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)(SAP TDI 스토리지 백서)에 따른 공간 권장 사항과 관련된 볼륨 크기를 초과합니다. 하지만 이 표에 표시된 항목은 SAP HANA에 필요한 충분한 디스크 처리량을 제공하도록 선택되었습니다. 메모리 볼륨의 두 배인 백업을 유지하기 위해 크기 지정된 **/hana/backup** 볼륨을 변경해야 하는 경우 자유롭게 조정할 수 있습니다.   
제안된 다른 볼륨에 대한 스토리지 처리량이 실행하려는 워크로드를 충족하는지 여부를 확인합니다. 작업에 **/hana/data** 및 **/hana/log**에 대한 더 높은 볼륨이 필요한 경우 Azure Premium Storage VHD의 수를 증가시켜야 합니다. 나열된 것보다 더 많은 VHD로 볼륨을 크기 조정하면 Azure 가상 머신 유형의 한도 내 IOPS 및 I/O 처리량이 증가합니다. 

> [!NOTE]
> 위의 구성은 Azure Premium Storage와 Azure Standard Storage의 혼합을 사용하지 않기 때문에 [Azure 가상 머신 단일 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/)의 이점을 얻지 못합니다. 그러나 비용을 최적화하기 위해 선택 영역이 선택되었습니다. VM 구성을 Azure 단일 VM SLA와 호환되도록 하려면 Azure Standard Storage(Sxx)로 나열된 위의 모든 디스크에 대한 Premium Storage를 선택해야 합니다.


> [!NOTE]
> 명시된 디스크 구성 권장 사항은 SAP가 인프라 공급자에 대해 표현한 최소 요구 사항을 대상으로 합니다. 실제 고객 배치 및 워크로드 시나리오에서 이러한 권장 사항이 여전히 충분한 기능을 제공하지 못하는 상황이 발생했습니다. 이는 고객이 HANA를 다시 시작한 후 또는 백업 구성에서 스토리지에 대해 더 높은 대역폭을 요구한 경우 데이터를 빠르게 재로드하도록 요구한 상황일 수 있습니다. 다른 사례로는 특정 워크로드에 대해 5000 IOPS가 충분하지 않은 **/hana/log**가 포함됐습니다. 따라서 이러한 권장 사항을 시작으로 워크로드의 요구 사항에 따라 적절히 조정합니다.
>  

## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>SAP HANA용 Azure Ultra Disk 스토리지 구성
Microsoft는 [Azure Ultra Disk](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk)라는 새로운 Azure 스토리지 유형을 롤아웃하는 과정에 있습니다. 이제까지 제공된 Azure 스토리지와 Ultra Disk의 중요한 차이점은 디스크 기능이 더 이상 디스크 크기에 구속되지 않는다는 것입니다. 고객은 Ultra Disk에 대해 다음과 같은 기능을 정의할 수 있습니다.

- 4GiB에서 65,536GiB까지의 디스크 크기
- 100IOPS에서 160,000IOPS까지의 IOPS 범위(최대값은 VM 유형에 따라 다름)
- 300MB/초에서 2,000MB/초까지의 스토리지 처리량

Ultra Disk를 사용하면 원하는 크기, IOPS 및 디스크 처리량 범위를 충족하는 단일 디스크를 정의할 수 있습니다. IOPS 및 스토리지 처리량 요구 사항을 충족하는 볼륨을 구성하기 위해 Azure Premium Storage 위에 LVM 또는 MDADM과 같은 논리 볼륨 관리자를 사용하는 대신 Ultra Disk와 Premium Storage 간에 구성 조합을 실행할 수 있습니다. 따라서 Ultra Disk의 사용을 성능이 중요한 /hana/data 및/hana/log 볼륨으로 제한하고 Azure Premium Storage를 사용하여 다른 볼륨을 처리할 수 있습니다.

Ultra Disk의 다른 이점은 Premium Storage에 비해 향상된 읽기 대기 시간일 수 있습니다. 더 빠른 읽기 대기 시간은 HANA 시작 시간과 이후 데이터를 메모리로 로드하는 시간을 단축하려는 경우에 이점이 될 수 있습니다. HANA가 저장점을 작성하는 경우에도 Ultra Disk 스토리지의 이점을 느낄 수 있습니다. /hana/data용 Premium Storage 디스크는 일반적으로 쓰기 가속기로 캐시되지 않으므로, Premium Storage 기반 /hana/data에 대한 쓰기 대기 시간은 Ultra Disk보다 더 높습니다. Ultra Disk를 사용한 저장점 기록이 Ultra Disk에서 더 나은 성능을 발휘할 것을 예상할 수 있습니다.

> [!IMPORTANT]
> Ultra Disk는 아직 모든 Azure 지역에 출시되지 않았고 아직 아래 나열된 모든 VM 유형을 지원하지 않습니다. Ultra Disk를 사용할 수 있는 지역 및 지원되는 VM 제품군에 대한 자세한 내용은 [Azure에서 사용할 수 있는 디스크 유형](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk) 문서를 참조하세요.

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>순수 Ultra Disk 구성을 사용하는 프로덕션 권장 스토리지 솔루션
이 구성에서는 /hana/data 볼륨과 /hana/log 볼륨을 별도로 유지합니다. 제안된 값은 SAP가 [SAP TDI 스토리지 백서](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)에서 권장하는 대로 SAP HANA 및 스토리지 구성에 대한 VM 유형을 인증하는 KPI에서 도출된 것입니다.

권장 사항은 이 문서의 앞부분에서 설명한 대로 SAP 최소 요구 사항을 초과하는 경우가 많습니다. 나열된 권장 사항은 SAP에서 권장하는 크기와 다양한 VM 유형이 제공하는 최대 스토리지 처리량 간의 절충입니다.

| VM SKU | RAM | 최대 VM I/O<br /> 처리량 | /hana/data 볼륨 | /hana/data I/O 처리량 | /hana/data IOPS | /hana/log 볼륨 | /hana/log I/O 처리량 | /hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E64s_v3 | 432GiB | 1,200MB/s | 600GB | 700MBps | 7,500 | 512 GB | 500MBps  | 2,000 |
| M32ts | 192GiB | 500MB/s | 250GB | 400MBps | 7,500 | 256GB | 250MBps  | 2,000 |
| M32ls | 256GiB | 500MB/s | 300GB | 400MBps | 7,500 | 256GB | 250MBps  | 2,000 |
| M64ls | 512GiB | 1,000MB/s | 600GB | 600MBps | 7,500 | 512 GB | 400MBps  | 2,500 |
| M64s | 1,000GiB | 1,000MB/s |  1,200GB | 600MBps | 7,500 | 512 GB | 400MBps  | 2,500 |
| M64ms | 1,750GiB | 1,000MB/s | 2,100GB | 600MBps | 7,500 | 512 GB | 400MBps  | 2,500 |
| M128s | 2,000GiB | 2,000MB/s |2,400GB | 1,200MBps |9,000 | 512 GB | 800MBps  | 3,000 | 
| M128ms | 3,800GiB | 2,000MB/s | 4,800GB | 1200MBps |9,000 | 512 GB | 800MBps  | 3,000 | 
| M208s_v2 | 2,850GiB | 1,000MB/s | 3,500GB | 1,000MBps | 9,000 | 512 GB | 400MBps  | 2,500 | 
| M208ms_v2 | 5,700GiB | 1,000MB/s | 7,200GB | 1,000MBps | 9,000 | 512 GB | 400MBps  | 2,500 | 
| M416s_v2 | 5,700GiB | 2,000MB/s | 7,200GB | 1,500MBps | 9,000 | 512 GB | 800MBps  | 3,000 | 
| M416ms_v2 | 11,400GiB | 2,000MB/s | 14,400GB | 1,500MBps | 9,000 | 512 GB | 800MBps  | 3,000 |   

나열된 값은 시작점으로 사용되며 실제 수요를 기준으로 평가해야 합니다. Azure Ultra Disk의 장점은 VM을 종료하거나 시스템에 적용되는 워크로드를 중지하지 않고도 IOPS 및 처리량 값을 조정할 수 있다는 것입니다.   

> [!NOTE]
> 이제까지 Ultra Disk 스토리지를 사용하는 스토리지 스냅샷은 사용할 수 없습니다. 따라서 Azure Backup 서비스에서 VM 스냅샷을 사용할 수 없습니다.

### <a name="cost-conscious-storage-solution-with-pure-ultra-disk-configuration"></a>순수 Ultra Disk 구성을 사용하는 비용 인식 스토리지 솔루션
이 구성에서는 /hana/data 및 /hana/log 볼륨이 동일한 디스크에 있습니다. 제안된 값은 SAP가 [SAP TDI 스토리지 백서](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)에서 권장하는 대로 SAP HANA 및 스토리지 구성에 대한 VM 유형을 인증하는 KPI에서 도출된 것입니다. 

권장 사항은 이 문서의 앞부분에서 설명한 대로 SAP 최소 요구 사항을 초과하는 경우가 많습니다. 나열된 권장 사항은 SAP에서 권장하는 크기와 다양한 VM 유형이 제공하는 최대 스토리지 처리량 간의 절충입니다.

| VM SKU | RAM | 최대 VM I/O<br /> 처리량 | /hana/data 및 /hana/log 볼륨 | /hana/data 및 /hana/log I/O 처리량 | /hana/data 및 /hana/log IOPS |
| --- | --- | --- | --- | --- | --- |
| E64s_v3 | 432GiB | 1,200MB/s | 1,200GB | 1,200MBps | 9,500 | 
| M32ts | 192GiB | 500MB/s | 512 GB | 400MBps | 9,500 | 
| M32ls | 256GiB | 500MB/s | 600GB | 400MBps | 9,500 | 
| M64ls | 512GiB | 1,000MB/s | 1,100GB | 900MBps | 10000 | 
| M64s | 1,000GiB | 1,000MB/s |  1,700GB | 900MBps | 10000 | 
| M64ms | 1,750GiB | 1,000MB/s | 2,600GB | 900MBps | 10000 | 
| M128s | 2,000GiB | 2,000MB/s |2,900GB | 1,800MBps |12,000 | 
| M128ms | 3,800GiB | 2,000MB/s | 5,300GB | 1,800MBps |12,000 |  
| M208s_v2 | 2,850GiB | 1,000MB/s | 4,000GB | 900MBps | 10000 |  
| M208ms_v2 | 5,700GiB | 1,000MB/s | 7,700GB | 900MBps | 10000 | 
| M416s_v2 | 5,700GiB | 2,000MB/s | 7,700GB | 1,800MBps | 12,000 |  
| M416ms_v2 | 11,400GiB | 2,000MB/s | 15,000GB | 1,800MBps | 12,000 |    

나열된 값은 시작점으로 사용되며 실제 수요를 기준으로 평가해야 합니다. Azure Ultra Disk의 장점은 VM을 종료하거나 시스템에 적용되는 워크로드를 중지하지 않고도 IOPS 및 처리량 값을 조정할 수 있다는 것입니다.  

## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>Azure NetApp Files 기반 NFS v 4.1 볼륨
Azure NetApp Files는 /hana/shared, /hana/data 및 /hana/log 볼륨에 사용할 수 있는 네이티브 NFS 공유를 제공합니다. /hana/data 및 /hana/log 볼륨에 ANF 기반 NFS 공유를 사용하려면 v4.1 NFS 프로토콜을 사용해야 합니다. 공유가 ANF를 기반으로 하는 경우 NFS 프로토콜 v3은 /hana/data 및 /hana/log 볼륨에서 사용이 지원되지 않습니다. 

> [!IMPORTANT]
> Azure NetApp Files에서 구현된 NFS v3 프로토콜은 /hana/data 및 /hana/log에서 사용이 지원되지 **않습니다**. /hana/data 및 /hana/log 볼륨에는 기능 관점에서 NFS 4.1 사용이 필수입니다. 반면 /hana/shared 볼륨에는 기능 관점에서 NFS v3 또는 NFS v 4.1 프로토콜을 사용할 수 있습니다.

### <a name="important-considerations"></a>중요 고려 사항
SAP Netweaver 및 SAP HANA에 Azure NetApp Files를 고려하는 경우 다음과 같은 중요한 사항을 고려해야 합니다.

- 최소 용량 풀은 4TiB입니다.  
- 최소 볼륨 크기는 100GiB입니다.
- Azure NetApp Files 및 Azure NetApp Files 볼륨이 탑재되는 모든 가상 머신은 동일한 Azure Virtual Network 또는 동일한 지역의 [피어링된 가상 네트워크](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)에 있어야 합니다.  
- 선택한 가상 네트워크에는 Azure NetApp Files로 위임된 서브넷이 있어야 합니다.
- Azure NetApp 볼륨의 처리량은 [Azure NetApp Files에 대한 서비스 수준](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)에 설명된 대로 볼륨 할당량과 서비스 수준의 함수입니다. HANA Azure NetApp 볼륨을 크기 조정할 때 결과 처리량이 HANA 시스템 요구 사항을 충족해야 합니다.  
- Azure NetApp Files는 [내보내기 정책](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)을 제공합니다. 사용자는 허용되는 클라이언트, 액세스 유형(읽기 및 쓰기, 읽기 전용 등)을 제어할 수 있습니다. 
- Azure NetApp Files 기능은 아직 영역을 인식하지 않습니다. 현재 Azure NetApp Files 기능은 Azure 지역의 모든 가용성 영역에 배포되지 않습니다. 일부 Azure 지역에서 대기 시간이 미칠지도 모르는 영향을 염두에 두어야 합니다.  
- 짧은 대기 시간을 위해 Azure NetApp 스토리지와 근접하게 가상 머신을 배포하는 것이 중요합니다. 
- 가상 머신의 <b>sid</b>adm 사용자 ID 및 `sapsys` 그룹 ID는 Azure NetApp Files의 구성과 일치해야 합니다. 

> [!IMPORTANT]
> SAP HANA 워크로드의 경우 짧은 대기 시간이 매우 중요합니다. Microsoft 담당자와 협력하여 가상 머신과 Azure NetApp Files 볼륨이 근접하게 배포되도록 해야 합니다.  

> [!IMPORTANT]
> 가상 머신과 Azure NetApp 구성 간에 <b>sid</b>adm 사용자 ID 및 `sapsys` 그룹 ID가 일치하지 않는 경우 가상 머신에 탑재된 Azure NetApp 볼륨의 파일에 대한 권한이 `nobody`로 표시됩니다. Azure NetApp Files에 [새 시스템을 온보딩](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u)할 때 <b>sidadm</b> 사용자 ID 및 `sapsys` 그룹 ID를 올바로 지정해야 합니다.

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Azure NetApp Files에서 HANA 데이터베이스 크기 조정

Azure NetApp 볼륨의 처리량은 [Azure NetApp Files에 대한 서비스 수준](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)에 설명된 대로 볼륨 크기와 서비스 수준의 함수입니다. 

Azure에서 SAP용 인프라를 설계할 때 다음의 최소 처리량 특성으로 변환되는 SAP의 최소 스토리지 처리량 요구 사항에 대해 알고 있어야 합니다.

- 1MB I/O 크기의 /hana/log에 대해 250MB/초의 읽기/쓰기 작업을 사용하도록 설정합니다.  
- 16MB 및 64MB I/O 크기의 /hana/data에 대해 최소 400MB/초의 읽기 작업을 사용하도록 설정합니다.  
- 16MB 및 64MB I/O 크기의 /hana/data에 대해 최소 250MB/초의 쓰기 작업을 사용하도록 설정합니다.  

볼륨 할당량 1TiB당 [Azure NetApp Files 처리량 한도](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)는 다음과 같습니다.
- Premium Storage 계층 - 64MiB/s  
- Ultra Storage 계층 - 128MiB/s  

> [!IMPORTANT]
> 단일 NFS 볼륨에 배포하는 용량에 관계없이 처리량은 가상 머신의 소비자가 활용하는 1.2~1.4GB/초 대역폭의 범위에서 안정될 것으로 예상됩니다. 이는 ANF 제품의 기본 아키텍처와 NFS 관련 Linux 세션 제한과 관련이 있습니다. [Azure NetApp Files에 대한 성능 벤치 마크 테스트 결과](https://docs.microsoft.com/azure/azure-netapp-files/performance-benchmarks-linux) 문서에 설명된 성능 및 처리량 수치는 여러 클라이언트 VM이 있는 한 공유 NFS 볼륨에서 여러 세션에 걸쳐 테스트한 결과입니다. 이 시나리오는 SAP에서 측정하는 시나리오와 다릅니다. 즉, ANF에서 호스트되는 단일 NFS 볼륨에 대해 단일 VM에서 처리량을 측정합니다.

데이터 및 로그에 대한 SAP 최소 처리량 요구 사항을 충족하기 위해 또한 `/hana/shared`에 대한 지침에 따라 권장 크기는 다음과 같습니다.

| 볼륨 | 크기<br /> Premium Storage 계층 | 크기<br /> Ultra Storage 계층 | 지원되는 NFS 프로토콜 |
| --- | --- | --- |
| /hana/log/ | 4TiB | 2TiB | v4.1 |
| /hana/data | 6.3TiB | 3.2TiB | v4.1 |
| /hana/shared | 4개 작업자 노드당 최대(512GB, 1xRAM) | 4개 작업자 노드당 최대(512GB, 1xRAM) | v3 또는 v4.1 |


> [!NOTE]
> 여기에 명시된 Azure NetApp Files 크기 조정 권장 사항은 SAP가 인프라 공급자에게 표명하는 최소 요구 사항을 충족하는 것을 목표로 합니다. 실제 고객 배포 및 워크로드 시나리오에서는 충분하지 않을 수 있습니다. 이러한 권장 사항을 시작점으로 삼아 워크로드의 요구 사항에 따라 조정합니다.  

따라서 Ultra Disk 스토리지에 대해 나열된 것처럼 ANF 볼륨에 비슷한 처리량을 배포하는 것을 고려할 수 있습니다. 또한 이미 Ultra Disk 테이블에서 수행한 것처럼 다른 VM SKU의 볼륨에 대해 나열된 크기에 대해서도 크기를 고려합니다.

> [!TIP]
> 볼륨을 `unmount`하거나 가상 머신을 중지하거나 SAP HANA를 중지하지 않고도 Azure NetApp Files 볼륨의 크기를 동적으로 조정할 수 있습니다. 그러므로 애플리케이션이 예상된 처리량 수요와 예측하지 못한 처리량 수요를 모두 충족할 수 있습니다.

ANF에서 호스트되는 NFS v4.1 볼륨을 사용하는 대기 노드로 SAP HANA 스케일 아웃 구성을 배포하는 방법에 대한 설명서는 [SUSE Linux Enterprise Server의 Azure NetApp Files를 사용하여 Azure VM의 대기 노드로 SAP HANA 스케일 아웃](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)에 게시되어 있습니다.


## <a name="next-steps"></a>다음 단계
자세한 내용은 다음을 참조하세요.

- [Azure Virtual Machines의 SAP HANA 고가용성 가이드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
