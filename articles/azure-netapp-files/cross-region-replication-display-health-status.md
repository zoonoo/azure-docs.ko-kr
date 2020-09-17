---
title: Azure NetApp Files 복제 관계의 상태를 표시 합니다. | Microsoft Docs
description: 원본 볼륨 또는 Azure NetApp Files의 대상 볼륨에서 복제 상태를 보는 방법에 대해 설명 합니다.
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
ms.openlocfilehash: 95c1202fb56e882554d40926e9d5ecec7be49086
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708810"
---
# <a name="display-health-status-of-replication-relationship"></a>복제 관계의 상태를 표시 합니다. 

원본 볼륨 또는 대상 볼륨에서 복제 상태를 볼 수 있습니다.  

## <a name="display-replication-status"></a>복제 상태 표시

1. 원본 볼륨 또는 대상 볼륨에서 하나 이상의 볼륨에 대 한 저장소 서비스에서 **복제** 를 클릭 합니다.

    다음 복제 상태 및 상태 정보가 표시 됩니다.  
    * **끝점 유형** – 볼륨이 복제의 원본 또는 대상 인지 여부를 식별 합니다.
    * **상태** – 복제 관계의 상태를 표시 합니다.
    * **미러 상태** – 다음 값 중 하나를 표시 합니다.
        * *초기화 되지 않음*:  
            이는 피어 링 관계를 만들 때 초기 및 기본 상태입니다. 초기화가 성공적으로 완료 될 때까지 상태는 초기화 되지 않은 상태로 유지 됩니다.
        * *미러됨*:   
            대상 볼륨이 초기화 되었으며 미러링 업데이트를 받을 준비가 되었습니다.
        * *끊어짐*:   
            이는 피어 링 관계를 해제 한 후의 상태입니다. 대상 볼륨이이 `‘RW’` 고 스냅숏이 있는 경우
    * **관계 상태** – 다음 값 중 하나를 표시 합니다. 
        * *유휴 상태*:  
            전송 작업이 진행 중이 아니고 이후 전송은 사용 하지 않도록 설정 되었습니다.
        * *전송*:  
            전송 작업이 진행 중 이며 이후 전송은 사용 하지 않도록 설정 되어 있습니다.
    * **복제 일정** – 초기화 (기준선 복사)가 완료 되 면 증분 미러링 업데이트가 수행 되는 빈도를 표시 합니다.

    * **총 진행률** -현재 전송 작업에 대해 전송 된 데이터의 총 크기 (바이트)를 표시 합니다. 이 크기는 전송 된 실제 비트 이며 원본 및 대상 볼륨이 보고 하는 논리적 공간과 다를 수 있습니다.  

    ![복제 상태](../media/azure-netapp-files/cross-region-replication-health-status.png)

> [!NOTE] 
> 이전 복제 작업이 완료 되지 않은 경우 복제 관계에서 상태가 *비정상* 으로 표시 됩니다. 이 상태는 대용량 볼륨에 대 한 10 분의 전송 시간과 같은 대용량 볼륨을 전송 하는 경우에 발생 합니다. 이 경우 관계 상태가 *전송* 중으로 표시 되 고 상태가 *비정상*으로 표시 됩니다.

## <a name="next-steps"></a>다음 단계  

* [지역 간 복제](cross-region-replication-introduction.md)
* [재해 복구 관리](cross-region-replication-manage-disaster-recovery.md)
* [볼륨 복제 메트릭](azure-netapp-files-metrics.md#replication)
* [지역 간 복제 문제 해결](troubleshoot-cross-region-replication.md)

