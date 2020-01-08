---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/15/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 31a9da0678f602afcc117e5b2f7927af379da668
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75467589"
---
Azure managed disks는 현재 4 개의 디스크 유형을 제공 합니다. 각 유형은 특정 고객 시나리오를 목표로 합니다.

## <a name="disk-comparison"></a>디스크 비교

다음 표에서는 사용할 항목을 결정 하는 데 도움이 되는 관리 디스크에 대 한 ultra disks, 프리미엄 SSD (반도체 드라이브), 표준 SSD 및 표준 HDD (하드 디스크 드라이브)의 비교를 제공 합니다.

|   | Ultra disk   | 프리미엄 SSD   | 표준 SSD   | 표준 HDD   |
|---------|---------|---------|---------|---------|
|디스크 유형   |SSD   |SSD   |SSD   |HDD   |
|시나리오   |[SAP HANA](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md), 최상위 계층 데이터베이스 (예: SQL, Oracle) 및 기타 트랜잭션 집약적 워크 로드 등의 IO 집약적 워크 로드   |프로덕션 및 성능이 중요한 워크로드   |웹 서버, 적게 사용되는 엔터프라이즈 애플리케이션 및 개발/테스트   |백업, 중요하지 않음, 가끔 액세스   |
|최대 디스크 크기   |65536기비바이트(GiB)    |32,767GiB    |32,767GiB   |32,767GiB   |
|최대 처리량   |2,000MiB/초    |900 MiB/s   |750 MiB/s   |500 MiB/s   |
|최대 IOPS   |160,000    |20,000   |6,000   |2,000   |

## <a name="ultra-disk"></a>Ultra disk

Azure ultra disks는Azure IaaS VM에 대해 높은 처리량, 높은 IOPS 및 일관성 있는 짧은 대기 시간 디스크 스토리지를 제공합니다. Ultra disks의 몇 가지 추가 이점에는 VM (가상 머신)을 다시 시작할 필요 없이 워크 로드와 함께 디스크의 성능을 동적으로 변경 하는 기능이 포함 됩니다. Ultra disks는 SAP HANA, 최상위 계층 데이터베이스 및 트랜잭션 집약적 워크로드와 같은 데이터 집약적 워크로드에 적합합니다. Ultra disks는 데이터 디스크로만 사용할 수 있습니다. 프리미엄 SSD는 OS 디스크로 사용하는 것이 좋습니다.

### <a name="performance"></a>성능 중심

울트라 SSD를 프로비저닝할 때 디스크의 용량과 성능을 독립적으로 구성할 수 있습니다. 울트라 디스크는 4 GiB 최대 64 TiB에 이르는 여러 고정 크기를 제공 하며, IOPS 및 처리량을 독립적으로 구성할 수 있는 유연한 성능 구성 모델을 제공 합니다.

울트라 디스크의 몇 가지 주요 기능은 다음과 같습니다.

- 디스크 용량: Ultra disks 용량은 GiB 4부터 64 TiB까지 범위를 가집니다.
- 디스크 IOPS: Ultra disks는 디스크당 300 IOPS/160 GiB의 IOPS 제한을 지원 합니다. 프로 비전 된 IOPS를 얻으려면 선택한 디스크 IOPS가 VM IOPS 제한 보다 적은지 확인 합니다. 디스크당 최소 IOPS는 2 IOPS/GiB 이며 전체 기준이 최소 100 IOPS입니다. 예를 들어 GiB 울트라 디스크가 4 개 있는 경우 8 IOPS 대신 최소 100 IOPS를 갖게 됩니다.
- 디스크 처리량: ultra disks에서 단일 디스크의 처리량 제한은 각 프로 비전 된 2000 IOPS에 대해 256 KiB/s입니다 (초당 MBps = 10 ^ 6 바이트). 디스크당 최소 처리량은 프로 비전 된 각 IOPS에 대해 4KiB/s 이며, 최소 1 MBps의 전체 기준이 사용 됩니다.
- Ultra disks는 가상 머신에서 디스크를 분리 하지 않고도 런타임에 디스크 성능 특성 (IOPS 및 처리량) 조정을 지원 합니다. 디스크 성능 크기 조정 작업이 디스크에서 실행된 후 변경 내용이 실제로 적용되려면 최대 한 시간이 걸릴 수 있습니다. 24 시간 동안 성능 크기 조정 작업은 4 개로 제한 됩니다. 성능 대역폭 용량이 부족 하기 때문에 성능 크기 조정 작업이 실패할 수 있습니다.

### <a name="disk-size"></a>디스크 크기

|디스크 크기(GiB)  |IOPS 상한  |처리량 용량(MBps)  |
|---------|---------|---------|
|4     |1,200         |300         |
|8     |2,400         |600         |
|16     |4,800         |1,200         |
|32     |9,600         |2,000         |
|64     |19,200         |2,000         |
|128     |38,400         |2,000         |
|256     |76,800         |2,000         |
|512     |80,000         |2,000         |
|1,024-65,536(1TiB의 단위로 증가하는 이 범위의 크기)     |160,000         |2,000         |

### <a name="ga-scope-and-limitations"></a>GA 범위 및 제한 사항

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]