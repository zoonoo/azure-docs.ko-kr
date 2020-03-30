---
title: NDv2 시리즈 - Azure 가상 시스템
description: NDv2 시리즈 VM에 대한 사양.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 8a2d275501816dd504130b255b73a752c5615f0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247285"
---
# <a name="updated-ndv2-series"></a>업데이트된 NDv2 시리즈

NDv2 시리즈 가상 머신은 가장 까다로운 GPU 가속 AI, 머신 러닝, 시뮬레이션 및 HPC 워크로드의 요구에 맞게 설계된 GPU 제품군에 새로 추가되었습니다.

NDv2는 8 엔비디아 테슬라 V100 NVLINK 연결 GPU에 의해 구동, 각각 32 기가바이트의 GPU 메모리. 각 NDv2 VM에는 40개의 비하이퍼스레드 인텔 제온 플래티넘 8168(스카이레이크) 코어와 672GiB의 시스템 메모리가 있습니다.

NDv2 인스턴스는 CUDA GPU에 최적화된 계산 커널을 활용하는 HPC 및 AI 워크로드와 텐서플로우, 피토치, 카페, RAPIDS 및 기타 와 같은 GPU 가속 '즉시 사용 가능'을 지원하는 많은 AI, ML 및 분석 도구에 탁월한 성능을 제공합니다. 프레임 워크.

비판적으로 NDv2는 계산적으로 강렬한 확장(VM당 8개의 GPU 를 활용) 및 확장(여러 VM을 함께 작업하는) 워크로드를 위해 제작되었습니다. NDv2 시리즈는 이제 HPC VM의 HB 시리즈에서 사용할 수 있는 것과 유사한 100기가비트 InfiniBand EDR 백엔드 네트워킹을 지원하여 AI 및 ML에 대한 분산 교육을 포함한 병렬 시나리오에 대한 고성능 클러스터링을 허용합니다. 이 백 엔드 네트워크는 NVIDIA의 NCCL2 라이브러리에서 사용하는 프로토콜을 포함하여 모든 주요 InfiniBand 프로토콜을 지원하므로 GPU의 원활한 클러스터링이 가능합니다.

> ND40rs_v2 VM에서 [인피니밴드를 활성화할](https://docs.microsoft.com/azure/virtual-machines/workloads/hpc/enable-infiniband) 때는 4.7-1.0.0.1 멜라녹스 OFED 드라이버를 사용하십시오.
>
> GPU 메모리가 증가하기 때문에 새로운 ND40rs_v2 VM은 [2세대 VM과](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) 마켓플레이스 이미지를 사용해야 합니다. 
>
> 참고: GPU당 16GB 메모리가 포함된 ND40s_v2 더 이상 미리 보기로 사용할 수 없으며 업데이트된 ND40rs_v2 대체되었습니다.

<br>

Premium Storage: 지원됨

프리미엄 스토리지 캐싱: 지원

라이브 마이그레이션: 지원되지 않음

업데이트 메모리 보존: 지원되지 않음

인피니밴드: 지원

| 크기 | vCPU | 메모리: GiB | 온도 저장 (SSD) : GiB | GPU | GPU 메모리: GiB | 최대 데이터 디스크 수 | 최대 캐시되지 않은 디스크 처리량: IOPS/MBps | 최대 네트워크 대역폭 | 최대 NIC 수 |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND40rs_v2 | 40 | 672 | 2948 | 8 V100 32 GB (NVLink) | 32 | 32 | 80000 / 800 | 24000Mbps | 8 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>지원되는 운영 체제 및 드라이버

Azure N 시리즈 VM의 GPU 기능을 활용하려면 NVIDIA GPU 드라이버를 설치해야 합니다.

[NVIDIA GPU 드라이버 확장](./extensions/hpccompute-gpu-linux.md)은 N 시리즈 VM에 적절한 NVIDIA CUDA 또는 GRID 드라이버를 설치합니다. Azure PowerShell 또는 Azure Resource Manager 템플릿과 같은 도구나 Azure Portal을 사용하여 확장을 설치 또는 관리합니다. VM 확장에 대한 일반적인 내용은 [Azure 가상 머신 확장 및 기능](./extensions/overview.md)을 참조하세요.

수동으로 엔비디아 GPU 드라이버를 설치 하도록 선택한 경우, [리눅스에 대 한 N 시리즈 GPU 드라이버 설정을](./linux/n-series-driver-setup.md)참조 하십시오.

## <a name="other-sizes"></a>기타 크기

- [범용](sizes-general.md)
- [메모리 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)

## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
