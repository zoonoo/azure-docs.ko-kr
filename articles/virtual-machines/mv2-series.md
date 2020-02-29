---
title: Mv2 시리즈-Azure Virtual Machines
description: Mv2 시리즈 Vm에 대 한 사양입니다.
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 6fb0f93d3ac124b21bbc52ddc57bc720de6406e6
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2020
ms.locfileid: "78163899"
---
# <a name="mv2-series"></a>Mv2 시리즈

Mv2 시리즈는 2.5 GHz의 모든 코어 기본 주파수와 3.8 g h z의 최대 터보 주파수를 사용 하 여 하이퍼 스레드 Intel® Xeon® 플래티넘 8180M 2.5 g h z (Skylake) 프로세서에서 실행 되는 높은 처리량, 짧은 대기 시간 플랫폼을 갖추고 있습니다. 모든 Mv2 시리즈 가상 머신 크기는 standard 및 premium 영구 디스크를 모두 사용할 수 있습니다. Mv2 시리즈 인스턴스는 메모리 내 데이터베이스 및 워크 로드를 지원 하기 위해 뛰어난 계산 성능을 제공 하는 메모리 액세스에 최적화 된 VM 크기 이며, 관계형 데이터베이스 서버, 큰 캐시 및 메모리 내에서 가장 적합 한 메모리 대 CPU 비율을 제공 합니다. analytics.

Mv2 시리즈 VM의 기능 Intel® 하이퍼 스레딩 기술

Premium Storage: 지원됨

Premium Storage 캐싱: 지원 됨

실시간 마이그레이션: 지원 되지 않음

메모리 보존 업데이트: 지원 되지 않음

쓰기 가속기: [지원 됨](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

|크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시된 임시 스토리지 처리량: IOPS/MBps(GiB 단위의 캐시 크기) | 최대 캐시되지 않은 디스크 처리량: IOPS/MBps | 최대 NIC 수 / 예상 네트워크 대역폭(Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_M208ms_v2<sup>1</sup> | 208 | 5700 | 4096 | 64 | 8만/800 (7040) | 4만/1000 | 8 / 16000 |
| Standard_M208s_v2<sup>1</sup> | 208 | 2850 | 4096 | 64 | 8만/800 (7040) | 4만/1000 | 8 / 16000 |
| Standard_M416ms_v2<sup>1, 2</sup> | 416 | 11400 | 8192 | 64 | 25만/1600 (14080) | 8만/2000 | 8/32000 |
| Standard_M416s_v2<sup>1, 2</sup> | 416 | 5700 | 8192 | 64 | 25만/1600 (14080) | 8만/2000 | 8/32000 |

<sup>1</sup> Mv2 시리즈 vm은 2 세대 전용입니다. Linux를 사용 하는 경우 이미지를 찾고 선택 하는 방법에 대 한 지침은 [Azure의 2 세대 vm에 대 한 지원](./linux/generation-2.md) 을 참조 하세요.

<sup>2</sup> M416ms_v2 및 M416s_v2 크기에 대해서는 다음 이미지만 지원 됩니다. "GEN2: SUSE LINUX ENTERPRISE SERVER (SLES) 12 SP4 For SAP Applications"를 참조 하십시오.

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
