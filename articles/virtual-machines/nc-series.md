---
title: NC 시리즈-Azure Virtual Machines
description: NC 시리즈 Vm에 대 한 사양입니다.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: eab7dfe79aa5cdf234c8bc9472387214f7df3563
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "78164528"
---
# <a name="nc-series"></a>NC 시리즈

NC 시리즈 Vm은 [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) 카드 및 Intel Xeon E5-2690 V3 (haswell) 프로세서를 기반으로 합니다. 사용자는 에너지 탐색 애플리케이션, 충돌 시뮬레이션, 광선 추적 렌더링, 딥러닝 등에 CUDA를 활용하여 데이터를 더 빠르게 처리할 수 있습니다. NC24r 구성은, 긴밀하게 결합된 병렬 컴퓨팅 워크로드용으로 최적화된 대기 시간이 짧고 처리량이 높은 네트워크 인터페이스를 제공합니다.

Premium Storage: 지원되지 않음

Premium Storage 캐싱: 지원 되지 않음

실시간 마이그레이션: 지원 되지 않음

메모리 보존 업데이트: 지원 되지 않음

| Size | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | GPU | GPU 메모리: GiB | 최대 데이터 디스크 수 | 최대 NIC 수 |
|---|---|---|---|---|---|---|---|
| Standard_NC6    | 6  | 56  | 340  | 1 | 12 | 24 | 1 |
| Standard_NC12   | 12 | 112 | 680  | 2 | 24 | 48 | 2 |
| Standard_NC24   | 24 | 224 | 1440 | 4 | 48 | 64 | 4 |
| Standard_NC24r* | 24 | 224 | 1440 | 4 | 48 | 64 | 4 |

1 GPU = K80 카드의 절반.

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
