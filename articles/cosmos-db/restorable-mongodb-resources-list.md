---
title: REST API를 사용 하 여 Azure Cosmos DB API의 복원 가능한 리소스 나열
description: 지정 된 타임 스탬프 및 위치의 계정에 있는 데이터베이스 및 컬렉션 콤보의 목록을 반환 합니다. 이는 지정 된 타임 스탬프 및 위치에 존재 하는 리소스의 유효성을 검사 하는 시나리오에 도움이 됩니다.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: dc90bfb6325831276b3c6171b73aebfa2877cf68
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527690"
---
# <a name="list-restorable-resources-in-azure-cosmos-db-api-for-mongodb-using-rest-api"></a>REST API를 사용 하 여 Azure Cosmos DB API의 복원 가능한 리소스 나열

지정 된 타임 스탬프 및 위치의 계정에 있는 데이터베이스 및 컬렉션 콤보의 목록을 반환 합니다. 이는 지정 된 타임 스탬프 및 위치에 존재 하는 리소스의 유효성을 검사 하는 시나리오에 도움이 됩니다. 이 API `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` 에는 권한이 필요 합니다.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbResources?api-version=2020-06-01-preview
```

선택적 매개 변수 사용:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbResources?api-version=2020-06-01-preview&amp;restoreLocation={restoreLocation}&amp;restoreTimestampInUtc={restoreTimestampInUtc}
```

## <a name="uri-parameters"></a>URI 매개 변수

| Name | In(다음 안에) | 필수 | Type | 설명 |
| --- | --- | --- | --- | --- |
| **instanceId** | path | True |문자열| 복원 가능한 데이터베이스 계정의 instanceId GUID입니다. |
| **location** | path | True | 문자열| 단어 사이에 공백을 사용 하는 Azure Cosmos DB 영역 및 각 단어는 대문자로 되어 있습니다. |
| **subscriptionId** | path | True | 문자열| 대상 구독의 ID입니다. |
| **api-버전** | Query | True | 문자열 | 이 작업에 사용할 API 버전입니다. |
| **restoreLocation** | Query | |문자열| 복원 가능한 리소스가 있는 위치입니다. |
| **restoreTimestampInUtc** | Query | |문자열| 복원 가능한 리소스가 있었던 타임 스탬프입니다. |

## <a name="responses"></a>응답

| Name | 유형 | 설명 |
| --- | --- | --- |
| 200 정상 | [RestorableMongodbResourcesListResult](#restorablemongodbresourceslistresult)| 작업이 성공적으로 완료되었습니다. |
| 기타 상태 코드 | [DefaultErrorResponse](#defaulterrorresponse)| 작업이 실패 한 이유를 설명 하는 오류 응답입니다. |


## <a name="examples"></a>예

### <a name="cosmosdbrestorablemongodbresourcelist"></a>CosmosDBRestorableMongodbResourceList

**샘플 요청**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableMongodbResources?api-version=2020-06-01-preview&amp;restoreLocation=WestUS&amp;restoreTimestampInUtc=10/13/2020 4:56
```

**샘플 응답**

상태 코드: 200

```json
{
  "value": [
    {
      "databaseName": "Database1",
      "collectionNames": [
        "Collection1"
      ]
    },
    {
      "databaseName": "Database2",
      "collectionNames": [
        "Collection1",
        "Collection2"
      ]
    },
    {
      "databaseName": "Database3",
      "collectionNames": []
    }
  ]
}
```

## <a name="definitions"></a>정의

| 정의 | 설명 | | --- || --- | | [Databaserestoreresource](#databaserestoreresource) | 복원할 특정 데이터베이스 | | [DefaultErrorResponse](#defaulterrorresponse) | 서비스의 오류 응답입니다. | | [Errorresponse](#errorresponse) | 오류 응답입니다. | | [RestorableMongodbResourcesListResult](#restorablemongodbresourceslistresult) | 복원 가능한 SQL 리소스를 포함 하는 List 작업 응답입니다. |

### <a name="databaserestoreresource"></a><a id="databaserestoreresource"></a>DatabaseRestoreResource

복원할 특정 데이터베이스

| **이름** | **형식** | **설명** |
| --- | --- | --- |
| collectionNames |string[]| 복원에 사용할 수 있는 컬렉션의 이름입니다. |
| databaseName |문자열| 복원에 사용할 수 있는 데이터베이스의 이름입니다. |

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

### <a name="restorablemongodbresourceslistresult"></a><a id="restorablemongodbresourceslistresult"></a>RestorableMongodbResourcesListResult

MongoDB 리소스에 대 한 복원 가능한 Azure Cosmos DB API를 포함 하는 List 작업 응답입니다.

| **이름** | **형식** | **설명** |
| --- | --- | --- |
| value |[Databaserestorerg[](#databaserestoreresource)]| 데이터베이스 및 컬렉션 이름을 포함 하 여 MongoDB 리소스에 대 한 복원 가능한 Azure Cosmos DB API의 목록입니다. |

## <a name="next-steps"></a>다음 단계

* REST API를 사용 하 여 Azure Cosmos DB API for MongoDB의 [복원 가능한 데이터베이스를 나열](restorable-mongodb-databases-list.md) 합니다.
* 연속 백업 모드의 [리소스 모델](continuous-backup-restore-resource-model.md) 입니다.