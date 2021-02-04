---
title: Azure Cosmos DB MongoDB API에서 복원 가능한 컬렉션 나열-REST API
description: 특정 데이터베이스의 모든 Azure Cosmos DB MongoDB 컬렉션에서 수행 된 모든 변경이의 이벤트 피드를 표시 합니다. 이는 컨테이너가 실수로 삭제 된 시나리오에서 유용 합니다.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: c19de134f40c58a687dcf6bac6ac156e46cef7da
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537546"
---
# <a name="list-restorable-collections-in-azure-cosmos-db-api-for-mongodb-using-rest-api"></a>REST API를 사용 하 여 Azure Cosmos DB API의 복원 가능한 컬렉션 나열

> [!IMPORTANT]
> Azure Cosmos DB에 대 한 특정 시점 복원 기능 (연속 백업 모드)은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

특정 데이터베이스의 MongoDB 컬렉션에 대 한 모든 Azure Cosmos DB API에서 수행 되는 모든 변경이의 이벤트 피드를 표시 합니다. 이는 컨테이너가 실수로 삭제 된 시나리오에서 유용 합니다. 이 API에는 권한이 필요 합니다. `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read`

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbCollections?api-version=2020-06-01-preview
```

선택적 매개 변수 사용:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbCollections?api-version=2020-06-01-preview&amp;restorableMongodbDatabaseRid={restorableMongodbDatabaseRid}
```

## <a name="uri-parameters"></a>URI 매개 변수

| Name | In(다음 안에) | 필수 | Type | 설명 |
| --- | --- | --- | --- | --- |
| **instanceId** | path | True |문자열| 복원 가능한 데이터베이스 계정의 instanceId GUID입니다. |
| **location** | path | True | 문자열| 단어 사이에 공백을 사용 하는 Azure Cosmos DB 영역 및 각 단어는 대문자로 되어 있습니다. |
| **subscriptionId** | path | True | 문자열| 대상 구독의 ID입니다. |
| **api-버전** | Query | True | 문자열 | 이 작업에 사용할 API 버전입니다. |
| **restorableMongodbDatabaseRid** | Query | |문자열| MongoDB 데이터베이스에 대 한 Azure Cosmos DB API의 리소스 ID입니다. |

## <a name="responses"></a>응답

| Name | 유형 | 설명 |
| --- | --- | --- |
| 200 정상 | [RestorableMongodbCollectionsListResult](#restorablemongodbcollectionslistresult)| 작업이 성공적으로 완료되었습니다. |
| 기타 상태 코드 | [DefaultErrorResponse](#defaulterrorresponse)| 작업이 실패 한 이유를 설명 하는 오류 응답입니다.|

## <a name="examples"></a>예

### <a name="cosmosdbrestorablemongodbcollectionlist"></a>CosmosDBRestorableMongodbCollectionList

**샘플 요청**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableMongodbCollections?api-version=2020-06-01-preview&amp;restorableMongodbDatabaseRid=PD5DALigDgw=
```

**샘플 응답**

상태 코드: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableMongodbCollections/79609a98-3394-41f8-911f-cfab0c075c86",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableMongodbCollections",
      "name": "79609a98-3394-41f8-911f-cfab0c075c86",
      "properties": {
        "resource": {
          "_rid": "zAyAPQAAAA==",
          "eventTimestamp": "2020-10-13T04:56:42Z",
          "ownerId": "Collection1",
          "ownerResourceId": "V18LoLrv-qA=",
          "operationType": "Create"
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>정의

|정의  | Description|
| --- | --- |
| [DefaultErrorResponse](#defaulterrorresponse) | 서비스의 오류 응답입니다. |
| [ErrorResponse](#errorresponse) | 오류 응답입니다. |
| [OperationType](#operationtype) | 이벤트의 작업 유형을 나타내는 열거형입니다. |
| [리소스](#resource) | MongoDB collection 이벤트에 대 한 Azure Cosmos DB API의 리소스 |
| [RestorableMongodbCollectionGetResult](#restorablemongodbcollectiongetresult) | MongoDB collection 이벤트에 대 한 Azure Cosmos DB API |
| [RestorableMongodbCollectionProperties](#restorablemongodbcollectionproperties) | MongoDB collection 이벤트에 대 한 Azure Cosmos DB API의 속성 |
| [RestorableMongodbCollectionsListResult](#restorablemongodbcollectionslistresult) | 컬렉션 이벤트와 해당 속성을 포함 하는 목록 작업 응답입니다. |

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
| 만들기 |문자열|컬렉션 만들기 이벤트|
| 삭제 |문자열|컬렉션 삭제 이벤트|
| 바꾸기 |문자열|컬렉션 수정 이벤트|

### <a name="resource"></a><a id="resource"></a>리소스나

Azure Cosmos DB MongoDB collection 이벤트의 리소스입니다.

| **이름** | **형식** | **설명** |
| --- | --- | --- |
| _rid |문자열| 시스템 생성 속성입니다. 고유 식별자입니다. |
| eventTimestamp |문자열| 이 컬렉션 이벤트가 발생 한 시간입니다. |
| VM에서 operationType 작업을 |[OperationType](#operationtype)|  이 컬렉션 이벤트의 작업 유형입니다. |
| ownerId |문자열| MongoDB 컬렉션의 이름입니다.|
| 소유자 Resourceid |문자열| MongoDB collection의 리소스 ID입니다. |

### <a name="restorablemongodbcollectiongetresult"></a><a id="restorablemongodbcollectiongetresult"></a>RestorableMongodbCollectionGetResult

Azure Cosmos DB MongoDB collection 이벤트

| **이름** | **형식** | **설명** |
| --- | --- | --- |
| ID |문자열| Azure Resource Manager 리소스의 고유 리소스 식별자입니다. |
| name |문자열| 리소스 관리자 리소스의 이름입니다. |
| properties |[RestorableMongodbCollectionProperties](#restorablemongodbcollectionproperties)| 컬렉션 이벤트의 속성입니다. |
| 형식 |문자열| Azure 리소스의 유형입니다. |

### <a name="restorablemongodbcollectionproperties"></a><a id="restorablemongodbcollectionproperties"></a>RestorableMongodbCollectionProperties

Azure Cosmos DB MongoDB collection 이벤트의 속성입니다.

| **이름** | **형식** | **설명** |
| --- | --- | --- |
| 리소스 | [리소스](#resource)| MongoDB collection 이벤트에 대 한 Azure Cosmos DB API의 리소스 |

### <a name="restorablemongodbcollectionslistresult"></a><a id="restorablemongodbcollectionslistresult"></a>RestorableMongodbCollectionsListResult

컬렉션 이벤트와 해당 속성을 포함 하는 목록 작업 응답입니다.

| **이름** | **형식** | **설명** |
| --- | --- | --- |
| value |[RestorableMongodbCollectionGetResult](#restorablemongodbcollectiongetresult)[]| MongoDB collection 이벤트 및 해당 속성에 대 한 Azure Cosmos DB API 목록입니다. |

## <a name="next-steps"></a>다음 단계

* REST API를 사용 하 여 Azure Cosmos DB API for MongoDB의 [복원 가능한 데이터베이스를 나열](restorable-mongodb-databases-list.md) 합니다.
* 연속 백업 모드의 [리소스 모델](continuous-backup-restore-resource-model.md) 입니다.