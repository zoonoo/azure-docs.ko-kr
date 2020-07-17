---
title: Azure VM 크기-계산에 최적화 됨 | Microsoft Docs
description: Azure의 가상 컴퓨터에 사용할 수 있는 다양 한 계산 최적화 크기를 나열 합니다. 이 시리즈의 크기에 대한 스토리지 처리량 및 네트워크 대역폭뿐만 아니라 vCPU, 데이터 디스크 및 NIC의 수에 대한 정보를 제공합니다.
author: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: b945348f6fabfa698e45c111241fbfcef088d45e
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86058081"
---
# <a name="compute-optimized-virtual-machine-sizes"></a>컴퓨팅 최적화 가상 머신 크기

계산에 최적화 된 VM 크기는 CPU 대 메모리 비율이 높습니다. 이러한 크기는 보통 트래픽 웹 서버, 네트워크 어플라이언스, 일괄 처리 프로세스 및 응용 프로그램 서버에 적합 합니다. 이 문서에서는 vCPUs, 데이터 디스크 및 Nic의 수에 대 한 정보를 제공 합니다. 또한이 그룹의 각 크기에 대 한 저장소 처리량 및 네트워크 대역폭에 대 한 정보도 포함 됩니다.

[Fsv2 시리즈](fsv2-series.md) 는 2 세대 Intel® Xeon® 플래티넘 8272CL (케스케이드 Lake) 프로세서 및 Intel® Xeon® 플래티넘 8168 (Skylake) 프로세서에서 실행 됩니다. 3.4 g h z의 모든 코어 터보 클록 속도와 3.7 g h z의 최대 싱글 코어 터보 주파수를 제공 합니다. Intel® AVX-512 지침은 Intel 확장 가능한 프로세서에 새로 있습니다. 이러한 지침은 단일 및 배정밀도 부동 소수점 연산에 대 한 벡터 처리 워크 로드에 대해 최대 2 배의 성능 향상을 제공 합니다. 즉, 계산 워크 로드에 대해 매우 빠릅니다.

시간당 가격이 더 낮은 Fsv2 시리즈는 vCPU당 ACU(Azure 컴퓨팅 단위)를 기준으로 하는 Azure 포트폴리오에서 가격 대비 성능이 가장 좋습니다.

## <a name="other-sizes"></a>기타 크기

- [범용](sizes-general.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)

## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
