---
title: ND 시리즈-Azure Virtual Machines
description: ND 시리즈 Vm에 대 한 사양입니다.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 163dc0d6ecd4f91273a82b04ce186f7a8df868af
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79465059"
---
# <a name="nd-series"></a>ND 시리즈

ND 시리즈 가상 머신은 AI 및 딥러닝 워크로드용으로 설계된 GPU 제품군에 새로 추가됩니다. 이 가상 머신은 교육 및 유추에 우수한 성능을 제공합니다. ND 인스턴스는 [NVIDIA Tesla P40](https://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) Gpu 및 Intel Xeon E5-2690 V4 (Broadwell) cpu에 의해 구동 됩니다. 이 인스턴스는 단정밀도 부동 소수점 작업, Microsoft Cognitive Toolkit, TensorFlow, Caffe 및 기타 프레임워크를 활용하는 AI 워크로드에 우수한 성능을 제공합니다. ND 시리즈는 훨씬 큰 GPU 메모리 크기(24GB)도 제공하므로 더 큰 규모의 신경망 모델에도 적합합니다. NC 시리즈와 마찬가지로 ND 시리즈는 RDMA를 통한 대기 시간이 낮고 처리량이 높은 보조 네트워크 및 InfiniBand 연결과 함께 구성할 수 있으므로 여러 GPU를 사용한 대규모 교육 작업을 실행할 수 있습니다.

Premium Storage: 지원됨

Premium Storage 캐싱: 지원 됨

실시간 마이그레이션: 지원 되지 않음

메모리 보존 업데이트: 지원 되지 않음

> [!IMPORTANT]
> 이 VM 시리즈의 경우 구독에서 지역별 vCPU (코어) 할당량은 처음에 0으로 설정 됩니다. [사용 가능한 지역](https://azure.microsoft.com/regions/services/)에서이 시리즈에 대 한 [vcpu 할당량 증가를 요청](../azure-supportability/resource-manager-core-quotas-request.md) 합니다.
>
| Size | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | GPU | GPU 메모리: GiB | 최대 데이터 디스크 수 | 최대 캐시 되지 않은 디스크 처리량: IOPS/MBps | 최대 NIC 수 |
|---|---|---|---|---|---|---|---|---|
| Standard_ND6s    | 6  | 112 | 736  | 1 | 24 | 12 | 20000/200 | 4 |
| Standard_ND12s   | 12 | 224 | 1474 | 2 | 48 | 24 | 40000/400 | 8 |
| Standard_ND24s   | 24 | 448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |
| Standard_ND24rs* | 24 | 448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |

한 개의 GPU = 한 개의 P40 카드

*RDMA 지원

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>지원되는 운영 체제 및 드라이버

Azure N 시리즈 Vm의 GPU 기능을 활용 하려면 NVIDIA GPU 드라이버를 설치 해야 합니다.

[NVIDIA GPU 드라이버 확장](./extensions/hpccompute-gpu-windows.md)은 N 시리즈 VM에 적절한 NVIDIA CUDA 또는 GRID 드라이버를 설치합니다. Azure PowerShell 또는 Azure Resource Manager 템플릿과 같은 도구나 Azure Portal을 사용하여 확장을 설치 또는 관리합니다. 지원되는 운영 체제 및 배포 단계는 [NVIDIA GPU 드라이버 확장 설명서](./extensions/hpccompute-gpu-windows.md)를 참조하세요. VM 확장에 대한 일반적인 내용은 [Azure 가상 머신 확장 및 기능](./extensions/overview.md)을 참조하세요.

NVIDIA GPU 드라이버를 수동으로 설치 하도록 선택 하는 경우 지원 되는 운영 체제, 드라이버, 설치 및 확인 단계에 대 한 [n 시리즈 gpu driver setup For Windows](./windows/n-series-driver-setup.md) 또는 [n 시리즈 gpu Driver setup for Linux](./linux/n-series-driver-setup.md) 를 참조 하세요.

## <a name="other-sizes"></a>기타 크기

- [범용](sizes-general.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)

## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
