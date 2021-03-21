---
title: 파일 포함
description: 파일 포함
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 03/04/2021
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 3035b5d2803ff91e84bc6b47a99963185f9195d3
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102623592"
---
## <a name="disk-level-bursting"></a>디스크 수준 버스트

### <a name="on-demand-bursting-preview"></a>주문형 버스트 (미리 보기)

디스크 버스트의 주문형 버스트 모델을 사용 하는 디스크는 최대 버스트 대상까지 워크 로드에 필요한 만큼 원래 프로 비전 된 목표 이상으로 버스트 될 수 있습니다. 예를 들어, TiB P30 디스크에서 프로 비전 된 IOPS는 5000 IOPS입니다. 이 디스크에서 디스크 버스트를 사용 하도록 설정 하면 워크 로드에서이 디스크에 대 한 IOs를 3만 IOPS 및 1000 MBps의 최대 버스트 성능까지 실행할 수 있습니다.

워크 로드가 프로 비전 된 성능 목표를 초과 하 여 자주 실행 될 것으로 기대 하는 경우 디스크 버스트는 비용 효율적이 지 않습니다. 이 경우 더 나은 기준 성능을 위해 디스크의 성능 계층을 [더 높은 계층](../articles/virtual-machines/disks-performance-tiers.md) 으로 변경 하는 것이 좋습니다. 청구 세부 정보를 검토 하 고 워크 로드의 트래픽 패턴에 대해 평가 합니다.

주문형 버스트를 사용 하도록 설정 하기 전에 다음 사항을 이해 해야 합니다.

[!INCLUDE [managed-disk-bursting-regions-limitations](managed-disk-bursting-regions-limitations.md)]

#### <a name="regional-availability"></a>지역별 가용성

[!INCLUDE [managed-disk-bursting-availability](managed-disk-bursting-availability.md)]

#### <a name="billing"></a>결제

주문형 버스트 모델을 사용 하는 디스크에는 시간당 버스트 사용에 대 한 요금이 부과 되며, 프로 비전 된 대상 이상의 버스트 트랜잭션에는 트랜잭션 비용이 적용 됩니다. 트랜잭션 비용은 프로 비전 된 대상을 초과 하는 읽기 및 쓰기를 포함 하 여 캐시 되지 않은 disk IOs에 따라 종 량 제 모델을 사용 하 여 부과 됩니다. 다음은 청구 시간에 대 한 디스크 트래픽 패턴의 예입니다.

디스크 구성: 프리미엄 SSD – 1 TiB (P30), 디스크 버스트 사용.

- 00:00:00 – 00:10:00 디스크 IOPS의 프로 비전 된 대상 5000 IOPS 
- 00:10:01 – 00:10:10 응용 프로그램에서 10 초 동안 6000 IOPS로 디스크 IOPS를 버스트 하는 batch 작업을 실행 했습니다. 
- 00:10:11 – 00:59:00 디스크 IOPS의 프로 비전 된 대상 5000 IOPS 
- 00:59:01 – 01:00:00 응용 프로그램이 다른 일괄 작업을 실행 하 여 디스크 IOPS를 60 초 동안 7000 IOPS로 버스트 했습니다. 

이 청구 시간에서 버스트 비용은 다음과 같은 두 가지 요금으로 구성 됩니다.

첫 번째 요금은 $X (지역에 따라 결정 됨)의 버스트 활용 요금입니다. 이 기본 요금은 사용 하지 않도록 설정 될 때까지 연결 상태를 무시 하는 디스크에서 항상 청구 됩니다. 

Second는 버스트 트랜잭션 비용입니다. 두 시간 슬롯에서 디스크 버스트가 발생 했습니다. 00:10:01 – 00:10:10에서 누적 버스트 트랜잭션은 (6000 – 5000) X 10 = 1만입니다. 00:59:01 – 01:00:00에서 누적 버스트 트랜잭션은 (7000 – 5000) X 60 = 12만입니다. 총 버스트 트랜잭션은 1만 + 12만 = 13만입니다. 버스트 트랜잭션 비용은 1만 트랜잭션의 13 개 단위 (지역별 가격에 따라)를 기반으로 $Y 요금이 청구 됩니다.

