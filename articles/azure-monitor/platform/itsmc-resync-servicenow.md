---
title: ServiceNow 동기화 문제를 수동으로 수정 하는 방법
description: Microsoft Azure에서 경고가 다시 ServiceNow를 호출할 수 있도록 ServiceNow에 대 한 연결을 다시 설정 합니다.
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: a3382f93990612b0ab34eb0848cbf3d6577c44ff
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87087937"
---
# <a name="how-to-manually-fix-servicenow-sync-problems"></a>ServiceNow 동기화 문제를 수동으로 수정 하는 방법

Azure Monitor 타사 ITSM (IT Service Management) 공급자에 연결할 수 있습니다. ServiceNow는 이러한 공급자 중 하나입니다.

보안상의 이유로 ServiceNow를 사용 하 여 연결 하는 데 사용 되는 인증 토큰을 새로 고쳐야 할 수도 있습니다.
다음 동기화 프로세스를 사용 하 여 연결을 다시 활성화 하 고 토큰을 새로 고칩니다.


1. 위쪽 검색 배너에서 솔루션을 검색 한 다음 관련 솔루션을 선택 합니다.

    ![새 연결](media/itsmc-resync-servicenow/solution-search-8bit.png)

1. 솔루션 화면에서 구독 필터의 "모두 선택"을 선택한 다음 "ServiceDesk"로 필터링 합니다.

    ![새 연결](media/itsmc-resync-servicenow/solutions-list-8bit.png)

1. ITSM 연결의 솔루션을 선택 합니다.
1. 왼쪽 배너에서 ITSM 연결을 선택 합니다.

    ![새 연결](media/itsmc-resync-servicenow/itsm-connector-8bit.png)

1. 목록에서 각 커넥터를 선택 합니다. 
    1. 커넥터 이름을 클릭 하 여 구성 합니다.
    1. 더 이상 사용 하지 않는 모든 커넥터 삭제

    1. 파트너 유형에 따라 [이러한 정의](./itsmc-connections.md) 에 따라 필드를 업데이트 합니다.

    1. 동기화를 클릭 합니다.

       ![새 연결](media/itsmc-resync-servicenow/resync-8bit2.png)

    1. 저장을 클릭 합니다.

        ![새 연결](media/itsmc-resync-servicenow/save-8bit.png)

f.    알림을 검토 하 여 프로세스가 성공으로 완료 되는지 확인 합니다. 

## <a name="next-steps"></a>다음 단계

[IT Service Management 연결](itsmc-connections.md) 에 대 한 자세한 정보
