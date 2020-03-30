---
title: DC 시리즈 - Azure 가상 시스템
description: DC 시리즈 VM에 대한 사양입니다.
services: virtual-machines
author: susaxen
ms.service: virtual-machines
ms.topic: article
ms.date: 02/20/2020
ms.author: lahugh
ms.openlocfilehash: 7834c8a32d4d85fc354bac209e13f19f3b8315fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80256932"
---
# <a name="preview-dcsv2-series"></a>미리보기: DCsv2 시리즈


DCsv2 시리즈는 퍼블릭 클라우드에서 처리되는 동안 데이터와 코드의 기밀성과 무결성을 보호하는 데 도움이 될 수 있습니다. 이 기계는 SGX 기술이 장착된 최신 인텔 XEON E-2288G 프로세서로 뒷받침됩니다. 인텔 터보 부스트 기술을 통해 이 기계는 최대 5.0GHz까지 올라갈 수 있습니다. DCsv2 시리즈 인스턴스를 사용하면 고객이 안전한 영토 기반 응용 프로그램을 빌드하여 사용 중 코드와 데이터를 보호할 수 있습니다.

예를 들어 사용 사례로는 기밀 다자간 데이터 공유, 사기 탐지, 자금 세탁 방지, 블록체인, 기밀 사용 분석, 인텔리전스 분석 및 기밀 기계 학습이 포함됩니다.

프리미엄 스토리지: 지원*

프리미엄 스토리지 캐싱: 지원*

라이브 마이그레이션: 지원되지 않음

업데이트 메모리 보존: 지원되지 않음

*Standard_DC8_v2 제외



| 크기             | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시된 임시 스토리지 처리량: IOPS/MBps(GiB 단위의 캐시 크기) | 최대 캐시되지 않은 디스크 처리량: IOPS/MBps | 최대 NIC / 예상 네트워크 대역폭(MBps) |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2000/16 (21)                                                            | 1600/24                                   | 2                                            |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4000/32 (43)                                                            | 3200/48                                   | 2                                            |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8000/64 (86)                                                            | 6400/96                                   | 2                                            |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16000/128 (172)                                                         | 12800/192                                 | 2                                            |

- DCsv2 시리즈 VM은 [2세대 VM이며](./linux/generation-2.md#creating-a-generation-2-vm) 이미지만 지원합니다. `Gen2`
- 현재 영국 남부 및 캐나다 중부에서만 사용할 수 있습니다.
- 이전 세대의 기밀 컴퓨팅 VM: [DC 시리즈](sizes-previous-gen.md)
- Azure 포털 만들기 [VM - 포털을](./linux/quick-create-portal.md) 사용하여 DCv2 VM 만들기



## <a name="other-sizes"></a>기타 크기

- [범용](sizes-general.md)
- [메모리 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)

## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
