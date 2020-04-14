---
title: 수동으로 서비스 지금 동기화 문제를 해결하는 방법
description: ServiceNow에 대한 연결을 재설정하여 Microsoft Azure의 경고가 다시 ServiceNow를 호출할 수 있도록 합니다.
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: 9d32b66b7e3ab27012ee671ba8d70735dce73884
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274277"
---
# <a name="how-to-manually-fix-servicenow-sync-problems"></a>수동으로 서비스 지금 동기화 문제를 해결하는 방법

Azure Monitor는 타사 IT 서비스 관리(ITSM) 공급자에 연결할 수 있습니다. ServiceNow는 이러한 공급자 중 하나입니다. 

보안상의 이유로 ServiceNow와의 연결에 사용되는 인증 토큰을 새로 고쳐야 할 수 있습니다.
다음 동기화 프로세스를 사용하여 연결을 다시 활성화하고 토큰을 새로 고칩니다. 


1. 상단 검색 배너에서 솔루션을 검색한 다음 관련 솔루션을 선택합니다.

    ![새 연결](media/itsmc-resync-servicenow/solution-search-8bit.png)

1. 솔루션 화면에서 구독 필터에서 "모두 선택"을 선택한 다음 "ServiceDesk"로 필터링합니다.

    ![새 연결](media/itsmc-resync-servicenow/solutions-list-8bit.png)

1. ITSM 연결의 솔루션을 선택합니다.
1. 왼쪽 배너에서 ITSM 연결을 선택합니다.

    ![새 연결](media/itsmc-resync-servicenow/itsm-connector-8bit.png)

1. 목록에서 각 커넥터를 선택합니다. 
    1. 커넥터 이름을 클릭하여 구성합니다.
    1. 더 이상 사용되지 않는 커넥터 삭제

    1. 파트너 유형에 따라 [이러한 정의에](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-connections) 따라 필드 업데이트

    1. 동기화를 클릭합니다.

       ![새 연결](media/itsmc-resync-servicenow/resync-8bit2.png)

    1. 저장을 클릭합니다.

        ![새 연결](media/itsmc-resync-servicenow/save-8bit.png)

f.    알림을 검토하여 프로세스가 성공으로 완료되었는지 확인합니다. 

## <a name="next-steps"></a>다음 단계

[IT 서비스 관리 연결에](itsmc-connections.md) 대해 자세히 알아보기
