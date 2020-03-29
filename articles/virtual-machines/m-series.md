---
title: M 시리즈 - Azure 가상 시스템
description: M 시리즈 VM에 대한 사양.
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: article
ms.date: 02/05/2019
ms.author: lahugh
ms.openlocfilehash: e8952218675493710cbdd1f92661e9a9c077f34c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164052"
---
# <a name="m-series"></a>M 시리즈

M 시리즈는 vCPU 수(최대 128vCPU)와 다량의 메모리(최대 3.8TiB)를 제공합니다. 또한 vCPU 수가 많고 메모리가 많은 매우 큰 데이터베이스 나 다른 응용 프로그램에도 적합합니다. M 시리즈 크기는 인텔® 제온® CPU E7-8890 v3 @ 2.50GHz를 기반으로합니다.

M 시리즈 VM의 기능 인텔® 하이퍼 스레딩 기술

ACU: 160-180

Premium Storage: 지원됨

프리미엄 스토리지 캐싱: 지원

라이브 마이그레이션: 지원되지 않음

업데이트 메모리 보존: 지원되지 않음

Write Accelerator: [지원됨](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시 및 임시 저장 처리량: IOPS/MBps(GiB의 캐시 크기) | 최대 캐시되지 않은 디스크 처리량: IOPS/MBps | 최대 NIC/예상 네트워크 대역폭(Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_M8ms <sup>2</sup>       | 8   | 218.75 | 256   | 8  | 10000/100 (793)     | 5000/125   | 4/2000  |
| Standard_M16ms <sup>2</sup>      | 16  | 437.5  | 512   | 16 | 20000/200 (1587)    | 10000/250  | 8/4000  |
| Standard_M32ts                   | 32  | 192    | 1024  | 32 | 40000/400 (3174)    | 20000/500  | 8/8000  |
| Standard_M32ls                   | 32  | 256    | 1024  | 32 | 40000/400 (3174)    | 20000/500  | 8/8000  |
| Standard_M32ms <sup>2</sup>      | 32  | 875    | 1024  | 32 | 40000/400 (3174)    | 20000/500  | 8/8000  |
| Standard_M64s                    | 64  | 1024   | 2048  | 64 | 80000/800 (6348)    | 40000/1000 | 8/16000 |
| Standard_M64ls                   | 64  | 512    | 2048  | 64 | 80000/800 (6348)    | 40000/1000 | 8/16000 |
| Standard_M64ms <sup>3</sup>      | 64  | 1792   | 2048  | 64 | 80000/800 (6348)    | 40000/1000 | 8/16000 |
| Standard_M128s <sup>1</sup>      | 128 | 2048   | 4096  | 64 | 160000/1600 (12696) | 80000/2000 | 8/30000 |
| Standard_M128ms <sup>1,2,3</sup> | 128 | 3892   | 4096  | 64 | 160000/1600 (12696) | 80000/2000 | 8/30000 |
| Standard_M64                     | 64  | 1024   | 7168  | 64 | 80000/800 (1228)    | 40000/1000 | 8/16000 |
| Standard_M64m                    | 64  | 1792   | 7168  | 64 | 80000/800 (1228)    | 40000/1000 | 8/16000 |
| Standard_M128 <sup>1</sup>       | 128 | 2048   | 14336 | 64 | 250000/1600 (2456)  | 80000/2000 | 8/32000 |
| Standard_M128m <sup>1</sup>      | 128 | 3892   | 14336 | 64 | 250000/1600 (2456)  | 80000/2000 | 8/32000 |

<sup>1</sup> 64 vCPU 는 이러한 지원 되는 게스트 OS 중 하나를 필요: 윈도우 서버 2016, 우분투 16.04 LTS, SLES 12 SP2, 레드 햇 엔터프라이즈 리눅스, CentOS 7.3 또는 오라클 리눅스 7.3 LIS 4.2.1.

<sup>2</sup> 제한된 코어 크기를 사용할 수 있습니다.

<sup>3</sup> 인스턴스는 단일 고객 전용의 하드웨어에 격리되어 있습니다.

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
