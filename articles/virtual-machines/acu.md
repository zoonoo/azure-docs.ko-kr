---
title: Azure 컴퓨팅 단위 개요 | Microsoft Docs
description: Azure compute 단위의 개념에 대 한 개요입니다. ACU에서는 Azure SKU에서 CPU 성능을 비교하는 방법을 제공합니다.
services: virtual-machines
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: azure-resource-manager,azure-service-management
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: e344d09497a30dec546dfaedd3d78f30c7d214d2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79535700"
---
# <a name="azure-compute-unit-acu"></a>ACU(Azure 컴퓨팅 단위)

ACU(Azure 컴퓨팅 단위) 개념은 Azure SKU에서 컴퓨팅(CPU) 성능을 비교하는 방법을 제공합니다. 어떤 SKU가 성능 요구 사항을 충족할 가능성이 높은지 쉽게 식별하도록 합니다. ACU는 현재 100인 작은(Standard_A1) VM에서 표준화되고 다른 SKU는 모두 SKU가 표준 벤치 마크를 얼마나 빨리 실행할 수 있는지를 나타냅니다.

> [!IMPORTANT]
> ACU는 단지 지침일 뿐입니다. 워크로드에 대한 결과가 달라질 수 있습니다.
<br>

| SKU 제품군 | ACU \ vCPU | vCPU: 코어 |
| --- | --- |---|
| [A0](sizes-previous-gen.md) |50 | 1:1 |
| [A1 - A4](sizes-previous-gen.md) |100 | 1:1 |
| [A5 - A7](sizes-previous-gen.md) |100 | 1:1 |
| [A1_v2 - A8_v2](sizes-general.md) |100 | 1:1 |
| [A2m_v2 - A8m_v2](sizes-general.md) |100 | 1:1 |
| [A8 - A11](sizes-previous-gen.md) |225* | 1:1 |
| [D1 - D14](sizes-previous-gen.md) |160-250 | 1:1 |
| [D1_v2 - D15_v2](dv2-dsv2-series.md) |210 - 250* | 1:1 |
| [DS1 - DS14](sizes-previous-gen.md) |160-250 | 1:1 |
| [DS1_v2 - DS15_v2](dv2-dsv2-series.md) |210 - 250* | 1:1 |
| [D_v3](dv3-dsv3-series.md) |160 - 190* | 2:1\*\*\* |
| [Ds_v3](dv3-dsv3-series.md) |160 - 190* | 2:1\*\*\* |
| [E_v3](ev3-esv3-series.md) |160 - 190* | 2:1\*\*\*|
| [Es_v3](ev3-esv3-series.md) |160 - 190* | 2:1\*\*\* |
| [F2s_v2 - F72s_v2](fsv2-series.md) |195-210 * | 2:1\*\*\* |
| [F1 - F16](sizes-previous-gen.md) |210 - 250* | 1:1 |
| [F1s - F16s](sizes-previous-gen.md) |210 - 250* | 1:1 |
| [G1 - G5](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [GS1 - GS5](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [넣기](h-series.md) |290 - 300* | 1:1 |
| [HB](hb-series.md) |199-216 * * | 1:1 |
| [HC](hc-series.md) |297-315 * | 1:1 |
| [L4s - L32s](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [L8s_v2 - L80s_v2](lsv2-series.md) |150 - 175** | 2:1 |
| [M](m-series.md) | 160-180 | 2:1\*\*\* |

*ACU는 Intel® Turbo 기술을 사용하여 CPU 빈도를 늘리고 성능을 개선합니다.  성능 증가량은 VM 크기, 워크로드 및 동일한 호스트에서 실행 중인 다른 워크로드에 따라 달라질 수 있습니다.
**ACU는 AMD® Boost 기술을 사용하여 CPU 빈도를 늘리고 성능을 개선합니다.  성능 증가량은 VM 크기, 워크로드 및 동일한 호스트에서 실행 중인 다른 워크로드에 따라 달라질 수 있습니다.
***하이퍼 스레드 및 중첩된 가상화 실행 기능

다양한 크기에 대한 자세한 내용은 다음 링크를 참조하세요.

- [범용](sizes-general.md)
- [메모리에 최적화](sizes-memory.md)
- [컴퓨팅 최적화](sizes-compute.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [Storage에 최적화](sizes-storage.md)
