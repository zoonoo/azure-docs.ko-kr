---
title: Azure API for FHIR 다시 인덱스 작업을 실행하는 방법
description: 이 문서에서는 다시 인덱스 작업을 실행하여 데이터베이스에서 아직 인덱싱되지 않은 검색 또는 정렬 매개 변수를 인덱싱하는 방법을 설명합니다.
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 4/23/2021
ms.author: cavoeg
ms.openlocfilehash: 905d9381ac93e38575e9d0ff5c6f5571122b2990
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110476650"
---
# <a name="running-a-reindex-job"></a>다시 인덱스 작업 실행

아직 인덱싱되지 않은 Azure API for FHIR 검색 또는 정렬 매개 변수가 있을 수 있는 시나리오가 있습니다. 이는 사용자 고유의 검색 매개 변수를 정의할 때 특히 관련이 있습니다. 검색 매개 변수가 인덱싱될 때까지 검색에 사용할 수 없습니다. 이 문서에서는 다시 인덱스 작업을 실행하여 데이터베이스에 아직 인덱싱되지 않은 검색 또는 정렬 매개 변수를 인덱싱하는 방법에 대해 간략하게 설명합니다.

> [!Warning]
> 시작하기 전에 이 전체 문서를 읽는 것이 중요합니다. 다시 인덱스 작업은 성능이 매우 많이 사용될 수 있습니다. 이 문서에는 다시 인덱스 작업을 제한하고 제어하는 방법에 대한 옵션이 포함되어 있습니다.

## <a name="how-to-run-a-reindex-job"></a>재색인 작업 실행 방법 

다시 인덱스 작업을 시작하려면 다음 코드 예제를 사용합니다.

```json
POST {{FHIR URL}}/$reindex 

{ 

“resourceType”: “Parameters”,  

“parameter”: [] 

}
 ```

요청이 성공하면 **201 Created** 상태가 반환됩니다. 이 메시지의 결과는 다음과 같습니다.

```json
HTTP/1.1 201 Created 
Content-Location: https://{{FHIR URL}}/_operations/reindex/560c7c61-2c70-4c54-b86d-c53a9d29495e 

{
  "resourceType": "Parameters",
  "id": "560c7c61-2c70-4c54-b86d-c53a9d29495e",
  "meta": {
    "versionId": "\"4c0049cd-0000-0100-0000-607dc5a90000\""
  },
  "parameter": [
    {
      "name": "id",
      "valueString": "560c7c61-2c70-4c54-b86d-c53a9d29495e"
    },
    {
      "name": "queuedTime",
      "valueDateTime": "2021-04-19T18:02:17.0118558+00:00"
    },
    {
      "name": "totalResourcesToReindex",
      "valueDecimal": 0.0
    },
    {
      "name": "resourcesSuccessfullyReindexed",
      "valueDecimal": 0.0
    },
    {
      "name": "progress",
      "valueDecimal": 0.0
    },
    {
      "name": "status",
      "valueString": "Queued"
    },
    {
      "name": "maximumConcurrency",
      "valueDecimal": 1.0
    },
    {
      "name": "resources",
      "valueString": ""
    },
    {
      "name": "searchParams",
      "valueString": ""
    }
  ]
}
```

> [!NOTE]
> 상태를 확인하거나 다시 인덱스 작업을 취소하려면 다시 인덱스 ID가 필요합니다. 결과 매개 변수 리소스의 ID입니다. 위의 예제에서 다시 인덱스 작업의 ID는 `560c7c61-2c70-4c54-b86d-c53a9d29495e` 입니다.

 ## <a name="how-to-check-the-status-of-a-reindex-job"></a>다시 인덱스 작업의 상태를 확인하는 방법

다시 인덱스 작업을 시작한 후에는 다음을 사용하여 작업의 상태를 확인할 수 있습니다.

`GET {{FHIR URL}}/_operations/reindex/{{reindexJobId}`

다시 인덱스 작업 결과의 상태는 다음과 같습니다.

```json
{

  "resourceType": "Parameters",
  "id": "b65fd841-1c62-47c6-898f-c9016ced8f77",
  "meta": {

    "versionId": "\"1800f05f-0000-0100-0000-607a1a7c0000\""
  },
  "parameter": [

    {

      "name": "id",
      "valueString": "b65fd841-1c62-47c6-898f-c9016ced8f77"
    },
    {

      "name": "startTime",
      "valueDateTime": "2021-04-16T23:11:35.4223217+00:00"
    },
    {

      "name": "queuedTime",
      "valueDateTime": "2021-04-16T23:11:29.0288163+00:00"
    },
    {

      "name": "totalResourcesToReindex",
      "valueDecimal": 262544.0
    },
    {

      "name": "resourcesSuccessfullyReindexed",
      "valueDecimal": 5754.0
    },
    {

      "name": "progress",
      "valueDecimal": 2.0
    },
    {

      "name": "status",
      "valueString": "Running"
    },
    {

      "name": "maximumConcurrency",
      "valueDecimal": 1.0
    },
    {

      "name": "resources",
      "valueString": 
      "{LIST OF IMPACTED RESOURCES}"
    },
    {
```

