---
title: Dv3 및 Dsv3 시리즈 - Azure 가상 컴퓨터
description: Dv3 및 Dsv3 시리즈 VM용 사양입니다.
services: virtual-machines
author: joelpelley
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 9ea5811fa20ea4866655de74d79ff3905ba03f16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164409"
---
# <a name="dv3-and-dsv3-series"></a>Dv3 및 Dsv3 시리즈

Dv3 시리즈는 인텔® 제온® 8171M 2.1GHz (스카이 레이크), 인텔® 제온® E5-2673 v4 2.3 GHz (브로드 웰) 또는 인텔® 제온® E5-2673 v3 2.4 GHz (하스웰) 프로세서에서 실행됩니다. 목적 워크로드. 하이퍼 스레딩으로 이동하기 위해 디스크 및 네트워크 제한이 코어 단위로 조정되는 동안 메모리가 확장되었습니다(~ 3.5GiB/vCPU에서 4GiB/vCPU로). Dv3 시리즈는 더 이상 D/Dv2 시리즈의 높은 메모리 VM 크기를 가지고 있지 않으며, [Ev3 및 Esv3 시리즈에](ev3-esv3-series.md)최적화된 메모리로 이동되었습니다.

D 시리즈 사용 사례에는 엔터프라이즈급 응용 프로그램, 관계형 데이터베이스, 메모리 내 캐싱 및 분석이 포함됩니다.

## <a name="dv3-series"></a>Dv3 시리즈

Dv3 시리즈 크기는 인텔® 제온® 8171M 2.1GHz (스카이 레이크), 인텔® 제온® E5-2673 v4 2.3 GHz (브로드 웰), 또는 인텔® 제온® E5-2673 v3 2.4 GHz의 (하스웰) 프로세서 인텔 터보 부스트 기술 2.0. Dv3 시리즈 크기는 대부분의 프로덕션 워크로드에 적합한 vCPU, 메모리 및 임시 스토리지의 조합을 제공합니다.

데이터 디스크 스토리지는 가상 머신과 별도로 비용이 청구됩니다. Premium Storage 디스크를 사용하려면 Dsv3 크기를 사용합니다. Dsv3 크기의 가격 및 요금 청구 기준은 Dv3 시리즈와 동일합니다.

Dv3 시리즈 VM은 인텔® 하이퍼 스레딩 기술을 갖추고 있습니다.

ACU: 160-190

Premium Storage: 지원되지 않음

프리미엄 스토리지 캐싱: 지원되지 않음

라이브 마이그레이션: 지원됨

메모리 보존 업데이트: 지원

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 온도 저장 처리량: IOPS/읽기 MBps/쓰기 MBps | 최대 NIC/네트워크 대역폭 |
|---|---|---|---|---|---|---|
| Standard_D2_v3  | 2  | 8   | 50   | 4  | 3000/46/23     | 2/1000  |
| Standard_D4_v3  | 4  | 16  | 100  | 8  | 6000/93/46     | 2/2000  |
| Standard_D8_v3  | 8  | 32  | 200  | 16 | 12000/187/93   | 4/4000  |
| Standard_D16_v3 | 16 | 64  | 400  | 32 | 24000/375/187  | 8/8000  |
| Standard_D32_v3 | 32 | 128 | 800  | 32 | 48000/750/375  | 8/16000 |
| Standard_D48_v3 | 48 | 192 | 1200 | 32 | 96000/1000/500 | 8/24000 |
| Standard_D64_v3 | 64 | 256 | 1600 | 32 | 96000/1000/500 | 8/30000 |

## <a name="dsv3-series"></a>Dsv3 시리즈

Dsv3 시리즈 크기는 인텔® 제온® 8171M 2.1GHz (스카이 레이크), 인텔® 제온® E5-2673 v4 2.3 GHz (브로드 웰), 또는 인텔® 제온® E5-2673 v3 2.4 GHz (하스웰) 프로세서인텔 터보 부스트 기술 2.0 프리미엄. Dsv3 시리즈 크기는 대부분의 프로덕션 워크로드에 적합한 vCPU, 메모리 및 임시 스토리지의 조합을 제공합니다.

Dsv3 시리즈 VM은 인텔® 하이퍼 스레딩 기술을 갖추고 있습니다.

ACU: 160-190

Premium Storage: 지원됨

프리미엄 스토리지 캐싱: 지원

라이브 마이그레이션: 지원됨

메모리 보존 업데이트: 지원

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시 및 임시 저장 처리량: IOPS/MBps(GiB의 캐시 크기) | 최대 캐시되지 않은 디스크 처리량: IOPS/MBps | 최대 NIC/예상 네트워크 대역폭(Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D2s_v3  | 2  | 8   | 16  | 4  | 4000/32 (50)       | 3200/48    | 2/1000  |
| Standard_D4s_v3  | 4  | 16  | 32  | 8  | 8000/64 (100)      | 6400/96    | 2/2000  |
| Standard_D8s_v3  | 8  | 32  | 64  | 16 | 16000/128 (200)    | 12800/192  | 4/4000  |
| Standard_D16s_v3 | 16 | 64  | 128 | 32 | 32000/256 (400)    | 25600/384  | 8/8000  |
| Standard_D32s_v3 | 32 | 128 | 256 | 32 | 64000/512 (800)    | 51200/768  | 8/16000 |
| Standard_D48s_v3 | 48 | 192 | 384 | 32 | 96000/768 (1200)   | 76800/1152 | 8/24000 |
| Standard_D64s_v3 | 64 | 256 | 512 | 32 | 128000/1024 (1600) | 80000/1200 | 8/30000 |

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
