---
title: 삭제된 Azure Database for MySQL 서버 복원
description: 이 문서에서는 Azure Portal을 사용하여 Azure Database for MySQL에서 삭제된 서버를 복원하는 방법을 설명합니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/09/2020
ms.openlocfilehash: 361af28932226640db077bf4949f7bd472ceb986
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108166580"
---
# <a name="restore-a-deleted-azure-database-for-mysql-server"></a>삭제된 Azure Database for MySQL 서버 복원

서버가 삭제되었을 때 데이터베이스 서버 백업이 서비스에 최대 5일 동안 보존될 수 있습니다. 데이터베이스 백업은 서버가 원래 있었던 Azure 구독에서만 액세스하고 복원할 수 있습니다. 다음 권장 단계를 수행하여 서버 삭제 시점부터 5일 이내에 삭제된 MySQL 서버 리소스를 복구할 수 있습니다. 서버에 대한 백업을 계속 사용할 수 있고 시스템에서 삭제되지 않은 경우에만 권장 단계가 작동합니다. 

## <a name="pre-requisites"></a>필수 구성 요소
삭제된 Azure Database for MySQL 서버를 복원하려면 다음 항목이 필요합니다.
- 원래 서버를 호스팅하는 Azure 구독 이름
- 서버를 만든 위치

## <a name="steps-to-restore"></a>복원 단계

1. Azure portal의 모니터링 블레이드에서 [활동 로그](https://ms.portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade)로 이동합니다. 

2. 활동 로그에서 **필터 추가** 를 표시된 대로 클릭하고 다음과 같이 필터를 설정합니다. 

    - **구독** = 삭제된 서버를 호스팅하는 구독
    - **리소스 종류** = MySQL용 Azure Database 서버(Microsoft.DBforMySQL/servers) 
    - **작업** = MySQL 서버 삭제(Microsoft.DBforMySQL/servers/delete) 
 
     [![MySQL 서버 삭제 작업에 대해 필터링된 활동 로그](./media/howto-restore-dropped-server/activity-log.png)](./media/howto-restore-dropped-server/activity-log.png#lightbox)
   
 3. MySQL Server 삭제 이벤트를 두 번 클릭하고 JSON 탭을 클릭한 다음 JSON 출력에서 "resourceId" 및 "submissionTimestamp" 특성을 확인합니다. resourceId는 다음과 같은 형식입니다: /subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBforMySQL/servers/deletedserver
 
 4. [서버 REST API 만들기 페이지](/rest/api/mysql/singleserver/servers(2017-12-01)/create)로 이동하여 녹색으로 강조 표시된 "시도" 탭을 클릭하고 Azure 계정으로 로그인합니다.
 
 5. 3단계에서 캡처된 resourceId 특성에서 파생된 resourceGroupName, serverName(삭제된 서버 이름) 및 subscriptionId를 제공합니다. 이미지에서와 같이 api-version은 미리 채워져 있습니다.
 
     [![REST API를 사용하여 서버 만들기](./media/howto-restore-dropped-server/create-server-from-rest-api.png)](./media/howto-restore-dropped-server/create-server-from-rest-api.png#lightbox)
  
 6. 요청 본문 섹션에서 아래로 스크롤하고 다음 내용을 붙여넣습니다.
 
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
7. 위의 요청 본문에서 다음 값을 바꿉니다.
   * "Dropped server Location"은 삭제된 서버가 원래 만들어졌던 Azure 지역으로 바꿉니다.
   * "submissionTimestamp" 및 "resourceId"는 3단계에서 캡처된 값으로 바꿉니다. 
   * "restorePointInTime"의 경우 "submissionTimestamp"에서 **15분** 을 뺀 값을 지정하여 명령이 오류를 출력하지 않도록 합니다.
   
8. 응답 코드 201 또는 202가 표시되면 복원 요청이 성공적으로 제출된 것입니다. 

9. 원본 서버에 프로비전된 컴퓨팅 리소스 및 데이터베이스 크기에 따라 서버를 만드는 데 시간이 걸릴 수 있습니다. 복원 상태는 다음에 대해 필터링된 활동 로그에서 모니터링할 수 있습니다. 
   - **구독** = 사용자의 구독
   - **리소스 종류** = MySQL용 Azure Database 서버(Microsoft.DBforMySQL/servers) 
   - **작업** = MySQL Server 만들기 업데이트

## <a name="next-steps"></a>다음 단계
- 5일 이내에 서버를 복원하려고 시도 중인데 앞에서 설명한 단계를 정확하게 따른 후에도 여전히 오류가 발생하는 경우 도움을 받기 위해 지원 인시던트를 엽니다. 5일 후에 삭제된 서버를 복원하려고 시도 중인 경우 백업 파일을 찾을 수 없으므로 오류가 예상됩니다. 이 시나리오에서는 지원 티켓을 열지 않습니다. 백업이 시스템에서 삭제된 경우 지원팀에서는 어떤 도움도 제공할 수 없습니다. 
- 서버를 실수로 삭제하는 것을 방지하려면 [리소스 잠금](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/preventing-the-disaster-of-accidental-deletion-for-your-mysql/ba-p/825222)을 사용하는 것을 강력 권장합니다.
