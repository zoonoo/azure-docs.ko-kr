---
title: Azure NetApp 파일에 대 한 비용된 모델 | Microsoft Docs
description: 서비스에서 비용을 관리 하기 위한 Azure NetApp 파일에 대 한 비용 모델을 설명 합니다.
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
ms.openlocfilehash: b06e3366224b90899dd3f9f9439edf897de82794
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524222"
---
# <a name="cost-model-for-azure-netapp-files"></a>Azure NetApp Files 비용 모델 

Azure NetApp 파일에 대 한 비용 모델을 이해 서비스에서 사용자 비용을 관리할 수 있습니다.

## <a name="calculation-of-capacity-consumption"></a>용량 사용량 계산

Azure NetApp 파일 프로 비전 된 저장소 용량에 대 한 요금이 청구 됩니다.  용량 풀을 만들어 프로 비전 된 용량이 할당 됩니다.  용량 풀 기반 $/ 프로 비전-GiB/월 단위로 시간당으로 청구 됩니다. 단일 용량 풀의 최소 크기는 4 TiB 및 용량 풀 이후에 1 TiB 단위로 확장할 수 있습니다. 볼륨 용량 풀 내에서 만들어집니다.  각 볼륨에는 할당량을 할당 된 풀을 프로 비전 된 용량에서 감소 하는 합니다. 할당할 수 있는 볼륨에 범위 92 TiB 최대 100gib 최소에서 할당량입니다.  

활성 볼륨의 경우 할당량에 대 한 용량 소비는 논리 (유효) 용량을 기반으로 합니다.

해당 저장소 할당량을 초과 하는 볼륨의 실제 용량 소비 하는 경우 볼륨 계속 증가할 수 있습니다. 쓰기의 실제 볼륨 크기는 시스템 제한 (100 TiB) 미만으로 여전히 허용 됩니다.  

