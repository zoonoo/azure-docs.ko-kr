---
title: 삭제 된 Azure Database for PostgreSQL 서버 복원
description: 이 문서에서는 Azure Portal를 사용 하 여 Azure Database for PostgreSQL에서 삭제 된 서버를 복원 하는 방법을 설명 합니다.
author: Bashar-MSFT
ms.author: bahusse
ms.service: postgresql
ms.topic: how-to
ms.date: 11/03/2020
ms.openlocfilehash: 81764294cc29ad74d5a77f2055f10498d69b59e5
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93343116"
---
# <a name="restore-a-dropped-azure-database-for-postgresql-server"></a>삭제 된 Azure Database for PostgreSQL 서버 복원

서버를 삭제 하면 서비스에서 데이터베이스 서버 백업이 최대 5 일 동안 유지 될 수 있습니다. 데이터베이스 백업은 서버가 원래 있었던 Azure 구독 에서만 액세스 하 고 복원할 수 있습니다. 다음 권장 단계를 수행 하 여 서버 삭제 시점부터 5 일 이내에 삭제 된 PostgreSQL 서버 리소스를 복구할 수 있습니다. 서버에 대한 백업을 계속 사용할 수 있고 시스템에서 삭제되지 않은 경우에만 권장 단계가 작동합니다. 

## <a name="pre-requisites"></a>필수 구성 요소
삭제 된 Azure Database for PostgreSQL 서버를 복원 하려면 다음이 필요 합니다.
- 원래 서버를 호스트 하는 Azure 구독 이름
- 서버를 만든 위치

## <a name="steps-to-restore"></a>복원 단계

1. [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade)로 이동합니다. **Azure Monitor** 서비스를 선택 하 고 **활동 로그** 를 선택 합니다.

2. 활동 로그에서 표시 된 대로 **필터 추가** 를 클릭 하 고 다음에 대 한 필터를 설정 합니다.

    - **구독** = 삭제 된 서버를 호스트 하는 구독
    - **리소스 종류** = Azure Database for PostgreSQL 서버 (DBforPostgreSQL/servers)
    - **작업** = PostgreSQL 서버 삭제 (DBforPostgreSQL/servers/delete)
 
    ![Delete PostgreSQL 서버 작업에 대해 필터링 된 활동 로그](./media/howto-restore-dropped-server/activity-log-azure.png)

3. **Delete PostgreSQL Server** 이벤트를 선택한 다음 **JSON 탭** 을 선택 합니다. `resourceId` `submissionTimestamp` JSON 출력에서 및 특성을 복사 합니다. ResourceId는 다음과 같은 형식 `/subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/deletedserver` 입니다.


 4. PostgreSQL [서버 REST API 만들기 페이지로](/rest/api/PostgreSQL/servers/create) 이동 하 고 녹색으로 강조 표시 된 **시도** 를 선택 합니다. Azure 계정으로 로그인합니다.

 5. 이전 3 단계에서 캡처된 resourceId 특성 JSON 값에 따라 **resourceGroupName** , **serverName** (deleted Server name), **subscriptionId** 속성을 제공 합니다. 다음 그림에 표시 된 것 처럼, api-version 속성은 미리 채워져 있으며 그대로 유지 될 수 있습니다.

    ![REST API를 사용 하 여 서버 만들기](./media/howto-restore-dropped-server/create-server-from-rest-api-azure.png)
  
 6. 요청 본문 섹션에서 아래를 스크롤하고 "삭제 된 서버 위치", "submissionTimestamp" 및 "resourceId"를 대체 합니다. "RestorePointInTime"의 경우 "submissionTimestamp" 값을 **15 분** 으로 지정 하 여 명령이 오류를 출력 하지 않도록 합니다.
    ```json
        {
          "location": "Dropped Server Location",  
          "properties": 
              {
                  "restorePointInTime": "submissionTimestamp - 15 minutes",
                  "createMode": "PointInTimeRestore",
                  "sourceServerId": "resourceId"
            }
        }
    ```
    예를 들어 현재 시간이 2020-11-02T23:59:59.0000000 Z 인 경우 15 분 이전 복원 시점 (2020-11-02T23:44:59.0000000 Z)의 이전 복원 시점을 권장 합니다.
    > [!Important]
    > 서버를 삭제 한 후 5 일의 시간 제한이 있습니다. 5 일 후 백업 파일을 찾을 수 없으므로 오류가 예상 됩니다.
    
7. 201 또는 202 응답 코드가 표시 되 면 복원 요청이 성공적으로 전송 된 것입니다. 

    서버 생성은 원본 서버에서 프로 비전 된 계산 리소스 및 데이터베이스 크기에 따라 시간이 걸릴 수 있습니다. 복원 상태는 다음에 대해 필터링 하 여 활동 로그에서 모니터링할 수 있습니다. 
   - **Subscription** = 구독
   - **리소스 종류** = Azure Database for PostgreSQL 서버 (DBforPostgreSQL/servers) 
   - **Operation** = Update PostgreSQL Server Create

## <a name="next-steps"></a>다음 단계
- 5 일 이내에 서버를 복원 하려고 하지만 앞에서 설명한 단계를 정확 하 게 수행한 후에도 오류가 계속 발생 하는 경우 지원에 대 한 지원 인시던트를 엽니다. 5 일 후에 삭제 된 서버를 복원 하려는 경우 백업 파일을 찾을 수 없으므로 오류가 예상 됩니다. 이 시나리오에서는 지원 티켓을 열지 마십시오. 시스템에서 백업을 삭제 하는 경우 지원 팀에서 지원을 제공할 수 없습니다. 
- 서버를 실수로 삭제 하는 것을 방지 하려면 [리소스 잠금을](https://techcommunity.microsoft.com/t5/azure-database-for-PostgreSQL/preventing-the-disaster-of-accidental-deletion-for-your-PostgreSQL/ba-p/825222)사용 하는 것이 좋습니다.
