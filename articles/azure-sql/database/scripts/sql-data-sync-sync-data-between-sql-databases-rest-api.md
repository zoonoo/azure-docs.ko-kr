---
title: 'REST API: 여러 데이터베이스 간 동기화'
description: REST API 예제 스크립트를 사용하여 여러 데이터베이스 간에 동기화합니다.
services: sql-database
ms.service: sql-database
ms.subservice: sql-data-sync
ms.custom: sqldbrb=1
ms.devlang: REST API
ms.topic: sample
author: MaraSteiu
ms.author: masteiu
ms.reviewer: mathoma
ms.date: 03/12/2019
ms.openlocfilehash: b0e8b12c7a58f3376c4e05819dbe60c9436ecef8
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110693360"
---
# <a name="use-rest-api-to-sync-data-between-multiple-databases"></a>REST API를 사용하여 여러 데이터베이스 간에 데이터 동기화 

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

이 REST API 예제는 여러 데이터베이스 간에 데이터를 동기화하도록 SQL 데이터 동기화를 구성합니다.

SQL 데이터 동기화에 대한 개요는 [Azure의 SQL 데이터 동기화를 사용하여 여러 클라우드 및 온-프레미스 데이터베이스에서 데이터 동기화](../sql-data-sync-data-sql-server-sql-database.md)를 참조하세요.

> [!IMPORTANT]
> SQL 데이터 동기화는 현재 Azure SQL Managed Instance를 지원하지 않습니다.

## <a name="create-sync-group"></a>동기화 그룹 만들기

[만들기 또는 업데이트](/rest/api/sql/syncgroups/createorupdate) 템플릿을 사용하여 동기화 그룹을 만듭니다.
 
동기화 그룹을 만들 때 동기화 스키마(table\column)와 masterSyncMemberName을 전달하지 마세요. 이때 동기화 그룹에는 아직 table\column 정보가 없기 때문입니다.

동기화 그룹을 만들기 위한 샘플 요청: 

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser"
  }
}
```

동기화 그룹을 만들기 위한 샘플 응답:

상태 코드: 200
```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```

상태 코드: 201
```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```

## <a name="create-sync-member"></a>동기화 멤버 만들기

[만들기 또는 업데이트](/rest/api/sql/syncmembers/createorupdate) 템플릿을 사용하여 동기화 멤버를 만듭니다.

동기화 멤버를 만들기 위한 샘플 요청:

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  }
}
```
동기화 멤버를 만들기 위한 샘플 응답:

상태 코드: 200
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

상태 코드: 201
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

## <a name="refresh-schema"></a>스키마 새로 고침

동기화 그룹이 성공적으로 만들어지면 다음 템플릿을 사용하여 스키마를 새로 고칩니다.

[허브 스키마 새로 고침](/rest/api/sql/syncgroups/refreshhubschema) 템플릿을 사용하여 허브 데이터베이스의 스키마를 새로 고칩니다. 

허브 데이터베이스 스키마 새로 고침을 위한 샘플 요청: 

```http
POST https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/refreshHubSchema?api-version=2015-05-01-preview
```

허브 데이터베이스 스키마 새로 고침을 위한 샘플 응답: 

상태 코드: 200

상태 코드: 202

[허브 스키마 나열](/rest/api/sql/syncgroups/listhubschemas) 템플릿을 사용하여 허브 데이터베이스 스키마를 나열합니다. 

[멤버 스키마 새로 고침](/rest/api/sql/syncmembers/refreshmemberschema) 템플릿을 사용하여 멤버 데이터베이스 스키마를 새로 고칩니다. 

[목록 멤버 스키마](/rest/api/sql/syncmembers/listmemberschemas) 템플릿을 사용하여 멤버 데이터베이스 스키마를 나열합니다. 

스키마가 성공적으로 새로 고쳐진 후에만 다음 단계로 진행합니다. 

## <a name="update-sync-group"></a>동기화 그룹 업데이트 

[만들기 또는 업데이트](/rest/api/sql/syncgroups/createorupdate) 템플릿을 사용하여 동기화 그룹을 업데이트합니다.

동기화 스키마를 지정하여 동기화 그룹을 업데이트합니다. 스키마 및 사용하려는 스키마를 보유하는 이름인 masterSyncMemberName을 포함합니다. 

동기화 그룹 업데이트에 대한 샘플 요청: 

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser"
  }
}
```

동기화 그룹 업데이트에 대한 샘플 응답: 

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```
## <a name="update-sync-member"></a>동기화 멤버 업데이트

[만들기 또는 업데이트](/rest/api/sql/syncmembers/createorupdate) 템플릿을 사용하여 동기화 멤버를 업데이트합니다.

동기화 멤버를 업데이트하기 위한 샘플 요청: 

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  }
}
```

동기화 멤버를 업데이트하기 위한 샘플 응답: 

상태 코드: 200
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

상태 코드: 201
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

## <a name="trigger-sync"></a>동기화 트리거

[동기화 트리거](/rest/api/sql/syncgroups/triggersync) 템플릿을 사용하여 동기화 작업을 트리거합니다.

동기화 작업을 트리거하기 위한 샘플 요청: 

```http
POST https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/triggerSync?api-version=2015-05-01-preview
```

동기화 작업을 트리거하기 위한 샘플 응답: 

상태 코드: 200

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/)를 참조하세요.

추가 SQL Database PowerShell 스크립트 샘플은 [Azure SQL Database PowerShell 스크립트](../powershell-script-content-guide.md)에 있습니다.

SQL 데이터 동기화에 대한 자세한 내용은 다음 항목을 참조하세요.

- 개요 - [Azure의 SQL 데이터 동기화를 사용하여 여러 클라우드 및 온-프레미스 데이터베이스에서 데이터 동기화](../sql-data-sync-data-sql-server-sql-database.md)
- 데이터 동기화 설정
    - Azure Portal 사용 - [자습서: Azure SQL Database와 SQL Server 간에 데이터를 동기화하도록 SQL 데이터 동기화 설정](../sql-data-sync-sql-server-configure.md)
    - PowerShell 사용 - [PowerShell을 사용하여 Azure SQL Database의 데이터베이스와 SQL Server 간에 데이터 동기화](sql-data-sync-sync-data-between-azure-onprem.md)
- 데이터 동기화 에이전트 - [Azure의 SQL 데이터 동기화용 데이터 동기화 에이전트](../sql-data-sync-agent-overview.md)
- 모범 사례 - [Azure의 SQL 데이터 동기화에 대한 모범 사례](../sql-data-sync-best-practices.md)
- 모니터 - [Azure Monitor 로그를 사용하여 SQL 데이터 동기화 모니터링](../monitor-tune-overview.md)
- 문제 해결 - [Azure의 SQL 데이터 동기화 문제 해결](../sql-data-sync-troubleshoot.md)
- 동기화 스키마 업데이트
    - Transact-SQL 사용 - [Azure의 SQL 데이터 동기화에서 스키마 변경 내용 복제 자동화](../sql-data-sync-update-sync-schema.md)
    - PowerShell 사용 - [PowerShell을 사용하여 기존 동기화 그룹의 동기화 스키마 업데이트](update-sync-schema-in-sync-group.md)

SQL Database에 대한 자세한 내용은 다음 항목을 참조하세요.

- [SQL Database 개요](../sql-database-paas-overview.md)
- [데이터베이스 수명 주기 관리](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))