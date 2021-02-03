---
title: REST API를 사용 하 여 Azure Cosmos DB 복원 가능한 SQL API 컨테이너 나열
description: 특정 데이터베이스의 모든 Azure Cosmos DB SQL 컨테이너에 대해 수행 된 모든 변경이의 이벤트 피드를 표시 합니다. 이는 컨테이너가 실수로 삭제 된 시나리오에서 유용 합니다.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 90018d3e1b793575830ba34756ad685927612006
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527714"
---
# <a name="list-restorable-sql-api-containers-in-azure-cosmos-db-using-rest-api"></a>REST API를 사용 하 여 Azure Cosmos DB 복원 가능한 SQL API 컨테이너 나열

특정 데이터베이스의 모든 Azure Cosmos DB SQL 컨테이너에 대해 수행 된 모든 변경이의 이벤트 피드를 표시 합니다. 이는 컨테이너가 실수로 삭제 된 시나리오에서 유용 합니다. 이 API에는 권한이 필요 합니다. `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read`

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlContainers?api-version=2020-06-01-preview
```

선택적 매개 변수 사용:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlContainers?api-version=2020-06-01-preview&amp;restorableSqlDatabaseRid={restorableSqlDatabaseRid}
```

## <a name="uri-parameters"></a>URI 매개 변수

| Name | In(다음 안에) | 필수 | Type | 설명 |
| --- | --- | --- | --- | --- |
| **instanceId** | path | True |문자열| 복원 가능한 데이터베이스 계정의 instanceId GUID입니다. |
| **location** | path | True | 문자열| 단어 사이에 공백을 사용 하는 Azure Cosmos DB 영역 및 각 단어는 대문자로 되어 있습니다. |
| **subscriptionId** | path | True | 문자열| 대상 구독의 ID입니다. |
| **api-버전** | Query | True | 문자열 | 이 작업에 사용할 API 버전입니다. |
| **restorableSqlDatabaseRid** | Query | |문자열| SQL 데이터베이스의 리소스 ID입니다. |

## <a name="responses"></a>응답

