---
title: REST API를 사용 하 여 Azure Cosmos DB 복원 가능한 SQL API 데이터베이스 나열
description: 복원 가능한 계정의 모든 Azure Cosmos DB SQL 데이터베이스에 대해 수행 된 모든 변경이의 이벤트 피드를 표시 합니다. 이를 통해 데이터베이스를 실수로 삭제 한 경우 삭제 시간을 가져올 수 있습니다.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 218a39a2bf8e9269e43c4876c1654d94be886997
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539504"
---
# <a name="list-restorable-sql-api-databases-in-azure-cosmos-db-using-rest-api"></a>REST API를 사용 하 여 Azure Cosmos DB 복원 가능한 SQL API 데이터베이스 나열

> [!IMPORTANT]
> Azure Cosmos DB에 대 한 특정 시점 복원 기능 (연속 백업 모드)은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

복원 가능한 계정의 모든 Azure Cosmos DB SQL 데이터베이스에 대해 수행 된 모든 변경이의 이벤트 피드를 표시 합니다. 이를 통해 데이터베이스를 실수로 삭제 한 경우 삭제 시간을 가져올 수 있습니다. 이 API에는 권한이 필요 합니다. `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read`

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlDatabases?api-version=2020-06-01-preview
```

## <a name="uri-parameters"></a>URI 매개 변수

| Name | In(다음 안에) | 필수 | Type | 설명 |
| --- | --- | --- | --- | --- |
| **instanceId** | path | True |문자열| 복원 가능한 데이터베이스 계정의 instanceId GUID입니다. |
| **location** | path | True | 문자열| 단어 사이에 공백을 사용 하는 Azure Cosmos DB 영역 및 각 단어는 대문자로 되어 있습니다. |
| **subscriptionId** | path | True | 문자열| 대상 구독의 ID입니다. |
| **api-버전** | Query | True | 문자열 | 이 작업에 사용할 API 버전입니다. |

## <a name="responses"></a>응답

| Name | 유형 | 설명 |
| --- | --- | --- |
| 200 정상 | [RestorableSqlDatabasesListResult](#restorablesqldatabaseslistresult)| 작업이 성공적으로 완료되었습니다. |
| 기타 상태 코드 | [DefaultErrorResponse](#defaulterrorresponse)| 작업이 실패 한 이유를 설명 하는 오류 응답입니다. |

## <a name="examples"></a>예

### <a name="cosmosdbrestorablesqldatabaselist"></a>CosmosDBRestorableSqlDatabaseList

**샘플 요청**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlDatabases?api-version=2020-06-01-preview
```

**샘플 응답**

