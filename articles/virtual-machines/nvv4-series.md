---
title: NVv4 시리즈
description: NVv4 시리즈 Vm에 대 한 사양입니다.
services: virtual-machines
ms.subservice: sizes
author: vikancha-MSFT
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 9b841b6422a4314b43a594cb0b22040f884228eb
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87543897"
---
# <a name="nvv4-series"></a>NVv4 시리즈 

NVv4 시리즈 가상 머신은 [Amd Radeon 이러한 MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) GPU 및 AMD Epyc 7V12 (로마) cpu로 구동 됩니다. NVv4 시리즈 Azure는 부분 Gpu를 사용 하는 가상 머신을 소개 합니다. Gpu 가속 그래픽 응용 프로그램 및 가상 데스크톱에 대 한 적절 한 크기의 가상 컴퓨터를 선택 합니다. gpu의 1/8에서 시작 하는 GiB 프레임 버퍼는 16 GiB 프레임 버퍼를 사용 하는 전체 GPU입니다. NVv4 가상 머신은 현재 Windows 게스트 운영 체제만 지원 합니다.

<br>

ACU: 230-260

Premium Storage:  지원됨

Premium Storage 캐싱:  지원됨

실시간 마이그레이션: 지원되지 않음

메모리 보존 업데이트: 지원되지 않음

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | GPU | GPU 메모리: GiB | 최대 데이터 디스크 수 | 최대 NIC 수 / 예상 네트워크 대역폭(MBps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 / 1000 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4 / 2000 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8 / 4000 |
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8 / 8000 |

<sup>1</sup> NVv4 시리즈 VM 기능 AMD 동시 다중 스레딩 기술

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>지원되는 운영 체제 및 드라이버

Windows를 실행 하는 Azure NVv4 시리즈 Vm의 GPU 기능을 활용 하려면 AMD GPU 드라이버를 설치 해야 합니다.

AMD GPU 드라이버를 수동으로 설치 하려면 지원 되는 운영 체제, 드라이버, 설치 및 확인 단계에 대해 [Windows 용 N 시리즈 AMD GPU 드라이버 설치](./windows/n-series-amd-driver-setup.md) 를 참조 하세요.

## <a name="other-sizes"></a>기타 크기

- [범용](sizes-general.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)

## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
