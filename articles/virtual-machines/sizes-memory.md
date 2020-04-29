---
title: Azure VM 크기-메모리 | Microsoft Docs
description: Azure의 가상 컴퓨터에 사용할 수 있는 다양 한 메모리 최적화 크기를 나열 합니다. 이 시리즈의 크기에 대한 스토리지 처리량 및 네트워크 대역폭뿐만 아니라 vCPU, 데이터 디스크 및 NIC의 수에 대한 정보를 제공합니다.
services: virtual-machines
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
keywords: VM 격리, 격리된 VM, 격리, 격리됨
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: 5a20e9c64b6ef948167333b54b16b34e84dc0e32
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81273582"
---
# <a name="memory-optimized-virtual-machine-sizes"></a>메모리 최적화 가상 머신 크기

메모리 최적화 VM 크기는 관계형 데이터베이스 서버, 중대형 캐시 및 메모리 내 분석에 적합한 높은 메모리 대 CPU 비율을 제공합니다. 이 문서에서는 이 그룹화에서 각 크기에 대한 스토리지 처리량 및 네트워크 대역폭뿐만 아니라 vCPU, 데이터 디스크 및 NIC의 수에 대한 정보를 제공합니다.

- [Dv2 및 DSv2 시리즈](dv2-dsv2-series-memory.md)는 원본 D 시리즈에 대 한 후속 기능으로, 더 강력한 CPU를 제공 합니다. Dv2 시리즈는 D 시리즈 보다 약 35% 더 빠릅니다. Intel&reg; xeon&reg; 8171m 2.1 g h z (Skylake) 또는 intel&reg; Xeon&reg; e5-2673 v4 2.3 ghz (Broadwell) 또는 intel&reg; Xeon&reg; e5-2673 V3 2.4 g h z (haswell) 프로세서 및 intel 터보 부스트 기술 2.0에서 실행 됩니다. Dv2 시리즈는 D 시리즈와 메모리 및 디스크 구성이 같습니다.

    Dv2 및 DSv2 시리즈는 더 빠른 vCPUs를 요구 하거나 더 나은 임시 저장소 성능을 요구 하거나 더 높은 메모리 수요를 요구 하는 응용 프로그램에 적합 합니다. 이들은 많은 엔터프라이즈급 애플리케이션을 위한 강력한 조합을 제공합니다.

- [Eav4 및 Easv4 시리즈](eav4-easv4-series.md) 는 최대 256mb L3 캐시가 포함 된 다중 스레드 구성에서 AMD의 2.35 GHZ Epyc<sup>TM</sup> 7452 프로세서를 활용 하 여 대부분의 메모리 액세스에 최적화 된 작업을 실행 하는 옵션을 늘립니다. Eav4 시리즈와 Easv4 시리즈는 Ev3 & Esv3 시리즈와 동일한 메모리 및 디스크 구성을 포함 합니다.

- [Ev3 및 Esv3 시리즈](ev3-esv3-series.md) intel&reg; &reg; xeon 8171M 2.1 g h z (Skylake) 또는 하이퍼 스레드&reg; 구성&reg; 의 intel xeon E5-2673 v4 2.3 ghz (Broadwell) 프로세서를 사용 하 여 대부분의 범용 워크 로드에 대해 더 나은 가치를 제공 하 고 Ev3를 대부분의 다른 클라우드의 범용 vm과 맞춥니다. 디스크 및 네트워크 제한은 코어 단위로 조정 되어 하이퍼 스레딩으로의 이동에 맞게 조정 된 동안 (7 GiB/vCPU에서 8 GiB/vCPU로) 확장 되었습니다. Ev3는 D/Dv2 제품군의 상위 메모리 VM 크기에 대한 후속 시리즈입니다.

- [M 시리즈](m-series.md) 는 높은 vcpu 수 (최대 128 vcpu) 및 많은 양의 메모리 (최대 3.8 TiB)를 제공 합니다. 매우 큰 데이터베이스 또는 높은 vCPU 수 및 많은 양의 메모리를 활용 하는 다른 응용 프로그램에도 적합 합니다.

- [Mv2 시리즈](mv2-series.md) 는 클라우드에서 VM의 가장 높은 vcpu 수 (최대 416 vcpu) 및 가장 큰 메모리 (최대 11.4 TiB)를 제공 합니다. 많은 vCPU 수와 많은 양의 메모리를 활용 하는 매우 큰 데이터베이스 또는 다른 응용 프로그램에 이상적입니다.

Azure Compute는 특정 하드웨어 유형에서 격리되고 단일 고객 전용인 가상 머신 크기를 제공합니다. 이러한 가상 머신 크기는 규정 준수 및 규정 요구 사항과 같은 요소를 포함하는 작업에 대해 다른 고객으로부터 높은 수준의 격리가 필요한 작업에 가장 적합합니다. 고객은 [중첩된 가상 머신에 대한 Azure 지원](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)을 사용하여 이러한 격리된 가상 머신의 리소스를 보다 세분화할 수도 있습니다. 격리 된 VM 옵션에 대해서는 아래의 virtual machine 제품군에 대 한 페이지를 참조 하세요.

## <a name="other-sizes"></a>기타 크기

- [범용](sizes-general.md)
- [컴퓨팅 최적화](sizes-compute.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)

## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
