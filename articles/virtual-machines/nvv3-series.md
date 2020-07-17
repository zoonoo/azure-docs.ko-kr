---
title: NVv3 시리즈-Azure Virtual Machines
description: NVv3 시리즈 Vm에 대 한 사양입니다.
services: virtual-machines
ms.subservice: sizes
author: vikancha-MSFT
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 9f5d5bd9775507a663a4171faa8b8892357f65b0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84736546"
---
# <a name="nvv3-series"></a>NVv3 시리즈

NVv3 시리즈 가상 머신은 Intel E5-2690 v4 (Broadwell) Cpu 및 Intel 하이퍼 스레딩 기술을 사용 하 여 [Nvidia Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU 및 nvidia GRID 기술로 구동 됩니다. 이러한 가상 머신은 고객이 데이터를 시각화하고 결과를 시뮬레이트하여 보고 CAD에서 작업하거나 콘텐츠를 렌더링 및 스트림하려고 하는 GPU 가속 그래픽 애플리케이션 및 가상 데스크톱을 대상으로 합니다. 또한 이러한 가상 머신은 인코딩 및 렌더링과 같은 단정밀도 워크로드를 실행할 수 있습니다. NVv3 virtual machines는 Premium Storage을 지원 하 고 선행 NV 시리즈와 비교할 때 시스템 메모리 (RAM)의 두 배를 제공 합니다.  

NVv3 인스턴스의 각 GPU는 그리드 라이선스로 제공 됩니다. 이 라이선스가 있으면 NV 인스턴스를 단일 사용자를 위한 가상 워크스테이션으로 유연하게 사용하거나, 25명의 동시 사용자가 가상 애플리케이션 시나리오를 위해 VM에 연결할 수 있습니다.

Premium Storage 캐싱:  지원됨

실시간 마이그레이션: 지원되지 않음

메모리 보존 업데이트: 지원되지 않음

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | GPU | GPU 메모리: GiB | 최대 데이터 디스크 수 | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 최대 NIC 수 | 가상 워크스테이션 | 가상 애플리케이션 |
|---|---|---|---|---|---|---|---|---|---|---|
| Standard_NV12s_v3 |12 | 112 | 320  | 1 | 8  | 12 | 20000/200 | 4 | 1 | 25  |
| Standard_NV24s_v3 |24 | 224 | 640  | 2 | 16 | 24 | 40000/400 | 8 | 2 | 50  |
| Standard_NV48s_v3 |48 | 448 | 1280 | 4 | 32 | 32 | 80000/800 | 8 | 4 | 100 |

1 GPU = M60 카드 절반.

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
