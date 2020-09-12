---
title: HC 시리즈-Azure Virtual Machines
description: HC 시리즈 Vm에 대 한 사양입니다.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: bb76dbf975ce15f72d3ad339853c407de42cf507
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89594393"
---
# <a name="hc-series"></a>HC 시리즈

HC 시리즈 Vm은 암시적 유한 요소 분석, 분자 dynamics 및 계산 연금술과 같이 조밀한 계산으로 구동 되는 응용 프로그램에 최적화 되어 있습니다. HC Vm 기능 44 Intel Xeon 플래티넘 8168 프로세서 코어, CPU 코어 당 8gb RAM, 하이퍼스레딩을 없음 Intel Xeon Platinum 플랫폼은 intel 수학 커널 라이브러리 및 AVX-512와 같은 고급 벡터 처리 기능과 같은 다양 한 소프트웨어 도구 에코 시스템을 지원 합니다.

HC 시리즈 Vm 기능 100 g b/초 Mellanox EDR InfiniBand. 이러한 Vm은 최적화 되 고 일관 된 RDMA 성능을 위해 차단 되지 않는 fat 트리에 연결 됩니다. 이러한 Vm은 적응 라우팅 및 DCT (표준 RC 및 UD 전송에 대 한 추가)를 지원 합니다. 이러한 기능은 응용 프로그램의 성능, 확장성 및 일관성을 향상 시키고 이러한 기능을 사용 하는 것이 좋습니다.

ACU: 297-315

Premium Storage: 지원됨

Premium Storage 캐싱: 지원됨

실시간 마이그레이션: 지원되지 않음

메모리 보존 업데이트: 지원되지 않음

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

- [Hpc 워크 로드](./workloads/hpc/overview.md)에서 [vm을 구성](./workloads/hpc/configure.md)하 고, [InfiniBand를 사용 하도록](./workloads/hpc/enable-infiniband.md)설정 하 고, [MPI를 설정](./workloads/hpc/setup-mpi.md)하 고, Azure 용 hpc 응용 프로그램을 최적화 하는 방법을 알아보세요
- [Azure Compute 기술 커뮤니티 블로그](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)에서 최신 공지 사항과 HPC 예제 및 결과를 읽어 보세요.
- 실행 중인 HPC 워크 로드에 대 한 개략적인 아키텍처 보기는 [Azure의 hpc (고성능 컴퓨팅)](/azure/architecture/topics/high-performance-computing/)를 참조 하세요.
- [ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
