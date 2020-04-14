---
title: Azure VM 크기 - 메모리 | 마이크로 소프트 문서
description: Azure의 가상 컴퓨터에 사용할 수 있는 다양한 메모리 최적화 크기를 나열합니다. 이 시리즈의 크기에 대한 스토리지 처리량 및 네트워크 대역폭뿐만 아니라 vCPU, 데이터 디스크 및 NIC의 수에 대한 정보를 제공합니다.
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
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273582"
---
# <a name="memory-optimized-virtual-machine-sizes"></a>메모리 최적화 가상 머신 크기

메모리 최적화 VM 크기는 관계형 데이터베이스 서버, 중대형 캐시 및 메모리 내 분석에 적합한 높은 메모리 대 CPU 비율을 제공합니다. 이 문서에서는 이 그룹화에서 각 크기에 대한 스토리지 처리량 및 네트워크 대역폭뿐만 아니라 vCPU, 데이터 디스크 및 NIC의 수에 대한 정보를 제공합니다.

- [Dv2 및 DSv2 시리즈는](dv2-dsv2-series-memory.md)오리지널 D 시리즈의 후속 작으로, 더욱 강력한 CPU를 제공합니다. Dv2 시리즈는 D 시리즈보다 약 35% 빠릅니다. 인텔&reg; &reg; 제온 8171M 2.1 G H z (스카이 레이크) 또는 인텔&reg; 제온&reg; E5-2673 v4 2.3&reg; G h&reg; z (브로드 웰) 또는 인텔 제온 E5-2673 v3 2.4 G H z (하스웰) 프로세서, 그리고 인텔 터보 부스트 기술 2.0. Dv2 시리즈는 D 시리즈와 메모리 및 디스크 구성이 같습니다.

    Dv2 및 DSv2 계열은 더 빠른 vCPU, 더 나은 임시 스토리지 성능 또는 더 높은 메모리 요구가 있는 애플리케이션에 이상적입니다. 이들은 많은 엔터프라이즈급 애플리케이션을 위한 강력한 조합을 제공합니다.

- [Eav4 및 Easv4 계열은](eav4-easv4-series.md) 최대 256MB L3 캐시가 있는 다중 스레드 구성에서 AMD의 2.35Ghz EPYC<sup>TM</sup> 7452 프로세서를 활용하여 대부분의 메모리 최적화 워크로드를 실행할 수 있는 옵션을 늘입니다. Eav4 시리즈와 Easv4 시리즈는 Ev3 & Esv3 시리즈와 동일한 메모리 및 디스크 구성을 갖습니다.

- [Ev3 및 Esv3](ev3-esv3-series.md) 시리즈&reg; 인텔&reg; 제온 8171M 2.1 G Hz&reg; (스카이&reg; 레이크) 또는 인텔 제온 E5-2673 v4 2.3 G H z (Broadwell) 프로세서 하이퍼 스레드 구성, 대부분의 범용 워크로드에 대 한 더 나은 가치 제안을 제공 하 고 Ev3 대부분의 다른 클라우드의 범용 VM과 정렬. 메모리가 확장되었으며(7GiB/vCPU에서 8GiB/vCPU) 디스크 및 네트워크 제한이 하이퍼 스레딩으로이동에 맞춰 코어단위로 조정되었습니다. Ev3는 D/Dv2 제품군의 상위 메모리 VM 크기에 대한 후속 시리즈입니다.

- [M 시리즈는](m-series.md) vCPU 수(최대 128vCPU)와 다량의 메모리(최대 3.8TiB)를 제공합니다. 또한 vCPU 수가 많고 메모리가 많은 매우 큰 데이터베이스 나 다른 응용 프로그램에도 적합합니다.

- [Mv2 시리즈는](mv2-series.md) 클라우드에서 VM 중 가장 높은 vCPU 수(최대 416vCPU)와 최대 메모리(최대 11.4TiB)를 제공합니다. vCPU 수가 많고 메모리가 많은 매우 큰 데이터베이스 나 다른 응용 프로그램에 이상적입니다.

Azure Compute는 특정 하드웨어 유형에서 격리되고 단일 고객 전용인 가상 머신 크기를 제공합니다. 이러한 가상 머신 크기는 규정 준수 및 규정 요구 사항과 같은 요소를 포함하는 작업에 대해 다른 고객으로부터 높은 수준의 격리가 필요한 작업에 가장 적합합니다. 고객은 [중첩된 가상 머신에 대한 Azure 지원](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)을 사용하여 이러한 격리된 가상 머신의 리소스를 보다 세분화할 수도 있습니다. 격리된 VM 옵션은 아래의 가상 컴퓨터 제품군 페이지를 참조하십시오.

## <a name="other-sizes"></a>기타 크기

- [범용 가상 컴퓨터](sizes-general.md)
- [컴퓨팅 최적화](sizes-compute.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)

## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
