---
title: Azure NetApp Files의 수동 QoS 용량 풀 Microsoft Docs
description: 수동 QoS 용량 풀 및 추가 정보에 대 한 참조를 소개 합니다.
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
ms.date: 10/12/2020
ms.author: b-juche
ms.openlocfilehash: 239b985bf45942afbff8ca49d7e15ead24456304
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91993990"
---
# <a name="manual-qos-capacity-pool"></a>수동 QoS 용량 풀

이 문서에서는 수동 QoS (서비스 품질) 용량 풀 기능에 대해 소개 합니다.

## <a name="how-manual-qos-differs-from-auto-qos"></a>수동 QoS가 자동 QoS와 어떻게 다른 지

[QoS 유형은](azure-netapp-files-understand-storage-hierarchy.md#qos_types) 용량 풀의 특성입니다. Azure NetApp Files에서는 자동 (기본값) 및 수동 이라는 두 가지 QoS 유형의 용량 풀을 제공 합니다.  

*수동* QoS 용량 풀에서는 볼륨에 대 한 용량 및 처리량을 독립적으로 할당할 수 있습니다. 수동 QoS 용량 풀로 만든 모든 볼륨의 총 처리량은 풀의 총 처리량으로 제한됩니다. 이는 풀 크기와 서비스 수준 처리량의 조합에 따라 결정됩니다. 

*자동* QoS 용량 풀에서 처리량은 볼륨에 할당 된 크기 할당량에 비례하여 풀의 볼륨에 자동으로 할당 됩니다.  

QoS 형식에 대 한 고려 사항은 [Azure NetApp Files의 저장소 계층 구조](azure-netapp-files-understand-storage-hierarchy.md) 및 [Azure NetApp Files에 대 한 성능 고려 사항](azure-netapp-files-performance-considerations.md) 을 참조 하세요.

## <a name="example-of-using-manual-qos"></a>수동 QoS 사용 예

예를 들어 SAP HANA 시스템, Oracle 데이터베이스 또는 여러 볼륨이 필요한 다른 워크 로드와 함께 수동 QoS 용량 풀을 사용 하는 경우 용량 풀을 사용 하 여 이러한 응용 프로그램 볼륨을 만들 수 있습니다.  각 볼륨은 응용 프로그램 요구 사항을 충족 하기 위해 개별 크기와 처리량을 제공할 수 있습니다.  이점에 대 한 자세한 내용은 [수동 QoS 용량 풀에 있는 볼륨의 처리량 제한 예](azure-netapp-files-service-levels.md#throughput-limit-examples-of-volumes-in-a-manual-qos-capacity-pool) 를 참조 하세요.  

## <a name="how-to-specify-the-manual-qos-type"></a>수동 QoS 유형을 지정 하는 방법

[용량 풀을 만들](azure-netapp-files-set-up-capacity-pool.md)때 수동 QoS 유형을 사용 하도록 용량 풀을 지정할 수 있습니다.  수동 QoS 유형을 사용 하도록 [기존 용량 풀을 변경할](manage-manual-qos-capacity-pool.md#change-to-qos) 수도 있습니다. 

용량 유형을 수동 QoS로 설정 하는 것은 영구적으로 변경 된 것입니다. 수동 QoS 유형 용량 도구를 자동 QoS 용량 풀로 변환할 수 없습니다. 

수동 QoS 유형을 사용 하려면 [기능을 등록](manage-manual-qos-capacity-pool.md#register-the-feature)해야 합니다.  

## <a name="next-steps"></a>다음 단계

* [수동 QoS 용량 풀 관리](manage-manual-qos-capacity-pool.md)
* [용량 풀 설정](azure-netapp-files-set-up-capacity-pool.md)
* [저장소 계층 구조](azure-netapp-files-understand-storage-hierarchy.md) 
* [Azure NetApp Files에 대한 서비스 수준](azure-netapp-files-service-levels.md)
* [Azure NetApp Files의 성능 고려 사항](azure-netapp-files-performance-considerations.md)
* [Azure NetApp Files 비용 모델](azure-netapp-files-cost-model.md)
* [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
* [NFS 볼륨 만들기](azure-netapp-files-create-volumes.md)
* [SMB 볼륨 만들기](azure-netapp-files-create-volumes-smb.md)
* [이중 프로토콜 볼륨 만들기](create-volumes-dual-protocol.md)
* [Azure NetApp Files에 대한 메트릭](azure-netapp-files-metrics.md)
* [용량 풀 문제 해결](troubleshoot-capacity-pools.md)
