---
title: DC 시리즈-Azure Virtual Machines
description: DC 시리즈 Vm에 대 한 사양입니다.
services: virtual-machines
author: susaxen
ms.service: virtual-machines
ms.topic: article
ms.date: 02/20/2020
ms.author: lahugh
ms.openlocfilehash: d500f7a1f28eff1f638a244022226b0aa0e715cb
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78199600"
---
# <a name="preview-dcv2-series"></a>미리 보기: DCv2 시리즈


DCv2 시리즈는 공용 클라우드에서 처리 되는 동안 데이터 및 코드의 기밀성과 무결성을 보호 하는 데 도움이 될 수 있습니다. 이러한 컴퓨터는 SGX 기술을 사용 하는 Intel XEON E-2288G 프로세서의 최신 세대에서 지원 됩니다. Intel 터보 부스트 기술을 사용 하 여 이러한 컴퓨터는 최대 5.0 g h z까지 갈 수 있습니다. DCv2 series 인스턴스를 사용 하면 고객이 사용 중인 코드와 데이터를 보호 하기 위해 보안 enclave 기반 응용 프로그램을 빌드할 수 있습니다.

사용 사례에는 기밀 단체 데이터 공유, 사기 감지, 사기 방지, 블록 체인, 비밀 사용 분석, 인텔리전스 분석 및 기밀 machine learning이 포함 됩니다.

Premium Storage: 지원 됨 *

Premium Storage 캐싱: 지원 됨 *

실시간 마이그레이션: 지원 되지 않음

메모리 보존 업데이트: 지원 되지 않음

\* Standard_DC8_v2 제외



| 크기             | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시된 임시 스토리지 처리량: IOPS/MBps(GiB 단위의 캐시 크기) | 최대 캐시되지 않은 디스크 처리량: IOPS/MBps | 최대 Nic/예상 네트워크 대역폭 (MBps) |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2000/16 (21)                                                            | 1600/24                                   | 2                                            |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4000/32 (43)                                                            | 3200/48                                   | 2                                            |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8000/64 (86)                                                            | 6400/96                                   | 2                                            |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16000/128 (172)                                                         | 12800/192                                 | 2                                            |

- DCv2 시리즈 Vm은 [2 세대 vm](./linux/generation-2.md#creating-a-generation-2-vm) 이며 `Gen2` 이미지만 지원 합니다.




## <a name="other-sizes"></a>기타 크기

- [범용](sizes-general.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)

## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