해당 프로 비전 된 크기에 대 한 용량 풀의 총 사용량의 할당 된 할당량 중 더 큰 실제 합계인 풀 내의 모든 볼륨의 사용량: 

   ![사용된 된 총 용량 계산](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

아래 다이어그램은 이러한 개념을 보여 줍니다.  
* 4 TiB의 프로 비전 된 용량을 사용 하 여 용량 풀을 해야합니다.  풀에는 세 개의 볼륨을 포함 합니다.  
    * 볼륨 1 2 TiB 할당량인을 할당 되어 있고 800 GiB 소비 합니다.  
    * 볼륨 2 1tib 할당량인을 할당 되어 있고 100gib 소비 합니다.  
    * 볼륨 3 500 GiB 할당량인 할당 되었지만 800 GiB 소비율 (초과분)입니다.  
* 용량 풀 4 TiB의 용량 (프로 비전 된 양)에 대 한 측정 됩니다.  
    3.8 용량 TiB을 사용 하는 (2 TiB 및 1tib 할당량 볼륨 1에서 및 2 및 볼륨 3에 대 한 실제 소비량의 800 GiB). 및 용량의 200 GiB 남아 있습니다.

   ![세 개의 볼륨을 사용 하 여 용량 풀](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="overage-in-capacity-consumption"></a>용량 소비 초과  

총 풀의 용량을 사용 하는 경우 해당 프로 비전 된 용량을 초과 하, 기록 된 데이터는 여전히 허용 합니다.  유예 기간 (1 시간) 후 풀의 사용된 된 용량에는 여전히 해당 프로 비전 된 용량을 초과 하는 경우 다음 풀 크기를 자동으로 증가 합니다 1tib 단위로 프로 비전된 된 용량 사용된 된 총 용량을 초과할 때까지 합니다.  예를 들어 위의 그림에서 볼륨 3 계속 증가 하 고 실제 소비량 1.2 TiB 도달 하는 경우 다음 유예 기간 후 풀 자동으로 크기가 조정 됩니다 5tib입니다.  결과 사용된 된 용량 (TiB 4.2)을 초과 하는 프로 비전 된 풀 용량 (TiB 5).  

## <a name="manual-changes-of-the-pool-size"></a>풀 크기의 수동 변경 내용  

수동으로 증가 하거나 풀 크기를 줄일 수 있습니다. 그러나 다음 제약 조건이 적용 됩니다.
* 서비스 최소 및 최대 제한  
    에 대 한 문서를 참조 하세요 [리소스 제한](azure-netapp-files-resource-limits.md)합니다.
* 초기 4 TiB 최소 구입한 후에 1-TiB 증가
* 1 시간 동안 최소 요금 청구 증분
* 총 풀의 용량을 사용 하는 보다 작은 값으로 프로 비전 된 풀 크기를 줄일 수 있습니다.

## <a name="behavior-of-maximum-size-pool-overage"></a>최대 크기 풀 초과분의 동작   

만들거나 크기를 조정할 수 있는 용량 풀의 최대 크기는 500tib 합니다.  총 용량 풀의 용량을 사용 하는 경우를 초과 500tib, 다음과 같은 경우 발생 합니다.
* 데이터 쓰기 (볼륨이 경우 시스템에 대 한 최대 100tib 아래)도 허용 됩니다.
* 1 시간의 유예 기간 후 풀을 자동으로 크기가 조정 됩니다 1 TiB 단위로 프로 비전 된 풀 용량이 사용된 된 총 용량을 초과할 때까지 합니다.
* 추가 프로 비전 되며 청구 풀 용량 초과 500tib를 사용 하 여 볼륨 할당량을 할당할 수 없습니다. 또한 해당 추가 확장 성능 QoS 제한에 사용할 수 없습니다.  
    참조 [서비스 수준](azure-netapp-files-service-levels.md) QoS 크기 조정 및 성능 제한에 대 한 합니다.

아래 다이어그램은 이러한 개념을 보여 줍니다.
* 프리미엄 저장소 계층 및 500 TiB 용량을 사용 하 여 용량 풀을 해야합니다. 풀에는 9 개 볼륨을 포함 합니다.
    * 볼륨 1 8 ~ 60 TiB의 할당량을 할당 됩니다.  총 사용 되는 용량은 480 TiB입니다.  
        각 볼륨에 QoS 최대 3.75 초 처리량 (60 TiB * 64 초)입니다.  
    * 볼륨 9 20 TiB의 할당량을 할당 됩니다.  
        볼륨 9에 QoS 최대 1.25 GiB/s의 처리량 (60 TiB * 64 초)입니다.
* 볼륨 9는는 초과 시나리오입니다. 25 TiB 실제 소비량의 있습니다.  
    * 1 시간의 유예 기간 후 용량 풀 505 TiB로 조정 됩니다.  
        총 용량을 사용 하는, 8 * 60 =-8 통해 볼륨 1에서 25 TiB 볼륨 9에 대 한 실제 소비량의에 대 한 TiB 할당량입니다.
    * 청구 용량은 505 TiB입니다.
    * (풀에 할당 된 총 할당량 500tib를 초과할 수 없습니다) 때문에 볼륨 9에 대 한 볼륨 할당량을 늘릴 수 없습니다.
    * (풀에 대 한 총 QoS 기반 않기 때문에 여전히 500tib) 추가 QoS 처리량을 할당할 수 있습니다.

   ![9 개의 볼륨을 사용 하 여 용량 풀](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-nine-vols.png)

## <a name="capacity-consumption-of-snapshots"></a>스냅숏 용량 소비 

Azure NetApp 파일의 스냅숏 용량 소비 부모 볼륨의 할당량에 대해 요금이 청구 됩니다.  결과적으로, 동일한 요율 볼륨이 속한 용량 풀으로 공유 됩니다.  그러나 활성 볼륨 달리 스냅숏 소비 증분 소비 된 용량에 따라 측정 됩니다.  Azure NetApp 파일 스냅숏은 기본적으로 차등입니다. 데이터 변경 률에 따라 스냅숏을 자주 활성 볼륨의 논리 용량 보다 훨씬 적은 용량을 사용합니다. 예를 들어만 10gib 차등 데이터를 포함 하는 500 GiB 볼륨의 스냅숏이 있다고 가정 합니다. 해당 스냅숏에 대 한 볼륨 할당량에 대해 청구 되는 용량 10gib 500 GiB 되지 것입니다. 

## <a name="next-steps"></a>다음 단계

* [가격 책정 페이지 azure NetApp 파일](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Azure NetApp Files에 대한 서비스 수준](azure-netapp-files-service-levels.md)
* [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
