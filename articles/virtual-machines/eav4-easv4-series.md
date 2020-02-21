---
title: Eav4 시리즈 및 Easv4-Azure Virtual Machines
description: Eav4 및 Easv4 시리즈 Vm에 대 한 사양입니다.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 96cd7b4f4627e7c0335df9c745d8193a3dac7044
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493775"
---
# <a name="eav4-and-easv4-series"></a>Eav4 및 Easv4 시리즈

Eav4 시리즈 및 Easv4 시리즈는 최대 256MB L3 캐시가 포함 된 다중 스레드 구성에서 AMD의 2.35 Ghz EPYC<sup>TM</sup> 7452 프로세서를 활용 하 여 대부분의 메모리 액세스에 최적화 된 워크 로드를 실행 하기 위한 옵션을 늘립니다. Eav4 시리즈와 Easv4 시리즈는 Ev3 & Esv3 시리즈와 동일한 메모리 및 디스크 구성을 포함 합니다.

## <a name="eav4-series"></a>Eav4 시리즈

ACU: 230-260

Premium Storage: 지원 되지 않음

Premium Storage 캐싱: 지원 되지 않음

Eav4 시리즈 크기는 3.35 GHz의 승격 된 최대 주파수를 실현 하 고 프리미엄 SSD를 사용할 수 있는 2.35 Ghz AMD EPYC<sup>TM</sup> 7452 프로세서를 기반으로 합니다. Eav4 시리즈 크기는 메모리를 많이 사용 하는 엔터프라이즈 응용 프로그램에 적합 합니다. 데이터 디스크 스토리지는 가상 머신과 별도로 비용이 청구됩니다. 프리미엄 SSD를 사용 하려면 Easv4 시리즈 크기를 사용 합니다. Easv4 크기에 대 한 가격 책정 및 요금 청구 기준은 Eav3 시리즈와 동일 합니다.

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 임시 스토리지 처리량: IOPS/읽기 MBps/쓰기 MBps | 최대 Nic/예상 네트워크 대역폭 (MBps) |
| -----|-----|-----|-----|-----|-----|-----|
| 표준\_E2a\_v4|2|16|50|4|3000 / 46 / 23|2 / 1000 |
| 표준\_E4a\_v4|4|32|100|8|6000 / 93 / 46|2 / 2000 |
| 표준\_E8a\_v4|8|64|200|16|12000 / 187 / 93|4 / 4000 |
| 표준\_E16a\_v4|16|128|400|32|24000 / 375 / 187|8 / 8000 |
| 표준\_E20a\_v4|20|160|500|32|3만/468/234|8 / 10000 |
| 표준\_E32a\_v4|32|256|800|32|48000 / 750 / 375|8 / 16000 |
| 표준\_E48a\_v4 <sup>**</sup> |48|384|1200|32| | |
| 표준\_E64a\_v4 <sup>**</sup> |64|512|1600|32| | |
| 표준\_E96a\_v4 <sup>**</sup> |96|672|2400|32| | |

<sup>**</sup>  이러한 크기는 미리 보기 상태입니다. 이러한 큰 크기를 확인 하려는 경우 [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview)에서 등록 하세요.

## <a name="easv4-series"></a>Easv4 시리즈

ACU: 230-260

Premium Storage: 지원됨

Premium Storage 캐싱: 지원 됨

Easv4 시리즈 크기는 3.35 GHz의 승격 된 최대 주파수를 실현 하 고 프리미엄 SSD를 사용할 수 있는 2.35 Ghz AMD EPYC<sup>TM</sup> 7452 프로세서를 기반으로 합니다. Easv4 시리즈 크기는 메모리를 많이 사용 하는 엔터프라이즈 응용 프로그램에 적합 합니다.

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시된 임시 스토리지 처리량: IOPS/MBps(GiB 단위의 캐시 크기) | 최대 캐시되지 않은 디스크 처리량: IOPS/MBps | 최대 Nic/예상 네트워크 대역폭 (MBps) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_E2as_v4|2|16|32|4|4000/32 (50)|3200 / 48|2 / 1000 |
| Standard_E4as_v4|4|32|64|8|8000/64 (100)|6400/96|2 / 2000 |
| Standard_E8as_v4|8|64|128|16|16000/128 (200)|12800 / 192|4 / 4000 |
| Standard_E16as_v4|16|128|256|32|32000/255 (400)|25600/384|8 / 8000 |
| Standard_E20as_v4|20|160|320|32|4만/320 (500)|32000 / 480|8 / 10000 |
| Standard_E32as_v4|32|256|512|32|64000/510 (800)|51200/768|8 / 16000 |
| Standard_E48as_v4 <sup>**</sup> |48|384|768|32|  | |
| Standard_E64as_v4 <sup>**</sup> |64|512|1024|32| | |
| Standard_E96as_v4 <sup>**</sup> |96|672|1344|32| | |  

<sup>**</sup>  이러한 크기는 미리 보기 상태입니다. 이러한 큰 크기를 확인 하려는 경우 [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview)에서 등록 하세요.

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