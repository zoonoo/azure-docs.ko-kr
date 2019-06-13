---
title: SAP HANA Azure 가상 머신 저장소 구성 | Microsoft Docs
description: SAP HANA에 배포 된 VM에 대 한 저장소 권장 사항
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d062b6fff9693d5bda75edd65b8fe88d834eff57
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735513"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>SAP HANA Azure 가상 머신 저장소 구성

Azure는 SAP HANA를 실행 하는 Azure Vm에 적합 하는 다양 한 유형의 저장소를 제공 합니다. 와 같은 SAP HANA 배포 목록에 대 한 간주할 수 있는 Azure storage 유형: 

- 표준 SSD 디스크 드라이브 (SSD)
- 프리미엄 SSD(반도체 드라이브)
- 공개 미리 보기로 제공 ultra SSD 및 프로덕션 SAP 응용 프로그램을 사용 하 여 지원 되지 않음

이러한 디스크 종류에 대 한 자세한 내용은 [디스크 유형 선택](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types)

Azure는 Azure Standard 및 Premium Storage에서 VHD를 위한 두 가지 배포 방법을 제공합니다. 전반적인 시나리오에서 가능하다면 [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) 배포를 활용하세요. 

IOPS 및 저장소 처리량에서 저장소 유형 및 해당 SLA의 목록을 보려면 [관리 디스크에 대한 Azure 설명서](https://azure.microsoft.com/pricing/details/managed-disks/)를 참조하세요.

**권장 사항: Azure Write Accelerator를 사용 하 여 함께에서 Azure Premium Storage를 사용 하 고 배포에 대 한 Azure Managed Disks를 사용**

온-프레미스 환경에서 거의 I/O 하위 시스템 및 해당 기능에 대 한 처리 해야 합니다. SAP HANA에 대한 최소 스토리지 요구 사항을 충족하는 것은 어플라이언스 공급업체의 몫이었기 때문입니다. Azure 인프라를 직접 작성할 때에 이러한 요구 사항의 일부를 숙지 해야 합니다. 필요 하 게 생성 되 라는 메시지가 표시 되는 최소 처리량 특성 중 일부입니다.

- 읽기/쓰기를 사용 하도록 설정 **/hana/log** I/O 크기로 1MB를 사용 하 여 250MB/sec
- 16MB 및 64MB I/O 크기의 **/hana/data**에 대해 최소 400MB/초의 읽기 작업을 사용하도록 설정합니다.
- 16MB 및 64MB I/O 크기의 **/hana/data**에 대해 최소 250MB/초의 쓰기 작업을 사용하도록 설정합니다.

낮은 스토리지 대기 시간이 DBMS 시스템에 중요하므로 SAP HANA와 같이 DBMS의 경우에도 데이터를 메모리 내에 유지합니다. 저장소에서 중요한 경로는 일반적으로 DBMS 시스템의 트랜잭션 로그 쓰기입니다. 그러나 쓰기 저장점 또는 충돌 복구 후 메모리 내 데이터 로드와 같은 작업도 중요할 수 있습니다. 따라서 **필수** 에 대 한 Azure Premium Disk를 활용 하 여 **hana/data** 하 고 **/hana/log** 볼륨입니다. SAP에서의 필요에 따라 **/hana/log** 및 **/hana/data**의 최소 처리량을 달성하기 위해 여러 Azure Premium Storage 디스크에 대한 MDADM 또는 LVM을 사용하여 RAID 0을 빌드해야 합니다. 또한 **/hana/data** 및 **/hana/log** 볼륨으로 RAID 볼륨을 사용합니다. 

**권장 사항: 권장 사항을 raid 0 스트라이프 크기로 사용 하는 것:**

- **/hana/data**에 대해 64KB 또는 128KB
- **/hana/log**에 대해 32KB

> [!NOTE]
> Azure Premium과 Standard 저장소는 세 개의 VHD 이미지를 유지하므로 RAID 볼륨을 사용하여 모든 중복 수준을 구성할 필요가 없습니다. RAID 볼륨의 사용법은 순수하게 충분한 I/O 처리량을 제공하는 볼륨을 구성하는 것입니다.

RAID 아래의 Azure VHD 수 누적은 IOPS 및 저장소 처리량 측면의 누적입니다. 따라서 3 x P30 Azure Premium Storage 디스크에 대해 RAID 0을 설정하는 경우 IOPS의 세 배 및 단일 Azure Premium Storage P30 디스크의 스토리지 처리량의 세 배를 제공해야 합니다.

아래 캐싱 권장 사항은 다음과 같이 나열된 SAP HANA에 대한 I/O 특성을 가정하고 있습니다.

- HANA 데이터 파일에 대한 읽기 워크로드가 거의 없습니다. 예외는 HANA 인스턴스를 다시 시작한 후 또는 HANA에 데이터를 로드할 때 발생하는 대규모 I/O입니다. 데이터 파일에 대한 대규모 I/O의 또 다른 사례는 HANA 데이터베이스 백업일 수 있습니다. 대부분의 경우 모든 데이터 파일 볼륨을 완전히 읽어야 하므로 결과적으로 읽기 캐싱은 의미가 없습니다.
- HANA 저장점 및 HANA 크래시 복구를 기반으로 데이터 파일에 대한 쓰기가 갑작스럽게 발생합니다. 저장점 쓰기는 비동기이며 사용자 트랜잭션을 유지하지 않습니다. 크래시 복구 중에 데이터 쓰기는 시스템이 다시 빠르게 응답하기 위해 성능이 중요합니다. 그러나 크래시 복구는 예외적인 상황이어야 합니다.
- HANA 다시 실행 파일에는 읽기가 거의 없습니다. 트랜잭션 로그 백업, 크래시 복구를 수행하는 경우 또는 HANA 인스턴스를 다시 시작하는 단계의 대규모 I/O는 예외입니다.  
- SAP HANA 다시 실행 로그 파일에 대한 기본 부하는 쓰기입니다. 워크로드의 특성에 따라 4KB 정도의 작은 I/O 또는 다른 경우 1MB 이상의 I/O 크기가 있을 수 있습니다. SAP HANA 다시 실행 로그에 대한 쓰기 대기 시간은 성능이 중요합니다.
- 모든 쓰기는 신뢰할 수 있는 방식으로 디스크에 유지되어야 합니다.

**권장 사항: SAP HANA에서 관찰 된 I/O 패턴에 결과로 Azure Premium Storage를 사용 하 여 서로 다른 볼륨에 대 한 캐싱 같이 설정 됩니다.**

- **/hana/data** - 캐싱 없음
- **/hana/log** - 캐싱 없음 - M 시리즈에 대한 예외(이 문서의 뒷부분 참조)
- **/hana/shared** - 읽기 캐싱


VM의 크기를 조정하거나 결정할 때도 전체 VM I/O 처리량을 고려하세요. 전체 VM 저장소 처리량은 [메모리 최적화 가상 머신 크기](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory) 문서에 설명되어 있습니다.

## <a name="linux-io-scheduler-mode"></a>Linux I/O 스케줄러 모드
Linux에는 몇 가지 다른 I/O 일정 예약 모드가 있습니다. Linux 공급 업체 및 SAP를 통해 일반적으로 권장에서 디스크 볼륨에 대 한 I/O 스케줄러 모드를 다시 구성 하는 것은 **cfq** 모드는 **noop** 모드입니다. 자세한 내용은 [SAP 참고 #1984798](https://launchpad.support.sap.com/#/notes/1984787)을 참조했습니다. 


## <a name="production-storage-solution-with-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Azure M 시리즈 virtual machines에 Azure Write Accelerator를 사용 하 여 프로덕션 저장소 솔루션
Azure Write Accelerator는 Azure M 시리즈 VM 전용으로 출시되는 기능입니다. 이름에서 알 수 있듯이 이 기능은 Azure Premium Storage에 대한 쓰기의 I/O 대기 시간을 향상시키기 위한 것입니다. SAP HANA의 경우 Write Accelerator는 **/hana/log** 볼륨에 대해서만 사용해야 합니다. 따라서 합니다 **hana/data** 및 **/hana/log** Azure Write Accelerator에서 지 원하는 별도 볼륨이 합니다 **/hana/log** 볼륨만 합니다. 

> [!IMPORTANT]
> Azure M 시리즈 가상 머신에 대한 SAP HANA 인증은 **/hana/log** 볼륨에 대해 Azure Write Accelerator를 독점적으로 사용하는 것입니다. 결과적으로, Azure M 시리즈 가상 머신에 프로덕션 시나리오 SAP HANA 배포는 **/hana/log** 볼륨에 대해 Azure Write Accelerator를 사용하여 구성되어야 합니다.  

> [!NOTE]
> 프로덕션 시나리오의 경우 특정 VM 유형이 [IAAS에 대한 SAP 설명서](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)에서 SAP별 SAP HANA에 대해 지원되는지 여부를 확인합니다.

**권장 사항: 프로덕션 시나리오에 대 한 권장된 구성을 같습니다.**

| VM SKU | RAM | 최대 VM I/O<br /> 처리량 | /hana/data | /hana/log | /hana/shared | /root 볼륨 | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192GiB | 500MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256GiB | 500MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512GiB | 1000MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1000GiB | 1000MB/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750GiB | 1000MB/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000GiB | 2000MB/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 2 x P40 |
| M128ms | 3800GiB | 2000MB/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P40 |
| M208s_v2 | 2850 GiB | 1000MB/s | 4 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P40 |
| M208ms_v2 | 5700 GiB | 1000MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416s_v2 | 5700 GiB | 2000MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416ms_v2 | 11400 GiB | 2000MB/s | 8 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P50 |

M416xx_v2 VM 유형에 아직 사용할 수 없습니다 Microsoft에서 공개 합니다. 제안 된 다른 볼륨에 대 한 저장소 처리량 실행 하려는 워크 로드를 충족 하는지 확인 합니다. 작업에 **/hana/data** 및 **/hana/log**에 대한 더 높은 볼륨이 필요한 경우 Azure Premium Storage VHD의 수를 증가시켜야 합니다. Azure 가상 머신 유형의 한도 내에서 I/O 처리량을 나열 된 증가 IOPS 보다 더 많은 Vhd 사용 하 여 볼륨 크기를 조정 합니다.

Azure Write Accelerator는 [Azure 관리 디스크](https://azure.microsoft.com/services/managed-disks/)와만 함께 작동합니다. 따라서 **/hana/log** 볼륨을 형성하는 Azure Premium Storage 디스크는 관리 디스크로 배포되어야 합니다.

Azure Write Accelerator에서 지원할 수 있는 VM당 Azure Premium Storage VHD 수는 제한됩니다. 이 제한은 현재 다음과 같습니다.

- M128xx 및 M416xx VM에 대해 16 개 Vhd
- M64xx 및 M208xx vm 8 개 Vhd
- M32xx VM당 4개 VHD

Azure Write Accelerator를 활성화하는 방법에 대한 자세한 지침은 [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) 문서에서 확인할 수 있습니다.

Azure Write Accelerator에 대한 세부 정보 및 제한 사항은 동일한 설명서에서 찾을 수 있습니다.

**권장 사항: /Hana/log 볼륨을 형성 하는 디스크에 Write Accelerator를 사용 해야 합니다.**


## <a name="cost-conscious-azure-storage-configuration"></a>비용 인식 Azure Storage 구성
다음 표에서 Azure Vm에서 SAP HANA를 호스트에 고객을 사용 하는 VM 유형의 구성을 보여 줍니다. SAP에서 SAP HANA를 사용 하 여 지원 공식적으로 되지 않습니다 또는 SAP HANA에 대 한 모든 최소 저장소 조건에 맞지 않을 수 있는 일부 VM 유형이 있을 수 있습니다. 하지만 지금까지 해당 VM은 비-프로덕션 시나리오에 대해 제대로 수행되는 것처럼 보였습니다. 합니다 **hana/data** 하 고 **/hana/log** 볼륨 하나에 결합 됩니다. 결과적으로 Azure Write Accelerator 사용에는 IOPS 기준으로 제한 될 수 있습니다.

> [!NOTE]
> SAP 지원 시나리오에서 SAP 별 SAP HANA에 대 한 특정 VM 유형을 지원 되는지 여부를 확인 합니다 [IAAS에 대 한 SAP 설명서](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)합니다.

> [!NOTE]
> 비용 인식 솔루션에 대 한 이전의 권장 사항에서 변경 된 내용에서 이동 하는 것 [Azure 표준 HDD 디스크](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) 향상을 수행 하 고 안정적 [Azure 표준 SSD 디스크](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd)


| VM SKU | RAM | 최대 VM I/O<br /> 처리량 | /hana/data 및 /hana/log<br /> (LVM 또는 MDADM으로 스트라이프됨) | /hana/shared | /root 볼륨 | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112GiB | 768MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E16v3 | 128GiB | 384MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E32v3 | 256GiB | 768MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| E64v3 | 432GiB | 1200MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| GS5 | 448GiB | 2000MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| M32ts | 192GiB | 500MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M32ls | 256GiB | 500MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M64ls | 512GiB | 1000MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 |1 x E30 |
| M64s | 1000GiB | 1000MB/s | 2 x P30 | 1 x E30 | 1 x E6 | 1 x E6 |2 x E30 |
| M64ms | 1750GiB | 1000MB/s | 3 x P30 | 1 x E30 | 1 x E6 | 1 x E6 | 3 x E30 |
| M128s | 2000GiB | 2000MB/s |3 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E40 |
| M128ms | 3800GiB | 2000MB/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E50 |
| M208s_v2 | 2850 GiB | 1000MB/s | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 |  3 x E40 |
| M208ms_v2 | 5700 GiB | 1000MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416s_v2 | 5700 GiB | 2000MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416ms_v2 | 11400 GiB | 2000MB/s | 8 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E50 |


M416xx_v2 VM 유형에 아직 사용할 수 없습니다 Microsoft에서 공개 합니다. 3 x P20을 사용하는 작은 VM 유형에 권장되는 디스크는 [SAP TDI Storage Whitepaper](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)(SAP TDI 스토리지 백서)에 따른 공간 권장 사항과 관련된 볼륨 크기를 초과합니다. 하지만 이 표에 표시된 항목은 SAP HANA에 필요한 충분한 디스크 처리량을 제공하도록 선택되었습니다. 메모리 볼륨의 두 배인 백업을 유지하기 위해 크기 지정된 **/hana/backup** 볼륨을 변경해야 하는 경우 자유롭게 조정할 수 있습니다.   
제안 된 다른 볼륨에 대 한 저장소 처리량 실행 하려는 워크 로드를 충족 하는지 확인 합니다. 작업에 **/hana/data** 및 **/hana/log**에 대한 더 높은 볼륨이 필요한 경우 Azure Premium Storage VHD의 수를 증가시켜야 합니다. Azure 가상 머신 유형의 한도 내에서 I/O 처리량을 나열 된 증가 IOPS 보다 더 많은 Vhd 사용 하 여 볼륨 크기를 조정 합니다. 

> [!NOTE]
> 위의 구성은 Azure Premium Storage와 Azure Standard Storage의 혼합을 사용하지 않기 때문에 [Azure 가상 머신 단일 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/)의 이점을 얻지 못합니다. 그러나 비용을 최적화하기 위해 선택 영역이 선택되었습니다. VM 구성을 Azure 단일 VM SLA와 호환되도록 하려면 Azure Standard Storage(Sxx)로 나열된 위의 모든 디스크에 대한 Premium Storage를 선택해야 합니다.


> [!NOTE]
> 명시된 디스크 구성 권장 사항은 SAP가 인프라 공급자에 대해 표현한 최소 요구 사항을 대상으로 합니다. 실제 고객 배치 및 워크로드 시나리오에서 이러한 권장 사항이 여전히 충분한 기능을 제공하지 못하는 상황이 발생했습니다. 이는 고객이 HANA를 다시 시작한 후 또는 백업 구성에서 저장소에 대해 더 높은 대역폭을 요구한 경우 데이터를 빠르게 재로드하도록 요구한 상황일 수 있습니다. 다른 사례로는 특정 워크로드에 대해 5000 IOPS가 충분하지 않은 **/hana/log**가 포함됐습니다. 따라서 이러한 권장 사항을 시작으로 워크로드의 요구 사항에 따라 적절히 조정합니다.
>  

## <a name="azure-ultra-ssd-storage-configuration-for-sap-hana"></a>SAP HANA에 대 한 azure Ultra SSD 저장소 구성
Microsoft는 새 Azure storage 형식을 소개 하는 중 이라고 [Azure Ultra SSD](https://azure.microsoft.com/updates/ultra-ssd-new-azure-managed-disks-offering-for-your-most-latency-sensitive-workloads/)합니다. 지금까지 제공 하는 Azure storage 및 울트라 SSD의 가장 큰 차이 디스크 기능 디스크 크기에 더 이상 바인딩되지 않았음을 합니다. 고객에 게 Ultra SSD에 대 한 이러한 기능을 정의할 수 있습니다.

- 65,536 GiB 까지의 4 GiB에서 디스크의 크기
- 160 20,000iops 100 IOPS에서 IOPS 범위 (최대 VM 형식에 따라 다름)
- 300 MB/sec에서 초당 2,000MB의 저장소 처리량

문서 세부 정보에 대 한 조회 [Ultra SSD 발표-차세대 Azure Disks 기술 (미리 보기)](https://azure.microsoft.com/blog/announcing-ultra-ssd-the-next-generation-of-azure-disks-technology-preview/)

UltraSSD에 크기, IOPS 및 디스크 처리량 범위를 충족 하는 단일 디스크를 정의할 수가 있습니다. LVM 또는 MDADM Azure Premium Storage를 기반으로 같은 논리 볼륨 관리자를 사용 하 여 IOPS 및 저장소 처리량 요구 사항을 충족 하는 볼륨을 생성 하는 대신 합니다. Premium Storage UltraSSD 사이의 구성 혼합을 실행할 수 있습니다. 성능이 중요 한 /hana/data 및 /hana/log 볼륨에 UltraSSD 사용 하는 제한 Azure Premium Storage를 사용 하 여 다른 볼륨을 포함 하는 결과적으로,

| VM SKU | RAM | 최대 VM I/O<br /> 처리량 | hana/data 볼륨 | hana/data I/O 처리량 | hana/data IOPS | hana/log 볼륨 | hana/log I/O 처리량 | hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192GiB | 500MB/s | 250GB | 500mbps | 7,500 | 256GB | 500mbps  | 2000 |
| M32ls | 256GiB | 500MB/s | 300GB | 500mbps | 7,500 | 256GB | 500mbps  | 2000 |
| M64ls | 512GiB | 1000MB/s | 600GB | 500mbps | 7,500 | 512 GB | 500mbps  | 2000 |
| M64s | 1000GiB | 1,000 MB/s |  1200GB | 500mbps | 7,500 | 512 GB | 500mbps  | 2000 |
| M64ms | 1750GiB | 1,000 MB/s | 2100 GB | 500mbps | 7,500 | 512 GB | 500mbps  | 2000 |
| M128s | 2000GiB | 2,000 MB/s |2400 GB | 1200 MBps |9000 | 512 GB | 800 MBps  | 2000 | 
| M128ms | 3800GiB | 2,000 MB/s | 4800 GB | 1200 MBps |9000 | 512 GB | 800 MBps  | 2000 | 
| M208s_v2 | 2850 GiB | 1,000 MB/s | 3500 GB | 1000 MBps | 9000 | 512 GB | 500mbps  | 2000 | 
| M208ms_v2 | 5700 GiB | 1,000 MB/s | 7200 GB | 1000 MBps | 9000 | 512 GB | 500mbps  | 2000 | 
| M416s_v2 | 5700 GiB | 2,000 MB/s | 7200 GB | 1500 M Bps | 9000 | 512 GB | 800 MBps  | 2000 | 
| M416ms_v2 | 11400 GiB | 2,000 MB/s | 14400 GB | 1500 MBps | 9000 | 512 GB | 800 MBps  | 2000 |   

M416xx_v2 VM 유형에 아직 사용할 수 없습니다 Microsoft에서 공개 합니다. 나열 된 값은 시작 지점으로 이며 실제 요구에 대해 평가 해야 합니다. Azure Ultra SSD를 사용 하 여 장점은 IOPS 및 처리량에 대 한 값을 VM을 종료 하지 않고도 사용할 수 있습니다 또는 시스템에 적용 된 워크 로드를 중지 합니다.   

> [!NOTE]
> 지금 UltraSSD 저장소를 사용 하 여 저장소 스냅숏을 사용할 수 없는 경우 Azure Backup 서비스를 사용 하 여 VM 스냅숏 사용이 차단

## <a name="next-steps"></a>다음 단계
자세한 내용은 다음을 참조하세요.

- [Azure virtual machines의 SAP HANA 고가용성 가이드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)합니다.