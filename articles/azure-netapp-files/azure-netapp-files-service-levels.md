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
ms.date: 10/06/2020
ms.author: b-juche
ms.openlocfilehash: 1c64bd10b34b61797cb3bf3de0cd7d2aa819e795
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91777136"
---
# <a name="service-levels-for-azure-netapp-files"></a>Azure NetApp Files에 대한 서비스 수준
서비스 수준은 용량 풀의 특성입니다. 서비스 수준은 볼륨에 할당 된 할당량을 기준으로 용량 풀의 볼륨에 허용 되는 최대 처리량으로 정의 되 고 구분 됩니다.

## <a name="supported-service-levels"></a>지원 되는 서비스 수준

Azure NetApp Files은 *Ultra*, *Premium*및 *Standard*의 세 가지 서비스 수준을 지원 합니다. 

* <a name="Ultra"></a>Ultra storage

    Ultra storage 계층은 프로 비전 된 용량의 TiB 1 개에 대 한 최대 128 MiB/s 처리량을 제공 합니다. 

* <a name="Premium"></a>Premium storage

    Premium storage 계층은 프로 비전 된 용량의 TiB 1 개에 대 한 최대 64 MiB/s 처리량을 제공 합니다. 

* <a name="Standard"></a>표준 저장소

    표준 저장소 계층은 프로 비전 된 용량의 1 TiB 최대 16 개의 MiB/s를 제공 합니다.

## <a name="throughput-limits"></a>처리량 한도

볼륨의 처리량 제한은 다음 요소의 조합에 따라 결정 됩니다.
* 볼륨이 속한 용량 풀의 서비스 수준
* 볼륨에 할당 된 할당량  
* 용량 풀의 QoS 유형 (*자동* 또는 *수동*)  

### <a name="throughput-limit-examples-of-volumes-in-an-auto-qos-capacity-pool"></a>처리량 제한 자동 QoS 용량 풀의 볼륨 예

아래 다이어그램은 자동 QoS 용량 풀에 있는 볼륨의 처리량 한도 예를 보여 줍니다.

![서비스 수준 그림](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

* 위의 예제 1에서 할당량의 2 TiB 할당 된 Premium storage 계층이 있는 자동 QoS 용량 풀의 볼륨에는 처리량 제한인 128 MiB/s (2 TiB * 64 MiB/s)가 할당 됩니다. 이 시나리오는 용량 풀 크기나 실제 볼륨 소비에 관계 없이 적용 됩니다.

* 위의 예 2에서 100 GiB 할당 된 Premium storage 계층이 있는 자동 QoS 용량 풀의 볼륨에는 6.25 MiB/s (0.09765625 TiB * 64 MiB/s)의 처리량 한도가 할당 됩니다. 이 시나리오는 용량 풀 크기나 실제 볼륨 소비에 관계 없이 적용 됩니다.

### <a name="throughput-limit-examples-of-volumes-in-a-manual-qos-capacity-pool"></a>수동 QoS 용량 풀의 볼륨에 대 한 처리량 제한 예 

수동 QoS 용량 풀을 사용 하는 경우 볼륨의 용량과 처리량을 독립적으로 할당할 수 있습니다. 수동 QoS 용량 풀에서 볼륨을 만들 때 처리량 (MiB/S) 값을 지정할 수 있습니다. 수동 QoS 용량 풀의 볼륨에 할당 되는 총 처리량은 풀의 크기와 서비스 수준에 따라 달라 집니다. (TiB x 서비스 수준 처리량/TiB의 용량 풀 크기)에 의해 제한 됩니다. 예를 들어, TiB 용량 풀의 Ultra service 수준이 인 경우 볼륨에 대해 1280 MiB/s (10 TiB x 128 MiB/s/TiB)의 총 처리량 용량이 있습니다.

SAP HANA 시스템의 경우이 용량 풀을 사용 하 여 다음 볼륨을 만들 수 있습니다. 각 볼륨은 응용 프로그램 요구 사항을 충족 하기 위해 개별 크기와 처리량을 제공 합니다.

* SAP HANA 데이터 볼륨: 크기 4 TiB, 최대 704 MiB/s
* SAP HANA 로그 볼륨: 최대 256 MiB/s를 사용 하는 크기 0.5 TiB
* SAP HANA 공유 볼륨: 크기 1 TiB 최대 64 MiB/s
* SAP HANA 백업 볼륨: 최대 256 MiB/s를 사용 하는 크기 4.5 TiB

아래 다이어그램은 SAP HANA 볼륨의 시나리오를 보여 줍니다.

![QoS SAP HANA 볼륨 시나리오](../media/azure-netapp-files/qos-sap-hana-volume-scenarios.png) 

## <a name="next-steps"></a>다음 단계

- [가격 책정 페이지 Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [Azure NetApp Files 비용 모델](azure-netapp-files-cost-model.md) 
- [용량 풀 설정](azure-netapp-files-set-up-capacity-pool.md)
- [Azure NetApp Files에 대 한 Service Level Agreement(서비스 수준 약정) (SLA)](https://azure.microsoft.com/support/legal/sla/netapp/)
- [볼륨의 서비스 수준을 동적으로 변경](dynamic-change-volume-service-level.md) 
- [지역 간 복제에 대 한 서비스 수준 목표](cross-region-replication-introduction.md#service-level-objectives)
