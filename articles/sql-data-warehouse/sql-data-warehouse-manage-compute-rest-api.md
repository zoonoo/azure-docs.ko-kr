---
title: Azure SQL Data Warehouse의 REST를 사용한 일시 중지, 다시 시작, 크기 조정 | Microsoft Docs
description: REST API를 통해 SQL Data Warehouse에서 계산 능력을 관리합니다.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/29/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 5b8652a0b08b426e708a909ff988e51eee9c0821
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476065"
---
# <a name="rest-apis-for-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse용 REST API
Azure SQL Data Warehouse에서 계산을 관리하기 위한 REST API입니다.

## <a name="scale-compute"></a>계산 조정
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

## <a name="pause-compute"></a>계산 일시 중지

데이터베이스를 일시 중지하려면 [데이터베이스 일시 중지](/rest/api/sql/databases/pause) REST API를 사용합니다. 다음 예에서는 Server01 서버에서 호스트하는 이름이 Database02인 데이터베이스를 일시 중지합니다. 서버는 이름이 ResourceGroup1인 Azure 리소스 그룹 내에 있습니다.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>계산 다시 시작

데이터베이스를 시작하려면 [데이터베이스 다시 시작](/rest/api/sql/databases/resume) REST API를 사용합니다. 다음 예에서는 Server01 서버에서 호스팅되는 이름이 Database02인 데이터베이스를 시작합니다. 서버는 이름이 ResourceGroup1인 Azure 리소스 그룹 내에 있습니다. 

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>데이터베이스 상태 확인

> [!NOTE]
> 현재 데이터베이스에서 연결 오류가 발생 하 고 온라인 워크플로 완료 하는 동안 데이터베이스 상태 확인 온라인을 반환할 수 있습니다. 연결 시도 트리거하려면이 API 호출을 사용 하는 경우 2 ~ 3 분 지연 응용 프로그램 코드에 추가 해야 합니다.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01 HTTP/1.1
```

## <a name="get-maintenance-schedule"></a>유지 관리 일정 가져오기
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
자세한 내용은 [계산 관리](sql-data-warehouse-manage-compute-overview.md)를 참조하세요.

