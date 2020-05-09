---
title: M 시리즈-Azure Virtual Machines
description: M 시리즈 Vm에 대 한 사양입니다.
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: article
ms.date: 03/31/2020
ms.author: jushiman
ms.openlocfilehash: 7b84537693b3ffc3f7398c6eb84615a96bf8e7df
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744695"
---
# <a name="m-series"></a>M 시리즈

M 시리즈는 높은 vCPU 수 (최대 128 Vcpu) 및 많은 양의 메모리 (최대 3.8 TiB)를 제공 합니다. 매우 큰 데이터베이스 또는 높은 vCPU 수 및 많은 양의 메모리를 활용 하는 다른 응용 프로그램에도 적합 합니다. M 시리즈 크기&reg; 는 intel Xeon&reg; CPU E7-8890 v3 @ 2.50 ghz 및 Intel&reg; Xeon&reg; Platinum 8280m (Cascade Lake)에서 모두 지원 됩니다.

M 시리즈 VM의 기능 Intel&reg; 하이퍼 스레딩 기술입니다.

ACU: 160-180

Premium Storage: 지원됨

Premium Storage 캐싱: 지원 됨

실시간 마이그레이션: 지원 되지 않음

메모리 보존 업데이트: 지원 되지 않음

Write Accelerator: [지원됨](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

| Size | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시 및 임시 저장소 처리량: IOPS/MBps (GiB의 캐시 크기) | 최대 캐시 되지 않은 디스크 처리량: IOPS/MBps | 최대 Nic/예상 네트워크 대역폭 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_M8ms                    | 8   | 218.75 | 256   | 8  | 10000/100 (793)     | 5000/125   | 4/2000  |
| Standard_M16ms                   | 16  | 437.5  | 512   | 16 | 20000/200 (1587)    | 10000/250  | 8/4000  |
| Standard_M32ts                   | 32  | 192    | 1024  | 32 | 40000/400 (3174)    | 20000/500  | 8/8000  |
| Standard_M32ls                   | 32  | 256    | 1024  | 32 | 40000/400 (3174)    | 20000/500  | 8/8000  |
| Standard_M32ms                   | 32  | 875    | 1024  | 32 | 40000/400 (3174)    | 20000/500  | 8/8000  |
| Standard_M64s <sup>1</sup>       | 64  | 1024   | 2048  | 64 | 80000/800 (6348)    | 40000/1000 | 8/16000 |
| Standard_M64ls <sup>1</sup>      | 64  | 512    | 2048  | 64 | 80000/800 (6348)    | 40000/1000 | 8/16000 |
| Standard_M64ms <sup>1</sup>      | 64  | 1792   | 2048  | 64 | 80000/800 (6348)    | 40000/1000 | 8/16000 |
| Standard_M128s <sup>1</sup>    | 128 | 2048   | 4096  | 64 | 160000/1600 (12696) | 80000/2000 | 8/30000 |
| Standard_M128ms <sup>1, 2</sup>   | 128 | 3892   | 4096  | 64 | 160000/1600 (12696) | 80000/2000 | 8/30000 |
| Standard_M64 <sup>1</sup>        | 64  | 1024   | 7168  | 64 | 80000/800 (1228)    | 40000/1000 | 8/16000 |
| Standard_M64m <sup>1</sup>       | 64  | 1792   | 7168  | 64 | 80000/800 (1228)    | 40000/1000 | 8/16000 |
| Standard_M128 <sup>1</sup>     | 128 | 2048   | 14336 | 64 | 250000/1600 (2456)  | 80000/2000 | 8/32000 |
| Standard_M128m <sup>1</sup>    | 128 | 3892   | 14336 | 64 | 250000/1600 (2456)  | 80000/2000 | 8/32000 |

<sup>1</sup> 64 이상의 vcpu에는 Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2, Red Hat Enterprise Linux, CentOS 7.3 또는 Oracle Linux 7.3, LIS 4.2.1와 같은 지원 되는 게스트 os 중 하나가 필요 합니다.

<sup>2</sup> 인스턴스는 단일 고객 전용의 하드웨어에 격리되어 있습니다.

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
