---
title: Azure NetApp Files 지역 간 복제를 위한 볼륨 복제 또는 볼륨 삭제 | Microsoft Docs
description: 원본 볼륨과 대상 볼륨 사이에 더 이상 필요 하지 않은 복제 연결을 삭제 하는 방법을 설명 합니다.
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
ms.date: 11/18/2020
ms.author: b-juche
ms.openlocfilehash: 5ce7a591acd8203775808457219b0ec392cd696e
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2020
ms.locfileid: "95249897"
---
# <a name="delete-volume-replications-or-volumes"></a>볼륨 복제 또는 볼륨 삭제

이 문서에서는 볼륨 복제를 삭제 하는 방법을 설명 합니다. 또한 원본 또는 대상 볼륨을 삭제 하는 방법을 설명 합니다.

## <a name="delete-volume-replications"></a>볼륨 복제 삭제

볼륨 복제를 삭제 하 여 원본 볼륨과 대상 볼륨 간의 복제 연결을 종료할 수 있습니다. 대상 볼륨에서 복제를 삭제 해야 합니다. 삭제 작업은 복제에 대 한 권한 부여만 제거 합니다. 원본 또는 대상 볼륨은 제거 되지 않습니다. 

1. 볼륨 복제를 삭제 하기 전에 복제 피어 링이 손상 되었는지 확인 하십시오. 복제 피어 링을 중단 하려면: 

    1. *대상* 볼륨을 선택 합니다. 저장소 서비스에서 **복제** 를 클릭 합니다.  

    2.  계속 하기 전에 다음 필드를 확인 하십시오.  
        * 미러 상태가 ***미러된** _를 표시 하는지 확인 합니다.   
            미러 상태가 _Uninitialized *로 표시 되는 경우 복제 피어 링을 중단 하지 마십시오.
        * 관계 상태가 ***Idle** _을 표시 하는지 확인 합니다.   
            관계 상태가 _Transferring *로 표시 되는 경우 복제 피어 링을 중단 하려고 하지 마세요.   

        [복제 관계의 상태 표시를](cross-region-replication-display-health-status.md)참조 하세요. 

    3.  **피어 링 끊기** 를 클릭 합니다.  

    4.  메시지가 표시 되 면 **예** 를 입력 하 고 **중단** 을 클릭 합니다. 

        ![복제 피어 링 중단](../media/azure-netapp-files/cross-region-replication-break-replication-peering.png)


1. 볼륨 복제를 삭제 하려면 원본 또는 대상 볼륨에서 **복제** 를 선택 합니다.  

2. **삭제** 를 클릭합니다.    

3. **예** 를 입력 하 고 **삭제** 를 클릭 하 여 삭제를 확인 합니다.   

    ![복제 삭제](../media/azure-netapp-files/cross-region-replication-delete-replication.png)

## <a name="delete-source-or-destination-volumes"></a>원본 또는 대상 볼륨 삭제

원본 또는 대상 볼륨을 삭제 하려면 설명 된 순서 대로 다음 단계를 수행 해야 합니다. 그렇지 않으면 `Volume with replication cannot be deleted` 오류가 발생 합니다.  

1. 대상 볼륨에서 [볼륨 복제를 삭제](#delete-volume-replications)합니다.   

2. 필요에 따라 볼륨 이름을 마우스 오른쪽 단추로 클릭 하 고 **삭제** 를 선택 하 여 대상 또는 원본 볼륨을 삭제 합니다.   

    ![볼륨의 오른쪽 클릭 메뉴를 표시 하는 스크린샷](../media/azure-netapp-files/cross-region-replication-delete-volume.png)

## <a name="next-steps"></a>다음 단계  

* [지역 간 복제](cross-region-replication-introduction.md)
* [지역 간 복제 사용을 위한 요구 사항 및 고려 사항](cross-region-replication-requirements-considerations.md)
* [복제 관계의 상태 표시](cross-region-replication-display-health-status.md)
* [지역 간 복제 문제 해결](troubleshoot-cross-region-replication.md)

