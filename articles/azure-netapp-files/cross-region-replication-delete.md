---
title: 지역 간 복제 Azure NetApp Files에 대 한 복제 삭제 | Microsoft Docs
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
ms.date: 11/17/2020
ms.author: b-juche
ms.openlocfilehash: e08b69271ba9d115c26418bc5e421ee6c94b031d
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94695710"
---
# <a name="delete-replications"></a>복제 삭제

볼륨 복제를 삭제 하 여 원본 볼륨과 대상 볼륨 간의 복제 연결을 종료할 수 있습니다. 원본 또는 대상 볼륨에서 삭제 작업을 수행할 수 있습니다. 삭제 작업은 복제에 대 한 권한 부여만 제거 합니다. 원본 또는 대상 볼륨은 제거 되지 않습니다. 

## <a name="steps"></a>단계

1. 볼륨 복제를 삭제 하기 전에 복제 피어 링이 손상 되었는지 확인 하십시오.    
    복제 관계 및 [중단 복제 피어 링](cross-region-replication-manage-disaster-recovery.md#break-replication-peering-to-activate-the-destination-volume) [의 상태 표시](cross-region-replication-display-health-status.md) 를 참조 하세요.  

1. 볼륨 복제를 삭제 하려면 원본 또는 대상 볼륨에서 **복제** 를 선택 합니다.  

2. **삭제** 를 클릭합니다.    

3. **예** 를 입력 하 고 **삭제** 를 클릭 하 여 삭제를 확인 합니다.   

    ![복제 삭제](../media/azure-netapp-files/cross-region-replication-delete-replication.png)

## <a name="next-steps"></a>다음 단계  

* [지역 간 복제](cross-region-replication-introduction.md)
* [지역 간 복제 사용을 위한 요구 사항 및 고려 사항](cross-region-replication-requirements-considerations.md)
* [복제 관계의 상태 표시](cross-region-replication-display-health-status.md)
* [지역 간 복제 문제 해결](troubleshoot-cross-region-replication.md)

