---
title: Azure VM 크기 - 컴퓨팅 최적화 | 마이크로 소프트 문서
description: Azure의 가상 컴퓨터에 사용할 수 있는 다양한 계산 최적화 크기를 나열합니다. 이 시리즈의 크기에 대한 vCPU, 데이터 디스크 및 NIC 수와 스토리지 처리량 및 네트워크 대역폭에 대한 정보를 나열합니다.
services: virtual-machines
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: e08d593f641c42f9ad605fda013206e70a34e52f
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81269638"
---
# <a name="compute-optimized-virtual-machine-sizes"></a>컴퓨팅 최적화 가상 머신 크기

컴퓨팅 최적화된 VM 크기는 CPU 대 메모리 비율이 높습니다. 이러한 크기는 중간 트래픽 웹 서버, 네트워크 어플라이언스, 배치 프로세스 및 응용 프로그램 서버에 적합합니다. 이 문서에서는 vCPU, 데이터 디스크 및 NIC 수에 대한 정보를 제공합니다. 또한 이 그룹화의 각 크기에 대한 저장소 처리량 및 네트워크 대역폭에 대한 정보도 포함되어 있습니다.

[Fsv2 시리즈는](fsv2-series.md) 인텔® 제온® 플래티넘 8168 프로세서를 기반으로 합니다. 3.4GHz의 모든 코어 터보 클럭 속도와 3.7GHz의 최대 단일 코어 터보 주파수를 특징으로 합니다. 인텔® AVX-512 지침은 인텔 확장 형 프로세서에 새로운. 이 지침은 단일 및 이중 정밀 부동 점 작업 모두에서 벡터 처리 워크로드에 최대 2배의 성능 향상을 제공합니다. 즉, 모든 계산 워크로드에 대해 매우 빠릅니다.

시간당 가격이 더 낮은 Fsv2 시리즈는 vCPU당 ACU(Azure 컴퓨팅 단위)를 기준으로 하는 Azure 포트폴리오에서 가격 대비 성능이 가장 좋습니다.

## <a name="other-sizes"></a>기타 크기

- [범용 가상 컴퓨터](sizes-general.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)

## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
