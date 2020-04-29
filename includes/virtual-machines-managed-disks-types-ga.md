---
title: 파일 포함
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6740ea320f2d950386da12eb44726e2c826b60a4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80386099"
---
## <a name="premium-ssd"></a>프리미엄 SSD

Azure 프리미엄 SSD는 IO(입출력) 집약적 워크로드가 있는 VM(가상 머신)에 대기 시간이 짧은 고성능 디스크를 지원합니다. 프리미엄 스토리지 디스크의 속도와 성능을 활용하기 위해 기존 VM 디스크를 프리미엄 SSD로 마이그레이션할 수 있습니다. 프리미엄 SSD는 중요 업무용 프로덕션 애플리케이션에 적합합니다. Premium Ssd는 프리미엄 저장소와 호환 되는 VM 시리즈에만 사용할 수 있습니다.

프리미엄 저장소와 호환 되는 크기를 비롯 하 여 Windows 용 Azure에서 개별 VM 유형 및 크기에 대 한 자세한 내용은 [WINDOWS VM 크기](../articles/virtual-machines/windows/sizes.md)를 참조 하세요. 프리미엄 저장소와 호환 되는 크기를 포함 하 여 Linux 용 Azure의 개별 VM 유형 및 크기에 대해 자세히 알아보려면 [LINUX VM 크기](../articles/virtual-machines/linux/sizes.md)를 참조 하세요. 이러한 문서 중 하나에서 각 개별 VM 크기 문서를 확인 하 여 premium storage와 호환 되는지 확인 해야 합니다.

### <a name="disk-size"></a>디스크 크기
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

Premium Storage 디스크를 프로비전하면 표준 스토리지와 달리, 해당 디스크의 용량, IOPS 및 처리량이 보장됩니다. 예를 들어 P50 디스크를 만들면 Azure에서 해당 디스크에 스토리지 용량 4,095GB, 7,500 IOPS, 250MB/초 처리량이 프로비전됩니다. 애플리케이션에서 용량 및 성능의 전체 또는 일부를 사용할 수 있습니다. 프리미엄 SSD 디스크는 이전 테이블의 99.9%에 설명 된 낮은 1 자리 밀리초 대기 시간 및 대상 IOPS 및 처리량을 제공 하도록 설계 되었습니다.

## <a name="bursting"></a>화

P30 보다 작은 프리미엄 SSD 크기는 이제 디스크 버스트를 제공 하 고 디스크당 IOPS를 3500까지, 대역폭을 170 Mbps까지 버스트 할 수 있습니다. 버스트는 자동화 되며 신용 시스템을 기반으로 작동 합니다. 디스크 트래픽이 프로 비전 된 성능 목표 보다 낮은 경우에는 버스트 버킷에 따라 크레딧이 자동으로 누적 되며, 트래픽이 최대 버스트 제한까지 목표를 초과 하는 경우 자동으로 사용 됩니다. 최대 버스트 제한은 사용할 버스트 크레딧을 보유 하 고 있는 경우에도 디스크 IOPS & 대역폭의 최대값을 정의 합니다. 디스크 버스트는 IO 패턴의 예기치 않은 변경에 대 한 더 나은 허용 오차를 제공 합니다. 급증 트래픽을 사용 하는 OS 디스크 부팅 및 응용 프로그램에 대해 가장 잘 활용할 수 있습니다.    

디스크 버스트 지원은 기본적으로 적용 가능한 디스크 크기의 새 배포에서 사용 하도록 설정 되며 사용자 작업은 필요 하지 않습니다. 해당 하는 크기의 기존 디스크의 경우 두 옵션 중 하나를 사용 하 여 버스트를 사용 하도록 설정할 수 있습니다. 디스크를 분리 하 고 다시 연결 하거나 연결 된 VM을 중지 했다가 다시 시작 합니다. 최대 버스트 제한인 30 분의 최대 기간을 지 원하는 가상 컴퓨터에 디스크가 연결 되어 있는 경우 적용 가능한 모든 디스크 크기가 전체 버스트 크레딧 버킷으로 시작 됩니다. Azure 디스크에서 버스트가 작동 하는 방식에 대해 자세히 알아보려면 [프리미엄 SSD 버스트](../articles/virtual-machines/linux/disk-bursting.md)를 참조 하세요. 

### <a name="transactions"></a>트랜잭션

Premium Ssd의 경우 256 KiB 처리량 보다 작거나 같은 각 i/o 작업은 단일 i/o 작업으로 간주 됩니다. KiB의 처리량이 256 보다 큰 i/o 작업은 크기 256 KiB의 여러 i/o로 간주 됩니다.

## <a name="standard-ssd"></a>표준 SSD

