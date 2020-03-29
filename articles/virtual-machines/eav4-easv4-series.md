---
title: Eav4 시리즈 및 Easv4 시리즈 - Azure 가상 컴퓨터
description: Eav4 및 Easv4 시리즈 VM용 사양입니다.
services: virtual-machines
author: migerdes
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: e05a2b97a25ce4cab7d619f4b62d405765cb08db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164392"
---
# <a name="eav4-and-easv4-series"></a>Eav4 및 Easv4 시리즈

Eav4 시리즈와 Easv4 시리즈는 최대 256MB L3 캐시를 갖춘 다중 스레드 구성에서 AMD의 2.35Ghz EPYC<sup>TM</sup> 7452 프로세서를 활용하여 대부분의 메모리 최적화 워크로드를 실행할 수 있는 옵션을 증가시가지 않습니다. Eav4 시리즈와 Easv4 시리즈는 Ev3 & Esv3 시리즈와 동일한 메모리 및 디스크 구성을 갖습니다.

## <a name="eav4-series"></a>Eav4 시리즈

ACU: 230 - 260

프리미엄 스토리지: 지원되지 않음

프리미엄 스토리지 캐싱: 지원되지 않음

라이브 마이그레이션: 지원됨

메모리 보존 업데이트: 지원

Eav4 계열 크기는 2.35Ghz AMD EPYC<sup>TM</sup> 7452 프로세서를 기반으로 하며, 최대 주파수 3.35GHz를 달성하고 프리미엄 SSD를 사용할 수 있습니다. Eav4 계열 크기는 메모리 집약적인 엔터프라이즈 애플리케이션에 이상적입니다. 데이터 디스크 스토리지는 가상 머신과 별도로 비용이 청구됩니다. 프리미엄 SSD를 사용하려면 Easv4 시리즈 크기를 사용하십시오. Easv4 크기의 가격 및 청구 미터는 Eav3 시리즈와 동일합니다.

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 임시 스토리지 처리량: IOPS/읽기 MBps/쓰기 MBps | 최대 NIC / 예상 네트워크 대역폭(MBps) |
| -----|-----|-----|-----|-----|-----|-----|
| 표준\_E2a\_v4|2|16|50|4|3000 / 46 / 23|2 / 1000 |
| 표준\_E4a\_v4|4|32|100|8|6000 / 93 / 46|2 / 2000 |
| 표준\_E8a\_v4|8|64|200|16|12000 / 187 / 93|4 / 4000 |
| 표준\_E16a\_v4|16|128|400|32|24000 / 375 / 187|8 / 8000 |
| 표준\_E20a\_v4|20|160|500|32|30000 / 468 / 234|8 / 10000 |
| 표준\_E32a\_v4|32|256|800|32|48000 / 750 / 375|8 / 16000 |
| 표준\_E48a\_v4<sup>**</sup> |48|384|1200|32| | |
| 표준\_E64a\_v4<sup>**</sup> |64|512|1600|32| | |
| 표준\_E96a\_v4<sup>**</sup> |96|672|2400|32| | |

<sup>**</sup>이러한 크기는 미리 보기로 표시됩니다. 이러한 더 큰 크기를 시도하는 데 관심이 있다면 [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview)에 등록하십시오.

## <a name="easv4-series"></a>Easv4 시리즈

ACU: 230 - 260

Premium Storage: 지원됨

프리미엄 스토리지 캐싱: 지원

라이브 마이그레이션: 지원됨

메모리 보존 업데이트: 지원

Easv4 계열 크기는 2.35Ghz AMD EPYC<sup>TM</sup> 7452 프로세서를 기반으로 하며, 최대 주파수 3.35GHz를 달성하고 프리미엄 SSD를 사용할 수 있습니다. Easv4 계열 크기는 메모리 집약적인 엔터프라이즈 애플리케이션에 이상적입니다.

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시된 임시 스토리지 처리량: IOPS/MBps(GiB 단위의 캐시 크기) | 최대 캐시되지 않은 디스크 처리량: IOPS/MBps | 최대 NIC / 예상 네트워크 대역폭(MBps) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_E2as_v4|2|16|32|4|4000 / 32 (50)|3200 / 48|2 / 1000 |
| Standard_E4as_v4|4|32|64|8|8000 / 64 (100)|6400 / 96|2 / 2000 |
| Standard_E8as_v4|8|64|128|16|16000 / 128 (200)|12800 / 192|4 / 4000 |
| Standard_E16as_v4|16|128|256|32|32000 / 255 (400)|25600 / 384|8 / 8000 |
| Standard_E20as_v4|20|160|320|32|40000 / 320 (500)|32000 / 480|8 / 10000 |
| Standard_E32as_v4|32|256|512|32|64000 / 510 (800)|51200 / 768|8 / 16000 |
| Standard_E48as_v4<sup>**</sup> |48|384|768|32|  | |
| Standard_E64as_v4<sup>**</sup> |64|512|1024|32| | |
| Standard_E96as_v4<sup>**</sup> |96|672|1344|32| | |  

<sup>**</sup>이러한 크기는 미리 보기로 표시됩니다. 이러한 더 큰 크기를 시도하는 데 관심이 있다면 [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview)에 등록하십시오.

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
