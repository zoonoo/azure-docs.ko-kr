---
title: NDv2 시리즈
description: NDv2 시리즈 Vm에 대 한 사양입니다.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 7ab9d270ae5da52cbf9b5ba0ed4730233225a7c1
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653450"
---
# <a name="updated-ndv2-series"></a>업데이트 된 NDv2 시리즈

NDv2 시리즈 가상 머신은 가장 까다로운 GPU 가속 AI, 기계 학습, 시뮬레이션 및 HPC 워크 로드의 요구에 맞게 설계 된 GPU 제품군에 새로 추가 된 기능입니다.

NDv2는 8 개의 NVIDIA Tesla V100 NVLINK에 연결 된 Gpu로, 각각 32 GB의 GPU 메모리를 제공 합니다. 또한 각 NDv2 VM에는 40 비 하이퍼 스레드 Intel Xeon 플래티넘 8168 (Skylake) 코어 및 672 GiB 시스템 메모리가 있습니다.

NDv2 인스턴스는 TensorFlow, Pytorch, Caffe, RAPIDS 및 기타 프레임 워크와 같은 GPU 가속을 지 원하는 다양 한 AI, ML 및 분석 도구를 통해,,,, 및 기타 프레임 워크를 활용 하는 HPC 및 AI 워크 로드에 뛰어난 성능을 제공 합니다.

NDv2는 계산에 강한 확장 (VM 당 활용 8 Gpu) 및 규모 확장 (여러 Vm이 함께 작동 하는 활용)에 대해 빌드 되었습니다. 이제 NDv2 시리즈는 AI 및 ML에 대 한 분산 학습을 포함 하 여 병렬 시나리오에 고성능 클러스터링을 허용 하기 위해 HPC VM의 HB 시리즈에서 사용할 수 있는 것과 유사한 100 기가 비트 InfiniBand EDR 백 엔드 네트워킹을 지원 합니다. 이 백 엔드 네트워크는 NVIDIA의 NCCL2 라이브러리에서 사용 되는 프로토콜을 포함 하 여 모든 주요 InfiniBand 프로토콜을 지원 하므로 Gpu의 원활한 클러스터링이 가능 합니다.

> [!IMPORTANT]
> ND40rs_v2 VM에서 [InfiniBand을 사용 하도록 설정](./workloads/hpc/enable-infiniband.md) 하는 경우 4.7-1.0.0.1 Mellanox OFED 드라이버를 사용 하세요.
>
> GPU 메모리가 늘어남에 따라 새 ND40rs_v2 VM은 [2 세대 vm](./windows/generation-2.md) 및 마켓플레이스 이미지를 사용 해야 합니다. 
>
> 참고: GPU 당 16gb의 메모리를 포함 하는 ND40s_v2는 더 이상 미리 보기에 사용할 수 없으며 업데이트 된 ND40rs_v2에 의해 대체 되었습니다.

<br>

[Premium Storage](premium-storage-performance.md): 지원 됨<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원 됨<br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원 되지 않음<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원 되지 않음<br>
[VM 생성 지원](generation-2.md): 1 세대<br>
InfiniBand: 지원 됨<br>
<br>

| 크기 | vCPU | 메모리: GiB | 임시 저장소 (SSD): GiB | GPU | GPU 메모리: GiB | 최대 데이터 디스크 수 | 최대 캐시되지 않은 디스크 처리량: IOPS/MBps | 최대 네트워크 대역폭 | 최대 NIC 수 |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND40rs_v2 | 40 | 672 | 2948 | 8 V100 32 g b (NVLink) | 32 | 32 | 8만/800 | 24000 Mbps | 8 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>지원되는 운영 체제 및 드라이버

Azure N 시리즈 Vm의 GPU 기능을 활용 하려면 NVIDIA GPU 드라이버를 설치 해야 합니다.

[NVIDIA GPU 드라이버 확장](./extensions/hpccompute-gpu-linux.md)은 N 시리즈 VM에 적절한 NVIDIA CUDA 또는 GRID 드라이버를 설치합니다. Azure PowerShell 또는 Azure Resource Manager 템플릿과 같은 도구나 Azure Portal을 사용하여 확장을 설치 또는 관리합니다. VM 확장에 대한 일반적인 내용은 [Azure 가상 머신 확장 및 기능](./extensions/overview.md)을 참조하세요.

NVIDIA GPU 드라이버를 수동으로 설치 하는 경우 [Linux 용 N 시리즈 GPU 드라이버 설치](./linux/n-series-driver-setup.md)를 참조 하세요.

## <a name="other-sizes"></a>기타 크기

- [범용](sizes-general.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)

## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
