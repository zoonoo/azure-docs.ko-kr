---
title: Azure NetApp Files 지역 간 복제를 사용 하 여 재해 복구 관리 | Microsoft Docs
description: Azure NetApp Files 지역 간 복제를 사용 하 여 재해 복구를 관리 하는 방법을 설명 합니다.
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
ms.topic: how-to
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: ad006279a656758ba856cd3f39c17b0410e525e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90708790"
---
# <a name="manage-disaster-recovery-using-cross-region-replication"></a>지역 간 복제를 사용 하 여 재해 복구 관리 

원본 볼륨과 대상 볼륨 간에 진행 중인 복제 ( [복제 피어 링 만들기](cross-region-replication-create-peering.md)참조)는 재해 복구 이벤트를 준비 합니다. 

이러한 이벤트가 발생 하는 경우 [대상 볼륨으로 장애 조치 (failover)](#break-replication-peering-to-activate-the-destination-volume)하 여 클라이언트가 대상 볼륨을 읽고 쓸 수 있게 합니다. 

재해 복구 후 원본 볼륨 데이터를 대상 볼륨 데이터로 덮어쓰는 다시 [동기화 작업](#resync-replication-to-reactivate-the-source-volume) 을 사용 하 여 원본 볼륨으로 장애 복구 (failback) 할 수 있습니다.  그런 다음 [원본-대상 복제를 다시 설정](#reestablish-source-to-destination-replication) 하 고 클라이언트가 액세스할 원본 볼륨을 다시 탑재 합니다. 

세부 정보는 아래에 설명 되어 있습니다. 

## <a name="break-replication-peering-to-activate-the-destination-volume"></a>복제 피어 링을 중단 하 여 대상 볼륨 활성화

대상 볼륨을 활성화 해야 하는 경우 (예: 대상 지역으로 장애 조치 (failover) 하려는 경우) 복제 피어 링을 중단 하 고 대상 볼륨을 탑재 해야 합니다.  

1. 복제 피어 링을 중단 하려면 대상 볼륨을 선택 합니다. 저장소 서비스에서 **복제** 를 클릭 합니다.  

2.  계속 하기 전에 다음 필드를 확인 하십시오.  
    * 미러 상태가 ***미러된***것으로 표시 되는지 확인 합니다.   
        미러 상태가 *초기화 되지 않음*으로 표시 되는 경우 복제 피어 링을 중단 하지 마십시오.
    * 관계 상태가 ***유휴***상태를 표시 하는지 확인 합니다.   
        관계 상태가 *전송*중으로 표시 되는 경우 복제 피어 링을 중단 하지 마십시오.   

    [복제 관계의 상태 표시를](cross-region-replication-display-health-status.md)참조 하세요. 

3.  **피어 링 끊기**를 클릭 합니다.  

4.  메시지가 표시 되 면 **예** 를 입력 하 고 **중단** 단추를 클릭 합니다. 

    ![복제 피어 링 중단](../media/azure-netapp-files/cross-region-replication-break-replication-peering.png)

5.  [Windows 또는 Linux 가상 머신에 대 한 볼륨 탑재 또는 분리](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)의 단계를 수행 하 여 대상 볼륨을 탑재 합니다.   
    이 단계를 통해 클라이언트는 대상 볼륨에 액세스할 수 있습니다.

## <a name="resync-replication-to-reactivate-the-source-volume"></a>복제를 다시 동기화 하 여 원본 볼륨 다시 활성화   

재해 복구 후 다시 동기화 작업을 수행 하 여 원본 볼륨을 다시 활성화할 수 있습니다.  다시 동기화 작업은 복제 프로세스를 반대로 하 고 대상 볼륨에서 원본 볼륨으로 데이터를 동기화 합니다.  

> [!IMPORTANT] 
> 다시 동기화 작업은 원본 볼륨 데이터를 대상 볼륨 데이터로 덮어씁니다.  UI는 데이터 손실 가능성을 경고 합니다. 작업이 시작 되기 전에 다시 동기화 작업을 확인 하 라는 메시지가 표시 됩니다.

1. 복제를 다시 동기화 하려면 *원본* 볼륨을 선택 합니다. 저장소 서비스에서 **복제** 를 클릭 합니다. 그런 다음 다시 **동기화**를 클릭 합니다.  

2. 메시지가 표시 되 면 **예** 를 입력 하 고 다시 **동기화** 단추를 클릭 합니다. 
 
    ![복제 다시 동기화](../media/azure-netapp-files/cross-region-replication-resync-replication.png)

3. [복제 관계 상태 표시](cross-region-replication-display-health-status.md)의 단계에 따라 원본 볼륨 상태를 모니터링 합니다.   
    원본 볼륨 상태에 다음 값이 표시 되 면 다시 동기화 작업이 완료 되 고 대상 볼륨의 변경 내용이 이제 원본 볼륨에 캡처됩니다.   

    * 미러된 상태: *미러됨*  
    * 전송 상태: *유휴* 상태  

## <a name="reestablish-source-to-destination-replication"></a>원본-대상 복제 다시 설정

대상에서 원본으로 다시 동기화 작업을 완료 한 후에는 복제 피어 링을 다시 중단 하 여 원본-대상 복제를 다시 설정 해야 합니다. 또한 클라이언트에서 액세스할 수 있도록 원본 볼륨을 다시 탑재 해야 합니다.  

1. 복제 피어 링을 중단 합니다.  
    a. *대상* 볼륨을 선택 합니다. 저장소 서비스에서 **복제** 를 클릭 합니다.  
    b. 계속 하기 전에 다음 필드를 확인 하십시오.   
    * 미러 상태가 ***미러된***것으로 표시 되는지 확인 합니다.   
    미러 상태가 *초기화 되지 않음*으로 표시 되는 경우 복제 피어 링을 중단 하지 마십시오.  
    * 관계 상태가 ***유휴***상태를 표시 하는지 확인 합니다.   
    관계 상태가 *전송*중으로 표시 되는 경우 복제 피어 링을 중단 하지 마십시오.    

        [복제 관계의 상태 표시를](cross-region-replication-display-health-status.md)참조 하세요. 

    다. **피어 링 끊기**를 클릭 합니다.   
    d. 메시지가 표시 되 면 **예** 를 입력 하 고 **중단** 단추를 클릭 합니다.  

2. 원본 볼륨을 대상 볼륨으로 다시 동기화 합니다.  
    a. *대상* 볼륨을 선택 합니다. 저장소 서비스에서 **복제** 를 클릭 합니다. 그런 다음 다시 **동기화**를 클릭 합니다.   
    b. 메시지가 표시 되 면 **예** 를 입력 하 고 다시 **동기화** 단추를 클릭 합니다.

3. [Windows 또는 Linux 가상 머신에 대 한 볼륨 탑재 또는 분리](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)의 단계를 수행 하 여 원본 볼륨을 다시 탑재 합니다.  
    이 단계를 통해 클라이언트가 원본 볼륨에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계  

* [지역 간 복제](cross-region-replication-introduction.md)
* [지역 간 복제 사용을 위한 요구 사항 및 고려 사항](cross-region-replication-requirements-considerations.md)
* [복제 관계의 상태 표시](cross-region-replication-display-health-status.md)
* [볼륨 복제 메트릭](azure-netapp-files-metrics.md#replication)
* [지역 간 복제 문제 해결](troubleshoot-cross-region-replication.md)

