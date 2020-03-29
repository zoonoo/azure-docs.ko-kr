---
title: HBv2 시리즈 - Azure 가상 시스템
description: HBv2 시리즈 VM용 사양.
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: amverma
ms.openlocfilehash: eea649610ca53ccbb98b5ca361555280dcd3dafe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164800"
---
# <a name="hbv2-series"></a>HBv2 시리즈

HBv2 계열 VM은 유체 역학, 유한 요소 분석 및 저장소 시뮬레이션과 같은 메모리 대역폭에 의해 구동되는 애플리케이션에 최적화되어 있습니다. HBv2 VM은 120 AMD EPYC 7742 프로세서 코어, CPU 코어당 4GB의 RAM, 동시 멀티스레딩이 없습니다. 각 HBv2 VM은 최대 340GB/sec의 메모리 대역폭과 최대 4테라플롭스의 FP64 컴퓨팅을 제공합니다.

Premium Storage: 지원됨

라이브 마이그레이션: 지원되지 않음

업데이트 메모리 보존: 지원되지 않음

| 크기 | vCPU | 프로세서 | 메모리(GB) | 메모리 대역폭 GB/s | 기본 CPU 주파수(GHz) | 모든 코어 주파수(GHz, 피크) | 단일 코어 주파수(GHz, 피크) | RDMA 성능(Gb/s) | MPI 지원 | 임시 저장 장치(GB) | 최대 데이터 디스크 수 | 맥스 이더넷 NIC |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs_v2 | 120 | AMD EPYC 7V12 | 480 | 350 | 2.45 | 3.1 | 3.3 | 200 | 모두 | 480 + 960 | 8 | 1 |


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>기타 크기

- [범용](sizes-general.md)
- [메모리 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)

## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.