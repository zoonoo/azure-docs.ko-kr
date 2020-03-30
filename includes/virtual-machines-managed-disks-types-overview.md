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
ms.openlocfilehash: 72d87142f9b9c1f7bcb2b02281851bd1e29bc9c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78261988"
---
Azure 관리 디스크는 현재 네 가지 디스크 유형을 제공하며 각 유형은 특정 고객 시나리오를 대상으로 합니다.

## <a name="disk-comparison"></a>디스크 비교

다음 표에서는 관리형 디스크에 대한 울트라 디스크, 프리미엄 솔리드 스테이트 드라이브(SSD), 표준 SSD 및 표준 하드 디스크 드라이브(HDD)를 비교하여 사용할 용도를 결정하는 데 도움을 줍니다.

|   | Ultra disk   | 프리미엄 SSD   | 표준 SSD   | 표준 HDD   |
|---------|---------|---------|---------|---------|
|디스크 유형   |SSD   |SSD   |SSD   |HDD   |
|시나리오   |[SAP HANA,](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)최상위 데이터베이스(예: SQL, Oracle) 및 기타 트랜잭션이 많은 워크로드와 같은 IO 집약적인 워크로드입니다.   |프로덕션 및 성능이 중요한 워크로드   |웹 서버, 적게 사용되는 엔터프라이즈 애플리케이션 및 개발/테스트   |백업, 중요하지 않음, 가끔 액세스   |
|최대 디스크 크기   |65536기비바이트(GiB)    |32,767GiB    |32,767GiB   |32,767GiB   |
|최대 처리량   |2,000MiB/초    |900MiB/초   |750MiB/초   |500MiB/초   |
|최대 IOPS   |160,000    |20,000   |6,000   |2,000   |

## <a name="ultra-disk"></a>Ultra disk

Azure ultra disks는Azure IaaS VM에 대해 높은 처리량, 높은 IOPS 및 일관성 있는 짧은 대기 시간 디스크 스토리지를 제공합니다. 울트라 디스크의 몇 가지 추가 이점에는 가상 컴퓨터(VM)를 다시 시작할 필요 없이 워크로드와 함께 디스크 성능을 동적으로 변경할 수 있는 기능이 있습니다. Ultra disks는 SAP HANA, 최상위 계층 데이터베이스 및 트랜잭션 집약적 워크로드와 같은 데이터 집약적 워크로드에 적합합니다. Ultra disks는 데이터 디스크로만 사용할 수 있습니다. 프리미엄 SSD는 OS 디스크로 사용하는 것이 좋습니다.

### <a name="performance"></a>성능

울트라 SSD를 프로비저닝할 때 디스크의 용량과 성능을 독립적으로 구성할 수 있습니다. 울트라 디스크는 4GiB에서 최대 64TiB에 이르는 여러 고정 크기로 제공되며 IOPS 및 처리량을 독립적으로 구성할 수 있는 유연한 성능 구성 모델을 갖추고 있습니다.

울트라 디스크의 몇 가지 주요 기능은 다음과 같습니다.

- 디스크 용량: 울트라 디스크 용량은 4GiB에서 64TiB까지다양합니다.
- 디스크 IOPS: 울트라 디스크는 디스크당 최대 160K IOPS의 300 IOPS/GiB의 IOPS 제한을 지원합니다. 프로비전한 IOPS를 얻으려면 선택한 디스크 IOPS가 VM IOPS 제한보다 적도록 합니다. 디스크당 최소 보장IOPS는 2IOPS/GiB이며 전체 기준최소 100IOPS입니다. 예를 들어 4GiB 울트라 디스크가 있는 경우 8개의 IOPS 대신 최소 100개의 IOPS를 갖게 됩니다.
- 디스크 처리량: 울트라 디스크의 경우 프로비저닝된 각 IOPS당 단일 디스크의 처리량 제한은 256KiB/s이며, 디스크당 최대 2,000MBps입니다(MBps = 초당 10^6바이트). 디스크당 최소 보장 처리량은 프로비저닝된 각 IOPS에 대해 4KiB/s이며 전체 기준최소는 1MBps입니다.
- 울트라 디스크는 가상 컴퓨터에서 디스크를 분리하지 않고 런타임에 디스크 성능 특성(IOPS 및 처리량)을 조정하는 것을 지원합니다. 디스크 성능 크기 조정 작업이 디스크에서 실행된 후 변경 내용이 실제로 적용되려면 최대 한 시간이 걸릴 수 있습니다. 24시간 동안 4개의 성능 크기 조정 작업으로 제한됩니다. 성능 대역폭 용량부족으로 인해 성능 크기 조정 작업이 실패할 수 있습니다.

### <a name="disk-size"></a>디스크 크기

|디스크 크기(GiB)  |IOPS 캡  |처리량 용량(MBps)  |
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
