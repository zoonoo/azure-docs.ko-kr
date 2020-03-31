---
title: Azure NetApp Files에 대한 메트릭 | Microsoft Docs
description: Azure NetApp Files에 대한 메트릭을 설명합니다.
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
ms.date: 03/17/2020
ms.author: b-juche
ms.openlocfilehash: c8e3b616dee1ab4e6bb6e77c6a8bab5661d4e20b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460435"
---
# <a name="metrics-for-azure-netapp-files"></a>Azure NetApp Files에 대한 메트릭

Azure NetApp 파일은 할당된 저장소, 실제 저장소 사용량, 볼륨 IOPS 및 대기 시간에 대한 메트릭을 제공합니다. 이러한 메트릭을 분석하여 NetApp 계정의 사용 패턴 및 볼륨 성능을 더 잘 파악할 수 있습니다.  

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>용량 풀에 대한 사용량 메트릭

<!-- 
- *Pool Provisioned Size*  
    The logical space (GiB) the capacity pool is provisioned with.  
    This size is the size you selected during capacity pool creation. 
--> 
- *볼륨 크기에 할당된 풀*  
    지정된 용량 풀의 총 볼륨 할당량(GiB)입니다(즉, 용량 풀에서 볼륨프로비저닝된 크기의 합계).  
    이 크기는 볼륨 을 만드는 동안 선택한 크기입니다.  
- *풀 소비 크기*  
    용량 풀의 볼륨에서 사용되는 GiB(논리 공간)의 합계입니다.  
<!-- 
- *Pool Consumed Snapshot Size*  
    The total of logical space (GiB) used by snapshots across all volumes in a capacity pool. 
-->

## <a name="usage-metrics-for-volumes"></a><a name="volumes"></a>볼륨에 대한 사용량 메트릭

<!--
- *Volume Quota Size*    
    The quota size (GiB) the volume is provisioned with.   
    This size is the size you selected during capacity pool creation. 
-->
- *볼륨 소비 크기*   
    볼륨(GiB)에 사용되는 총 논리 공간입니다.  
    이 크기에는 활성 파일 시스템 및 스냅샷에서 사용하는 논리 공간이 포함됩니다.  
- *볼륨 스냅샷 크기*   
   볼륨의 스냅숏에서 사용하는 증분 논리 공간입니다.  

## <a name="performance-metrics-for-volumes"></a>볼륨에 대한 성능 메트릭

- *평균판독지연*   
    볼륨에서 읽는 평균 시간(밀리초)입니다.
- *평균작성지연*   
    볼륨에서 쓰기의 평균 시간(밀리초)입니다.
- *읽기 약스*   
    초당 볼륨에 대한 읽기 수입니다.
- *쓰기 이오프스*   
    초당 볼륨에 대한 쓰기 수입니다.

## <a name="next-steps"></a>다음 단계

* [Azure NetApp Files의 스토리지 계층 구조 이해](azure-netapp-files-understand-storage-hierarchy.md)
* [용량 풀 설정](azure-netapp-files-set-up-capacity-pool.md)
* [Azure NetApp Files에 대한 볼륨 만들기](azure-netapp-files-create-volumes.md)
