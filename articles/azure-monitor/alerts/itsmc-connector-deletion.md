---
title: 사용하지 않는 ITSM 커넥터 삭제
description: 이 문서에서는 ITSM 커넥터 및 ITSM 커넥터와 연결된 작업 그룹을 삭제하는 방법을 설명합니다.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/29/2020
ms.custom: references_regions
ms.openlocfilehash: 3dc84ea6def9b762527226dbeb3e2eaab78ec200
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387941"
---
# <a name="delete-unused-itsm-connectors"></a>사용하지 않는 ITSM 커넥터 삭제

사용하지 않는 ITSM(IT 서비스 관리) 커넥터 삭제는 두 단계로 진행됩니다. ITSM 커넥터와 연결된 모든 작업을 삭제한 다음 커넥터 자체를 삭제합니다. 커넥터 없이 작업을 수행하면 구독에서 오류가 발생할 수 있으므로 먼저 작업을 삭제해야 합니다.

## <a name="delete-associated-actions"></a>연결된 작업 삭제

1. Azure Portal에서 **모니터** 를 선택합니다.
  
    ![모니터 선택의 스크린샷.](media/itsmc-connector-deletion/itsmc-monitor-selection.png)

2. **경고** 를 선택합니다.
   
    ![경고 선택의 스크린샷.](media/itsmc-connector-deletion/itsmc-alert-selection.png)

3. **작업 관리** 를 선택합니다.
   
    ![작업 관리 선택의 스크린샷.](media/itsmc-connector-deletion/itsmc-actions-selection.png)

4. 삭제할 ITSM 커넥터와 연결된 작업 그룹을 선택합니다. 이 문서에서는 Cherwell 커넥터 예시를 사용합니다.
   
    ![Cherwell 커넥터와 연결된 작업의 스크린샷.](media/itsmc-connector-deletion/itsmc-actions-screen.png)

5. 정보를 검토한 다음 **작업 그룹 삭제** 를 선택합니다.

    ![작업 그룹 정보 및 그룹 삭제 단추의 스크린샷.](media/itsmc-connector-deletion/itsmc-action-deletion.png)

## <a name="delete-the-connector"></a>커넥터 삭제

1. 검색 창에서 **servicedesk** 를 검색합니다. 그런 다음 리소스 목록에서 **ServiceDesk** 를 선택합니다.

    ![ServiceDesk 검색 및 선택의 스크린샷.](media/itsmc-connector-deletion/itsmc-connector-selection.png)

2. **ITSM 연결** 을 선택하고 Cherwell 커넥터를 선택합니다.

    ![Cherwell I T S M 커넥터의 스크린샷.](media/itsmc-connector-deletion/itsmc-cherwell-connector.png)

3. **삭제** 를 선택합니다.

    ![I T S M 커넥터 삭제 단추의 스크린샷.](media/itsmc-connector-deletion/itsmc-connector-deletion.png)

## <a name="next-steps"></a>다음 단계

* [ITSM 커넥터의 문제 해결](./itsmc-resync-servicenow.md)
