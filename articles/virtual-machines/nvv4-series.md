---
title: NVv4 시리즈 - Azure 가상 시스템
description: NVv4 시리즈 VM용 사양.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 0295ed7d44d64fcc1aeb68e1beaa37987b177edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273855"
---
# <a name="nvv4-series"></a>NVv4 시리즈 

NVv4 시리즈 가상 머신은 [AMD 라데온 본능 MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) GPU와 AMD EPYC 7V12(로마) CPU로 구동됩니다. NVv4 시리즈 Azure는 부분 GPU가 있는 가상 컴퓨터를 도입하고 있습니다. 2GiB 프레임 버퍼가 있는 GPU의 1/8부터 시작하여 16GiB 프레임 버퍼가 있는 전체 GPU에 GPU 가속 그래픽 응용 프로그램 및 가상 데스크톱에 적합한 크기의 가상 머신을 선택합니다. NVv4 가상 머신은 현재 Windows 게스트 운영 체제만 지원합니다.

<br>

Premium Storage: 지원됨

프리미엄 스토리지 캐싱: 지원

라이브 마이그레이션: 지원되지 않음

업데이트 메모리 보존: 지원되지 않음

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | GPU | GPU 메모리: GiB | 최대 데이터 디스크 수 | 최대 NIC 수 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8 |
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8 |

<sup>1</sup> NVv4 시리즈 VM은 AMD 동시 멀티스레딩 기술을 갖추고 있습니다.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>지원되는 운영 체제 및 드라이버

Windows를 실행하는 Azure NVv4 시리즈 VM의 GPU 기능을 활용하려면 AMD GPU 드라이버를 설치해야 합니다.

AMD GPU 드라이버를 수동으로 설치하려면 지원되는 운영 체제, 드라이버, 설치 및 확인 단계에 [대한 Windows용 N 시리즈 AMD GPU 드라이버 설정을](./windows/n-series-amd-driver-setup.md) 참조하십시오.

## <a name="other-sizes"></a>기타 크기

- [범용](sizes-general.md)
- [메모리 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)

## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
