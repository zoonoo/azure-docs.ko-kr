---
title: DC 시리즈-Azure Virtual Machines
description: DC 시리즈 Vm에 대 한 사양입니다.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 8f36220ab81c0fd9533bf2f025b801b37823da5f
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493814"
---
# <a name="dc-series"></a>DC 시리즈

[DC 시리즈](#dc-series) 는 공용 클라우드에서 처리 되는 동안 데이터 및 코드의 기밀성과 무결성을 보호 하는 Azure의 가상 머신 패밀리입니다. 이러한 컴퓨터는 SGX 기술을 사용 하 여 3.7 g h z Intel XEON E-2176G 프로세서에서 지원 됩니다. Intel Turbo Boost 기술을 사용하면 이 머신은 최대 4.7GHz까지 작동할 수 있습니다. DC 시리즈 인스턴스를 사용하면 고객은 사용 중인 고객의 코드와 데이터를 보호할 수 있는 보안 Enclave 기반 애플리케이션을 빌드할 수 있습니다.

Premium Storage: 지원됨

Premium Storage 캐싱: 지원 됨

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시된 임시 스토리지 처리량: IOPS/MBps(GiB 단위의 캐시 크기) | 최대 캐시되지 않은 디스크 처리량: IOPS/MBps | 최대 NIC 수 / 예상 네트워크 대역폭(Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_DC2s | 2 | 8  | 100 | 2 | 4000 / 32(43) | 3200 /48 | 2 / 1500 |
| Standard_DC4s | 4 | 16 | 200 | 4 | 8000 / 64 (86) | 6400 /96 | 2 / 3000 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>기타 크기

- [범용](sizes-general.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)

## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.