Azure 표준 SSD는 더 낮은 IOPS 수준에서 일관된 성능이 필요한 워크로드에 최적화된 비용 효율적 스토리지 옵션입니다. 특히 표준 SSD는 온-프레미스 HDD 솔루션에서 실행 중인 다양한 워크로드에 문제가 발생해서 클라우드로 전환하고자 하는 경우에 적합한 입문용 환경을 제공합니다. 표준 Hdd에 비해 표준 Ssd는 더 나은 가용성, 일관성, 안정성 및 대기 시간을 제공 합니다. 표준 SSD는 웹 서버, 낮은 IOPS 애플리케이션 서버, 가끔 사용되는 엔터프라이즈 애플리케이션 및 개발/테스트 워크로드에 적합합니다. Standard Hdd와 마찬가지로 standard Ssd는 모든 Azure Vm에서 사용할 수 있습니다.

### <a name="disk-size"></a>디스크 크기
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

표준 Ssd는 1 자리 밀리초 대기 시간 및 IOPS 및 처리량을 앞의 표 99%에 설명 된 제한까지 제공 하도록 설계 되었습니다. 실제 IOPS 및 처리량은 트래픽 패턴에 따라 때때로 달라질 수 있습니다. 표준 SSD는 HDD 디스크보다 더 일관적인 성능과 더 짧은 대기 시간을 제공합니다.

### <a name="transactions"></a>트랜잭션

표준 Ssd의 경우 256 KiB 처리량 보다 작거나 같은 각 i/o 작업은 단일 i/o 작업으로 간주 됩니다. KiB의 처리량이 256 보다 큰 i/o 작업은 크기 256 KiB의 여러 i/o로 간주 됩니다. 이러한 트랜잭션은 청구에 영향을 미칩니다.

## <a name="standard-hdd"></a>표준 HDD

Azure 표준 HDD는 대기 시간에 민감하지 않은 워크로드를 실행하는 VM에 안정적이고 저렴한 디스크를 지원합니다. 표준 스토리지를 사용하는 경우 데이터는 HDD(하드 디스크 드라이브)에 저장됩니다. 표준 HDD 디스크의 대기 시간, IOPS 및 처리량은 SSD 기반 디스크에 비해 크게 달라질 수 있습니다. 표준 HDD 디스크는 대부분의 IO 작업에서 20ms 아래의 10ms 및 읽기 대기 시간에 쓰기 대기 시간을 제공 하도록 설계 되었지만 실제 성능은 IO 크기 및 워크 로드 패턴에 따라 달라질 수 있습니다. Vm을 사용 하는 경우 개발/테스트 시나리오에 표준 HDD 디스크를 사용 하 고 중요 한 워크 로드를 줄일 수 있습니다. Standard Hdd는 모든 Azure 지역에서 사용할 수 있으며 모든 Azure Vm에서 사용할 수 있습니다.

### <a name="disk-size"></a>디스크 크기
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>트랜잭션

표준 Hdd 경우 각 IO 작업은 i/o 크기에 관계 없이 단일 트랜잭션으로 간주 됩니다. 이러한 트랜잭션은 청구에 영향을 미칩니다.

## <a name="billing"></a>결제

관리 디스크를 사용할 때 적용되는 청구 고려 사항은 다음과 같습니다.

- 디스크 유형
- 관리 디스크 크기
- 스냅샷
- 아웃바운드 데이터 전송
- 트랜잭션 수

**관리 디스크 크기:** 관리 디스크의 요금은 프로비저닝된 크기에 따라 청구됩니다. Azure에서 프로비저닝된 크기(반올림)는 가장 가깝게 제안되는 디스크 크기에 매핑됩니다. 제안되는 디스크 크기에 대한 자세한 내용은 이전 표를 참조하세요. 각 디스크는 지원되는 프로비저닝된 디스크 크기 제안에 매핑되고 이에 따라 요금이 청구됩니다. 예를 들어 200GiB 표준 SSD를 프로비저닝한 경우 E15(256GiB)의 디스크 크기 제안에 매핑됩니다. 프로비전된 디스크에 대한 청구는 Premium Storage 제품의 월별 가격을 사용하여 시간당 비례합니다. 예를 들어 E10 디스크를 프로비전하고 20시간 후 삭제한 경우 20시간에 비례하여 E10 제품에 대해 청구됩니다. 이는 디스크에 기록되는 실제 데이터 양에 관계없이 적용됩니다.

**스냅숏**: 스냅숏은 사용 된 크기를 기준으로 요금이 청구 됩니다. 예를 들어 프로비저닝된 용량이 64GiB이고 실제 사용된 데이터 크기가 10GiB인 관리 디스크의 스냅샷을 만들면 사용된 10GiB의 데이터 크기에 대해서만 스냅샷 요금이 청구됩니다.
