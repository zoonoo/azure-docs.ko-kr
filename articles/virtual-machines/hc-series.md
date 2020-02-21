---
title: HC 시리즈-Azure Virtual Machines
description: HC 시리즈 Vm에 대 한 사양입니다.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 10c26c738fe51f6e557491475afd0545d3f04ab3
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493229"
---
# <a name="hc-series"></a>HC 시리즈

HC 시리즈 Vm은 암시적 유한 요소 분석, 분자 dynamics 및 계산 연금술과 같이 조밀한 계산으로 구동 되는 응용 프로그램에 최적화 되어 있습니다. HC Vm 기능 44 Intel Xeon 플래티넘 8168 프로세서 코어, CPU 코어 당 8gb RAM, 하이퍼스레딩을 없음 Intel Xeon Platinum 플랫폼은 intel 수학 커널 라이브러리와 같은 소프트웨어 도구에 대 한 다양 한 소프트웨어 에코 시스템을 지원 합니다.

ACU: 297-315

Premium Storage: 지원됨

Premium Storage 캐싱: 지원됨

| 크기 | vCPU | 프로세서 | 메모리(GB) | 메모리 대역폭 (GB/초) | 기본 CPU 빈도 (GHz) | 모든 코어 빈도 (GHz, 최고) | 단일 코어 빈도 (GHz, 최고) | RDMA 성능 (Gb/s) | MPI 지원 | 임시 저장소 (GB) | 최대 데이터 디스크 수 | 최대 이더넷 Nic |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon 플래티넘 8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | 모두 | 700 | 4 | 1 |

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