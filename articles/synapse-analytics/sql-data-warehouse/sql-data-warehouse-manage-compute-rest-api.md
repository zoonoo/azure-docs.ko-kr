---
title: 전용 SQL 풀(이전의 SQL DW)을 위한 REST API로 일시 중지, 다시 시작, 크기 조정
description: REST API를 통해 Azure Synapse Analytics에서 전용 SQL 풀(이전의 SQL DW)의 컴퓨팅 능력을 관리합니다.
services: synapse-analytics
author: antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/29/2019
ms.author: anvang
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: aab2897b4042657492d04494b589fbaa2605cc6d
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107886787"
---
# <a name="rest-apis-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Azure Synapse Analytics의 전용 SQL 풀(이전의 SQL DW)을 위한 REST API

Azure Synapse Analytics의 전용 SQL 풀(이전의 SQL DW)에 대한 컴퓨팅 관리를 위한 REST API

> [!NOTE]
> 이 문서에 설명된 REST API는 Azure Synapse Analytics 작업 영역에서 만든 전용 SQL 풀에 적용할 수 없습니다. Azure Synapse Analytics 작업 영역에 특별히 사용할 REST API에 대한 자세한 내용은 [Azure Synapse analytics 작업 영역 REST API](/rest/api/synapse/)를 참조하세요.

## <a name="scale-compute"></a>컴퓨팅 크기 조정

데이터 웨어하우스 단위를 변경하려면 [데이터베이스 생성 또는 업데이트](/rest/api/sql/databases/createorupdate) REST API를 사용합니다. 다음 예제에서는 MyServer 서버에서 호스트되는 MySQLDW 데이터베이스에 대한 데이터 웨어하우스 단위를 DW1000으로 설정합니다. 서버는 이름이 ResourceGroup1인 Azure 리소스 그룹 내에 있습니다.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2020-08-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    location: "West Central US",
    "sku": {
    "name": "DW200c"
    }
}
```

## <a name="pause-compute"></a>컴퓨팅 일시 중지

데이터베이스를 일시 중지하려면 [데이터베이스 일시 중지](/rest/api/sql/databases/pause) REST API를 사용합니다. 다음 예에서는 Server01 서버에서 호스트하는 이름이 Database02인 데이터베이스를 일시 중지합니다. 서버는 이름이 ResourceGroup1인 Azure 리소스 그룹 내에 있습니다.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2020-08-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>컴퓨팅 다시 시작

데이터베이스를 시작하려면 [데이터베이스 다시 시작](/rest/api/sql/databases/resume) REST API를 사용합니다. 다음 예에서는 Server01 서버에서 호스팅되는 이름이 Database02인 데이터베이스를 시작합니다. 서버는 이름이 ResourceGroup1인 Azure 리소스 그룹 내에 있습니다.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2020-08-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>데이터베이스 상태 확인

> [!NOTE]
> 데이터베이스가 온라인 워크플로를 완료하는 동안 현재 데이터베이스 확인 상태가 온라인으로 반환되어 연결 오류가 발생할 수 있습니다. 이 API 호출을 사용하여 연결 시도를 트리거하는 경우 애플리케이션 코드에 2~3분 지연 시간을 추가해야 합니다.

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Sql/servers/{serverName}/databases/{databaseName}?api-version=2020-08-01-preview
```

## <a name="get-maintenance-schedule"></a>유지 관리 일정 가져오기

전용 SQL 풀(이전의 SQL DW)에 설정된 유지 관리 일정을 확인합니다.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

```

## <a name="set-maintenance-schedule"></a>유지 관리 일정 설정

기존 전용 SQL 풀(이전의 SQL DW)에서 유지 관리 일정을 설정하고 업데이트합니다.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

{
    "properties": {
        "timeRanges": [
                {
                                "dayOfWeek": "Saturday",
                                "startTime": "00:00",
                                "duration": "08:00",
                },
                {
                                "dayOfWeek": "Wednesday",
                                "startTime": "00:00",
                                "duration": "08:00",
                }
                ]
    }
}

```

## <a name="next-steps"></a>다음 단계

자세한 내용은 [컴퓨팅 관리](sql-data-warehouse-manage-compute-overview.md)를 참조하세요.
