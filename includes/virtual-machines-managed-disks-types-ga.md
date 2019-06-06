---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/14/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c7b73cad200666db9e926d8e808eaa4a8dccffb2
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66249079"
---
## <a name="premium-ssd"></a>프리미엄 SSD

Azure 프리미엄 SSD는 IO(입출력) 집약적 워크로드가 있는 VM(가상 머신)에 대기 시간이 짧은 고성능 디스크를 지원합니다. 프리미엄 스토리지 디스크의 속도와 성능을 활용하기 위해 기존 VM 디스크를 프리미엄 SSD로 마이그레이션할 수 있습니다. 프리미엄 SSD는 중요 업무용 프로덕션 애플리케이션에 적합합니다. Premium Ssd는 premium storage와 호환 되는 VM 시리즈만 사용할 수 있습니다.

개별 VM 유형 및 크기의 Azure에 대 한 Windows, premium storage와 호환 크기를 포함 하는 방법에 대 한 자세한 내용은 [Windows VM 크기](../articles/virtual-machines/windows/sizes.md)합니다. 참조에 대해 자세히 알아보려면 개별 VM 유형 및 Azure에서 linux의 경우 premium storage와 호환 되는 크기 포함 [Linux VM 크기](../articles/virtual-machines/linux/sizes.md)합니다.

### <a name="disk-size"></a>디스크 크기
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

Premium Storage 디스크를 프로비전하면 표준 스토리지와 달리, 해당 디스크의 용량, IOPS 및 처리량이 보장됩니다. 예를 들어 P50 디스크를 만들면 Azure에서 해당 디스크에 저장소 용량 4,095GB, 7,500 IOPS, 250MB/초 처리량이 프로비전됩니다. 애플리케이션에서 용량 및 성능의 전체 또는 일부를 사용할 수 있습니다. Premium SSD 디스크 IOPS 및 처리량 앞 테이블 99.9%의 시간에 설명 된 대상 및 낮은 자리 밀리초 대기 시간을 제공 하도록 설계 되었습니다.

### <a name="transactions"></a>트랜잭션

Premium Ssd에 대 한 각 I/O 작업이 적은 보다 작거나 256 3:n2}kib/ 처리량의 비율은 단일 I/O 작업. 입출력 256 3:n2}kib/ 보다 큰 처리량의 크기 256의 여러 I/o 것으로 간주 됩니다 3:n2}kib/ 합니다.

## <a name="standard-ssd"></a>표준 SSD

Azure 표준 SSD는 더 낮은 IOPS 수준에서 일관된 성능이 필요한 워크로드에 최적화된 비용 효율적 스토리지 옵션입니다. 특히 표준 SSD는 온-프레미스 HDD 솔루션에서 실행 중인 다양한 워크로드에 문제가 발생해서 클라우드로 전환하고자 하는 경우에 적합한 입문용 환경을 제공합니다. 표준 Ssd 표준 Hdd에 비해 더 나은 가용성, 일관성, 안정성 및 대기 시간 제공 합니다. 표준 SSD는 웹 서버, 낮은 IOPS 애플리케이션 서버, 가끔 사용되는 엔터프라이즈 애플리케이션 및 개발/테스트 워크로드에 적합합니다. 표준 Hdd와 같은 표준 SSDs는 모든 Azure Vm에서 사용할 수 있습니다.

### <a name="disk-size"></a>디스크 크기
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

표준 Ssd 1 자리 밀리초 대기 시간 및 IOPS 및 처리량 이전 표 99%의 시간에 설명 된 한도까지 제공 하도록 설계 되었습니다. 실제 IOPS 및 처리량 트래픽 패턴에 따라 경우에 따라 달라질 수 있습니다. 표준 SSD는 HDD 디스크보다 더 일관적인 성능과 더 짧은 대기 시간을 제공합니다.

### <a name="transactions"></a>트랜잭션

표준 Ssd에 대 한 각 I/O 작업이 적은 보다 작거나 256 3:n2}kib/ 처리량으로 간주 됩니다 단일 I/O 작업. 입출력 256 3:n2}kib/ 보다 큰 처리량의 크기 256의 여러 I/o 것으로 간주 됩니다 3:n2}kib/ 합니다. 이러한 트랜잭션은 청구 영향을 미칩니다.

## <a name="standard-hdd"></a>표준 HDD

Azure 표준 HDD는 대기 시간에 민감하지 않은 워크로드를 실행하는 VM에 안정적이고 저렴한 디스크를 지원합니다. 표준 스토리지를 사용하는 경우 데이터는 HDD(하드 디스크 드라이브)에 저장됩니다. 대기 시간, IOPS 및 표준 HDD의 처리량의 디스크는 SSD 기반 디스크를 비교 하 여 보다 광범위 하 게 달라질 수 있습니다. Vm에서 작업할 때에 덜 중요 한 워크 로드 및 개발/테스트 시나리오에 대 한 표준 HDD 디스크를 사용할 수 있습니다. 표준 Hdd 모든 Azure 지역에서 사용할 수 있으며 모든 Azure Vm에 사용 될 수 있습니다.

### <a name="disk-size"></a>디스크 크기
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>트랜잭션

표준 Hdd 각 IO 작업의 I/O 크기에 관계 없이 단일 트랜잭션으로 간주 됩니다. 이러한 트랜잭션은 청구 영향을 미칩니다.

## <a name="billing"></a>결제

관리 디스크를 사용할 때 적용되는 청구 고려 사항은 다음과 같습니다.

- 디스크 유형
- 관리 디스크 크기
- 스냅샷
- 아웃바운드 데이터 전송
- 트랜잭션 수

**관리 디스크 크기:** 관리 디스크의 요금은 프로비저닝된 크기에 따라 청구됩니다. Azure에서 프로비저닝된 크기(반올림)는 가장 가깝게 제안되는 디스크 크기에 매핑됩니다. 제안되는 디스크 크기에 대한 자세한 내용은 이전 표를 참조하세요. 각 디스크는 지원되는 프로비저닝된 디스크 크기 제안에 매핑되고 이에 따라 요금이 청구됩니다. 예를 들어 200GiB 표준 SSD를 프로비저닝한 경우 E15(256GiB)의 디스크 크기 제안에 매핑됩니다. 프로비전된 디스크에 대한 청구는 Premium Storage 제품의 월별 가격을 사용하여 시간당 비례합니다. 예를 들어 E10 디스크를 프로비전하고 20시간 후 삭제한 경우 20시간에 비례하여 E10 제품에 대해 청구됩니다. 이는 디스크에 기록되는 실제 데이터 양에 관계없이 적용됩니다.

**스냅숏**: 스냅샷 요금은 사용된 크기에 따라 청구됩니다. 예를 들어 프로비저닝된 용량이 64GiB이고 실제 사용된 데이터 크기가 10GiB인 관리 디스크의 스냅샷을 만들면 사용된 10GiB의 데이터 크기에 대해서만 스냅샷 요금이 청구됩니다.
