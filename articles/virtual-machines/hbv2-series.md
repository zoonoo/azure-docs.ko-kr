---
title: HBv2 시리즈-Azure Virtual Machines
description: HBv2 시리즈 Vm에 대 한 사양입니다.
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: amverma
ms.openlocfilehash: eea649610ca53ccbb98b5ca361555280dcd3dafe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "78164800"
---
# <a name="hbv2-series"></a>HBv2 시리즈

HBv2 시리즈 Vm은 유체 dynamics, 유한 요소 분석 및 저수지 시뮬레이션 등 메모리 대역폭을 기반으로 하는 응용 프로그램에 최적화 되어 있습니다. HBv2 Vm 기능 120 AMD EPYC 7742 프로세서 코어, CPU 코어 당 4gb RAM 및 동시 다중 스레딩은 없습니다. 각 HBv2 VM은 최대 340 g b/초 메모리 대역폭을 제공 하 고 최대 4 테라플롭 FP64 compute를 제공 합니다.

Premium Storage: 지원됨

실시간 마이그레이션: 지원 되지 않음

메모리 보존 업데이트: 지원 되지 않음

| Size | vCPU | 프로세서 | 메모리(GB) | 메모리 대역폭 (GB/초) | 기본 CPU 빈도 (GHz) | 모든 코어 빈도 (GHz, 최고) | 단일 코어 빈도 (GHz, 최고) | RDMA 성능 (Gb/s) | MPI 지원 | 임시 저장소 (GB) | 최대 데이터 디스크 수 | 최대 이더넷 Nic |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs_v2 | 120 | AMD EPYC 7V12 | 480 | 350 | 2.45 | 3.1 | 3.3 | 200 | 모두 | 480 + 960 | 8 | 1 |


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