상태 코드: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/36f09704-6be3-4f33-aa05-17b73e504c75/restorableSqlDatabases/59c21367-b98b-4a8e-abb7-b6f46600decc",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlDatabases",
      "name": "59c21367-b98b-4a8e-abb7-b6f46600decc",
      "properties": {
        "resource": {
          "_rid": "DLB14gAAAA==",
          "eventTimestamp": "2020-09-02T19:45:03Z",
          "ownerId": "Database1",
          "ownerResourceId": "3fu-hg==",
          "operationType": "Create",
          "database": {
            "id": "Database1",
            "_rid": "3fu-hg==",
            "_self": "dbs/3fu-hg==/",
            "_etag": "\"0000c20a-0000-0700-0000-5f4ff63f0000\"",
            "_colls": "colls/",
            "_users": "users/"
          }
        }
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlDatabases/8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlDatabases",
      "name": "8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "properties": {
        "resource": {
          "_rid": "ESXNLAAAAA==",
          "eventTimestamp": "2020-09-02T19:53:42Z",
          "ownerId": "Database1",
          "ownerResourceId": "3fu-hg==",
          "database": {
            "id": "Database1",
            "_rid": "3fu-hg==",
            "_self": "dbs/3fu-hg==/",
            "_etag": "\"0000c20a-0000-0700-0000-5f4ff63f0000\"",
            "_colls": "colls/",
            "_users": "users/",
            "_ts": 1599075903
          },
          "operationType": "Delete"
        }
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlDatabases/2c07991b-9c7c-4e85-be68-b18c1f2ff326",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlDatabases",
      "name": "2c07991b-9c7c-4e85-be68-b18c1f2ff326",
      "properties": {
        "resource": {
          "_rid": "aXFqUQAAAA==",
          "eventTimestamp": "2020-09-02T19:53:15Z",
          "ownerId": "Database2",
          "ownerResourceId": "0SziSg==",
          "database": {
            "id": "Database2",
            "_rid": "0SziSg==",
            "_self": "dbs/0SziSg==/",
            "_etag": "\"0000ca0a-0000-0700-0000-5f4ff82b0000\"",
            "_colls": "colls/",
            "_users": "users/"
          },
          "operationType": "Create"
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>정의

| 정의 | 설명 | | --- || --- | | [데이터베이스](#database) | Azure Cosmos DB SQL database 리소스 개체 | | [DefaultErrorResponse](#defaulterrorresponse) | 서비스의 오류 응답입니다. | | [Errorresponse](#errorresponse) | 오류 응답입니다. | | [OperationType](#operationtype) | 이벤트의 작업 유형을 나타내는 열거형입니다. | | [리소스](#resource) | Azure Cosmos DB SQL database 이벤트의 리소스 | | [RestorableSqlDatabaseGetResult](#restorablesqldatabasegetresult) | Azure Cosmos DB SQL database 이벤트 | | [RestorableSqlDatabaseProperties](#restorablesqldatabaseproperties) | Azure Cosmos DB SQL database 이벤트의 속성 | | [RestorableSqlDatabasesListResult](#restorablesqldatabaseslistresult) | SQL database 이벤트와 해당 속성을 포함 하는 목록 작업 응답입니다. |

### <a name="database"></a><a id="database"></a>데이터

Azure Cosmos DB SQL database 리소스 개체

| **이름**  |  **유형**  |  **설명** | | --- || --- | ---| | _colls | 문자열 | 컬렉션 리소스의 주소 지정 가능 경로를 지정 하는 시스템 생성 속성입니다. | | _etag | 문자열 | 낙관적 동시성 제어에 필요한 리소스 etag를 나타내는 시스템 생성 속성입니다. | | _rid | 문자열 | 시스템 생성 속성입니다. 고유 식별자입니다. | | _self | 문자열 | 데이터베이스 리소스의 주소 지정 가능 경로를 지정 하는 시스템 생성 속성입니다. | | _ts | | 리소스의 마지막 업데이트 된 타임 스탬프를 나타내는 시스템 생성 속성입니다. | | _users | 문자열 | 사용자 리소스의 주소 지정 가능한 경로를 지정 하는 시스템 생성 속성입니다. | | ID | 문자열 | Azure Cosmos DB SQL database의 이름 |

### <a name="defaulterrorresponse"></a><a id="defaulterrorresponse"></a>DefaultErrorResponse

서비스의 오류 응답입니다.

| **이름** | **형식** | **설명** |
| --- | --- | --- |
| 오류 | [ErrorResponse](#errorresponse)| 오류 응답입니다. |

### <a name="errorresponse"></a><a id="errorresponse"></a>ErrorResponse

오류 응답입니다.

| **이름** | **형식** | **설명** |
| --- | --- | --- |
| code |문자열| 오류 코드 |
| message |문자열| 작업이 실패 한 이유를 나타내는 오류 메시지입니다. |

### <a name="operationtype"></a><a id="operationtype"></a>OperationType

이벤트의 작업 유형을 나타내는 열거형입니다.

| **이름** | **형식** | **설명** |
| --- | --- | --- |
| 만들기 |문자열|데이터베이스 생성 이벤트|
| 삭제 |문자열|데이터베이스 삭제 이벤트|
| 바꾸기 |문자열|데이터베이스 수정 이벤트|
| SystemOperation |문자열|시스템에 의해 트리거되는 데이터베이스 수정 이벤트입니다. 이 이벤트는 사용자가 시작 하지 않습니다.|

### <a name="resource"></a><a id="resource"></a>리소스나

Azure Cosmos DB SQL database 이벤트의 리소스입니다.

| **이름** | **형식** | **설명** |
| --- | --- | --- |
| _rid |문자열| 시스템 생성 속성입니다. 고유 식별자입니다. |
| 데이터베이스 |[데이터베이스](#database)| Azure Cosmos DB SQL database 리소스 개체 |
| eventTimestamp |문자열| 이 데이터베이스 이벤트가 발생 한 시간입니다. |
| VM에서 operationType 작업을 |[OperationType](#operationtype)| 이 데이터베이스 이벤트의 작업 유형입니다. |
| ownerId |문자열| SQL 데이터베이스의 이름입니다. |
| 소유자 Resourceid |문자열| SQL 데이터베이스의 리소스 ID입니다. |

### <a name="restorablesqldatabasegetresult"></a><a id="restorablesqldatabasegetresult"></a>RestorableSqlDatabaseGetResult

Azure Cosmos DB SQL database 이벤트

| **이름** | **형식** | **설명** |
| --- | --- | --- |
| ID |문자열| Azure Resource Manager 리소스의 고유 리소스 식별자입니다. |
| name |문자열| Azure Resource Manager 리소스의 이름입니다. |
| properties | [RestorableSqlDatabaseProperties](#restorablesqldatabaseproperties)| SQL database 이벤트의 속성입니다. |
| 형식 |문자열| Azure 리소스의 유형입니다. |

### <a name="restorablesqldatabaseproperties"></a><a id="restorablesqldatabaseproperties"></a>RestorableSqlDatabaseProperties

Azure Cosmos DB SQL database 이벤트의 속성

| **이름** | **형식** | **설명** |
| --- | --- | --- |
| 리소스 |[리소스](#resource)| Azure Cosmos DB SQL database 이벤트의 리소스입니다. |

### <a name="restorablesqldatabaseslistresult"></a><a id="restorablesqldatabaseslistresult"></a>RestorableSqlDatabasesListResult

SQL database 이벤트와 해당 속성을 포함 하는 목록 작업 응답입니다.

| **이름** | **형식** | **설명** |
| --- | --- | --- |
| value |[RestorableSqlDatabaseGetResult](#restorablesqldatabasegetresult)[]| SQL database 이벤트 및 해당 속성의 목록입니다. |

## <a name="next-steps"></a>다음 단계

* REST API를 사용 하 여 SQL API Azure Cosmos DB [복원 가능한 컨테이너를 나열](restorable-sql-containers-list.md) 합니다.
* 연속 백업 모드의 [리소스 모델](continuous-backup-restore-resource-model.md) 입니다.