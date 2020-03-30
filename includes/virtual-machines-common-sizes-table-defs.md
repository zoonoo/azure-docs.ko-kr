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
ms.openlocfilehash: 63c53a9b95e27486d7d6944c28f8fb085b1bc6ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74279199"
---
<!-- Not used for Ls-series -->

## <a name="size-table-definitions"></a>크기 테이블 정의

- 스토리지 용량 단위는 GiB(1024^3바이트) 단위로 표시됩니다. GB(1000^3바이트)로 측정된 디스크와 GiB(1024^3)로 측정된 디스크를 비교할 때 GiB에 제공된 용량 수가 더 작게 나타날 수 있습니다. 예를 들어 1023 GiB = 1098.4GB입니다.
- 디스크 처리량은 IOPS(초당 입/출력 작업 수) 및 MBps로 측정되며, MBps = 10^6바이트/초입니다.
- 데이터 디스크는 캐시된 모드 또는 캐시되지 않은 모드에서 작동할 수 있습니다. 캐시된 데이터 디스크 작업의 경우 호스트 캐시 모드가 **ReadOnly** 또는 **ReadWrite**로 설정됩니다.  캐시되지 않은 데이터 디스크 작업의 경우에는 호스트 캐시 모드가 **None**으로 설정됩니다.
- VM에 가장 적합한 성능을 얻으려면 데이터 디스크 수를 vCPU당 두 개의 디스크로 제한해야 합니다.
- **예상 네트워크 대역폭**은 모든 NIC에서 모든 대상에 대해 VM 유형별로 할당된 최대 집계 대역폭입니다. 자세한 내용은 [가상 컴퓨터 네트워크 대역폭을](../articles/virtual-network/virtual-machine-network-throughput.md)참조하십시오.

  상한선은 보장되지 않습니다. 제한은 의도한 응용 프로그램에 적합한 VM 유형을 선택하기 위한 지침을 제공합니다. 실제 네트워크 성능은 네트워크 정체, 응용 프로그램 로드 및 네트워크 설정을 비롯한 여러 요인에 따라 달라집니다. 네트워크 처리량 최적화에 대한 자세한 내용은 [Azure 가상 시스템에 대한 네트워크 처리량 최적화를](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md)참조하십시오. Linux 또는 Windows에서 예상되는 네트워크 성능을 얻으려면 특정 버전을 선택하거나 VM을 최적화해야 할 수 있습니다. 자세한 내용은 [NTTTCP(대역폭/처리량 테스트)를](../articles/virtual-network/virtual-network-bandwidth-testing.md)참조하십시오.



