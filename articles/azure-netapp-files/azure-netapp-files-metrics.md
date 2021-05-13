---
title: Azure NetApp Files에 대한 메트릭 | Microsoft Docs
description: Azure NetApp Files는 할당된 스토리지, 실제 스토리지 사용량, 볼륨 IOPS 및 대기 시간에 대한 메트릭을 제공합니다. 이 메트릭을 사용하여 사용량 및 성능을 파악합니다.
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
ms.date: 04/12/2021
ms.author: b-juche
ms.openlocfilehash: b581470a886ff73739edfee7f45c64295eeeb1f0
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388614"
---
# <a name="metrics-for-azure-netapp-files"></a>Azure NetApp Files에 대한 메트릭

Azure NetApp Files는 할당된 스토리지, 실제 스토리지 사용량, 볼륨 IOPS 및 대기 시간에 대한 메트릭을 제공합니다. 이러한 메트릭을 분석하여 NetApp 계정의 사용 패턴 및 볼륨 성능을 더 잘 파악할 수 있습니다.  

**용량 풀** 또는 **볼륨** 을 선택하여 용량 풀 또는 볼륨의 메트릭을 찾을 수 있습니다.  그런 다음, **메트릭** 을 클릭하여 사용 가능한 메트릭을 표시합니다. 

[ ![메트릭 풀다운으로 이동하는 방법을 보여 주는 스냅샷](../media/azure-netapp-files/metrics-navigate-volume.png) ](../media/azure-netapp-files/metrics-navigate-volume.png#lightbox)

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>용량 풀에 대한 사용량 메트릭

- ‘할당된 풀 크기’   
    프로비저닝된 풀 크기

- ‘볼륨에 할당된 풀 크기’  
    지정된 용량 풀의 총 볼륨 할당량(GiB)(즉, 용량 풀에 있는 볼륨의 프로비저닝된 크기 총계)입니다.  
    이 크기는 볼륨을 만드는 동안 선택한 크기입니다.  

- ‘사용된 풀 크기’  
    용량 풀의 볼륨에서 사용되는 논리 공간 총계(GiB)입니다.  

- ‘풀의 총 스냅샷 크기’    
    풀에 있는 모든 볼륨의 스냅샷 크기 합계입니다.

## <a name="usage-metrics-for-volumes"></a><a name="volumes"></a>볼륨에 대한 사용량 메트릭

- ‘사용된 볼륨 크기 백분율’    
    스냅샷을 포함하여 사용된 볼륨의 백분율입니다.  
- ‘할당된 볼륨 크기’   
    프로비저닝된 볼륨 크기
- ‘볼륨 할당량 크기’    
    볼륨이 프로비저닝되는 할당량 크기(GiB)입니다.   
- ‘사용된 볼륨 크기’   
    볼륨의 논리적 크기(사용되는 바이트)입니다.  
    이 크기에는 활성 파일 시스템 및 스냅샷에서 사용하는 논리 공간이 포함됩니다.  
- ‘볼륨 스냅샷 크기’   
   볼륨에 있는 모든 스냅샷의 크기입니다.  

## <a name="performance-metrics-for-volumes"></a>볼륨에 대한 성능 메트릭

- ‘평균 읽기 대기 시간’   
    볼륨에서 읽기의 평균 시간(밀리초)입니다.
- ‘평균 쓰기 대기 시간’   
    볼륨에서 쓰기의 평균 시간(밀리초)입니다.
- ‘읽기 IOPS’   
    볼륨에 대한 초당 읽기 수입니다.
- ‘쓰기 IOPS’   
    초당 볼륨에 대한 쓰기 수입니다.
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

> [!NOTE] 
> * 네트워크 전송 크기(예: ‘볼륨 복제 총 전송’ 메트릭)는 지역 간 복제의 원본 또는 대상 볼륨과 다를 수 있습니다. 이 동작은 네트워크 전송 비용을 최소화하는 데 효율적인 복제 엔진을 사용한 결과입니다.
> * 볼륨 복제 메트릭은 현재 복제 관계의 원본이 아니라 복제 대상 볼륨에 대해 채워집니다.

- ‘볼륨 복제 상태가 정상’   
    복제 관계의 조건입니다. 정상 상태는 `1`로 표시됩니다. 비정상 상태는 `0`으로 표시됩니다.

- ‘볼륨 복제 전송 중’    
    볼륨 복제 상태가 ‘전송 중’인지 여부입니다. 
 
- ‘볼륨 복제 지연 시간’   
    미러의 데이터가 원본 뒤에서 지연되는 시간(초)입니다. 

- ‘볼륨 복제 마지막 전송 기간’   
    마지막 전송을 완료하는 데 걸린 시간(초)입니다. 

- ‘볼륨 복제 마지막 전송 크기’    
    마지막 전송의 일부로 전송된 총 바이트 수입니다. 

- ‘볼륨 복제 진행률’    
    현재 전송 작업에 대해 전송된 총 데이터 양입니다. 

- ‘볼륨 복제 총 전송’   
    관계에 대해 전송된 누적 바이트 수입니다. 

## <a name="next-steps"></a>다음 단계

* [Azure NetApp Files의 스토리지 계층 구조 이해](azure-netapp-files-understand-storage-hierarchy.md)
* [용량 풀 설정](azure-netapp-files-set-up-capacity-pool.md)
* [Azure NetApp Files에 대한 볼륨 만들기](azure-netapp-files-create-volumes.md)
