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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "74279199"
---
<!-- Not used for Ls-series -->

## <a name="size-table-definitions"></a>크기 테이블 정의

- 스토리지 용량 단위는 GiB(1024^3바이트) 단위로 표시됩니다. GB (1000 ^ 3 바이트) 단위로 측정 된 디스크를 GiB (1024 ^ 3)로 측정 한 디스크와 비교할 경우 GiB에 지정 된 용량 숫자가 더 작게 표시 될 수 있습니다. 예를 들면 1023 GiB = 1098.4 GB입니다.
- 디스크 처리량은 IOPS(초당 입/출력 작업 수) 및 MBps로 측정되며, MBps = 10^6바이트/초입니다.
- 데이터 디스크는 캐시된 모드 또는 캐시되지 않은 모드에서 작동할 수 있습니다. 캐시된 데이터 디스크 작업의 경우 호스트 캐시 모드가 **ReadOnly** 또는 **ReadWrite**로 설정됩니다.  캐시되지 않은 데이터 디스크 작업의 경우에는 호스트 캐시 모드가 **None**으로 설정됩니다.
- Vm에 대 한 최상의 성능을 얻으려면 데이터 디스크 수를 vCPU 당 두 개의 디스크로 제한 해야 합니다.
- **예상 네트워크 대역폭**은 모든 NIC에서 모든 대상에 대해 VM 유형별로 할당된 최대 집계 대역폭입니다. 자세한 내용은 [가상 컴퓨터 네트워크 대역폭](../articles/virtual-network/virtual-machine-network-throughput.md)을 참조 하세요.

  상한값은 보장 되지 않습니다. 제한은 원하는 응용 프로그램에 적합 한 VM 유형을 선택 하기 위한 지침을 제공 합니다. 실제 네트워크 성능은 네트워크 정체, 응용 프로그램 로드, 네트워크 설정 등의 여러 가지 요인에 따라 달라 집니다. 네트워크 처리량 최적화에 대 한 자세한 내용은 [Azure 가상 컴퓨터에 대 한 네트워크 처리량 최적화](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md)를 참조 하세요. Linux 또는 Windows에서 예상 되는 네트워크 성능을 얻으려면 특정 버전을 선택 하거나 VM을 최적화 해야 할 수 있습니다. 자세한 내용은 [대역폭/처리량 테스트 (NTTTCP)](../articles/virtual-network/virtual-network-bandwidth-testing.md)를 참조 하세요.



