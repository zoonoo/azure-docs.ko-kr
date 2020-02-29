---
title: NCv2 시리즈-Azure Virtual Machines
description: NCv2 시리즈 Vm에 대 한 사양입니다.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: f4a382e422fd56e754d8014bfb4f0f0af1a3ec77
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2020
ms.locfileid: "78163984"
---
# <a name="ncv2-series"></a>NCv2 시리즈

NCv2 시리즈 VM은 [NVIDIA Tesla P100](https://www.nvidia.com/data-center/tesla-p100/) GPU로 구동됩니다. 이러한 GPU는 NC 시리즈보다 2배를 초과하는 계산 성능을 제공할 수 있습니다. 고객은 저수지 모델링, DNA 배열, 단백질 분석, 몬테카를로 시뮬레이션 등 기존 HPC 워크로드에 이러한 업데이트된 GPU를 활용할 수 있습니다. Gpu 외에도 NCv2 시리즈 Vm은 Intel Xeon E5-2690 v4 (Broadwell) Cpu로 구동 됩니다.

NC24rs v2 구성은 긴밀하게 결합된 병렬 컴퓨팅 작업에 최적화된 짧은 대기 시간과 높은 처리량의 네트워크 인터페이스를 제공합니다.

Premium Storage: 지원됨

Premium Storage 캐싱: 지원 됨

실시간 마이그레이션: 지원 되지 않음

메모리 보존 업데이트: 지원 되지 않음

> [!IMPORTANT]
> 이 VM 시리즈의 경우 구독에서 vCPU (코어) 할당량은 초기에 각 지역에서 0으로 설정 됩니다. [사용 가능한 지역](https://azure.microsoft.com/regions/services/)에서이 시리즈에 대 한 [vcpu 할당량 증가를 요청](../azure-supportability/resource-manager-core-quotas-request.md) 합니다.
>
| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | GPU | GPU 메모리: GiB | 최대 데이터 디스크 수 | 최대 캐시 되지 않은 디스크 처리량: IOPS/MBps | 최대 NIC 수 |
|---|---|---|---|---|---|---|---|---|
| Standard_NC6s_v2    | 6  | 112 | 736  | 1 | 16 | 12 | 20000/200 | 4 |
| Standard_NC12s_v2   | 12 | 224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v2   | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |
| Standard_NC24rs_v2* | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

한 개의 GPU = 한 개의 P100 카드

*RDMA 지원

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>지원되는 운영 체제 및 드라이버

Azure N 시리즈 Vm의 GPU 기능을 활용 하려면 NVIDIA GPU 드라이버를 설치 해야 합니다.

[NVIDIA GPU 드라이버 확장](./extensions/hpccompute-gpu-windows.md)은 N 시리즈 VM에 적절한 NVIDIA CUDA 또는 GRID 드라이버를 설치합니다. Azure PowerShell 또는 Azure Resource Manager 템플릿과 같은 도구나 Azure Portal을 사용하여 확장을 설치 또는 관리합니다. 지원되는 운영 체제 및 배포 단계는 [NVIDIA GPU 드라이버 확장 설명서](./extensions/hpccompute-gpu-windows.md)를 참조하세요. VM 확장에 대한 일반적인 내용은 [Azure 가상 머신 확장 및 기능](/.extensions/overview.md)을 참조하세요.

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
