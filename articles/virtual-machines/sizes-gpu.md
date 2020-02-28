---
title: Azure VM 크기-GPU | Microsoft Docs
description: Azure의 가상 컴퓨터에 사용할 수 있는 다양 한 GPU 최적화 크기를 나열 합니다. 이 시리즈의 크기에 대한 스토리지 처리량 및 네트워크 대역폭뿐만 아니라 vCPU, 데이터 디스크 및 NIC의 수에 대한 정보를 제공합니다.
services: virtual-machines
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: e7e9ff795f7f639686b9f629dacb6f0752c20732
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77660209"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>GPU 최적화 가상 머신 크기

GPU 최적화 VM 크기는 단일 또는 여러 NVIDIA GPU에서 사용 가능한 특수한 가상 머신입니다. 이러한 크기는 계산 집약적이며 그래픽 집약적인 시각화 워크로드용으로 설계되었습니다. 이 문서에서는 GPU, vCPU, 데이터 디스크 및 NIC의 개수와 종류에 대한 정보를 제공합니다. 이 그룹화의 각 크기에 대해 스토리지 처리량 및 네트워크 대역폭도 포함되어 있습니다.

- [NC 시리즈](nc-series.md), [NCv2 시리즈](ncv2-series.md), [NCv3 시리즈](ncv3-series.md) 크기는 계산 집약적 및 네트워크 집약적인 응용 프로그램 및 알고리즘에 최적화 되어 있습니다. 몇 가지 예로는 OpenCL 기반 응용 프로그램 및 시뮬레이션, AI, 심층 학습 등이 있습니다. NCv3 시리즈는 NVIDIA의 Tesla V100 GPU를 지원하는 고성능 컴퓨팅 워크로드에 중점을 둡니다. NC 시리즈는 Intel Xeon E5-2690 v3 2.60 GHz v3 (Haswell) 프로세서를 사용 하 고 NCv2 시리즈 및 NCv3 시리즈 Vm은 Intel Xeon E5-2690 v4 (Broadwell) 프로세서를 사용 합니다.

- [ND 시리즈](nd-series.md)및 [NDv2 시리즈](ndv2-series.md) 크기는 심층 학습을 위한 학습 및 유추 시나리오에 중점을 두었습니다. NVIDIA Tesla P40 GPU 및 Intel Xeon E5-2690 v4 (Broadwell) 프로세서를 사용 합니다. NDv2 시리즈는 Intel Xeon Platinum 8168 (Skylake) 프로세서를 사용 합니다.

- [NV 시리즈](nv-series.md) 및 [NVv3 시리즈](nvv3-series.md) 크기는 OpenGL 및 DirectX와 같은 프레임 워크를 사용 하 여 원격 시각화, 스트리밍, 게임, 인코딩 및 VDI 시나리오에 맞게 최적화 되 고 설계 되었습니다. 이러한 VM은 NVIDIA Tesla M60 GPU의 지원을 받습니다.

- [NVv4 시리즈](nvv4-series.md) VDI 및 원격 가상화를 위해 최적화 되 고 설계 된 VM 크기입니다. 분할 된 Gpu를 사용 하면 NVv4는 더 작은 GPU 리소스가 필요한 워크 로드에 적합 한 크기를 제공 합니다. 이러한 Vm은 AMD Radeon 이러한 MI25 GPU에 의해 지원 됩니다. NVv4 Vm은 현재 Windows 게스트 운영 체제만 지원 합니다.

## <a name="supported-operating-systems-and-drivers"></a>지원되는 운영 체제 및 드라이버

Azure N 시리즈 Vm의 GPU 기능을 활용 하려면 NVIDIA GPU 드라이버를 설치 해야 합니다.

[NVIDIA GPU 드라이버 확장](/azure/virtual-machines/extensions/hpccompute-gpu-windows)은 N 시리즈 VM에 적절한 NVIDIA CUDA 또는 GRID 드라이버를 설치합니다. Azure PowerShell 또는 Azure Resource Manager 템플릿과 같은 도구나 Azure Portal을 사용하여 확장을 설치 또는 관리합니다. 지원되는 운영 체제 및 배포 단계는 [NVIDIA GPU 드라이버 확장 설명서](/azure/virtual-machines/extensions/hpccompute-gpu-windows)를 참조하세요. VM 확장에 대한 일반적인 내용은 [Azure 가상 머신 확장 및 기능](/azure/virtual-machines/extensions/overview)을 참조하세요.

NVIDIA GPU 드라이버를 수동으로 설치 하도록 선택 하는 경우 지원 되는 운영 체제, 드라이버, 설치 및 확인 단계에 대 한 [n 시리즈 gpu driver setup For Windows](/azure/virtual-machines/windows/n-series-driver-setup) 또는 [n 시리즈 gpu Driver setup for Linux](/azure/virtual-machines/linux/n-series-driver-setup) 를 참조 하세요.

## <a name="deployment-considerations"></a>배포 고려 사항

- N 시리즈 VM의 가용성에 대해서는 [지역별 사용 가능한 제품](https://azure.microsoft.com/regions/services/)을 참조하세요.

- N 시리즈 VM은 Resource Manager 배포 모델에만 배포할 수 있습니다.

- N 시리즈 VM은 디스크에 지원되는 Azure Storage의 유형이 다양합니다. NC 및 NV VM은 표준 디스크 스토리지(HDD)로 백업되는 VM 디스크만 지원합니다. NCv2, NCv3, ND, NDv2 및 NVv2 VM은 프리미엄 디스크 스토리지(SSD)로 백업되는 VM 디스크만 지원합니다.

- 몇 개의 N 시리즈 VM을 배포하려는 경우 종량제 구독 또는 기타 구매 옵션을 고려합니다. [Azure 무료 계정](https://azure.microsoft.com/free/)을 사용하는 경우, 제한된 수의 Azure 컴퓨팅 코어만 사용할 수 있습니다.

- Azure 구독에서 코어 할당량(지역당)을 늘리고 NC, NCv2, NCv3, ND, NDv2, NV 또는 NVv2 코어에 대한 별도의 할당량을 늘려야 할 수 있습니다. 할당량 증가를 요청하려면 무료로 [온라인 고객 지원 요청을 개설](../azure-portal/supportability/how-to-create-azure-support-request.md) 합니다. 기본 제한은 구독 범주에 따라 달라질 수 있습니다.

## <a name="other-sizes"></a>기타 크기

- [범용](sizes-general.md)
- [컴퓨팅 최적화](sizes-compute.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [이전 세대](sizes-previous-gen.md)

## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
