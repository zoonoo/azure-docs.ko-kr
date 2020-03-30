---
title: REST API로 일시 중지, 재개, 확장
description: REST API를 통해 Azure Synapse Analytics 데이터 웨어하우스에서 컴퓨팅 성능을 관리합니다.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/29/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 322f1dfcb709727ddd3a97ea22dbe8243aedca20
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350343"
---
# <a name="rest-apis-for-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse용 REST API
Azure Synapse 분석 데이터 웨어하우스에서 계산을 관리하기 위한 REST API입니다.

## <a name="scale-compute"></a>컴퓨팅 크기 조정
데이터 웨어하우스 단위를 변경하려면 [데이터베이스 생성 또는 업데이트](/rest/api/sql/databases/createorupdate) REST API를 사용합니다. 다음 예제에서는 MyServer 서버에서 호스트되는 MySQLDW 데이터베이스에 대한 데이터 웨어하우스 단위를 DW1000으로 설정합니다. 서버는 이름이 ResourceGroup1인 Azure 리소스 그룹 내에 있습니다.

```
PATCH https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

## <a name="pause-compute"></a>컴퓨팅 일시 중지

데이터베이스를 일시 중지하려면 [데이터베이스 일시 중지](/rest/api/sql/databases/pause) REST API를 사용합니다. 다음 예에서는 Server01 서버에서 호스트하는 이름이 Database02인 데이터베이스를 일시 중지합니다. 서버는 이름이 ResourceGroup1인 Azure 리소스 그룹 내에 있습니다.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>컴퓨팅 다시 시작

데이터베이스를 시작하려면 [데이터베이스 다시 시작](/rest/api/sql/databases/resume) REST API를 사용합니다. 다음 예에서는 Server01 서버에서 호스팅되는 이름이 Database02인 데이터베이스를 시작합니다. 서버는 이름이 ResourceGroup1인 Azure 리소스 그룹 내에 있습니다. 

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>데이터베이스 상태 확인

> [!NOTE]
> 데이터베이스가 온라인 워크플로를 완료하는 동안 데이터베이스 상태가 ONLINE으로 반환되어 연결 오류가 발생할 수 있습니다. 이 API 호출을 사용하여 연결 시도를 트리거하는 경우 응용 프로그램 코드에서 2~3분 지연을 추가해야 할 수 있습니다.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01 HTTP/1.1
```

## <a name="get-maintenance-schedule"></a>유지 보수 일정 받기
데이터 웨어하우스에 대해 설정된 유지 관리 일정을 확인합니다. 

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

```

## <a name="set-maintenance-schedule"></a>유지 관리 일정 설정
기존 데이터 웨어하우스의 유지 관리 일정을 설정하고 업데이트합니다.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

{
    "properties": {
        "timeRanges": [
                {
                                "dayOfWeek": Saturday,
                                "startTime": 00:00,
                                "duration": 08:00,
                },
                {
                                "dayOfWeek": Wednesday
                                "startTime": 00:00,
                                "duration": 08:00,
                }
                ]
    }
}

```


## <a name="next-steps"></a>다음 단계
자세한 내용은 [컴퓨팅 관리](sql-data-warehouse-manage-compute-overview.md)를 참조하세요.

