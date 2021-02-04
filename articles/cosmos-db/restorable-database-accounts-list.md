---
title: Azure Cosmos DB REST API를 사용 하 여 복원 가능한 database 계정 나열
description: 구독에서 사용할 수 있는 복원 가능한 Azure Cosmos DB 데이터베이스 계정을 모두 나열 합니다.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 71aafc756e5291e148c3b162f8946544b6e3c2d0
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537580"
---
# <a name="list-restorable-database-accounts-using-azure-cosmos-db-rest-api"></a>Azure Cosmos DB REST API를 사용 하 여 복원 가능한 database 계정 나열

> [!IMPORTANT]
> Azure Cosmos DB에 대 한 특정 시점 복원 기능 (연속 백업 모드)은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

구독에서 사용할 수 있는 복원 가능한 Azure Cosmos DB 데이터베이스 계정을 모두 나열 합니다. 이 호출 `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read` 에는 권한이 필요 합니다.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/restorableDatabaseAccounts?api-version=2020-06-01-preview
```

## <a name="uri-parameters"></a>URI 매개 변수

| Name | In(다음 안에) | 필수 | Type | 설명 |
| --- | --- | --- | --- | --- |
| **subscriptionId** | path | True | 문자열| 대상 구독의 ID입니다. |
| **api-버전** | Query | True | 문자열 | 이 작업에 사용할 API 버전입니다. |

## <a name="responses"></a>응답

| Name | 유형 | 설명 |
| --- | --- | --- |
| 200 정상 | [RestorableDatabaseAccountsListResult](#restorabledatabaseaccountslistresult)| 작업이 성공적으로 완료되었습니다. |
| 기타 상태 코드 | [DefaultErrorResponse](#defaulterrorresponse)| 작업이 실패 한 이유를 설명 하는 오류 응답입니다. |

## <a name="examples"></a>예

### <a name="cosmosdbdatabaseaccountlist"></a>CosmosDBDatabaseAccountList

**샘플 요청**

```http
GET https://management.azure.com/subscriptions/subid/providers/Microsoft.DocumentDB/restorableDatabaseAccounts?api-version=2020-06-01-preview
```

**샘플 응답**

상태 코드: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d",
      "name": "d9b26648-2f53-4541-b3d8-3044f4f9810d",
      "location": "West US",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts",
      "properties": {
        "accountName": "ddb1",
        "creationTime": "2020-04-11T21:56:15Z",
        "deletionTime": "2020-06-12T22:05:09Z",
        "apiType": "Sql",
        "restorableLocations": [
          {
            "locationName": "South Central US",
            "regionalDatabaseAccountInstanceId": "d7a01f78-606f-45c6-9dac-0df32f433bb5",
            "creationTime": "2020-10-30T21:13:10Z",
            "deletionTime": "2020-10-30T21:13:35Z"
          },
          {
            "locationName": "West US",
            "regionalDatabaseAccountInstanceId": "fdb43d84-1572-4697-b6e7-2bcda0c51b2c",
            "creationTime": "2020-10-30T21:13:10Z"
          }
        ]
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/4f9e6ace-ac7a-446c-98bc-194c502a06b4",
      "name": "4f9e6ace-ac7a-446c-98bc-194c502a06b4",
      "location": "East US",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts",
      "properties": {
        "accountName": "ddb2",
        "creationTime": "2020-05-01T08:05:18Z",
        "apiType": "Sql",
        "restorableLocations": [
          {
            "locationName": "South Central US",
            "regionalDatabaseAccountInstanceId": "d7a01f78-606f-45c6-9dac-0df32f433bb5",
            "creationTime": "2020-10-30T21:13:10Z",
            "deletionTime": "2020-10-30T21:13:35Z"
          },
          {
            "locationName": "West US",
            "regionalDatabaseAccountInstanceId": "fdb43d84-1572-4697-b6e7-2bcda0c51b2c",
            "creationTime": "2020-10-30T21:13:10Z"
          }
        ]
      }
    }
  ]
}
```

## <a name="definitions"></a>정의

