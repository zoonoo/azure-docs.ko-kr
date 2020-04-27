---
title: Azure NetApp Files에 대 한 성능 고려 사항 | Microsoft Docs
description: Azure NetApp Files에 대 한 성능 고려 사항을 설명 합니다.
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
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/26/2020
ms.locfileid: "67454140"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>Azure NetApp Files의 성능 고려 사항

볼륨의 [처리량 한도](azure-netapp-files-service-levels.md) 는 볼륨에 할당 된 할당량 및 선택한 서비스 수준 조합에 의해 결정 됩니다. Azure NetApp Files에 대 한 성능 계획을 세울 때 몇 가지 고려 사항을 이해 해야 합니다. 

## <a name="quota-and-throughput"></a>할당량 및 처리량  

처리량 한도는 현실화 될 실제 성능의 1 개에 불과합니다.  

읽기 및 쓰기 조합, 전송 크기, 임의 또는 순차 패턴, 다른 많은 요인을 포함 하는 일반적인 저장소 성능 고려 사항은 제공 되는 전체 성능에 영향을 주지 않습니다.  

테스트에서 관찰 된 최대 경험적 처리량은 4500 MiB/s입니다.  Premium storage 계층에서 70.31 TiB 볼륨 할당량은이 수준의 성능을 얻기에 충분 한 처리량 제한을 프로 비전 합니다.  

볼륨 할당량을 70.31 TiB 이상으로 할당 하는 것을 고려 하는 경우 추가 데이터를 저장 하기 위해 볼륨에 추가 할당량을 할당할 수 있습니다. 그러나 추가 된 할당량으로 인해 실제 처리량이 증가 하지 않습니다.  

자세한 내용은 [Azure NetApp Files에 대 한 성능 벤치 마크](azure-netapp-files-performance-benchmarks.md) 를 참조 하세요.

## <a name="overprovisioning-the-volume-quota"></a>볼륨 할당량 과도 프로 비전

워크 로드의 성능이 처리량 한계 인 경우 볼륨 할당량을 과도 하 게 프로 비전 하 여 더 높은 처리량 수준을 설정 하 고 더 높은 성능을 달성할 수 있습니다.  

예를 들어 Premium storage 계층의 볼륨에 500 GiB의 데이터만 있지만 128 MiB/s 처리량이 필요한 경우, 처리량 수준이 적절 하 게 설정 되도록 할당량을 2 TiB로 설정할 수 있습니다 (64 MiB/s (TB * 2 TiB = 128 MiB/s).  

더 높은 처리량을 달성 하기 위해 볼륨을 꾸준히 과도 하 게 프로 비전 하는 경우 더 높은 서비스 수준을 대신 사용 하는 것이 좋습니다.  위의 예에서는 대신 Ultra storage 계층 (128 MiB/s TiB * 1 TiB = 128 MiB/s)을 사용 하 여 볼륨 할당량의 절반으로 동일한 처리량 제한을 달성할 수 있습니다.

## <a name="dynamically-increasing-or-decreasing-volume-quota"></a>볼륨 할당량을 동적으로 늘리거나 줄이기

성능 요구 사항이 일시적 이거나 고정 된 기간 동안 성능 요구가 증가 한 경우 볼륨 할당량을 동적으로 늘리거나 줄여서 처리량 제한을 즉시 조정할 수 있습니다.  다음 고려 사항에 유의하세요. 

* IO를 일시 중지할 필요 없이 볼륨 할당량을 늘리거나 줄일 수 있으며, 볼륨에 대 한 액세스가 중단 되거나 영향을 받지 않습니다.  

    볼륨에 대해 활성 i/o 트랜잭션 중에 할당량을 조정할 수 있습니다.  볼륨 할당량은 볼륨에 저장 된 논리적 데이터의 양 이하로 감소할 수 없습니다.

* 볼륨 할당량이 변경 되 면 처리량 제한의 해당 변경 내용이 거의 즉시 이루어집니다. 

    변경 내용은 인터럽트 하거나 볼륨 액세스 또는 i/o에 영향을 주지 않습니다.  

* 볼륨 할당량을 조정 하려면 용량 풀 크기를 변경 해야 합니다.  

    용량 풀 크기는 볼륨 가용성 또는 i/o에 영향을 주지 않고 동적으로 조정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure NetApp Files에 대한 서비스 수준](azure-netapp-files-service-levels.md)
- [Azure NetApp Files의 성능 벤치마크](azure-netapp-files-performance-benchmarks.md)