다음 정보가 다시 인덱스 작업 결과에 표시됩니다.

* **totalResourcesToReindex**: 작업의 일부로 인덱싱해야 되는 총 리소스 수를 포함 합니다.

* **resourcesSuccessfullyReindexed**: 이미 인덱싱해야 된 합계입니다.

* **진행률**: 작업 완료율을 인덱싱합니다. ResourcesSuccessfullyReindexed/totalResourcesToReindex x 100와 같습니다.

* **상태**: 인덱스 취소 작업이 큐에 대기, 실행 중, 완료, 실패 또는 취소 된 경우 상태가 됩니다.

* **리소스**: 인덱스 작업의 영향을 받는 리소스 종류를 모두 나열 합니다.

## <a name="delete-a-reindex-job"></a>인덱스 삭제 작업 삭제

인덱스 재 작업을 취소 해야 하는 경우 delete 호출을 사용 하 고 인덱스 취소 작업 ID를 지정 합니다.

`Delete {{FHIR URL}}/_operations/reindex/{{reindexJobId}`

## <a name="performance-considerations"></a>성능 고려 사항

인덱스를 사용 하면 성능이 크게 향상 될 수 있습니다. 데이터베이스에서 재 인덱싱 작업이 실행 되는 방식을 관리 하는 데 도움이 되는 몇 가지 제한 컨트롤을 구현 했습니다.

> [!NOTE]
> 인덱스 작업을 일 동안 실행 하는 경우에는 일반적이 지 않은 대량 데이터 집합에서 자주 발생 하지 않습니다. 3000만 억 개의 리소스가 있는 데이터베이스의 경우 전체 데이터베이스의 인덱스를 다시 시작 하는 데 4-5 일이 소요 된 것을 알 수 있습니다.

다음은 사용 가능한 매개 변수, 기본값 및 권장 범위에 대 한 개요입니다. 이러한 매개 변수를 사용 하 여 프로세스의 속도를 높이고 (더 많은 계산 사용) 프로세스 속도를 낮출 수 있습니다 (더 낮은 계산 사용). 예를 들어, 낮은 트래픽 시간에 다시 인덱싱 작업을 실행 하 고 계산을 늘려 더 빠르게 수행할 수 있습니다. 대신 설정을 사용 하 여 계산을 매우 적게 사용 하 고 백그라운드에서 일 동안 실행 되도록 할 수 있습니다. 

| **매개 변수**                     | **설명**              | **기본값**        | **권장 범위**           |
| --------------------------------- | ---------------------------- | ------------------ | ------------------------------- |
| QueryDelayIntervalInMilliseconds  | 이는 일괄 처리 작업을 수행 하는 동안 시작 되는 각 리소스 일괄 처리 사이의 지연입니다. | 500 밀리초 (5 초) | 50에서 5000: 50은 다시 인덱스 작업의 속도를 증가시키고 5000은 기본값에서 속도가 느려집니다. |
| MaximumResourcesPerQuery  | 이는 다시 인덱스할 리소스 일괄 처리에 포함된 최대 리소스 수입니다.  | 100 | 1-500 |
| MaximumConcurreny  | 한 번에 수행되는 일괄 처리 수입니다.  | 1 | 1-5 |
| targetDataStoreUsagePercentrage | 이렇게 하면 다시 인덱스 작업에 사용할 데이터 저장소의 백분율을 지정할 수 있습니다. 예를 들어 50%를 지정할 수 있으며, 이를 통해 Cosmos DB 사용 가능한 RU의 50%를 다시 인덱스 작업에서 사용할 수 있습니다.  | 존재하지 않습니다. 즉, 최대 100%를 사용할 수 있습니다. | 1-100 |

위의 매개 변수를 사용하려는 경우 다시 인덱스 작업을 시작할 때 매개 변수 리소스에 전달할 수 있습니다.

```json
{
  "resourceType": "Parameters",
  "parameter": [
    {
      "name": "maximumConcurrency",
      "valueInteger": "3"
    },
    {
      "name": "targetDataStoreUsagePercentage",
      "valueInteger": "20"
    },
    {
      "name": "queryDelayIntervalInMilliseconds",
      "valueInteger": "1000"
    },
    {
      "name": "maximumNumberOfResourcesPerQuery",
      "valueInteger": "1"
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 다시 인덱스 작업을 시작하는 방법을 배웠습니다. 다시 인덱스 작업이 필요한 새 검색 매개 변수를 정의하는 방법을 알아보려면 다음을 참조하세요. 

>[!div class="nextstepaction"]
>[사용자 지정 검색 매개 변수 정의](how-to-do-custom-search.md)

         
     