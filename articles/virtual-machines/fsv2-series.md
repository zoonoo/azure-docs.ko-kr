---
title: Fsv2 시리즈 - Azure 가상 시스템
description: Fsv2 시리즈 VM에 대한 사양.
services: virtual-machines
author: brbell
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: f28c6b61aee3c8cbc078db1c2cfb48ed1fba4554
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164851"
---
# <a name="fsv2-series"></a>Fsv2 시리즈

Fsv2 시리즈는 인텔® 제온® 플래티넘 8168 프로세서를 기반으로 합니다. 3.4GHz의 모든 코어 터보 클럭 속도와 3.7GHz의 최대 단일 코어 터보 주파수를 특징으로 합니다. 인텔® AVX-512 지침은 인텔 확장 형 프로세서에 새로운. 이 지침은 단일 및 이중 정밀 부동 점 작업 모두에서 벡터 처리 워크로드에 최대 2배의 성능 향상을 제공합니다. 즉, 모든 계산 워크로드에 대해 매우 빠릅니다.

Fsv2 시리즈 VM은 인텔® 하이퍼 스레딩 기술을 갖추고 있습니다.

ACU: 195~210

Premium Storage: 지원됨

프리미엄 스토리지 캐싱: 지원

라이브 마이그레이션: 지원됨

메모리 보존 업데이트: 지원

| 크기 | vCPU 수 | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시 및 임시 저장 처리량: IOPS/MBps(GiB의 캐시 크기) | 최대 캐시되지 않은 디스크 처리량: IOPS/MBps | 최대 NIC/예상 네트워크 대역폭(Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_F2s_v2  | 2  | 4   | 16  | 4  | 4000/31 (32)       | 3200/47    | 2/875   |
| Standard_F4s_v2  | 4  | 8   | 32  | 8  | 8000/63 (64)       | 6400/95    | 2/1750  |
| Standard_F8s_v2  | 8  | 16  | 64  | 16 | 16000/127 (128)    | 12800/190  | 4/3500  |
| Standard_F16s_v2 | 16 | 32  | 128 | 32 | 32000/255 (256)    | 25600/380  | 4/7000  |
| Standard_F32s_v2 | 32 | 64  | 256 | 32 | 64000/512 (512)    | 51200/750  | 8/14000 |
| Standard_F48s_v2 | 48 | 96  | 384 | 32 | 96000/768 (768)    | 76800/1100 | 8/21000 |
| Standard_F64s_v2 | 64 | 128 | 512 | 32 | 128000/1024 (1024) | 80000/1100 | 8/28000 |
| Standard_F72s_v2<sup>1, 2</sup> | 72 | 144 | 576 | 32 | 144000/1152 (1520) | 80000/1100 | 8/30000 |

<sup>1</sup> 64vCPU 이상을 사용하려면 지원되는 게스트 운영 체제 중 하나가 필요합니다.

- Windows 서버 2016 이상
- 우분투 16.04 LTS 이상, Azure 조정 커널 (4.15 커널 이상)
- SLES 12 SP2 이상
- RHEL 또는 CentOS 버전 6.7 ~ 6.10, Microsoft에서 제공하는 LIS 패키지 4.3.1(이상) 설치
- RHEL 또는 CentOS 버전 7.3, Microsoft에서 제공하는 LIS 패키지 4.2.1(이상) 설치
- RHEL 또는 CentOS 버전 7.6 이상
- UEK4 이상 오라클 리눅스
- 데비안 9 백포트 커널, 데비안 10 이상
- 4.14 커널 이상인 코어OS

<sup>2</sup> 인스턴스는 단일 고객 전용의 하드웨어에 격리되어 있습니다.

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
