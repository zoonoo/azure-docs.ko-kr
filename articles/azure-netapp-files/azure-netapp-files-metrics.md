---
title: Azure NetApp Files에 대한 메트릭 | Microsoft Docs
description: Azure NetApp Files 할당 된 저장소, 실제 저장소 사용량, 볼륨 IOPS 및 대기 시간에 대 한 메트릭을 제공 합니다. 이러한 메트릭을 사용 하 여 사용량 및 성능을 파악 합니다.
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
ms.openlocfilehash: 7e6ab90010d4379c1640f73a8deeba874e601daf
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513811"
---
# <a name="metrics-for-azure-netapp-files"></a>Azure NetApp Files에 대한 메트릭

Azure NetApp Files 할당 된 저장소, 실제 저장소 사용량, 볼륨 IOPS 및 대기 시간에 대 한 메트릭을 제공 합니다. 이러한 메트릭을 분석하여 NetApp 계정의 사용 패턴 및 볼륨 성능을 더 잘 파악할 수 있습니다.  

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>용량 풀에 대한 사용량 메트릭

<!-- 
- *Pool Provisioned Size*  
    The logical space (GiB) the capacity pool is provisioned with.  
    This size is the size you selected during capacity pool creation. 
--> 
- *볼륨 크기에 할당 된 풀*  
    지정 된 용량 풀의 총 볼륨 할당량 (GiB)입니다 (용량 풀에 있는 볼륨의 프로 비전 된 크기).  
    이 크기는 볼륨을 만드는 동안 선택한 크기입니다.  
- *풀 사용 크기*  
    용량 풀의 볼륨에서 사용 되는 총 논리적 공간 (GiB)입니다.  
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
- *사용 된 볼륨 크기*   
    볼륨에 사용 된 총 논리적 공간 (GiB)입니다.  
    이 크기에는 활성 파일 시스템 및 스냅샷에서 사용하는 논리 공간이 포함됩니다.  
- *볼륨 스냅숏 크기*   
   볼륨의 스냅숏에 사용 되는 증분 논리적 공간입니다.  

## <a name="performance-metrics-for-volumes"></a>볼륨에 대 한 성능 메트릭

- *AverageReadLatency*   
    볼륨 읽기의 평균 시간 (밀리초)입니다.
- *AverageWriteLatency*   
    볼륨에서 쓰기에 대 한 평균 시간 (밀리초)입니다.
- *ReadIops*   
    볼륨에서 초당 읽기 수입니다.
- *WriteIops*   
    초당 볼륨에 대 한 쓰기 수입니다.

## <a name="next-steps"></a>다음 단계

* [Azure NetApp Files의 스토리지 계층 구조 이해](azure-netapp-files-understand-storage-hierarchy.md)
* [용량 풀 설정](azure-netapp-files-set-up-capacity-pool.md)
* [Azure NetApp Files에 대한 볼륨 만들기](azure-netapp-files-create-volumes.md)