| Name | 유형 | 설명 |
| --- | --- | --- |
| 200 정상 | [RestorableSqlContainersListResult](#restorablesqlcontainerslistresult)| 작업이 성공적으로 완료되었습니다. |
| 기타 상태 코드 | [DefaultErrorResponse](#defaulterrorresponse)| 작업이 실패 한 이유를 설명 하는 오류 응답입니다. |

## <a name="examples"></a>예

### <a name="cosmosdbrestorablesqlcontainerlist"></a>CosmosDBRestorableSqlContainerList

**샘플 요청**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableSqlContainers?api-version=2020-06-01-preview&amp;restorableSqlDatabaseRid=3fu-hg==
```

**샘플 응답**

상태 코드: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableSqlContainers/79609a98-3394-41f8-911f-cfab0c075c86",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlContainers",
      "name": "79609a98-3394-41f8-911f-cfab0c075c86",
      "properties": {
        "resource": {
          "_rid": "zAyAPQAAAA==",
          "eventTimestamp": "2020-10-13T04:56:42Z",
          "ownerId": "Container1",
          "ownerResourceId": "V18LoLrv-qA=",
          "operationType": "Create",
          "container": {
            "id": "Container1",
            "indexingPolicy": {
              "indexingMode": "Consistent",
              "automatic": true,
              "includedPaths": [
                {
                  "path": "/*"
                },
                {
                  "path": "/\"_ts\"/?"
                }
              ],
              "excludedPaths": [
                {
                  "path": "/\"_etag\"/?"
                }
              ]
            },
            "conflictResolutionPolicy": {
              "mode": "LastWriterWins",
              "conflictResolutionPath": "/_ts",
              "conflictResolutionProcedure": ""
            },
            "_rid": "V18LoLrv-qA=",
            "_self": "dbs/V18LoA==/colls/V18LoLrv-qA=/",
            "_etag": "\"00003e00-0000-0700-0000-5f85338a0000\""
          }
        }
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableSqlContainers/e85298a1-c631-4726-825e-a7ca092e9098",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlContainers",
      "name": "e85298a1-c631-4726-825e-a7ca092e9098",
      "properties": {
        "resource": {
          "_rid": "PrArcgAAAA==",
          "eventTimestamp": "2020-10-13T05:03:27Z",
          "ownerId": "Container1",
          "ownerResourceId": "V18LoLrv-qA=",
          "operationType": "Replace",
          "container": {
            "id": "Container1",
            "indexingPolicy": {
              "indexingMode": "Consistent",
              "automatic": true,
              "includedPaths": [
                {
                  "path": "/*"
                },
                {
                  "path": "/\"_ts\"/?"
                }
              ],
              "excludedPaths": [
                {
                  "path": "/\"_etag\"/?"
                }
              ]
            },
            "defaultTtl": 12345,
            "conflictResolutionPolicy": {
              "mode": "LastWriterWins",
              "conflictResolutionPath": "/_ts",
              "conflictResolutionProcedure": ""
            },
            "_rid": "V18LoLrv-qA=",
            "_self": "dbs/V18LoA==/colls/V18LoLrv-qA=/",
            "_etag": "\"00004400-0000-0700-0000-5f85351f0000\""
          }
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>정의

| 정의 | 설명 | | --- || --- | | [컨테이너](#container) | Azure Cosmos DB SQL 컨테이너 리소스 개체 | | [DefaultErrorResponse](#defaulterrorresponse) | 서비스의 오류 응답입니다. | | [Errorresponse](#errorresponse) | 오류 응답입니다. | | [OperationType](#operationtype) | 이벤트의 작업 유형을 나타내는 열거형입니다. | | [리소스](#resource) | Azure Cosmos DB SQL 컨테이너 이벤트의 리소스 | | [RestorableSqlContainerGetResult](#restorablesqlcontainergetresult) | Azure Cosmos DB SQL 컨테이너 이벤트 | | [RestorableSqlContainerProperties](#restorablesqlcontainerproperties) | Azure Cosmos DB SQL 컨테이너 이벤트의 속성 | | [RestorableSqlContainersListResult](#restorablesqlcontainerslistresult) | SQL 컨테이너 이벤트 및 해당 속성을 포함 하는 목록 작업 응답입니다. |

### <a name="container"></a><a id="container"></a>컨테이너

SQL 컨테이너 리소스 개체 Azure Cosmos DB

| **이름**  |  **유형**  |  **설명** | | --- || --- | ---| | _etag | 문자열 | 낙관적 동시성 제어에 필요한 리소스 etag를 나타내는 시스템 생성 속성입니다. | | _rid | 문자열 | 시스템 생성 속성입니다. 고유 식별자입니다. | | _self | 문자열 | 컨테이너 리소스의 주소 지정 가능한 경로를 지정 하는 시스템 생성 속성입니다. | | _ts | | 리소스의 마지막 업데이트 된 타임 스탬프를 나타내는 시스템 생성 속성입니다. | | ID | 문자열 | Azure Cosmos DB SQL 컨테이너의 이름 |

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
| 만들기 |문자열|컨테이너 만들기 이벤트|
| 삭제 |문자열|컨테이너 삭제 이벤트|
| 바꾸기 |문자열|컨테이너 수정 이벤트|
| SystemOperation |문자열|시스템에 의해 트리거되는 컨테이너 수정 이벤트입니다. 이 이벤트는 사용자가 시작 하지 않습니다.|

### <a name="resource"></a><a id="resource"></a>리소스나

Azure Cosmos DB SQL 컨테이너 이벤트의 리소스입니다.

| **이름** | **형식** | **설명** |
| --- | --- | --- |
| _rid |문자열| 시스템 생성 속성입니다. 고유 식별자입니다. |
| container |[컨테이너](#container)| SQL 컨테이너 리소스 개체 Azure Cosmos DB |
| eventTimestamp |문자열| 이 컨테이너 이벤트가 발생 한 시간입니다. |
| VM에서 operationType 작업을 |[OperationType](#operationtype)| 이 컨테이너 이벤트의 작업 유형입니다. |
| ownerId |문자열| SQL 컨테이너의 이름입니다. |
| 소유자 Resourceid |문자열| SQL 컨테이너의 리소스 ID입니다. |

### <a name="restorablesqlcontainergetresult"></a><a id="restorablesqlcontainergetresult"></a>RestorableSqlContainerGetResult

Azure Cosmos DB SQL 컨테이너 이벤트

| **이름** | **형식** | **설명** |
| --- | --- | ---
| ID |문자열| Azure Resource Manager 리소스의 고유 리소스 식별자입니다. |
| name |문자열| Azure Resource Manager 리소스의 이름입니다. |
| properties |[RestorableSqlContainerProperties](#restorablesqlcontainerproperties)| SQL 컨테이너 이벤트의 속성입니다. |
| 형식 |문자열| Azure 리소스의 유형입니다. |

### <a name="restorablesqlcontainerproperties"></a><a id="restorablesqlcontainerproperties"></a>RestorableSqlContainerProperties

Azure Cosmos DB SQL container 이벤트의 속성

| **이름** | **형식** | **설명** |
| --- | --- | --- |
| 리소스 |[리소스](#resource)| Azure Cosmos DB SQL 컨테이너 이벤트의 리소스입니다. |

### <a name="restorablesqlcontainerslistresult"></a><a id="restorablesqlcontainerslistresult"></a>RestorableSqlContainersListResult

SQL 컨테이너 이벤트 및 해당 속성을 포함 하는 목록 작업 응답입니다.

| **이름** | **형식** | **설명** |
| --- | --- | --- |
| value |[RestorableSqlContainerGetResult](#restorablesqlcontainergetresult)[]| SQL 컨테이너 이벤트 및 해당 속성의 목록입니다. |

## <a name="next-steps"></a>다음 단계

* REST API를 사용 하 여 Azure Cosmos DB API for MongoDB의 [복원 가능한 데이터베이스를 나열](restorable-mongodb-databases-list.md) 합니다.
* 연속 백업 모드의 [리소스 모델](continuous-backup-restore-resource-model.md) 입니다.