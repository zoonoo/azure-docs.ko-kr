---
title: Azure NetApp 파일의 성능 고려 사항 | 마이크로 소프트 문서
description: Azure NetApp 파일에 대한 성능 고려 사항에 대해 설명합니다.
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
ms.date: 06/25/2019
ms.author: b-juche
ms.openlocfilehash: 97e3c6212edd2ade4eabb96db3543e9b3b68e2ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67454140"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>Azure NetApp Files의 성능 고려 사항

볼륨의 [처리량 제한은](azure-netapp-files-service-levels.md) 볼륨에 할당된 할당량과 선택한 서비스 수준을 조합하여 결정합니다. Azure NetApp 파일에 대한 성능 계획을 세게 만들 때몇 가지 고려 사항을 이해해야 합니다. 

## <a name="quota-and-throughput"></a>할당량 및 처리량  

처리량 제한은 실현될 실제 성능의 결정요인 하나일 뿐입니다.  

읽기 및 쓰기 믹스, 전송 크기, 임의 또는 순차적 패턴 및 기타 여러 요소를 포함한 일반적인 스토리지 성능 고려 사항은 전달되는 총 성능에 기여합니다.  

테스트에서 관찰된 최대 경험적 처리량은 4,500 MiB/s입니다.  프리미엄 저장소 계층에서 70.31 TiB의 볼륨 할당량은 이러한 수준의 성능을 달성할 수 있을 만큼 충분히 높은 처리량 제한을 프로비전합니다.  

70.31 TiB를 초과하는 볼륨 할당량 양을 할당하려는 경우 추가 데이터를 저장하기 위해 볼륨에 추가 할당량이 할당될 수 있습니다. 그러나 추가된 할당량으로 인해 실제 처리량이 더 증가하지는 않습니다.  

자세한 내용은 [Azure NetApp 파일의 성능 벤치마크를](azure-netapp-files-performance-benchmarks.md) 참조하십시오.

## <a name="overprovisioning-the-volume-quota"></a>볼륨 할당량 오버프로비전

워크로드의 성능이 처리량 제한 바인딩인 경우 볼륨 할당량을 오버프로비저닝하여 더 높은 처리량 수준을 설정하고 더 높은 성능을 달성할 수 있습니다.  

예를 들어 프리미엄 저장소 계층의 볼륨에 500GiB의 데이터만 있지만 처리량의 128MiB/s가 필요한 경우 처리량 수준이 적절하게 설정되도록 할당량을 2TiB로 설정할 수 있습니다(TB당 64MiB/s = 2 TiB = 128MiB/s).  

더 높은 처리량을 달성하기 위해 볼륨을 지속적으로 오버프로비저닝하는 경우 대신 더 높은 서비스 수준을 사용하는 것이 좋습니다.  위의 예에서는 울트라 스토리지 계층을 대신 사용하여 볼륨 할당량의 절반으로 동일한 처리량 제한을 달성할 수 있습니다(TiB당 128 MiB/s * 1 TiB = 128 MiB/s).

## <a name="dynamically-increasing-or-decreasing-volume-quota"></a>볼륨 할당량을 동적으로 늘리거나 줄입니다.

성능 요구 사항이 일시적인 경우 또는 일정 기간 동안 성능 요구 사항이 증가한 경우 볼륨 할당량을 동적으로 늘리거나 줄이면 처리량 제한을 즉시 조정할 수 있습니다.  다음 고려 사항에 유의하세요. 

* 볼륨 할당량은 IO를 일시 중지할 필요 없이 증가하거나 감소할 수 있으며 볼륨에 대한 액세스가 중단되거나 영향을 받지 않습니다.  

    활성 I/O 트랜잭션 중에 볼륨에 대해 할당량을 조정할 수 있습니다.  볼륨 할당량은 볼륨에 저장된 논리 데이터의 양 보다 낮을 수 없습니다.

* 볼륨 할당량이 변경되면 처리량 제한의 해당 변경이 거의 즉각적입니다. 

    변경 사항은 볼륨 액세스 또는 I/O를 중단하거나 영향을 미치지 않습니다.  

* 볼륨 할당량을 조정하려면 용량 풀 크기를 변경해야 합니다.  

    용량 풀 크기는 볼륨 가용성 또는 I/O에 영향을 주지 않고 동적으로 조정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure NetApp Files에 대한 서비스 수준](azure-netapp-files-service-levels.md)
- [Azure NetApp Files의 성능 벤치마크](azure-netapp-files-performance-benchmarks.md)