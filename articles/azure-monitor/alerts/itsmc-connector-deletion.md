---
title: ITSM 커넥터 및 연결 된 작업 삭제
description: 이 문서에서는 ITSM 커넥터와 연결 된 작업 그룹을 삭제 하는 방법에 대해 설명 합니다.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/29/2020
ms.custom: references_regions
ms.openlocfilehash: 1fbaa48d104642984e604bc66a3aa914fbfed44c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100617909"
---
# <a name="deletion-of-unused-itsm-connectors"></a>사용 하지 않는 ITSM 커넥터 삭제

사용 하지 않는 커넥터를 삭제 하는 프로세스에는 다음 두 단계가 포함 됩니다.

1. 연결 된 작업 삭제: ITSM 커넥터와 연결 된 모든 작업을 삭제 해야 합니다. 이렇게 하려면 커넥터 없이 구독에서 오류를 일으킬 수 있는 작업을 수행 하지 않도록 해야 합니다.

2. 사용 하지 않는 ITSM 커넥터를 삭제 합니다.

## <a name="deletion-of-the-associated-actions"></a>연결 된 작업 삭제

1. 작업 그룹을 찾으려면 모니터 선택의 "모니터" 스크린샷으로 이동 해야 합니다  ![ .](media/itsmc-connector-deletion/itsmc-monitor-selection.png)

2. ![경고 선택 항목의 "경고" 스크린샷을 선택 합니다.](media/itsmc-connector-deletion/itsmc-alert-selection.png)
3. ![작업 선택 관리의 "작업 관리" 스크린샷을 선택 합니다.](media/itsmc-connector-deletion/itsmc-actions-selection.png)
4. Cherwell에 연결 된 ITSM 커넥터의 Cherwell 스크린샷에 연결 된 모든 ITSM 커넥터를 선택  ![ 합니다.](media/itsmc-connector-deletion/itsmc-actions-screen.png)
5. 작업  ![ 그룹 삭제의 작업 그룹 스크린샷 삭제 합니다.](media/itsmc-connector-deletion/itsmc-action-deletion.png)

## <a name="deletion-of-the-unused-itsm-connector"></a>사용 하지 않는 ITSM 커넥터 삭제

1. 검색의 위쪽 검색 표시줄 스크린샷에서 "ServiceDesk" LA를 검색 하 고 선택 하  ![ 고 "servicedesk" la를 선택 해야 합니다.](media/itsmc-connector-deletion/itsmc-connector-selection.png)
2. "ITSM 연결"을 선택 하 고  ![ Cherwell ITSM 커넥터의 Cherwell connector 스크린샷을 선택 합니다.](media/itsmc-connector-deletion/itsmc-cherwell-connector.png)
3. ![ITSM 커넥터 삭제의 "삭제" 스크린샷을 선택 합니다.](media/itsmc-connector-deletion/itsmc-connector-deletion.png)

## <a name="next-steps"></a>다음 단계

* [ITSM 커넥터의 문제 해결](./itsmc-resync-servicenow.md)