이 청구 시간의 디스크 버스트에 대 한 총 비용은 $X + $Y와 같습니다. MBps의 프로 비전 된 목표에 대해 버스트에 동일한 계산이 적용 됩니다. MB의 초과분을 IO 크기가 256KB 인 트랜잭션으로 변환 합니다. 디스크 트래픽이 프로 비전 된 IOPS 및 MBps 대상을 모두 초과 하는 경우 아래 예제를 참조 하 여 버스트 트랜잭션을 계산할 수 있습니다. 

디스크 구성: 프리미엄 SSD – 1TB (P30), 디스크 버스트 사용.

- 00:00:01 – 00:00:05 응용 프로그램에서 일괄 처리 작업을 실행 하 여 디스크 IOPS를 1만 IOPS로 버스트 하 고 5 초 동안 300 MBps를 발생 시킵니다.
- 00:00:06 – 00:00:10 응용 프로그램에서 디스크 IOPS를 5 초 동안 6000 IOPS 및 600 MBps로 버스트 하는 복구 작업을 실행 했습니다.

버스트 트랜잭션은 IOPS 또는 MBps 버스트의 최대 트랜잭션 수로 계산 됩니다. 00:00:01 – 00:00:05에서 누적 버스트 트랜잭션은 max ((1만 – 5000), (300-200) * 1024/256)) * 5 = 25000 트랜잭션입니다. 00:00:06 – 00:00:10에서 누적 버스트 트랜잭션은 max ((6000 – 5000), (600-200) * 1024/256)) * 5 = 8000 트랜잭션입니다. 그 위에는 버스트 사용 정액 요금을 포함 하 여 주문형 기반 디스크 버스트를 사용 하는 데 필요한 총 비용을 확보 합니다. 

