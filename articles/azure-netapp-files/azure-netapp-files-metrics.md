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
ms.date: 10/07/2020
ms.author: b-juche
ms.openlocfilehash: 80746d7cef4bb0a70c6bb9eb6ff2e053ea6c7a0a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91824689"
---
# <a name="metrics-for-azure-netapp-files"></a>Azure NetApp Files에 대한 메트릭

Azure NetApp Files 할당 된 저장소, 실제 저장소 사용량, 볼륨 IOPS 및 대기 시간에 대 한 메트릭을 제공 합니다. 이러한 메트릭을 분석하여 NetApp 계정의 사용 패턴 및 볼륨 성능을 더 잘 파악할 수 있습니다.  

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>용량 풀에 대한 사용량 메트릭

- *풀 할당 크기*   
    프로 비전 된 풀 크기입니다.

- *볼륨 크기에 할당 된 풀*  
    지정 된 용량 풀의 총 볼륨 할당량 (GiB)입니다 (용량 풀에 있는 볼륨의 프로 비전 된 크기).  
    이 크기는 볼륨을 만드는 동안 선택한 크기입니다.  

- *풀 사용 크기*  
    용량 풀의 볼륨에서 사용 되는 총 논리적 공간 (GiB)입니다.  

- *풀의 총 스냅숏 크기*    
    풀에 있는 모든 볼륨의 스냅숏 크기의 합계입니다.

## <a name="usage-metrics-for-volumes"></a><a name="volumes"></a>볼륨에 대한 사용량 메트릭

<!-- ANF-5023: fixed version: 2020.08, 2020.09
- *Percentage Volume Consumed Size*    
    The percentage of the volume consumed, including snapshots.  
-->
- *볼륨 할당 크기*   
    프로 비전 된 볼륨 크기
- *볼륨 할당량 크기*    
    볼륨이 프로 비전 되는 할당량 크기 (GiB)입니다.   
- *사용 된 볼륨 크기*   
    볼륨의 논리적 크기 (사용 된 바이트)입니다.  
    이 크기에는 활성 파일 시스템 및 스냅샷에서 사용하는 논리 공간이 포함됩니다.  
- *볼륨 스냅숏 크기*   
   볼륨에 있는 모든 스냅숏의 크기입니다.  

## <a name="performance-metrics-for-volumes"></a>볼륨에 대 한 성능 메트릭

- *평균 읽기 대기 시간*   
    볼륨 읽기의 평균 시간 (밀리초)입니다.
- *평균 쓰기 대기 시간*   
    볼륨에서 쓰기에 대 한 평균 시간 (밀리초)입니다.
- *읽기 IOPS*   
    볼륨에서 초당 읽기 수입니다.
- *쓰기 IOPS*   
    초당 볼륨에 대 한 쓰기 수입니다.
<!-- These two metrics are not yet available, until ~ 2020.09
- *Read MiB/s*   
    Read throughput in bytes per second.
- *Write MiB/s*   
    Write throughput in bytes per second.
--> 
<!-- ANF-4128; 2020.07
- *Pool Provisioned Throughput*   
    The total throughput a capacity pool can provide to its volumes based on "Pool Provisioned Size" and "Service Level".
- *Pool Allocated to Volume Throughput*   
    The total throughput allocated to volumes in a given capacity pool (that is, the total of the volumes' allocated throughput in the capacity pool).
-->

<!-- ANF-6443; 2020.11
- *Pool Consumed Throughput*    
    The total throughput being consumed by volumes in a given capacity pool.
-->


## <a name="volume-replication-metrics"></a><a name="replication"></a>볼륨 복제 메트릭

- *볼륨 복제 상태가 정상 임*   
    복제 관계의 조건입니다. 정상 상태는로 표시 됩니다 `1` . 비정상 상태는로 표시 됩니다 `0` .

- *볼륨 복제 전송*    
    볼륨 복제 상태가 ' 전송 중 ' 인지 여부입니다. 
 
- *볼륨 복제 지연 시간*   
    미러의 데이터가 원본 뒤에서 지연 되는 시간 (초)입니다. 

- *볼륨 복제 마지막 전송 기간*   
    마지막 전송을 완료 하는 데 걸린 시간 (초)입니다. 

- *볼륨 복제 마지막 전송 크기*    
    마지막 전송의 일부로 전송 된 총 바이트 수입니다. 

- *볼륨 복제 진행률*    
    현재 전송 작업에 대해 전송 된 총 데이터 양입니다. 

- *볼륨 복제 총 전송*   
    관계에 대해 전송 된 누적 바이트 수입니다. 

## <a name="next-steps"></a>다음 단계

* [Azure NetApp Files의 스토리지 계층 구조 이해](azure-netapp-files-understand-storage-hierarchy.md)
* [용량 풀 설정](azure-netapp-files-set-up-capacity-pool.md)
* [Azure NetApp Files에 대한 볼륨 만들기](azure-netapp-files-create-volumes.md)