|정의  | Description|
| --- | --- |
| [ApiType](#apitype) | 복원 가능한 데이터베이스 계정의 API 유형을 나타내는 Enum입니다. |
| [DefaultErrorResponse](#defaulterrorresponse) | 서비스의 오류 응답입니다. |
| [ErrorResponse](#errorresponse) | 오류 응답입니다. |
| [RestorableDatabaseAccountGetResult](#restorabledatabaseaccountgetresult) | Azure Cosmos DB 복원 가능한 데이터베이스 계정 |
| [RestorableDatabaseAccountsListResult](#restorabledatabaseaccountslistresult) | 복원 가능한 데이터베이스 계정 및 해당 속성을 포함 하는 List 작업 응답입니다. |
| [RestorableLocationResource](#restorablelocationresource) | 지역 복원 가능한 계정의 속성입니다. |

### <a name="apitype"></a><a id="apitype"></a>ApiType

복원 가능한 데이터베이스 계정의 API 유형을 나타내는 Enum입니다.

| **이름** | **형식** |
| --- | --- |
| Cassandra |문자열|
| Gremlin |문자열|
| GremlinV2 |문자열|
| MongoDB |문자열|
| Sql |문자열|
| 테이블 |문자열|

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

### <a name="restorabledatabaseaccountgetresult"></a><a id="restorabledatabaseaccountgetresult"></a>RestorableDatabaseAccountGetResult

Azure Cosmos DB 복원 가능한 데이터베이스 계정

| **이름** | **형식** | **설명** |
| --- | --- | --- |
| ID |문자열| Azure Resource Manager 리소스의 고유 리소스 식별자입니다. |
| 위치 |문자열| 리소스가 속한 리소스 그룹의 위치입니다. |
| name |문자열| 리소스 관리자 리소스의 이름입니다. |
| properties. accountName |문자열| 전역 데이터베이스 계정의 이름입니다. |
| apiType |[ApiType](#apitype)| 복원 가능한 데이터베이스 계정의 API 유형입니다. |
| creationTime |문자열| 복원 가능한 데이터베이스 계정의 만든 시간 (ISO-8601 형식)입니다. |
| deletionTime |문자열| 복원 가능한 데이터베이스 계정이 삭제 된 시간 (ISO-8601 형식)입니다. |
| restorableLocations |[RestorableLocationResource](#restorablelocationresource)[]| 데이터베이스 계정을 복원할 수 있는 영역 목록입니다. |
| 형식 |문자열| Azure 리소스의 유형입니다. |

### <a name="restorabledatabaseaccountslistresult"></a><a id="restorabledatabaseaccountslistresult"></a>RestorableDatabaseAccountsListResult

복원 가능한 database 계정 및 해당 속성을 포함 하는 목록 작업 응답입니다.

| **이름** | **형식** | **설명** |
| --- | --- | --- |
| value |[RestorableDatabaseAccountGetResult](#restorabledatabaseaccountgetresult)[]| 복원 가능한 데이터베이스 계정 및 해당 속성의 목록입니다. |

### <a name="restorablelocationresource"></a><a id="restorablelocationresource"></a>RestorableLocationResource

지역 복원 가능한 계정의 속성입니다.

| **이름** | **형식** | **설명** |
| --- | --- | --- |
| creationTime |문자열| 지역 복원 가능한 데이터베이스 계정의 만든 시간 (ISO-8601 형식)입니다. |
| deletionTime |문자열| 지역 복원 가능한 데이터베이스 계정이 삭제 된 시간 (ISO-8601 형식)입니다. |
| locationName |문자열| 지역 복원 가능한 계정의 위치입니다. |
| 지역 Aldatabaseaccountinstanceid |문자열| 지역 복원 가능한 계정의 인스턴스 ID입니다. |

## <a name="next-steps"></a>다음 단계

* [복원 가능한 database accounts-위치별 나열 합니다.](restorable-database-accounts-list-by-location.md)
* 연속 백업 모드의 [리소스 모델](continuous-backup-restore-resource-model.md) 입니다.