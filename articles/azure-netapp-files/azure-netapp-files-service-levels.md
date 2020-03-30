---
title: Azure NetApp Files의 서비스 수준 | Microsoft Docs
description: Azure NetApp Files의 서비스 수준에 대한 처리량 성능을 설명합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: b-juche
ms.openlocfilehash: 0398cc6a5336141f51dde26ed7cf4cce8c2c0bb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75832576"
---
# <a name="service-levels-for-azure-netapp-files"></a>Azure NetApp Files에 대한 서비스 수준
서비스 수준은 용량 풀의 특성입니다. 서비스 수준은 볼륨에 할당된 할당량을 기반으로 용량 풀의 볼륨에 대해 허용되는 최대 처리량으로 정의되고 차별화됩니다.

## <a name="supported-service-levels"></a>지원되는 서비스 수준

Azure NetApp 파일은 *울트라,* *프리미엄*및 *표준의*세 가지 서비스 수준을 지원합니다. 

* <a name="Ultra"></a>울트라 스토리지

    울트라 스토리지 계층은 할당된 볼륨 할당량의 1TiB당 최대 128MiB/s의 처리량을 제공합니다. 

* <a name="Premium"></a>Premium Storage

    프리미엄 스토리지 계층은 할당된 볼륨 할당량의 1TiB당 최대 64MiB/s의 처리량을 제공합니다. 

* <a name="Standard"></a>표준 스토리지

    표준 스토리지 계층은 할당된 볼륨 할당량의 1TiB당 최대 16개의 MiB/s처리량을 제공합니다.

## <a name="throughput-limits"></a>처리량 한도

볼륨의 처리량 제한은 다음 요소의 조합에 의해 결정됩니다.
* 볼륨이 속한 용량 풀의 서비스 수준
* 볼륨에 할당된 할당량  

이 개념은 아래 다이어그램에 나와 있습니다.

![서비스 수준 그림](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

위의 예 1에서 할당량 2TiB가 할당된 프리미엄 저장소 계층이 있는 용량 풀의 볼륨에는 처리량 제한이 128MiB/s(2 TiB * 64 MiB/s)로 할당됩니다. 이 시나리오는 용량 풀 크기 또는 실제 볼륨 소비에 관계 없이 적용됩니다.

위의 예 2에서 할당량 100GiB가 할당된 프리미엄 저장소 계층의 용량 풀의 볼륨에는 처리량 제한이 6.25 MiB/s(0.09765625 TiB * 64 MiB/s)로 할당됩니다. 이 시나리오는 용량 풀 크기 또는 실제 볼륨 소비에 관계 없이 적용됩니다.

## <a name="next-steps"></a>다음 단계

- 여러 서비스 수준의 가격은 [Azure NetApp Files 가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/netapp/)를 참조하세요.
- 용량 풀에서 용량 소비를 계산하는 [Azure NetApp 파일에 대한 비용 모델을](azure-netapp-files-cost-model.md) 참조하십시오. 
- [용량 풀 설정](azure-netapp-files-set-up-capacity-pool.md)
- [Azure NetApp 파일에 대한 서비스 수준 계약(SLA)](https://azure.microsoft.com/support/legal/sla/netapp/) 참조