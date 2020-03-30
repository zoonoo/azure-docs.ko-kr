---
title: Azure NetApp 파일의 비용 모델 | 마이크로 소프트 문서
description: 서비스의 비용을 관리하기 위한 Azure NetApp 파일의 비용 모델을 설명합니다.
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
ms.date: 05/01/2019
ms.author: b-juche
ms.openlocfilehash: aea783b818550b8219e1a0498256280f61f678e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70995109"
---
# <a name="cost-model-for-azure-netapp-files"></a>Azure NetApp Files 비용 모델 

Azure NetApp 파일의 비용 모델을 이해하면 서비스에서 비용을 관리하는 데 도움이 됩니다.

## <a name="calculation-of-capacity-consumption"></a>용량 소비 계산

Azure NetApp 파일은 프로비저닝된 저장소 용량에 대해 청구됩니다.  프로비저닝된 용량은 용량 풀을 만들어 할당됩니다.  용량 풀은 시간단위로 $/프로비저닝-GiB/월을 기준으로 청구됩니다. 단일 용량 풀의 최소 크기는 4TiB이며 용량 풀은 이후 1TiB 단위로 확장할 수 있습니다. 볼륨은 용량 풀 내에서 만들어집니다.  각 볼륨에는 풀 프로비전된 용량에서 감소하는 할당량이 할당됩니다. 볼륨에 할당할 수 있는 할당량은 최소 100GiB에서 최대 100TiB까지입니다.  

활성 볼륨의 경우 할당량에 대한 용량 소비는 논리적(유효) 용량을 기반으로 합니다.

볼륨의 실제 용량 사용량이 저장소 할당량을 초과하면 볼륨이 계속 증가할 수 있습니다. 실제 볼륨 크기가 시스템 제한(100 TiB)보다 작으면 쓰기가 계속 허용됩니다.  

