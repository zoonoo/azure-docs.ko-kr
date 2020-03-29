---
title: HC 시리즈 - Azure 가상 시스템
description: HC 시리즈 VM에 대한 사양.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: cc25fb9b21d535ef07bcfae673be48216427b370
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164783"
---
# <a name="hc-series"></a>HC 시리즈

HC 계열 VM은 암시적 유한 요소 분석, 분자 역학 및 계산 화학과 같은 고밀도 계산에 의해 구동되는 애플리케이션에 최적화되어 있습니다. HC VM은 44개의 인텔 제온 플래티넘 8168 프로세서 코어, CPU 코어당 8GB의 RAM, 하이퍼스레딩이 없습니다. 인텔 제온 플래티넘 플랫폼은 인텔 수학 커널 라이브러리와 같은 소프트웨어 도구의 풍부한 에코시스템을 지원합니다.

ACU: 297-315

Premium Storage: 지원됨

Premium Storage 캐싱: 지원됨

라이브 마이그레이션: 지원되지 않음

업데이트 메모리 보존: 지원되지 않음

| 크기 | vCPU | 프로세서 | 메모리(GB) | 메모리 대역폭 GB/s | 기본 CPU 주파수(GHz) | 모든 코어 주파수(GHz, 피크) | 단일 코어 주파수(GHz, 피크) | RDMA 성능(Gb/s) | MPI 지원 | 임시 저장 장치(GB) | 최대 데이터 디스크 수 | 맥스 이더넷 NIC |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | 인텔 제온 플래티넘 8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | 모두 | 700 | 4 | 1 |

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