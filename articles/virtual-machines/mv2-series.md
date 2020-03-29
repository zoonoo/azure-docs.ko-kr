---
title: Mv2 시리즈 - Azure 가상 시스템
description: Mv2 시리즈 VM용 사양입니다.
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 6fb0f93d3ac124b21bbc52ddc57bc720de6406e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78163899"
---
# <a name="mv2-series"></a>Mv2 시리즈

Mv2 시리즈는 하이퍼 스레드 인텔® 제온® 플래티넘 8180M 2.5GHz(스카이레이크) 프로세서에서 실행되는 높은 처리량, 낮은 대기 시간 플랫폼을 특징으로 하며, 2.5GHz의 모든 코어 기본 주파수와 3.8GHz의 최대 터보 주파수를 제공합니다. 모든 Mv2 시리즈 가상 머신 크기는 표준 및 프리미엄 영구 디스크를 모두 사용할 수 있습니다. Mv2 시리즈 인스턴스는 메모리에 최적화된 VM 크기로, 관계형 데이터베이스 서버, 대용량 캐시 및 인메모리에 이상적인 높은 메모리 대 CPU 비율로 대규모 메모리 데이터베이스 및 워크로드를 지원하는 탁월한 컴퓨팅 성능을 제공합니다. 분석.

Mv2 시리즈 VM의 기능 인텔® 하이퍼 스레딩 기술

Premium Storage: 지원됨

프리미엄 스토리지 캐싱: 지원

라이브 마이그레이션: 지원되지 않음

업데이트 메모리 보존: 지원되지 않음

액셀러레이터 쓰기: [지원](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

|크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시된 임시 스토리지 처리량: IOPS/MBps(GiB 단위의 캐시 크기) | 최대 캐시되지 않은 디스크 처리량: IOPS/MBps | 최대 NIC 수 / 예상 네트워크 대역폭(Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_M208ms_v2<sup>1</sup> | 208 | 5700 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 / 16000 |
| Standard_M208s_v2<sup>1</sup> | 208 | 2850 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 / 16000 |
| Standard_M416ms_v2<sup>1, 2</sup> | 416 | 11400 | 8192 | 64 | 250000 / 1600 (14080) | 80000 / 2000 | 8/32000 |
| Standard_M416s_v2<sup>1, 2</sup> | 416 | 5700 | 8192 | 64 | 250000 / 1600 (14080) | 80000 / 2000 | 8/32000 |

<sup>1Mv2</sup> 시리즈 VM은 2세대에 불과합니다. Linux를 사용하는 경우 [Azure의 2세대 VM 지원에서](./linux/generation-2.md) 이미지를 찾고 선택하는 방법에 대한 지침을 참조하세요.

<sup>2</sup> M416ms_v2 및 M416s_v2 크기의 경우 다음 이미지에 대해서만 초기 지원이 있습니다: "GEN2: SLES: SLES SLES) SAP 애플리케이션용 SP4 12 SP4."

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