프로비저닝된 금액에 대해 용량 풀에서 사용된 총 용량은 할당된 할당량 또는 풀 내의 모든 볼륨의 실제 소비량중 더 큰 용량의 합계입니다. 

   ![사용된 총 용량 계산](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

아래 다이어그램은 이러한 개념을 보여 줍니다.  
* 프로비저닝된 용량의 4TiB를 갖춘 용량 풀이 있습니다.  풀에는 세 개의 볼륨이 포함되어 있습니다.  
    * 볼륨 1에는 2TiB의 할당량이 할당되고 800 GiB의 소비가 있습니다.  
    * 볼륨 2에는 1TiB의 할당량이 할당되고 100 GiB의 소비가 있습니다.  
    * 볼륨 3에는 500 GiB의 할당량이 할당되지만 800 GiB의 소비량(초과)이 있습니다.  
* 용량 풀은 4TiB 용량(프로비저닝된 금액)에 대해 계량됩니다.  
    3.8 TiB 용량이 소비됩니다(볼륨 1과 2의 할당량 2TiB 및 볼륨 3의 실제 소비량 800GB). 그리고 200 GiB의 용량이 남아 있습니다.

   ![볼륨 3개인 용량 풀](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="overage-in-capacity-consumption"></a>용량 소비량 초과  

풀의 총 사용 용량이 프로비저닝된 용량을 초과해도 데이터 쓰기는 계속 허용됩니다.  유예 기간(1시간) 후에도 풀의 사용된 용량이 프로비저닝된 용량을 초과하면 프로비저닝된 용량이 총 사용된 용량보다 클 때까지 풀 크기가 1TiB 단위로 자동으로 증가합니다.  예를 들어 위의 그림에서 볼륨 3이 계속 증가하고 실제 사용량이 1.2 TiB에 도달하면 유예 기간 이후에 풀의 크기가 자동으로 5 TiB로 조정됩니다.  그 결과 프로비저닝된 풀 용량(5 TiB)이 사용된 용량(4.2 TiB)을 초과합니다.  

## <a name="manual-changes-of-the-pool-size"></a>풀 크기의 수동 변경  

풀 크기를 수동으로 늘리거나 줄일 수 있습니다. 그러나 다음과 같은 제약 조건이 적용됩니다.
* 서비스 최소 및 최대 한도  
    [리소스 제한에](azure-netapp-files-resource-limits.md)대한 문서를 참조하십시오.
* 초기 4-TiB 최소 구매 후 1TiB 증분
* 1시간 최소 청구 증분
* 프로비저닝된 풀 크기는 풀에서 사용된 총 용량보다 작게 줄어들 지 않을 수 있습니다.

## <a name="behavior-of-maximum-size-pool-overage"></a>최대 크기 풀 초과량의 동작   

만들거나 크기를 조정할 수 있는 용량 풀의 최대 크기는 500 TiB입니다.  용량 풀의 총 사용 용량이 500TiB를 초과하면 다음과 같은 상황이 발생합니다.
* 데이터 쓰기는 여전히 허용됩니다(볼륨이 시스템 최대 100TiB 미만인 경우).
* 1시간의 유예 기간이 지나면 풀프로비저닝된 용량이 총 사용 용량을 초과할 때까지 풀의 크기가 1TiB 단위로 자동으로 조정됩니다.
* 500TiB를 초과하는 추가 프로비저닝 및 청구된 풀 용량을 사용하여 볼륨 할당량을 할당할 수 없습니다. 또한 성능 QoS 제한을 확장하는 데 사용할 수 없습니다.  
    성능 제한 및 QoS 크기 조정에 대한 [서비스 수준을](azure-netapp-files-service-levels.md) 참조하십시오.

아래 다이어그램은 이러한 개념을 보여 줍니다.
* 프리미엄 스토리지 계층과 500TiB 용량의 용량 풀이 있습니다. 풀에는 9개의 볼륨이 포함되어 있습니다.
    * 볼륨 1에서 8까지는 각각 60TiB의 할당량이 할당됩니다.  총 사용 용량은 480 TiB입니다.  
        각 볼륨에는 처리량의 QoS 제한이 3.75 GiB/s입니다(60 TiB * 64 MiB/s).  
    * 볼륨 9에는 20TiB의 할당량이 할당됩니다.  
        볼륨 9에는 처리량의 QoS 제한이 1.25 GiB/s입니다(20 TiB * 64 MiB/s).
* 볼륨 9는 초과 시나리오입니다. 그것은 있다 25 실제 소비의 TiB.  
    * 1시간의 유예 기간이 지나면 용량 풀의 크기가 505 TiB로 조정됩니다.  
        즉, 총 사용 용량 = 볼륨 1 ~ 8에 대한 8 * 60-TiB 할당량, 볼륨 9에 대한 실제 소비의 25 TiB입니다.
    * 청구 된 용량은 505 TiB입니다.
    * 볼륨 9에 대한 볼륨 할당량은 증가할 수 없습니다(풀에 할당된 총 할당량이 500TiB를 초과할 수 없기 때문에).
    * 풀의 총 QoS가 여전히 500 TiB를 기반으로 하기 때문에 추가 QoS 처리량이 할당되지 않을 수 있습니다.

   ![9개의 볼륨을 가진 용량 풀](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-nine-vols.png)

## <a name="capacity-consumption-of-snapshots"></a>스냅숏의 용량 소비 

Azure NetApp 파일의 스냅숏 용량 소비량은 상위 볼륨의 할당량에 대해 청구됩니다.  결과적으로 볼륨이 속한 용량 풀과 동일한 청구 비율을 공유합니다.  그러나 활성 볼륨과 달리 스냅숏 소비는 소비된 증분 용량을 기반으로 측정됩니다.  Azure NetApp 파일 스냅숏은 본질적으로 차등입니다. 데이터의 변경 속도에 따라 스냅숏은 활성 볼륨의 논리적 용량보다 훨씬 적은 용량을 소비하는 경우가 많습니다. 예를 들어 차등 데이터의 10GiB만 포함하는 500-GiB 볼륨의 스냅숏이 있다고 가정합니다. 해당 스냅숏의 볼륨 할당량에 대해 청구되는 용량은 500 GiB가 아닌 10GiB입니다. 

## <a name="next-steps"></a>다음 단계

* [Azure NetApp 파일 가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Azure NetApp Files에 대한 서비스 수준](azure-netapp-files-service-levels.md)
* [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
