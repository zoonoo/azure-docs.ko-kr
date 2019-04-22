---
title: Azure에서 SAP HANA 인프라 구성 및 작업 | Microsoft Docs
description: Azure Virtual Machines에 배포된 SAP HANA 시스템에 대한 운영 가이드입니다.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: acccc553c5b63b2acd0f9793b0397b25145449dd
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59699341"
---
# <a name="sap-hana-infrastructure-configurations-and-operations-on-azure"></a>Azure에서 SAP HANA 인프라 구성 및 작업
이 문서에서는 Azure 인프라를 구성 하 고 Azure 네이티브 virtual machines (Vm)에 배포 된 SAP HANA 시스템을 작동 하는 방법에 지침을 제공 합니다. 또한이 문서에서는 SAP HANA를 위한 스케일 아웃 M128s VM SKU에 대 한 구성 정보를 포함합니다. 이 문서에서는 다음 내용을 포함 하는 표준 SAP 설명서를 대체 하기 위한 되지 않습니다.

- [SAP administration guide](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/330e5550b09d4f0f8b6cceb14a64cd22.html)(SAP 관리 가이드)
- [SAP installation guides](https://service.sap.com/instguides)(SAP 설치 가이드)
- [SAP Note](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>필수 조건
이 가이드를 사용하려면 다음 Azure 구성 요소에 대한 기본 지식이 필요합니다.

- [Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Azure 네트워킹 및 가상 네트워크](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure Storage](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Azure의 SAP NetWeaver 및 기타 SAP 구성 요소에 대해 자세히 알아보려면 [Azure 설명서](https://docs.microsoft.com/azure/)의 [Azure의 SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) 섹션을 참조하세요.

## <a name="basic-setup-considerations"></a>기본 설치 고려 사항
다음 섹션에서는 Azure VM에 SAP HANA 시스템을 배포하기 위한 기본 설치 고려 사항을 설명합니다.

### <a name="connect-to-azure-virtual-machines"></a>Azure virtual machines에 연결
에 설명 된 대로 합니다 [Azure virtual machines 계획 가이드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide), 기본적인 방법으로 Azure Vm에 연결할 때 사용 됩니다.

- 인터넷 및 SAP HANA를 실행 하는 VM 또는 JumpBox VM에 공용 끝점을 통해 연결 합니다.
- [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) 또는 Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/)를 통해 연결

VPN 또는 ExpressRoute를 통한 사이트 간 연결은 프로덕션 시나리오에 필요합니다. 이 유형의 연결은 프로덕션 시나리오 SAP 소프트웨어를 사용 하는 위치는 비프로덕션 시나리오에 대 한 필요 합니다. 다음 그림은 사이트 간 연결의 예를 보여 줍니다.

![사이트 간 연결](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Azure VM 유형 선택
프로덕션 시나리오에 사용할 Azure VM 형식을 합니다 [IAAS에 대 한 SAP 설명서](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)합니다. 비프로덕션 시나리오에 대 한 다양 한 네이티브 Azure VM 유형 사용할 수 있는 경우

>[!NOTE]
> 비프로덕션 시나리오의 경우에 나열 된 VM 유형을 사용 합니다 [SAP Note #1928533](https://launchpad.support.sap.com/#/notes/1928533)합니다. 프로덕션 시나리오에 대 한 사용 Azure vm에서 SAP HANA 인증 된 게시 된 sap에서 Vm에 대 한 확인 [인증 IaaS 플랫폼 목록](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)합니다.

Azure에서 Vm을 배포 하려면 다음을 사용 합니다.

- Azure Portal
- Azure PowerShell cmdlet
- Azure CLI

또한 전체 설치 된 SAP HANA 플랫폼을 통해 Azure VM 서비스를 배포할 수는 [SAP cloud platform](https://cal.sap.com/)합니다. 설치 프로세스에 대 한 자세한 내용은 [배포 SAP S/4HANA 또는 Azure에서 BW/4HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)합니다. 릴리스된 자동화에 대 한 내용은 참조 하세요 [이 GitHub 문서](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)합니다.

### <a name="choose-an-azure-storage-type"></a>Azure Storage 유형 선택
Azure는 SAP HANA를 실행 하는 Azure Vm에 적합 하는 두 가지 유형의 저장소를 제공 합니다.  

- 표준 하드 디스크 드라이브 (Hdd)
- Premium Ssd (반도체 드라이브)

이러한 디스크 종류를 알아보려면 [디스크 유형 선택](../../windows/disks-types.md)합니다.

Azure에 Azure standard storage 및 premium storage Vhd에 대 한 두 가지 배포 방법을 제공합니다. 전반적인 시나리오에서 가능하다면 [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) 배포를 활용하세요.

저장소 유형 및 해당 IOPS 및 저장소 처리량 Sla의 목록을 참조 하세요 [관리 되는 디스크에 대 한 Azure 설명서](https://azure.microsoft.com/pricing/details/managed-disks/)합니다.

### <a name="configure-the-storage-for-azure-virtual-machines"></a>Azure virtual machines에 대 한 저장소 구성

있습니다 하지 않을 다르고 I/O 하위 시스템 및 해당 기능에 대 한 SAP HANA에 대 한 최소 저장소 요구 사항을 충족 된 어플라이언스 공급 업체 때문입니다. Azure 인프라를 직접 빌드할 때 이러한 요구 사항 중 일부에 주의 해야 합니다. 또한 다음 섹션에서 제안 된 구성 요구 사항을 이해 해야 합니다. SAP HANA를 실행 하려는 가상 컴퓨터를 구성한 경우 해야 할 수 있습니다.

- /Hana/log 250MB 초당 1MB I/O 크기에 대 한 최소한의 읽기/쓰기 볼륨을 사용 하도록 설정 합니다.
- 16MB 및 64MB I/O 크기의 /hana/data에 대 한 최소 400MB/sec의 읽기 작업을 사용 하도록 설정 합니다.
- 16MB 및 64MB I/O 크기의 /hana/data에 대 한 최소 250MB/sec의 쓰기 작업을 사용 하도록 설정 합니다.

낮은 저장소 대기 시간은 DBMS SAP HANA와 같은 메모리 내 데이터를 유지 하는 경우에 DBMS 시스템에 대 한 중요 한입니다. 저장소에서 중요한 경로는 일반적으로 DBMS 시스템의 트랜잭션 로그 쓰기입니다. 그러나 쓰기 저장점 또는 충돌 복구도 중요할 수 후 메모리에서 데이터를 로드 등의 작업입니다. 

/Hana/data 및 /hana/log 볼륨에 대 한 Azure premium 디스크의 사용은 필수입니다. SAP에서 원하는 대로 /hana/data 및 /hana/log 최소 처리량을 달성 하려면 여러 Azure premium storage 디스크는 LVM 논리 볼륨 관리자 () 또는 mdadm을 사용 하 여 RAID 0을 빌드하십시오. RAID 볼륨을 /hana/data 및 /hana/log 볼륨을 사용할 수도 있습니다. RAID 0에 대 한 다음 스트라이프 크기를 사용 하는 것이 좋습니다.

- 64KB 또는 hana/data에 대 한 128kb입니다.
- 32KB/hana/log에 대 한

> [!NOTE]
> Azure 프리미엄 및 standard storage VHD의 세 가지 이미지를 유지 하므로 RAID 볼륨을 사용 하 여 모든 중복 수준을 구성할 필요가 없습니다. RAID 볼륨의 사용 방법은 순수 하 게 충분 한 I/O 처리량을 제공 하는 볼륨을 구성입니다.

RAID 아래의 Azure vhd 수 누적은 IOPS 및 저장소 처리량 측면을 합니다. 3 x P30 Azure premium storage 디스크는 RAID 0을 배치 하면 제공 세 번 단일 Azure 프리미엄 저장소 P30 디스크의 저장소 처리량 및 IOPS 세 번입니다.

다음과 같은 캐싱 권장 SAP HANA에 대 한 I/O 특징을 가정합니다.

- HANA 데이터 파일에 대 한 읽기 작업에는 있습니다. 예외에는 HANA 인스턴스 또는 HANA에 데이터 로드 되 면 다시 시작한 후 대규모 I/o입니다. 데이터 파일에 대한 대규모 I/O의 또 다른 사례는 HANA 데이터베이스 백업일 수 있습니다. 결과적으로 읽기 캐싱을 적합 하지 않습니다 대부분의 경우에서 모든 데이터 파일 볼륨 읽어야 할 완전히 때문에.
- HANA 저장점 및 HANA 크래시 복구를 기반으로 데이터 파일에 대한 쓰기가 갑작스럽게 발생합니다. 쓰기 저장점은 비동기적 이며 모든 사용자 트랜잭션을 지연 되지 않습니다. 크래시 복구 중에 데이터 쓰기는 시스템이 다시 빠르게 응답하기 위해 성능이 중요합니다. 크래시 복구는 대신 예외 상황을 이어야 합니다.
- HANA 다시 실행 파일에는 읽기가 거의 없습니다. 트랜잭션 로그 백업, 크래시 복구 또는 HANA 인스턴스를 다시 시작 단계를 수행 하면 대형 I/o은 예외입니다.  
- SAP HANA 다시 실행 로그 파일에 대 한 기본 부하가 씁니다. I/o 워크 로드의 특성에 따라 있습니다 4KB로 또는 다른 경우 I/o에에서 작은 크기를 1MB 이상. SAP HANA 다시 실행 로그에 대한 쓰기 대기 시간은 성능이 중요합니다.
- 모든 쓰기는 신뢰할 수 있는 방식으로 디스크에 유지 해야 합니다.

SAP HANA에서 관찰 된 I/O 패턴에 결과로 Azure premium storage를 사용 하는 서로 다른 볼륨에 대 한 캐싱을 설정 합니다.

- **/hana/data**: 캐싱 없음입니다.
- **/hana/log**: 캐싱 없음, M 시리즈 Vm (이 문서의 뒷부분에 보다 참조)에 대 한 예외로 합니다.
- **/hana/shared**: 캐시를 읽습니다.


또한 전체 VM I/O 처리량 때 염두 크기 또는 VM에서 결정 합니다. 전체 VM 저장소 처리량 된다 [메모리 최적화 가상 머신 크기](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)합니다.

#### <a name="linux-io-scheduler-mode"></a>Linux I/O 스케줄러 모드
Linux에는 몇 가지 다른 I/O 일정 예약 모드가 있습니다. Linux 공급 업체 및 SAP에서 일반적으로 권장에서 디스크 볼륨에 대 한 I/O 스케줄러 모드를 설정 하는 것은 **cfq** 모드는 **noop** 모드. 자세한 내용은 [SAP 참고 #1984798](https://launchpad.support.sap.com/#/notes/1984787)합니다. 


#### <a name="storage-solution-with-write-accelerator-for-azure-m-series-virtual-machines"></a>Azure Write Accelerator를 사용 하 여 저장소 솔루션 M 시리즈 virtual machines
 Write Accelerator는 출시할 Azure M 시리즈 Vm에 대 한 단독으로 Azure 기능을 현재 값을 가져옵니다. 기능의 목적은 Azure premium storage에 대 한 쓰기의 I/O 대기 시간을 개선 하기 위해입니다. SAP HANA의 경우 Write Accelerator는 /hana/log 볼륨에 대해서만 사용해야 합니다. 이러한 이유로 앞에서 설명한 구성은 변경 해야 합니다. 주요 변경만 /hana/log 볼륨에 대해 Write Accelerator를 사용 하기 위해 /hana/data 및 /hana/log 간의 사용자의 경우 

> [!IMPORTANT]
> Azure에 대 한 SAP HANA 인증 M 시리즈 가상 머신/hana 로그 볼륨에 Write Accelerator에만 적용 됩니다. 결과적으로 Azure에서 프로덕션 시나리오 SAP HANA 배포 M 시리즈 가상 머신/hana/에 대 한 Write Accelerator를 사용 하 여 구성 해야 하는 볼륨을 기록 합니다.

> [!NOTE]
> 프로덕션 시나리오의 경우 특정 VM 유형이 [IAAS에 대한 SAP 설명서](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)에서 SAP별 SAP HANA에 대해 지원되는지 여부를 확인합니다.

다음 표에서 권장된 구성을 보여 줍니다.

| VM SKU | RAM | 최대 VM I/O<br /> throughput | /hana/data | /hana/log | /hana/shared | /root 볼륨 | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192GiB | 500 MB/sec | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256GiB | 500 MB/sec | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512GiB | 초당 1,000MB | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1,000 giB | 초당 1,000MB | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 giB | 초당 1,000MB | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2,000 giB | 초당 2,000MB |3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3,800 giB | 초당 2,000MB | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |

제안 된 다른 볼륨에 대 한 저장소 처리량 실행 하려는 워크 로드를 충족 하는지 확인 합니다. 작업에 /hana/data 및 /hana/log에 대 한 높은 볼륨이 필요한 경우 Azure premium storage Vhd 수를 늘립니다. Azure 가상 머신 유형의 한도 내에서 I/O 처리량을 나열 된 증가 IOPS 보다 더 많은 Vhd 사용 하 여 볼륨 크기를 조정 합니다.

Write Accelerator는 [Azure 관리 디스크](https://azure.microsoft.com/services/managed-disks/)와만 함께 작동합니다. 최소한 /hana/log 볼륨을 형성 하는 Azure premium storage 디스크를 관리 되는 디스크로 배포 되어야 합니다.

Azure premium storage Vhd 당 Write Accelerator에서 지원할 수 있는 VM에 대 한 제한이 있습니다. 이 제한은 현재 다음과 같습니다.

- 16 개 Vhd m128xx VM.
- 8 개 Vhd m64xx VM.
- M32xx에 대 한 vhd 4 개 VM입니다.

Write Accelerator를 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 참조 하세요. [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)합니다.

자세한 내용은 및 Write Accelerator에 대 한 제한 동일한 설명서를 참조 합니다.


#### <a name="cost-conscious-azure-storage-configuration"></a>비용 인식 Azure Storage 구성
다음 표에서는 고객이 Azure VM에서 SAP HANA를 호스트하는 데 일반적으로 사용하는 VM 유형의 구성을 보여줍니다. SAP HANA에 대 한 모든 최소 조건을 충족 하지 않는 일부 VM 형식 있을 수 있습니다. 또한 있을 SAP에서 SAP HANA를 사용 하 여 공식적으로 지원 되지 않는 일부 VM 형식. 지금까지 해당 Vm은 비프로덕션 시나리오에 대 한 제대로 수행 했습니다. 

> [!NOTE]
> 프로덕션 시나리오의 경우 특정 VM 유형이 [IAAS에 대한 SAP 설명서](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)에서 SAP별 SAP HANA에 대해 지원되는지 여부를 확인합니다.


| VM SKU | RAM | 최대 VM I/O<br /> throughput | /hana/data 및 /hana/log<br /> LVM 또는 mdadm으로 스트라이프 | /hana/shared | /root 볼륨 | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112GiB | 768MB/sec | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E16v3 | 128GiB | 384MB/sec | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E32v3 | 256GiB | 768MB/sec | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 432GiB | 1, 200 MB/sec | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448GiB | 초당 2,000MB | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M32ts | 192GiB | 500 MB/sec | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M32ls | 256GiB | 500 MB/sec | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M64ls | 512GiB | 초당 1,000MB | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 |1 x S30 |
| M64s | 1,000 giB | 초당 1,000MB | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1750 giB | 초당 1,000MB | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2,000 giB | 초당 2,000MB |3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S40 |
| M128ms | 3,800 giB | 초당 2,000MB | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S50 |


제공 되는 공간 권장 사항과 관련 하 여 볼륨 3 x P20 초과 사용 하 여 더 작은 VM 유형에 권장 되는 디스크는 [SAP TDI 저장소 백서](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)합니다. 표에 표시 된 선택 하는 SAP HANA에 대 한 충분 한 디스크 처리량을 제공 하려고 합니다. 변경 해야 할 경우는 **/hana/백업** 메모리 볼륨의 두 배인 백업을 유지 하려면 크기가 지정 되어 있는 볼륨을 자유롭게 조정 합니다. 

제안 된 다른 볼륨에 대 한 저장소 처리량 실행 하려는 워크 로드를 충족 하는지 확인 합니다. 작업에 /hana/data 및 /hana/log에 대 한 높은 볼륨이 필요한 경우 Azure premium storage Vhd 수를 늘립니다. Azure 가상 머신 유형의 한도 내에서 I/O 처리량을 나열 된 증가 IOPS 보다 더 많은 Vhd 사용 하 여 볼륨 크기를 조정 합니다. 

> [!NOTE]
> 이전 구성에서 장점이 없는 [Azure 가상 머신 단일 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) Azure premium storage 및 Azure standard storage의 혼합을 사용 하기 때문에 있습니다. 비용을 최적화 하기 위해 선택 되었습니다. Azure standard storage (Sxx) VM 구성을 단일 VM SLA는 Azure와 호환 되도록으로 나열 된 테이블의 모든 디스크에 프리미엄 저장소를 선택 합니다.


> [!NOTE]
> SAP의 인프라 공급자를 제공 하는 최소 요구 사항을 만족 하는 디스크 구성 권장 사항. 실제 고객 배포 및 워크 로드 시나리오에서 이러한 권장 사항은 상황에 따라 충분 한 기능을 제공 하지 않았습니다. 예를 들어, 고객 데이터를 더 빠르게 다시 로드 하는 HANA 다시 시작 후 백업 구성 필요 하거나 저장소에 더 높은 대역폭입니다. 다른 경우 5,000 개의 IOPS 특정 워크 로드에 충분 하지 않은 /hana/log을 포함 합니다. 이러한 권장 사항을 시작 점으로 사용 하 고 워크 로드의 요구 사항에 맞게 조정 합니다.
>  

### <a name="set-up-azure-virtual-networks"></a>Azure 가상 네트워크 설정
Azure ExpressRoute 또는 VPN 통해 Azure에 사이트 간 연결을 설정한 경우 VPN 또는 ExpressRoute 회로에 가상 게이트웨이 통해 연결 되는 하나 이상의 Azure 가상 네트워크를 해야 합니다. 간단한 배포의 경우 너무 SAP HANA 인스턴스를 호스팅하는 Azure virtual network의 서브넷에 가상 게이트웨이 배포할 수 있습니다. 

SAP HANA를 설치 하려면 Azure 가상 네트워크 내에서 두 개의 추가 서브넷을 만듭니다. 한 서브넷은 SAP HANA 인스턴스를 실행하는 VM을 호스트하고, 다른 서브넷은 JumpBox 또는 관리 Vm을 호스트로 SAP HANA Studio, 기타 관리 소프트웨어 또는 응용 프로그램 소프트웨어를 실행합니다.

> [!IMPORTANT]
> 구성 [Azure 네트워크 가상 어플라이언스](https://azure.microsoft.com/solutions/network-appliances/) SAP 응용 프로그램의는 SAP NetWeaver-DBMS 계층 사이의 통신 경로에 Hybris 또는 S/4HANA 기반 SAP 시스템 지원 되지 않습니다. 이 제한은 기능 및 성능 이유로입니다. SAP 응용 프로그램 계층과 DBMS 계층 간의 통신 경로 직접 이어야 합니다. 제한 없는 [응용 프로그램 보안 그룹 (ASG) 및 네트워크 보안 그룹 (NSG) 규칙](https://docs.microsoft.com/azure/virtual-network/security-overview) ASG 및 NSG 규칙을 직접 통신 경로 허용 하는 경우. 
>
> 네트워크 가상 어플라이언스는 지원 되지 않습니다 여기서 다른 시나리오에: 
>
> - Linux Pacemaker를 나타내는 Azure Vm 간의 통신 경로 클러스터 노드 및 SBD 장치에 설명 된 대로 [SAP 응용 프로그램용 SUSE Linux Enterprise Server의 Azure Vm에서 SAP NetWeaver에 대 한 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)합니다.
> - Azure Vm 및 Windows Server 스케일 아웃 파일 서버 간의 통신 경로 설정에 설명 된 대로 최대 [Azure에서 파일 공유를 사용 하 여 SAP ASCS/SCS 인스턴스에 Windows 장애 조치 클러스터에서 클러스터](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)합니다. 
>
> 네트워크 가상 어플라이언스 통신 경로에 두 개의 통신 파트너 간의 네트워크 대기 시간이 쉽게 double 수 있습니다. SAP 응용 프로그램 계층과 DBMS 계층 사이 중요 경로에서 처리량을 제한할 수도 있습니다. 일부 고객 시나리오, 네트워크 가상 어플라이언스 Pacemaker Linux 클러스터 실패를 일으킬 수 있습니다. 이들은 경우 Linux Pacemaker 클러스터 노드 간 통신 SBD 장치를 네트워크 가상 어플라이언스를 통해 통신 하는 위치입니다.  
> 

> [!IMPORTANT]
> 다른 디자인의 *되지* 지원 되는 SAP 응용 프로그램 계층과 DBMS 계층의 분리 되지 않은 다른 Azure 가상 네트워크에도 [피어 링](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) 서로 합니다. SAP 응용 프로그램 계층과 DBMS 계층이 서로 다른 Azure 가상 네트워크를 사용 하 여 대신 Azure virtual network 내의 서브넷을 사용 하 여 분리 하는 것이 좋습니다. 
>
>두 가상 네트워크가 있어야 권장 사항을 따르고 대신 서로 다른 가상 네트워크에 두 계층을 분리할 필요가 않으려면 [피어 링](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)합니다. 
>
> 알고 있어야 하는 네트워크 간 트래픽 [피어 링](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) Azure virtual network는 전송 비용이 될 수 있습니다. 큰 데이터 볼륨 수 테라바이트에 달하는 구성 된 SAP 응용 프로그램 계층과 DBMS 계층 간에 교환 됩니다. SAP 응용 프로그램 계층과 DBMS 계층 피어 링 된 두 Azure virtual network 간에 분리 되는 경우에 상당한 비용을 누적 할 수 있습니다. 

SAP HANA를 실행할 VM을 설치할 때 VM에는 다음이 필요합니다.

- 두 가상 Nic를 설치 합니다. 하나의 NIC는 관리 서브넷에 연결합니다. 온-프레미스 네트워크 또는 다른 네트워크에서 Azure VM에서 SAP HANA 인스턴스를 다른 NIC를 연결합니다.
- 두 가상 NIC에 대해 배포된 고정 개인 IP 주소

> [!NOTE]
> Azure 통해 고정 IP 주소를 할당은 개별 가상 Nic에 할당 하는 것을 의미 합니다. 가상 NIC에 게스트 OS 내에서 고정 IP 주소를 할당 안 함 Azure Backup과 같은 일부 Azure 서비스는 사실에 의존에서 고정 IP 주소가 아닌 DHCP에 최소 기본 가상 NIC 설정 됩니다. 자세한 내용은 [문제를 해결 하는 Azure 가상 머신 백업](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking)합니다. VM에 여러 고정 IP 주소에 할당 하려면 VM에 여러 가상 Nic를 할당 합니다.
>
>

지속 되는 배포의 경우 Azure에서 가상 데이터 센터 네트워크 아키텍처를 만듭니다. 이 아키텍처는 온-프레미스에 연결 하는 별도 Azure 가상 네트워크에는 Azure 가상 네트워크 게이트웨이의 분리를 권장 합니다. 이 별도 가상 네트워크에 인터넷 또는 온-프레미스로 유지 하는 모든 트래픽을 호스트 합니다. 이 방법을 사용 하면이 별도 허브 가상 네트워크에 Azure에서 가상 데이터 센터를 입력 하는 로그 트래픽과 감사 하는 소프트웨어를 배포할 수 있습니다. 이 이렇게 해야 하나의 가상 네트워크 호스팅하는 모든 소프트웨어 및 구성 ingoing 및 나가는 트래픽이 Azure 배포에 연결 하는 합니다.


가상 데이터 센터 방법과 관련 된 Azure 가상 네트워크 디자인에 대 한 자세한 내용은 다음을 참조 하세요. 

- [Azure virtual datacenter: 네트워크 측면](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter)합니다.
- [Azure virtual datacenter 및 enterprise control plane](https://docs.microsoft.com/azure/architecture/vdc/)합니다.


>[!NOTE]
>사용 하 여 허브 가상 네트워크 및 스포크 가상 네트워크 간에 전달 되는 트래픽을 [Azure 가상 네트워크 피어 링](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) 는 추가 될 수 있습니다 [비용](https://azure.microsoft.com/pricing/details/virtual-network/)합니다. 해당 비용에 따라 수 해야 엄격한 허브-스포크 네트워크 디자인을 실행 하 고 여러 실행 절충 [Azure ExpressRoute 게이트웨이](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways) 가상 네트워크 피어 링을 무시할 스포크에 연결 하는 합니다. 
>
> Azure ExpressRoute 게이트웨이 추가 도입 [비용](https://azure.microsoft.com/pricing/details/vpn-gateway/) 너무 합니다. 또한 로그, 감사 및 네트워크 트래픽을 모니터링을 사용 하는 제 3 자 소프트웨어에 대 한 추가 비용이 발생할 수 있습니다. 추가 ExpressRoute 게이트웨이 및 소프트웨어 라이선스를 만들어 비용 및 피어 링 된 가상 네트워크를 통해 데이터 교환에 대 한 비용을 고려 합니다. 격리 단위 대신 가상 네트워크와 서브넷을 사용 하 여 하나의 가상 네트워크 내에서 마이크로 구분에 결정할 수 있습니다.


IP 주소를 할당 하 여 다양 한 방법의 개요를 참조 하세요 [IP 주소 유형 및 Azure에서 할당 방법](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)합니다. 

Vm의 SAP HANA를 실행 하는 경우 할당 된 고정 IP 주소를 사용 하 여 작동 합니다. 이유는 IP 주소를 HANA에 대 한 몇 가지 구성 특성으로 참조입니다.

[Azure Nsg](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) SAP HANA 인스턴스 또는 JumpBox로 라우팅되는 트래픽을 전달 하는 데 사용 됩니다. Nsg 및 결국 [응용 프로그램 보안 그룹](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups) SAP HANA 서브넷과 관리 서브넷에 연결 합니다.

다음 이미지는 허브 및 스포크 가상 네트워크 아키텍처는 SAP HANA에 대 한 대략적인 배포 스키마의 개요를 보여 줍니다.

![SAP HANA의 대략적인 배포 스키마](media/hana-vm-operations/hana-simple-networking.PNG)

사이트 간 연결 없이 Azure에서 SAP HANA를 배포 하려면 공용 인터넷에서 SAP HANA 인스턴스를 보호 하 고 프록시 뒤에 있는 정방향 숨깁니다. 이 기본 시나리오에서 배포는 Azure 기본 제공 DNS 서비스가 호스트 이름을 확인 하려면 사용 합니다. 공용 IP 주소가 사용되는 더 복잡한 배포에서는 Azure 기본 제공 DNS 서비스가 특히 중요합니다. Azure Nsg를 사용 하 고 [Azure 네트워크 가상 어플라이언스](https://azure.microsoft.com/solutions/network-appliances/) Azure에서 Azure 가상 네트워크 아키텍처로 인터넷에서 라우팅 모니터링. 

다음 이미지에는 허브 및 스포크 가상 네트워크 아키텍처에서는 사이트 간 연결 없이 SAP HANA를 배포 하는 방법에 대 한 대략적인 스키마를 보여 줍니다.
  
![사이트 간 연결 없는 SAP HANA의 대략적인 배포 스키마](media/hana-vm-operations/hana-simple-networking2.PNG)
 

허브 및 스포크 가상 네트워크 아키텍처 없이 인터넷에서 액세스 제어 및 모니터링 하려면 Azure 네트워크 가상 어플라이언스를 사용 하는 방법의 또 다른 설명은 참조 하세요. [고가용성 네트워크 가상 어플라이언스배포](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha).


## <a name="configure-azure-infrastructure-for-sap-hana-scale-out"></a>SAP HANA 스케일 아웃에 대 한 Azure 인프라 구성
Microsoft는 SAP HANA 스케일 아웃 구성에 대 한 인증 되어 있는 하나의 M 시리즈 VM SKU에 있습니다. VM 유형에 M128s은 최대 16 개의 노드로의 스케일 아웃에 대해 인증 됩니다. Azure Vm에서 SAP HANA 스케일 아웃 인증의 변경 내용에 대 한 참조를 [인증 IaaS 플랫폼 목록](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)합니다.

Azure Vm에서 스케일 아웃 구성에 배포 하는 데 최소 OS 릴리스는 다음과 같습니다.

- SUSE Linux 12 SP3.
- Red Hat Linux 7.4.

16 노드 스케일 아웃 인증:

- 하나의 노드는 마스터 노드입니다.
- 최대 15 개 노드는 작업자 노드입니다.

>[!NOTE]
>Azure VM의 스케일 아웃 배포에서는 대기 노드를 사용할 수 없는 합니다.
>

대기 노드를 구성할 수 없습니다는 이유는 두 가지가 있습니다.

- 이 시점에서 Azure에는 네이티브 NFS 서비스가 없습니다. 결과적으로, 타사 기능을 통해 NFS 공유를 구성 해야 합니다.
- Azure에 배포 된 솔루션을 사용 하 여 SAP HANA에 대 한 저장소 대기 시간 조건을 충족할 수 있습니다 타사 NFS 구성 중입니다.

따라서 /hana/data 및 /hana/log 볼륨을 공유할 수 없습니다. 단일 노드 중 이러한 볼륨을 공유 하지 않는 대기 SAP HANA 노드 스케일 아웃 구성에 사용할 수 없습니다.

다음 이미지는 스케일 아웃 구성에 단일 노드에 대 한 기본 디자인을 보여줍니다.

![단일 노드의 스케일 아웃 기본 사항](media/hana-vm-operations/scale-out-basics.PNG)

SAP HANA 스케일 아웃에 대한 VM 노드의 기본 구성은 다음과 같습니다.

- /Hana/shared에 대 한 구축 기반 SUSE Linux 12 SP3으로 항상 사용 가능한 NFS 클러스터 합니다. 이 클러스터에 스케일 아웃 구성 및 SAP NetWeaver 또는 BW/4HANA 중앙 서비스의 /hana/shared NFS 공유를 호스트합니다. 이러한 구성을 빌드하는 방법에 대 한 내용은 참조 하세요 [SUSE Linux Enterprise Server의 Azure Vm에 있는 NFS의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)합니다.
- 다른 모든 디스크 볼륨에는 다른 노드 간에 공유 되지 않습니다 및 NFS를 기반으로 하지. 설치 구성 및 비공유 /hana/data 및 /hana/log을 사용 하 여 스케일 아웃 HANA 설치에 대 한 단계는이 문서의 뒷부분에 제공 됩니다.

>[!NOTE]
>지금까지 그래픽에 표시된 대로 고가용성 NFS 클러스터는 SUSE Linux에서만 지원됩니다. Red Hat을 기반으로 하는 고가용성 NFS 솔루션은 나중에 알려드릴 예정입니다.

노드에 대 한 볼륨 크기는 수직 /hana/shared 제외 하 고 동일 합니다. 다음 표에서 M128s VM SKU에 대 한 제안 된 크기와 형식이 보여 줍니다.

| VM SKU | RAM | 최대 VM I/O<br /> throughput | /hana/data | /hana/log | /root 볼륨 | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- |
| M128s | 2,000 giB | 2,000 MB/s |3 x P30 | 2 x P20 | 1 x P6 | 1 x P6 | 2 x P40 |


제안 된 다른 볼륨에 대 한 저장소 처리량 실행 하려는 워크 로드를 충족 하는지 확인 합니다. 작업에 /hana/data 및 /hana/log에 대 한 높은 볼륨이 필요한 경우 Azure premium storage Vhd 수를 늘립니다. Azure 가상 머신 유형의 한도 내에서 I/O 처리량을 나열 된 증가 IOPS 보다 더 많은 Vhd 사용 하 여 볼륨 크기를 조정 합니다. /Hana/log 볼륨을 형성 하는 디스크에 Write Accelerator를도 적용 됩니다.
 

4 개의 작업자 노드 당 단일 작업자 노드의 메모리 크기로 확장에 대 한 hana/공유 볼륨의 크기를 정의 하는 수식 참조 [SAP HANA TDI 저장소 요구 사항](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)합니다.

SAP HANA 스케일 아웃 certified M128s Azure 사용 하 여 VM 약 2TB의 메모리가 수행한 가정으로 SAP 권장 사항은 요약 되어 있습니다.

- 1 개의 마스터 노드 및 최대 4 개의 작업자 노드 /hana/shared 볼륨의 크기는 2TB입니다.
- 1 개의 마스터 노드 및 5 및 8 개의 작업자 노드 /hana/shared 볼륨의 크기는 4TB입니다.
- 1 개의 마스터 노드 및 작업자 노드 9 ~ 12 /hana/shared 볼륨의 크기는 6TB 합니다.
- 1 개의 마스터 노드 및 작업자 노드 12 ~ 15의 경우 /hana/shared 볼륨의 크기가 8TB 합니다.

스케일 아웃 SAP HANA VM에 대 한 단일 노드 구성의 그래픽에 표시 되는 기타 중요 한 설계는 서브넷 구성 사용 하 여 가상 네트워크입니다. SAP은 HANA 노드 간의 통신에서 클라이언트 및 응용 프로그램 연결 트래픽 분리를 매우 권장합니다. 

이 목표를 달성 하려면 그래픽에 표시 된 대로 서로 다른 두 개의 가상 nic가 VM에 연결 합니다. 두 가상 Nic이 다른 서브넷에 있고 서로 다른 두 IP 주소입니다. 다음 Nsg 또는 사용자 정의 경로 사용 하 여 라우팅 규칙을 사용 하 여 트래픽 흐름을 제어 합니다.

Azure에서 특히 의미 및 없는 메서드 서비스 품질 및 특정 가상 Nic의 할당량을 적용 합니다. 결과적으로, 클라이언트 및 응용 프로그램 연결 및 노드 간 통신의 분리 하지 트래픽 스트림을 두 개를 우선 순위를 통해 다른 모든 기회를 엽니다. 대신, 분리 정도의 확장 구성의 노드 간 통신을 보호에서 보안을 유지 됩니다.  

>[!IMPORTANT]
>SAP 항상 클라이언트와 응용 프로그램 쪽과 노드 간 트래픽에이 문서에 설명 된 대로 네트워크 트래픽을 분리 하는 것이 좋습니다. 이전 그래픽에 표시 된 대로 진행에서 아키텍처를 추가 하는 것이 좋습니다.
>

다음 이미지는 네트워킹 관점에서 필요한 최소 네트워크 아키텍처를 보여 줍니다.

![단일 노드의 스케일 아웃 기본 사항](media/hana-vm-operations/scale-out-networking-overview.PNG)

지금까지 지원 되는 제한은 15 개 작업자 노드 1 개의 마스터 노드 외에도 합니다.

다음 이미지는 저장소 관점에서 저장소 아키텍처를 보여 줍니다.


![단일 노드의 스케일 아웃 기본 사항](media/hana-vm-operations/scale-out-storage-overview.PNG)

/Hana/shared 볼륨은 항상 사용 가능한 NFS 공유 구성에 있습니다. 다른 모든 드라이브는 개별 Vm에 로컬로 탑재 됩니다. 

### <a name="highly-available-nfs-share"></a>고가용성 NFS 공유
지금까지 항상 사용 가능한 NFS 클러스터가 작동 SUSE linux의 경우만. 설치 정보를 참조 하세요 [SUSE Linux Enterprise Server의 Azure Vm에 있는 NFS의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)합니다. Azure 가상 네트워크 외부의 SAP HANA 인스턴스를 실행 하는 다른 모든 HANA 구성을 사용 하 여 NFS 클러스터를 공유 하지 않습니다 하는 경우 동일한 가상 네트워크에 설치 합니다. 자체 서브넷에 설치 하 고 일부 임의 트래픽 서브넷에 액세스할 수 있도록 해야 합니다. 대신, 트래픽이 /hana/shared 볼륨에 실행 되는 VM의 IP 주소를 서브넷에 트래픽을 제한 합니다.

/Hana/shared 트래픽을 라우팅하는 HANA 스케일 아웃 VM의 가상 NIC와 관련 권장 사항은 다음과 같습니다.

- /Hana/shared 트래픽을 보통 이므로 최소 구성에서 클라이언트 네트워크에 할당 된 가상 NIC를 통해 라우팅하십시오.
- 결국 트래픽을/hana/공유에 SAP HANA 스케일 아웃 구성 배포할 가상 네트워크의 세 번째 서브넷을 배포 합니다. 해당 서브넷에 호스트 되는 세 번째 가상 NIC를 할당 합니다. NFS 공유로 트래픽에 대 한 세 번째 가상 NIC 및 연결 된 IP 주소를 사용 합니다. 그런 다음, 별도의 액세스 및 라우팅 규칙을 적용할 수 있습니다.

>[!IMPORTANT]
>배포 하는 스케일 아웃 방식으로 SAP HANA에 있는 Vm 및 관련 된 경우에 항상 사용 가능한 NFS 간에 네트워크 트래픽을 통해 라우팅될 수 있습니다는 [네트워크 가상 어플라이언스](https://azure.microsoft.com/solutions/network-appliances/) 또는 유사한 가상 어플라이언스입니다. Azure Nsg는 이러한 장치가 없습니다. SAP HANA를 실행 하는 Vm에서 네트워크 가상 어플라이언스 또는 유사한 가상 어플라이언스 고가용성 NFS에 액세스할 때 우회 됩니다 있도록 라우팅 규칙 공유를 확인 합니다.
> 

SAP HANA 구성 간에 항상 사용 가능한 NFS 클러스터를 공유 하려는 경우 동일한 가상 네트워크에 이러한 모든 HANA 구성을 이동 합니다. 
 

### <a name="install-an-sap-hana-scale-out-in-azure"></a>Azure는 SAP HANA 확장 설치
스케일 아웃 SAP 구성을 설치 하려면 다음 일반 단계를 수행 합니다.

- 새 배포 또는 새 Azure 가상 네트워크 인프라를 조정 합니다.
- Azure 관리 되는 프리미엄 저장소 볼륨을 사용 하 여 새 Vm을 배포 합니다.
- 새 배포 또는 기존 항상 사용 가능한 NFS 클러스터를 조정 합니다.
- 네트워크 라우팅, 예를 들어 Vm 간에 노드 간 통신 하지 라우팅된 되도록 조정 된 [네트워크 가상 어플라이언스](https://azure.microsoft.com/solutions/network-appliances/)합니다. Vm 및 항상 사용 가능한 NFS 클러스터 간 트래픽에 대해도 마찬가지입니다.
- SAP HANA 마스터 노드를 설치합니다.
- SAP HANA 마스터 노드의 구성 매개 변수를 조정 합니다.
- SAP HANA 작업자 노드에서 설치를 계속 합니다.

#### <a name="install-sap-hana-in-a-scale-out-configuration"></a>스케일 아웃 구성에 SAP HANA를 설치 합니다.
Azure VM 인프라 배포 되 고 다른 모든 준비 완료 됩니다. 이제 SAP HANA 스케일 아웃 구성에 설치 하려면 다음이 단계를 수행 합니다.

- SAP의 설명서에 따라 SAP HANA 마스터 노드를 설치 합니다.
- *설치 후 global.ini 파일을 변경 하 고 매개 변수를 추가 ' basepath_shared = no' global.ini에*입니다. 이 매개 변수는 노드 간에 공유 /hana/data 및 /hana/log 볼륨으로 없이 확장에서 실행 하려면 SAP HANA를 사용 합니다. 자세한 내용은 [SAP 참고 #2080991](https://launchpad.support.sap.com/#/notes/2080991)합니다.
- Global.ini 매개 변수를 변경한 후에 SAP HANA 인스턴스를 다시 시작 합니다.
- 추가 작업자 노드를 추가합니다. 자세한 내용은 [SAP HANA 관리 가이드](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US/0d9fe701e2214e98ad4f8721f6558c34.html)합니다. 설치 하는 동안 또는 나중에 사용 하 여, 예를 들어 로컬 hdblcm SAP HANA 노드 간 통신에 대 한 내부 네트워크를 지정 합니다. 자세한 내용은 [SAP 참고 #2183363](https://launchpad.support.sap.com/#/notes/2183363)합니다. 

이 설치 루틴이 다음 설치한 스케일 아웃 구성 /hana/data 및 /hana/log을 실행 하려면 공유 되지 않는 디스크를 사용 합니다. Hana/공유 볼륨에 항상 사용 가능한 위치한 NFS 공유 합니다.


## <a name="sap-hana-dynamic-tiering-20-for-azure-virtual-machines"></a>Azure virtual machines의 SAP HANA 동적 계층화 2.0

Azure M 시리즈 Vm에서 SAP HANA 인증을 하는 것 외에도 Microsoft Azure에서 SAP HANA 동적 계층화 2.0 (DT 2.0)도 지원 됩니다. SAP HANA 동적 계층화 설명서 링크에 대 한 섹션을 "링크 DT를 2.0 설명서"이이 문서의 뒷부분에 나오는 참조. 제품을 설치 또는 Azure VM 내에서 SAP HANA Cockpit을 통해 예를 들어, 운영, 차이가 있지만 몇 가지 중요 한 항목은 Azure의 공식 지원에 대 한 필수입니다. 이러한 요점 다음 섹션에 설명 되어 있습니다. 

SAP BW 또는 S4HANA SAP HANA DT 2.0 지원 하지 않습니다. 주요 사용 사례에는 지금 바로 HANA 응용 프로그램을 네이티브 이며


### <a name="overview"></a>개요

다음 이미지는 Microsoft Azure에서 DT 2.0 지원의 개요를 제공합니다. 이러한 필수 공식 인증을 사용 하 여 준수 요구를 수행 합니다.

- DT 2.0은 전용 Azure VM에 설치해야 합니다. SAP HANA 실행 되는 동일한 VM에서 실행 되지 않을 수 있습니다.
- SAP HANA 및 DT 2.0 Vm은 동일한 Azure 가상 네트워크 내에서 배포 되어야 합니다.
- Azure 가속 네트워킹을 사용 DT 2.0 Vm 및 SAP HANA를 배포 해야 합니다.
- DT 2.0 Vm의 저장소 유형은 Azure premium storage를 이어야 합니다.
- DT 2.0 VM에 여러 Azure 디스크를 연결 해야 합니다.
- 소프트웨어를 만드는 Azure 디스크에 걸쳐 스트라이프를 사용 하 여 RAID 또는 스트라이프 볼륨의 경우 LVM 또는 mdadm을 통해이 필요 합니다.

다음 섹션에서는 자세한 설명을 제공합니다.

![SAP HANA DT 2.0 아키텍처 개요](media/hana-vm-operations/hana-dt-20.PNG)



### <a name="dedicated-azure-vm-for-sap-hana-dt-20"></a>SAP HANA DT 2.0 전용 Azure VM

Azure IaaS에서 DT 2.0은 전용된 VM에 대해서만 지원 됩니다. DT 2.0 HANA 인스턴스가 실행 되 고 있는 동일한 Azure VM에서 실행 되도록 허용 되지 않습니다. 처음에 SAP HANA DT 2.0을 실행 하려면 두 개의 VM 유형은 사용할 수 있습니다.

- M64-32ms 
- E32sv3 

VM 유형 설명을 보려면 [메모리 최적화 가상 머신 크기](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)합니다.

해당 VM 크기를 사용 하는 것이 인 비용 절감을 위해 웜 데이터 오프 로드 하는 방법에 대 한 DT 2.0의 기본 개념을 제공 합니다. 가능한 조합에 대 한 엄격한 규칙이 없는 경우 이 조합은 특정 고객 워크로드에 따라 다릅니다.

다음 표에서 권장된 구성을 보여 줍니다.

| SAP HANA VM 유형 | DT 2.0 VM 유형 |
| --- | --- | 
| M128ms | M64-32ms |
| M128s | M64-32ms |
| M64ms | E32sv3 |
| M64s | E32sv3 |


M64 32ms E32sv3, 등의 지원 되는 DT 2.0 Vm을 사용 하 여 SAP HANA 인증 M 시리즈 Vm의 모든 조합을 사용할 수 있습니다.


### <a name="azure-networking-and-sap-hana-dt-20"></a>Azure 네트워킹 및 SAP HANA DT 2.0

DT 2.0 VM과 10GB 최소를 사용 하 여 SAP HANA VM 간의 네트워크 처리량이 필요한 전용된 VM에서 DT 2.0을 설치 합니다. 결과적으로 동일한 Azure 가상 네트워크 내의 모든 Vm을 배치 하 고 Azure Accelerated Networking을 사용 하도록 설정 하는 것이 반드시 합니다.

Azure 가속 네트워킹에 대 한 자세한 내용은 참조 하세요. [가속 네트워킹을 사용 하 여 Linux 가상 머신 만들기](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)합니다.

### <a name="vm-storage-for-sap-hana-dt-20"></a>SAP HANA DT 2.0에 대 한 VM 저장소

DT 2.0 모범 사례 지침에 따라 최소 디스크 IO 처리량 물리적 코어당 초당 50mb까지 할당 됩니다. 최대 디스크 IO VM에 대 한 처리량 한도 DT 2.0에 대 한 지원 되는 두 개의 Azure VM 형식에 대 한 사양을 확인:

- **E32sv3**:   768MB/sec, 캐시 되지 않은, 즉, 물리적 코어당 초당 48MB 비율
- **M64 32ms**:  (1,000MB) 초당, 캐시 되지 않은, 즉, 물리적 코어당 62.5 MB/sec의 비율

DT 2.0 VM에 여러 Azure 디스크를 연결 하 고 최대 제한인 VM 당 디스크 처리량을 달성 하기 위해 OS 수준 스트라이프를 사용 하 여 소프트웨어 RAID를 만들기가 필요 합니다. 단일 Azure 디스크는 최대 VM 제한에 도달 하기 위해 처리량을 제공할 수 없습니다. Azure premium storage는 DT 2.0을 실행 하는 필수입니다. 

- 사용 가능한 Azure 디스크 유형에 대 한 자세한 내용은 참조 하세요. [Azure IaaS Windows vm 디스크 유형 선택](../../windows/disks-types.md)합니다.
- Mdadm 통해 소프트웨어 RAID를 만드는 방법에 대 한 자세한 내용은 참조 하세요. [는 Linux에서 소프트웨어 RAID 구성](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)합니다.
- 최대 처리량에 대 한 스트라이프 볼륨 만들기에 LVM을 구성 하는 방법에 대 한 자세한 내용은 참조 하세요. [Azure에서 Linux VM에 LVM 구성](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)합니다.

크기 요구 사항에 따라 가지 VM의 최대 처리량에 연결할 다른 옵션이 있습니다. 여기에는 VM 처리량 상한을 달성하도록 각 DT 2.0 VM 유형에 적용할 수 있는 데이터 볼륨 디스크 구성이 있습니다. E32sv3 VM에는 더 작은 워크 로드에는 항목 수준으로 간주 됩니다. 속도가 충분히 빠르지 인 경우 M64 32ms VM 크기를 조정 해야 할 수도 있습니다.

M64 32ms VM에 많은 양의 메모리에 있으므로 IO 부하를에 읽기 집약적인 워크 로드를 위해 특별히의 제한에 도달 하지 않을 수 있습니다. 더 적은 디스크 스트라이프 집합의 고객 관련 워크 로드에 따라 충분할 수 있습니다. 이 문서에서는 되도록 다음 디스크 구성은 최대 처리량을 보장 하기 위해 선택 되었습니다.


| VM SKU | 디스크 구성 1 | 디스크 구성 2 | 디스크 구성 3 | 디스크 구성 4 | 디스크 구성 5 | 
| ---- | ---- | ---- | ---- | ---- | ---- | 
| M64-32ms | 4 x P50 -> 16TB | 4 x P40 -> 8TB | 5 x P30 -> 5TB | 7 x P20 -> 3.5TB | 8 x P15 -> 2TB | 
| E32sv3 | 3 x P50 -> 12TB | 3 x P40 -> 6TB | 4 x P30 -> 4TB | 5 x P20 -> 2.5TB | 6 x P15 -> 1.5TB | 


워크 로드를 읽기를 많이 사용 하는 경우에 특히 데이터베이스 소프트웨어의 데이터 볼륨에 대 한 권장 사항에 따라 Azure 호스트 캐시 "읽기 전용" 설정을 사용 하면 IO 성능이 향상 될 수 있습니다. 트랜잭션 로그에 대 한 Azure 호스트 디스크 캐시 해야 "none"입니다. 

로그 볼륨의 크기에 대 한 권장 되는 시작점은 데이터 크기의 약 15%의 추론 합니다. 로그 볼륨을 만들려면 비용 및 처리량 요구 사항에 따라 다양 한 Azure 디스크 형식을 사용 합니다. 로그 볼륨 높은 I/O 처리량이 필요 합니다. 

VM을 사용 하는 경우 M64 32ms 입력을 사용할 수 있도록 하는 것이 좋습니다 [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)합니다. Write Accelerator에는 트랜잭션 로그에 대 한 최적의 디스크 쓰기 대기 시간을 제공 하는 Azure 기능입니다. M 시리즈에 대해서만 제공 됩니다. 그러나 VM 유형별로 디스크의 최대 수와 같은 고려해 야 할 몇 가지 항목이 있습니다. Write Accelerator에 대 한 자세한 내용은 참조 하세요. [Write Accelerator 사용](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)합니다.


다음 표에서 로그 볼륨의 크기 조정 하는 데는 몇 가지 예를 보여 줍니다.

| 데이터 볼륨 크기와 디스크 유형 | 로그 볼륨 및 디스크 구성 1을 입력합니다. | 로그 볼륨 및 디스크 유형 구성 2 |
| --- | --- | --- |
| 4 x P50 -> 16TB | 5 x P20 -> 2.5TB | 3 x P30 -> 3TB |
| 6 x P15 -> 1.5TB | 4 x P6 -> 256GB | 1 x P15 -> 256GB |


SAP HANA 스케일 아웃 마찬가지로 /hana/shared 디렉터리를 SAP HANA VM 및 DT 2.0 VM 간에 공유 되어야 합니다. 고가용성 NFS 서버 역할을 하는 전용된 Vm을 사용 하 여 SAP HANA 스케일 아웃의 경우와 동일한 아키텍처를 사용 하는 것이 좋습니다. 백업 공유 볼륨을 제공 하려면 동일한 디자인을 사용 합니다. 하지만 백업 서버 역할을 하도록 충분 한 저장소 용량을 사용 하 여 전용된 VM을 사용 하는 충분 한 경우 또는 고가용성이 필요한 경우를 결정 하는 데에 달려 있습니다.



### <a name="links-to-dt-20-documentation"></a>DT 2.0 설명서에 대한 링크 

- [SAP HANA 동적 계층화 설치 및 업데이트 가이드](https://help.sap.com/viewer/88f82e0d010e4da1bc8963f18346f46e/2.0.03/en-US)
- [SAP HANA 동적 계층화 자습서 및 리소스](https://help.sap.com/viewer/fb9c3779f9d1412b8de6dd0788fa167b/2.0.03/en-US)
- [SAP HANA 동적 계층화 개념 증명](https://blogs.sap.com/2017/12/08/sap-hana-dynamic-tiering-delivering-on-low-tco-with-impressive-performance/)
- [향상된 SAP HANA 2.0 SPS 02 Dynamic Tiering 기능](https://blogs.sap.com/2017/07/31/sap-hana-2.0-sps-02-dynamic-tiering-enhancements/)




## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>Azure VM에서 SAP HANA를 배포하기 위한 작업
다음 섹션에서는 Azure VM에서 SAP HANA 시스템을 배포하는 일과 관련된 몇 가지 작업을 설명합니다.

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Azure VM의 백업 및 복원 작업
다음 문서에서는 SAP HANA 배포를 백업 및 복원하는 방법을 설명합니다.

- [SAP HANA 백업 개요](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA 파일 수준 백업](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [SAP HANA 저장소 스냅숏 벤치마크](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>SAP HANA를 포함하는 VM 시작 및 다시 시작
Azure 공용 클라우드의 두드러진 기능으로 사용한 컴퓨팅 시간(분)에 대해서만 요금이 부과되는 것을 들 수 있습니다. 예를 들어 SAP HANA를 실행 하는 VM을 종료 하는 경우 요금이 청구 저장소 비용에 대해서만 시간 동안. 초기 배포에서 VM에 대해 고정 IP 주소를 지정하는 경우 또 다른 기능을 사용할 수 있습니다. SAP HANA가 있는 VM을 다시 시작하면 VM이 이전 IP 주소로 다시 시작됩니다. 


### <a name="use-saprouter-for-sap-remote-support"></a>SAP 원격 지원에 SAProuter 사용
온-프레미스 위치와 Azure 간의 사이트 간 연결을 있고 SAP 구성 요소를 실행 하는 경우에 이미 SAProuter를 실행 하는 있습니다. 이 경우 원격 지원을 위해 다음이 단계를 따르세요.

- SAP HANA를 호스트하는 VM의 개인 및 고정 IP 주소를 SAProuter 구성에서 유지 관리합니다.
- HANA VM을 호스트하는 서브넷의 NSG에서 TCP/IP 포트 3299를 통해 트래픽을 허용하도록 구성합니다.

인터넷을 통해 Azure에 연결 하 고 SAP HANA를 사용 하 여 VM에 대 한 SAP 라우터가 없는 경우에 구성 요소를 설치 합니다. 관리 서브넷에서 별도 VM에 SAProuter를 설치 합니다. 

다음 그림은 사이트 간 연결 없이 SAProuter를 사용하여 SAP HANA를 배포하는 경우의 대략적인 스키마를 보여 줍니다.

![사이트 간 연결 및 SAProuter 없는 SAP HANA의 대략적인 배포 스키마](media/hana-vm-operations/hana-simple-networking3.PNG)

점프 박스 VM 아니라 별도 VM에 SAProuter를 설치 해야 합니다. 별도의 VM에는 고정 IP 주소가 있어야 합니다. 에 SAProuter를 SAP에서 호스트 하는 SAProuter에 연결 하려면 IP 주소에 대 한 SAP에 문의 합니다. SAP에서 호스트 하는 SAProuter는 VM에 설치 하는 SAProuter 인스턴스에 해당 합니다. SAP의 IP 주소를 사용하여 SAProuter 인스턴스를 구성합니다. 구성 설정에서 필수 포트는 TCP 포트 3299뿐입니다.

설정 및 SAProuter 통해 원격 지원 연결을 유지 관리 하는 방법에 대 한 자세한 내용은 참조 하세요. [SAP 설명서](https://support.sap.com/en/tools/connectivity-tools/remote-support.html)합니다.

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Azure 네이티브 Vm의 SAP hana 고가용성
SAP Applications 12 SP1 이상용 SUSE Linux Enterprise Server를 실행 하면 STONITH 장치를 사용 하 여 Pacemaker 클러스터를 설정할 수 있습니다. HANA 시스템 복제 및 자동 장애 조치를 사용 하 여 동기 복제를 사용 하는 SAP HANA 구성을 설정 하는 장치를 사용 합니다. 설치 절차에 대 한 자세한 내용은 참조 하세요. [Azure virtual machines의 SAP HANA 고가용성 가이드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)합니다.