가격 책정에 대 한 자세한 내용은 [Managed Disks 가격 책정 페이지](https://azure.microsoft.com/pricing/details/managed-disks/) 를 참조 하 고 [Azure 가격 책정 계산기](https://azure.microsoft.com/pricing/calculator/?service=storage) 를 사용 하 여 워크 로드에 대 한 평가를 수행할 수 있습니다. 

### <a name="credit-based-bursting"></a>신용 기반 버스트

신용 기반 버스트는 Azure 공용, 정부 및 중국 클라우드의 모든 지역에서 P20 하 고 작은 디스크 크기에 사용할 수 있습니다. 기본적으로 디스크 버스트는 지원 되는 디스크 크기의 모든 신규 및 기존 배포에서 사용 하도록 설정 됩니다. VM 수준 버스트는 신용 기반 버스트만 사용 합니다.

## <a name="virtual-machine-level-bursting"></a>가상 컴퓨터 수준 버스트

VM 수준 버스트는 버스트에 신용 기반 모델만 사용 하며,이 모델을 지 원하는 모든 Vm에 대해 기본적으로 사용 하도록 설정 되어 있습니다.

VM 수준 버스트는 다음과 같은 지원 되는 크기의 Azure 공용 클라우드의 모든 지역에서 사용 하도록 설정 됩니다. 
- [Lsv2 시리즈](../articles/virtual-machines/lsv2-series.md)
- [Dv3 및 Dsv3 시리즈](../articles/virtual-machines/dv3-dsv3-series.md)
- [Ev3 및 Esv3 시리즈](../articles/virtual-machines/ev3-esv3-series.md)

## <a name="bursting-flow"></a>버스트 흐름

버스트 크레딧 시스템은 VM 수준과 디스크 수준에서 동일한 방식으로 적용 됩니다. VM 또는 디스크 리소스는 자체 버스트 버킷의 완전히 보충 크레딧을 사용 하 여 시작 됩니다. 이러한 크레딧을 통해 최대 버스트 속도로 최대 30 분 동안 버스트를 수행할 수 있습니다. 리소스의 IOPS 또는 m b/s가 리소스의 성능 목표 아래에서 활용 될 때마다 크레딧을 누적 합니다. 리소스가 버스트 크레딧을 계산 하 고 워크 로드에 추가 성능이 필요한 경우 리소스는 해당 크레딧을 사용 하 여 성능 제한을 초과 하 고 워크 로드 요구에 맞게 성능을 향상 시킬 수 있습니다.

![버스트 버킷 다이어그램입니다.](media/managed-disks-bursting/bucket-diagram.jpg)

사용 가능한 크레딧을 확보 하는 방법은 사용자가 결정 합니다. 30 분 동안의 버스트 크레딧을 연속 해 서 또는 하루 종일 사용할 수 있습니다. 리소스를 배포 하는 경우 크레딧을 전체 할당 하 여 제공 합니다. 이러한 경우 restock 하는 데 하루 미만이 걸립니다. 크레딧을 원하는 대로 사용할 수 있으며, 리소스 버스트를 위해 버스트 버킷을 전체 하지 않아도 됩니다. 버스트 누적은 각 리소스에 따라 달라 집니다 .이는 사용 되지 않는 IOPS 및 성능 목표 보다 낮은 m b/초를 기반으로 하기 때문입니다. 기준 성능 리소스가 높으면 해당 버스트 크레딧을 보다 빠르게 계산 하 여 리소스를 사용할 수 있습니다. 예를 들어 P1 디스크 유휴은 초당 120 IOPS를 계산 하지만 유휴 P20 디스크는 초당 2300 IOPS를 계산 합니다.

## <a name="bursting-states"></a>버스트 상태
버스트를 사용 하 여 리소스를 사용할 수 있는 세 가지 상태가 있습니다.
- **발생** – 리소스의 IO 트래픽이 성능 목표 보다 더 작은 값을 사용 합니다. IOPS의 누적 버스트 크레딧 및 m b/초는 서로 별도로 수행 됩니다. 리소스는 발생 IOPS 크레딧을 소비 하 고, m b/s 크레딧을 사용할 수 있으며, 그 반대의 경우도 가능 합니다.
- **버스트** – 리소스의 트래픽이 성능 목표를 초과 하 여 사용 하 고 있습니다. 버스트 트래픽은 IOPS 또는 대역폭의 크레딧을 독립적으로 사용 합니다.
- **상수** – 리소스의 트래픽은 성능 목표에 정확히 맞습니다.

## <a name="bursting-examples"></a>버스트 예

다음 예에서는 다양 한 VM 및 디스크 조합에서 버스트가 작동 하는 방법을 보여 줍니다. 예제를 쉽게 수행할 수 있도록 MB/s에 집중 하지만 동일한 논리가 IOPS에 독립적으로 적용 됩니다.

### <a name="non-burstable-virtual-machine-with-burstable-disks"></a>안정적인 디스크를 사용 하는 비 삼로 안정화 된 가상 머신
**VM 및 디스크 조합:** 
- Standard_D8as_v4 
    - 캐시 되지 않은 m b/초: 192
- P4 OS 디스크
    - 프로 비전 된 MB/s: 25
    - 최대 버스트 MB/s: 170 
- 2 P10 데이터 디스크 
    - 프로 비전 된 MB/s: 100
    - 최대 버스트 MB/s: 170

 VM이 부팅 되 면 OS 디스크에서 데이터를 검색 합니다. OS 디스크는 부팅 중인 VM의 일부 이므로 OS 디스크는 버스트 크레딧을 차지 합니다. 이러한 크레딧을 통해 OS 디스크는 170 m b/초 초에 시작을 버스트 합니다.

![VM이 192 m b/s의 처리량에 대 한 요청을 OS 디스크로 보내고 OS 디스크는 170 m b/초 데이터를 사용 하 여 응답 합니다.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-startup.jpg)

부팅이 완료 되 면 응용 프로그램은 VM에서 실행 되 고 중요 하지 않은 작업을 포함 합니다. 이 워크 로드에는 모든 디스크에서 균등 하 게 분산 되는 15 m b/S가 필요 합니다.

![응용 프로그램에서 VM에 대 한 처리량을 15 m b/초 동안 요청 하 고, VM에서 요청을 수행 하 여 각 디스크를 5 m b/s에 대해 요청 하 고, 각 디스크가 5 m b/s를 반환 합니다.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-idling.jpg)

그런 다음 응용 프로그램에서 192 m b/s가 필요한 일괄 처리 된 작업을 처리 해야 합니다. 2 m b/s는 OS 디스크에서 사용 되며 나머지는 데이터 디스크 간에 균등 하 게 분할 됩니다.

![응용 프로그램에서 VM에 대 한 처리량 95 (192 m b/초)을 요청 하 고, VM에서 요청을 보내고, OS 디스크에 대 한 요청을 대량으로 전송 하 고, 데이터 디스크가 수요를 충족 하기 위해 버스트 하 고, 모든 디스크가 요청 된 처리량을 VM에 반환 하 여 응용 프로그램에 반환 합니다.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-bursting.jpg)

### <a name="burstable-virtual-machine-with-non-burstable-disks"></a>비 다른 안정적인 디스크로 안정화 된 안정적인 가상 머신
**VM 및 디스크 조합:** 
- Standard_L8s_v2 
    - 캐시 되지 않은 m b/초: 160
    - 최대 버스트 MB/s: 1280
- P50 OS 디스크
    - 프로 비전 된 MB/s: 250 
- 2 P10 데이터 디스크 
    - 프로 비전 된 MB/s: 250

 초기 부팅 후 응용 프로그램은 VM에서 실행 되 고 중요 하지 않은 워크 로드를 포함 합니다. 이 워크 로드에는 모든 디스크에 균등 하 게 분산 되는 30 m b/s가 필요 합니다.
![응용 프로그램에서 VM에 대 한 처리량의 30 m b/초 요청을 보내고, VM에서 요청을 수행 하 고 각각 10mb/s에 대 한 요청을 보냅니다. 각 디스크는 10mb/s를 반환 하 고, VM은 응용 프로그램에 30 m b/초를 반환 합니다.](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-normal.jpg)

그런 다음 응용 프로그램에서 600 m b/s가 필요한 일괄 처리 된 작업을 처리 해야 합니다. 이 수요를 충족 하는 Standard_L8s_v2 버스트 된 다음 디스크에 대 한 요청은 P50 디스크에 고르게 분산 됩니다.

![응용 프로그램에서 VM에 대 한 처리량 (600 m b/초)을 요청 하 고 VM에서 요청을 하 여 각 디스크를 200 m b/s에 대 한 요청을 전송 합니다. 각 디스크는 200 m b/s를 반환 하 고 VM 버스트를 반환 하 여 응용 프로그램에 600 MB/s를 반환](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-bursting.jpg)
### <a name="burstable-virtual-machine-with-burstable-disks"></a>안정적인 디스크를 사용 하는 안정적인 가상 머신
**VM 및 디스크 조합:** 
- Standard_L8s_v2 
    - 캐시 되지 않은 m b/초: 160
    - 최대 버스트 MB/s: 1280
- P4 OS 디스크
    - 프로 비전 된 MB/s: 25
    - 최대 버스트 MB/s: 170 
- 2 P4 데이터 디스크 
    - 프로 비전 된 MB/s: 25
    - 최대 버스트 MB/s: 170 

VM이 시작 되 면 OS 디스크에서 해당 버스트 제한인 1280 m b/s를 요청 하기 위해 버스트 되 고 OS 디스크는 170 m b/초 버스트 성능으로 응답 합니다.

![시작할 때 VM은 OS 디스크에 1280 m b/s 요청을 전송 하 고, OS 디스크 버스트는 1280 m b/초를 반환 합니다.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-startup.jpg)

시작한 후에는 중요 하지 않은 작업을 포함 하는 응용 프로그램을 시작 합니다. 이 응용 프로그램은 모든 디스크에서 균등 하 게 분산 되는 15 m b/초를 요구 합니다.

![응용 프로그램은 VM에 대 한 처리량을 15 m b/초에 요청 하 고, VM은 요청을 받아서 5 m b/s에 대 한 요청을 전송 합니다. 각 디스크는 5mb의 응답을 반환 하 고, VM은 응용 프로그램에 15mb/s를 반환 합니다.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-idling.jpg)

그런 다음 응용 프로그램에서 360 m b/s가 필요한 일괄 처리 된 작업을 처리 해야 합니다. 이 수요와 요청을 충족 하는 Standard_L8s_v2 버스트 됩니다. OS 디스크에는 20mb/s만 필요 합니다. 나머지 340 m b/s는 버스트 P4 데이터 디스크에 의해 처리 됩니다.

![응용 프로그램에서 VM에 대 한 처리량 (360 m b/초)을 요청 하 고, VM에서 요청을 하 고 각 데이터 디스크를 OS 디스크에서 170 m b/초 및 20mb/s에 대 한 요청으로 전송 합니다. 각 디스크는 요청 된 MB/s를 반환 하 고, VM 버스트는 360 m b/초를 응용 프로그램에 반환 합니다.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-bursting.jpg)
