---
title: Av2 시리즈 - Azure 가상 시스템
description: Av2 시리즈 VM용 사양입니다.
services: virtual-machines
author: migerdes
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: fe27a1cc78d1f37d535f364c03803a5196090a3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78163950"
---
# <a name="av2-series"></a>Av2 시리즈

다양한 하드웨어 유형 및 프로세서에 Av2 시리즈 VM을 배포할 수 있습니다. Av2 시리즈 VM에는 CPU 성능과 메모리 구성이 개발 및 테스트와 같은 엔트리 레벨 워크로드에 가장 적합합니다. 크기는 배포된 하드웨어에 관계없이 실행 중인 인스턴스에 대해 일관된 프로세서 성능을 제공하기 위해 제한됩니다. 이 크기가 배포되는 실제 하드웨어를 확인하려면 Virtual Machine 내에서 가상 하드웨어를 쿼리합니다. 몇 가지 예의 사용 사례로는 개발 및 테스트 서버, 트래픽이 적은 웹 서버, 중소 규모 데이터베이스, 개념 증명 및 코드 리포지토리가 있습니다.

ACU: 100

Premium Storage: 지원되지 않음

프리미엄 스토리지 캐싱: 지원되지 않음

라이브 마이그레이션: 지원됨

메모리 보존 업데이트: 지원

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 온도 저장 처리량: IOPS/읽기 MBps/쓰기 MBps | 최대 데이터 디스크/처리량: IOPS | 최대 NIC/예상 네트워크 대역폭(Mbps) |
|---|---|---|---|---|---|---|
| Standard_A1_v2  | 1 | 2  | 10 | 1000/20/10  | 2/2x500   | 2/250  |
| Standard_A2_v2  | 2 | 4  | 20 | 2000/40/20  | 4/4x500   | 2/500  |
| Standard_A4_v2  | 4 | 8  | 40 | 4000/80/40  | 8/8x500   | 4/1000 |
| Standard_A8_v2  | 8 | 16 | 80 | 8000/160/80 | 16/16x500 | 8/2000 |
| Standard_A2m_v2 | 2 | 16 | 20 | 2000/40/20  | 4/4x500   | 2/500  |
| Standard_A4m_v2 | 4 | 32 | 40 | 4000/80/40  | 8/8x500   | 4/1000 |
| Standard_A8m_v2 | 8 | 64 | 80 | 8000/160/80 | 16/16x500 | 8/2000 |

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
