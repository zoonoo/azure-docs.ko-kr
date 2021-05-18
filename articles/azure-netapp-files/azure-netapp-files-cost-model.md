---
title: Azure NetApp Files의 비용 모델 | Microsoft Docs
description: 서비스 비용을 관리하기 위한 Azure NetApp Files의 비용 모델에 대해 설명합니다.
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
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 9c4eebae6909c9ef0969bc85bcb9a985db2a7c02
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91325609"
---
# <a name="cost-model-for-azure-netapp-files"></a>Azure NetApp Files 비용 모델 

Azure NetApp Files의 비용 모델을 이해하면 서비스 비용을 관리하는 데 도움이 됩니다. 

지역 간 복제와 관련된 비용 모델은 [지역 간 복제의 비용 모델](cross-region-replication-introduction.md#cost-model-for-cross-region-replication)을 참조하세요.

## <a name="calculation-of-capacity-consumption"></a>사용량 계산

Azure NetApp Files는 프로비저닝된 스토리지 용량에 대해 요금이 청구됩니다.  프로비저닝된 용량은 용량 풀을 만들어 할당됩니다.  용량 풀은 시간당 증분 단위로 월 $/provisioned-GiB/의 요금이 청구됩니다. 단일 용량 풀의 최소 크기는 4TiB이며 나중에 1TiB 증분으로 용량 풀을 확장할 수 있습니다. 볼륨은 용량 풀 안에 생성됩니다.  각 볼륨에 할당된 할당량만큼 풀에 프로비저닝된 용량에서 차감됩니다. 볼륨에 할당할 수 있는 할당량은 최소 100GiB에서 최대 100TiB 사이입니다.  

활성 볼륨의 경우 할당량 대비 사용량은 논리적(유효) 용량을 기준으로 합니다.

볼륨의 실제 사용량아 스토리지 할당량을 초과할 경우 볼륨을 계속 늘릴 수 있습니다. 실제 볼륨 크기가 시스템 한도(100TiB)보다 작기만 하면 쓰기는 계속 허용됩니다.  

프로비저닝된 용량 대비 용량 풀에서 사용된 총 용량은 풀 내 모든 볼륨의 할당된 할당량 또는 실제 사용량 중에서 더 큰 값의 합계입니다. 

   ![총 사용량 계산](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

아래 다이어그램은 관련 개념을 설명합니다.  
* 프로비저닝된 용량이 4TiB인 용량 풀이 있습니다.  풀에는 세 개의 볼륨이 포함되어 있습니다.  
    * 볼륨 1에는 2TiB 할당량이 할당되었으며 사용량은 800GiB입니다.  
    * 볼륨 2에는 1TiB 할당량이 할당되었으며 사용량은 100GiB입니다.  
    * 볼륨 3에는 500GiB 할당량이 할당되었지만 사용량은 800GiB(초과분)입니다.  
* 용량 풀은 4TiB 용량(프로비저닝된 용량)으로 요금이 청구됩니다.  
    3.8TiB 용량이 사용되었습니다(볼륨 1과 2의 할당량 2TiB 및 1TiB, 볼륨 3의 실제 사용량 800GiB). 그리고 200GiB 용량이 남아 있습니다.

   ![3개의 볼륨이 있는 용량 풀](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="overage-in-capacity-consumption"></a>사용량 초과분  

풀의 총 사용량이 프로비저닝된 용량을 초과해도 데이터 쓰기는 계속 허용됩니다.  유예 기간(1시간) 후에 풀의 사용량이 여전히 프로비저닝된 용량을 초과하면 프로비저닝된 용량이 총 사용량보다 커질 때까지 풀 크기가 1TiB 증분으로 자동으로 증가합니다.  예를 들어 위 그림에서 볼륨 3이 계속 증가하고 실제 사용량이 1.2TiB에 도달하면 유예 기간 후에 풀 크기가 5TiB로 자동으로 조정됩니다.  결과적으로 프로비저닝된 풀 용량(5TiB)이 사용량(4.2TiB)을 초과하게 됩니다.  

용량 풀 크기는 볼륨 수요를 충족하기 위해 자동으로 증가하지만 볼륨 크기가 감소한 경우에는 자동으로 줄어들지 않습니다. 볼륨 크기가 감소한 후(예: 볼륨의 데이터 정리 후) 용량 풀의 크기를 줄이려면 용량 풀 크기를 ‘수동으로’ 줄여야 합니다.

## <a name="manual-changes-of-the-pool-size"></a>풀 크기 수동 변경  

필요한 경우 풀 크기를 수동으로 늘리거나 줄일 수 있습니다. 그러나 다음과 같은 제약 조건이 적용됩니다.
* 서비스 최소 및 최대 한도  
    [리소스 한도](azure-netapp-files-resource-limits.md)에 대한 문서를 참조하세요.
* 초기 4TiB 최소 구매 후 1TiB 증분
* 1시간 최소 청구 증분
* 프로비저닝된 풀 크기를 풀의 총 사용량보다 작게 줄일 수는 없습니다.
* 수동 QoS가 있는 용량 풀의 경우 크기 및 서비스 수준이 모든 볼륨의 실제 할당된 처리량보다 많은 처리량을 제공하는 경우에만 풀 크기를 줄일 수 있습니다.

## <a name="behavior-of-maximum-size-pool-overage"></a>최대 크기 풀 초과분 동작   

만들거나 크기를 조정할 수 있는 용량 풀의 최대 크기는 500TiB입니다.  용량 풀의 총 사용량이 500TiB를 초과하면 다음 상황이 발생합니다.
* 볼륨이 시스템 최대값인 100TiB 미만인 경우 데이터 쓰기가 계속 허용됩니다.
* 1시간 유예 기간 후 풀에서 프로비저닝된 용량이 총 사용량을 초과할 때까지 풀 크기가 1TiB 증분으로 자동으로 조정됩니다.
* 500TiB를 초과하여 추가로 프로비저닝되고 청구된 풀 용량은 볼륨 할당량을 할당하는 데 사용할 수 없습니다. 또한 성능 QoS 한도를 확장하는 데 사용할 수도 없습니다.  
    성능 한도 및 QoS 크기 조정에 대한 자세한 내용은 [서비스 수준](azure-netapp-files-service-levels.md)을 참조하세요.

아래 다이어그램은 관련 개념을 설명합니다.
* Premium Storage 계층과 500TiB 용량을 사용하는 용량 풀이 있습니다. 풀에는 9개 볼륨이 포함되어 있습니다.
    * 볼륨 1~8에는 각각 60TiB 할당량이 할당되었습니다.  총 사용량은 480TiB입니다.  
        각 볼륨의 QoS 한도는 처리량 (60TiB * 64MiB/초)의 3.75GiB/초입니다.  
    * 볼륨 9에는 20TiB 할당량이 할당되었습니다.  
        볼륨 9의 QoS 한도는 처리량 (20TiB * 64MiB/초)의 1.25GiB/초입니다.
* 볼륨 9는 초과분 시나리오입니다. 실제 사용량은 25TiB입니다.  
    * 1시간 유예 기간 후 용량 풀의 크기가 505TiB로 조정됩니다.  
        즉, 볼륨 1~8의 총 사용량은 8 * 60TiB이고 볼륨 9의 실제 사용량은 25TiB입니다.
    * 청구된 용량은 505TiB입니다.
    * 볼륨 9의 볼륨 할당량은 늘릴 수 없습니다(풀에 할당된 총 할당량은 500TiB를 초과할 수 없음).
    * 풀의 총 QoS는 여전히 500TiB를 기반으로 하기 때문에 추가 QoS 처리량을 할당할 수 없습니다.

   ![9개 볼륨이 있는 용량 풀](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-nine-vols.png)

## <a name="capacity-consumption-of-snapshots"></a>스냅샷의 사용량 

Azure NetApp Files의 스냅샷 사용량은 부모 볼륨의 할당량 대비 요금이 청구됩니다.  따라서 볼륨이 속한 용량 풀과 동일한 청구 요금을 공유합니다.  그러나 활성 볼륨과 달리 스냅샷 사용량은 증분 사용량을 기준으로 측정됩니다.  Azure NetApp Files 스냅샷은 특성상 차등 스냅샷입니다. 데이터 변경률에 따라 스냅샷은 활성 볼륨의 논리적 용량보다 훨씬 적은 용량을 사용하는 경우가 많습니다. 예를 들어 500GiB 볼륨 스냅샷에 10GiB 차등 데이터만 포함되어 있다고 가정합니다. 해당 스냅샷의 볼륨 할당량 대비 요금이 청구되는 용량은 500GiB가 아닌 10GiB입니다. 

## <a name="next-steps"></a>다음 단계

* [Azure NetApp Files 가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Azure NetApp Files에 대한 서비스 수준](azure-netapp-files-service-levels.md)
* [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
* [지역 간 복제의 비용 모델](cross-region-replication-introduction.md#cost-model-for-cross-region-replication)
