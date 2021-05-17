---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: d610193783d44b86d48cd5ceaa91377b32b7061b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99579947"
---
<!-- Not used for Ls-series -->

## <a name="size-table-definitions"></a>크기 테이블 정의

- 스토리지 용량 단위는 GiB(1024^3바이트) 단위로 표시됩니다. GB(1000^3바이트) 단위로 측정된 디스크를 GiB(1024^3바이트) 단위로 측정된 디스크와 비교할 때는 GiB 단위로 지정된 용량 수치가 더 작게 표시될 수 있음에 유의해야 합니다. 예를 들어 1023GiB = 1098.4GB입니다.
- 디스크 처리량은 IOPS(초당 입/출력 작업 수) 및 MBps로 측정되며, MBps = 10^6바이트/초입니다.
- 데이터 디스크는 캐시된 모드 또는 캐시되지 않은 모드에서 작동할 수 있습니다. 캐시된 데이터 디스크 작업의 경우 호스트 캐시 모드가 **ReadOnly** 또는 **ReadWrite** 로 설정됩니다.  캐시되지 않은 데이터 디스크 작업의 경우에는 호스트 캐시 모드가 **None** 으로 설정됩니다.
- VM에 가장 적합한 스토리지 성능을 얻는 방법에 대해 알아보려면 [가상 머신 및 디스크 성능](../articles/virtual-machines/disks-performance.md)을 참조하세요.
- **예상 네트워크 대역폭** 은 모든 NIC에서 모든 대상에 대해 VM 유형별로 할당된 최대 집계 대역폭입니다. 자세한 내용은 [가상 머신 네트워크 대역폭](../articles/virtual-network/virtual-machine-network-throughput.md)을 참조하세요.

  상한은 보장되지 않습니다. 제한은 원하는 애플리케이션에 적합한 VM 유형을 선택하기 위한 지침을 제공합니다. 실제 네트워크 성능은 네트워크 정체, 애플리케이션 로드 및 네트워크 설정을 비롯한 여러 요인에 따라 달라집니다. 네트워크 처리량 최적화에 대한 정보는 [Azure 가상 머신에 대한 네트워크 처리량 최적화](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md)를 참조하세요. Linux 또는 Windows에서 예상되는 네트워크 성능을 달성하려면 특정 버전을 선택하거나 VM을 최적화해야 할 수도 있습니다. 자세한 내용은 [대역폭/처리량 테스트(NTTTCP)](../articles/virtual-network/virtual-network-bandwidth-testing.md)를 참조하세요.



