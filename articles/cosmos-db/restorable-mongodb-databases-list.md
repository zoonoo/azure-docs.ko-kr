---
title: REST API를 사용 하 여 Azure Cosmos DB API의 복원 가능한 데이터베이스 나열
description: 복원 가능한 계정에서 모든 Azure Cosmos DB MongoDB 데이터베이스에 대해 수행 된 모든 변경이의 이벤트 피드를 표시 합니다. 이를 통해 데이터베이스를 실수로 삭제 한 경우 삭제 시간을 가져올 수 있습니다.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 64288f67728e59c32c662a6640d60daf52c53fe1
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527691"
---
# <a name="list-restorable-databases-in-azure-cosmos-db-api-for-mongodb-using-rest-api"></a>REST API를 사용 하 여 Azure Cosmos DB API의 복원 가능한 데이터베이스 나열

복원 가능한 계정에서 모든 Azure Cosmos DB MongoDB 데이터베이스에 대해 수행 된 모든 변경이의 이벤트 피드를 표시 합니다. 이를 통해 데이터베이스를 실수로 삭제 한 경우 삭제 시간을 가져올 수 있습니다. 이 API에는 권한이 필요 합니다. `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read`

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbDatabases?api-version=2020-06-01-preview
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
| 200 정상 | [RestorableMongodbDatabasesListResult](#restorablemongodbdatabaseslistresult)| 작업이 성공적으로 완료되었습니다. |
| 기타 상태 코드 | [DefaultErrorResponse](#defaulterrorresponse)| 작업이 실패 한 이유를 설명 하는 오류 응답입니다.|

## <a name="examples"></a>예

### <a name="cosmosdbrestorablemongodbdatabaselist"></a>CosmosDBRestorableMongodbDatabaseList

**샘플 요청**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableMongodbDatabases?api-version=2020-06-01-preview
```

**샘플 응답**

상태 코드: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/36f09704-6be3-4f33-aa05-17b73e504c75/restorableMongodbDatabases/59c21367-b98b-4a8e-abb7-b6f46600decc",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableMongodbDatabases",
      "name": "59c21367-b98b-4a8e-abb7-b6f46600decc",
      "properties": {
        "resource": {
          "_rid": "DLB14gAAAA==",
          "eventTimestamp": "2020-09-02T19:45:03Z",
          "ownerId": "Database1",
          "ownerResourceId": "PD5DALigDgw=",
          "operationType": "Create"
        }
      }
    },
    {
      "id": "/subscriptions/2296c272-5d55-40d9-bc05-4d56dc2d7588/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableMongodbDatabases/8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableMongodbDatabases",
      "name": "8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "properties": {
        "resource": {
          "_rid": "ESXNLAAAAA==",
          "eventTimestamp": "2020-09-02T19:53:42Z",
          "ownerId": "Database1",
          "ownerResourceId": "PD5DALigDgw=",
          "operationType": "Delete"
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>정의

| 정의 | 설명 | | --- || --- | | [DefaultErrorResponse](#defaulterrorresponse) | 서비스의 오류 응답입니다. | | [Errorresponse](#errorresponse) | 오류 응답입니다. | | [OperationType](#operationtype) | 이벤트의 작업 유형을 나타내는 열거형입니다. | | [리소스](#resource) | MongoDB database 이벤트에 대 한 Azure Cosmos DB API의 리소스 | | [RestorableMongodbDatabaseGetResult](#restorablemongodbdatabasegetresult) | MongoDB database 이벤트에 대 한 Azure Cosmos DB API | | [RestorableMongodbDatabaseProperties](#restorablemongodbdatabaseproperties) | MongoDB database 이벤트에 대 한 Azure Cosmos DB API의 속성 | | [RestorableMongodbDatabasesListResult](#restorablemongodbdatabaseslistresult) | MongoDB database 이벤트 및 해당 속성에 대 한 Azure Cosmos DB API를 포함 하는 목록 작업 응답입니다. |

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

### <a name="resource"></a><a id="resource"></a>리소스나

MongoDB 용 Azure Cosmos DB API 데이터베이스 이벤트의 리소스

| **이름** | **형식** | **설명** |
| --- | --- | --- |
| _rid |문자열| 시스템 생성 속성입니다. 고유 식별자입니다. |
| eventTimestamp |문자열| 이 데이터베이스 이벤트가 발생 한 시간입니다. |
| VM에서 operationType 작업을 |[OperationType](#operationtype)| 이 데이터베이스 이벤트의 작업 유형입니다.  |
| ownerId |문자열| MongoDB 데이터베이스에 대 한 Azure Cosmos DB API의 이름입니다.|
| 소유자 Resourceid |문자열| MongoDB 데이터베이스에 대 한 리소스 ID Azure Cosmos DB API입니다. |

### <a name="restorablemongodbdatabasegetresult"></a><a id="restorablemongodbdatabasegetresult"></a>RestorableMongodbDatabaseGetResult

MongoDB database 이벤트에 대 한 Azure Cosmos DB API

| **이름** | **형식** | **설명** |
| --- | --- | --- |
| ID |문자열| Azure Resource Manager 리소스의 고유 리소스 식별자입니다. |
| name |문자열| 리소스 관리자 리소스의 이름입니다. |
| properties |[RestorableMongodbDatabaseProperties](#restorablemongodbdatabaseproperties)| MongoDB 용 Azure Cosmos DB API 데이터베이스 이벤트의 속성입니다. |
| 형식 |문자열| Azure 리소스의 유형입니다. |

### <a name="restorablemongodbdatabaseproperties"></a><a id="restorablemongodbdatabaseproperties"></a>RestorableMongodbDatabaseProperties

MongoDB 용 Azure Cosmos DB API 데이터베이스 이벤트의 속성

| **이름** | **형식** | **설명** |
| --- | --- | --- |
| 리소스 |[리소스](#resource)| MongoDB 용 Azure Cosmos DB API 데이터베이스 이벤트의 리소스 |

### <a name="restorablemongodbdatabaseslistresult"></a><a id="restorablemongodbdatabaseslistresult"></a>RestorableMongodbDatabasesListResult

데이터베이스 이벤트와 해당 속성을 포함 하는 목록 작업 응답입니다.

| **이름** | **형식** | **설명** |
| --- | --- | --- |
| value |[RestorableMongodbDatabaseGetResult](#restorablemongodbdatabasegetresult)[]| 데이터베이스 이벤트 및 해당 속성의 목록입니다. |

## <a name="next-steps"></a>다음 단계

* REST API를 사용 하 여 Azure Cosmos DB API for MongoDB에 [복원 가능한 리소스를 나열](restorable-mongodb-resources-list.md) 합니다.
* REST API를 사용 하 여 Azure Cosmos DB API for MongoDB에 [복원 가능한 컬렉션을 나열](restorable-mongodb-collections-list.md) 합니다.
* 연속 백업 모드의 [리소스 모델](continuous-backup-restore-resource-model.md) 입니다.