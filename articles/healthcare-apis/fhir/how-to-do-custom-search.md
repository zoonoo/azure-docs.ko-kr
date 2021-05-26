---
title: Azure API for FHIR 사용자 지정 검색을 수행하는 방법
description: 이 문서에서는 데이터베이스에서 사용할 사용자 지정 검색 매개 변수를 정의하는 방법을 설명합니다.
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 05/03/2021
ms.author: cavoeg
ms.openlocfilehash: 5453b11cb49bb48c48e6c949a00654a797c89202
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110476668"
---
# <a name="defining-custom-search-parameters"></a>사용자 지정 검색 매개 변수 정의

FHIR 사양은 리소스에 특정한 모든 리소스 및 검색 매개 변수에 대한 검색 매개 변수 집합을 정의합니다. 그러나 FHIR 사양에 의해 표준 검색 매개 변수로 정의되지 않은 리소스의 요소에 대해 검색하려는 시나리오가 있습니다. 이 문서에서는 Azure API for FHIR 사용할 고유한 [검색 매개 변수를](https://www.hl7.org/fhir/searchparameter.html) 정의하는 방법을 설명합니다.

> [!NOTE]
> 검색 매개 변수를 만들거나 업데이트하거나 삭제할 때마다 [다시 인덱스 작업을](how-to-run-a-reindex.md) 실행하여 프로덕션에서 검색 매개 변수를 사용할 수 있도록 해야 합니다. 아래에서는 전체 FHIR 서버를 다시 인덱싱하기 전에 검색 매개 변수를 테스트하는 방법을 간략하게 설명합니다.

## <a name="create-new-search-parameter"></a>새 검색 매개 변수 만들기

새 검색 매개 변수를 만들려면 `POST` `SearchParameter` 데이터베이스에 대한 리소스입니다. 아래 코드 예제에서는 [리소스에 US Core Race SearchParameter를](http://hl7.org/fhir/us/core/STU3.1.1/SearchParameter-us-core-race.html) 추가하는 방법을 `Patient` 보여줍니다.

```rest
POST {{FHIR_URL}}/SearchParameter

{
  "resourceType" : "SearchParameter",
  "id" : "us-core-race",
  "url" : "http://hl7.org/fhir/us/core/SearchParameter/us-core-race",
  "version" : "3.1.1",
  "name" : "USCoreRace",
  "status" : "active",
  "date" : "2019-05-21",
  "publisher" : "US Realm Steering Committee",
  "contact" : [
    {
      "telecom" : [
        {
          "system" : "other",
          "value" : "http://www.healthit.gov/"
        }
      ]
    }
  ],
  "description" : "Returns patients with a race extension matching the specified code.",
  "jurisdiction" : [
    {
      "coding" : [
        {
          "system" : "urn:iso:std:iso:3166",
          "code" : "US",
          "display" : "United States of America"
        }
      ]
    }
  ],
  "code" : "race",
  "base" : [
    "Patient"
  ],
  "type" : "token",
  "expression" : "Patient.extension.where(url = 'http://hl7.org/fhir/us/core/StructureDefinition/us-core-race').extension.value.code"
}

``` 

> [!NOTE]
> 검색 매개 변수를 데이터베이스에 **게시하고** 데이터베이스를 다시 인덱스하면 새 검색 매개 변수가 FHIR 서버의 기능 문에 표시됩니다. `SearchParameter`capability 문에서 을 보는 것은 FHIR 서버에서 검색 매개 변수가 지원되는지 확인하는 유일한 방법입니다. 검색 매개 변수를 검색하여 검색 매개 변수를 찾을 수 있지만 capability 문에서 검색 매개 변수를 볼 수 없는 경우에도 검색 매개 변수를 인덱싱해야 합니다. 다시 인덱스 작업을 트리거하기 전에 여러 검색 매개 변수를 POST할 수 있습니다.

의 중요한 `SearchParameter` 요소:

* **url**: 검색 매개 변수를 설명하는 고유 키입니다. HL7과 같은 많은 조직에서는 위에서 US Core 경합 검색 매개 변수에 표시된 것처럼 정의하는 검색 매개 변수에 표준 URL 형식을 사용합니다.

* **code:** **코드에** 저장된 값은 검색할 때 사용할 값입니다. 위의 예제에서는 를 `GET {FHIR_URL}/Patient?race=<code>` 검색하여 특정 인종의 모든 환자를 얻습니다. 코드는 검색 매개 변수가 적용 되는 리소스에 대해 고유 해야 합니다.

* **base**: 검색 매개 변수가 적용 되는 리소스를 설명 합니다. 검색 매개 변수가 모든 리소스에 적용 되는 경우를 사용할 수 있습니다. `Resource` 그렇지 않으면 관련 된 모든 리소스를 나열할 수 있습니다.
 
* **유형**: 검색 매개 변수의 데이터 형식을 설명 합니다. 유형은 FHIR 용 Azure API에 대 한 지원에 의해 제한 됩니다. 즉, 특수 형식의 검색 매개 변수를 정의 하거나 지원 되는 조합이 아니면 [복합 검색 매개 변수](overview-of-search.md) 를 정의할 수 없습니다.

* **식**: 검색 값을 계산 하는 방법을 설명 합니다. 검색 매개 변수를 설명 하는 경우 사양에 필요 하지 않은 경우에도 식을 포함 해야 합니다. 이는 식이나 xpath 구문이 필요 하 고 FHIR 용 Azure API는 xpath 구문을 무시 하기 때문입니다.

## <a name="test-search-parameters"></a>테스트 검색 매개 변수

인덱스 다시 만들기 작업을 실행할 때까지 프로덕션에서 검색 매개 변수를 사용할 수는 없지만 전체 데이터베이스를 다시 인덱싱 전에 검색 매개 변수를 테스트 하는 방법에는 몇 가지가 있습니다. 

먼저 새 검색 매개 변수를 테스트 하 여 반환 되는 값을 확인할 수 있습니다. 해당 ID를 입력 하 여 특정 리소스 인스턴스에 대해 아래 명령을 실행 하면 검색 매개 변수 이름 및 특정 환자에 대해 저장 된 값을 사용 하 여 값 쌍의 목록을 다시 가져올 수 있습니다. 여기에는 리소스에 대 한 모든 검색 매개 변수가 포함 되며 사용자가 만든 검색 매개 변수를 찾기 위해 스크롤할 수 있습니다. 이 명령을 실행 해도 FHIR 서버의 동작은 변경 되지 않습니다. 

```rest
GET https://{{FHIR_URL}}/{{RESOURCE}}/{{RESOUCE_ID}}/$reindex

```
예를 들어 환자에 대 한 모든 검색 매개 변수를 찾으려면 다음을 수행 합니다.

```rest
GET https://{{FHIR_URL}}/Patient/{{PATIENT_ID}}/$reindex

```

결과는 다음과 같습니다.

```json
{
  "resourceType": "Parameters",
  "id": "8be24e78-b333-49da-a861-523491c3437a",
  "meta": {
    "versionId": "1"
  },
  "parameter": [
    {
      "name": "deceased",
      "valueString": "http://hl7.org/fhir/special-values|false"
    },
    {
      "name": "language",
      "valueString": "urn:ietf:bcp:47|en-US"
    },
    {
      "name": "race",
      "valueString": "2028-9"
    },
...
```
검색 매개 변수가 예상 대로 표시 되는 것을 확인 한 후에는 요소를 사용 하 여 검색을 테스트 하기 위해 단일 리소스의 인덱스를 만들 수 있습니다. 먼저 단일 리소스를 인덱스 합니다.

```rest
POST https://{{FHIR_URL}/{{RESOURCE}}/{{RESOURCE_ID}}/$reindex
```

이를 실행 하 여 해당 리소스 유형에 대해 정의한 특정 리소스에 대 한 검색 매개 변수의 인덱스를 설정 합니다. 그러면 FHIR 서버에 대 한 업데이트가 수행 됩니다. 이제 부분 인덱스 헤더 사용을 검색하고 true로 설정할 수 있습니다. 즉, 해당 형식의 모든 리소스에 인덱싱된 리소스가 없는 경우에도 인덱싱된 검색 매개 변수가 있는 결과를 반환합니다. 

위의 예제를 계속 진행하면서 한 명의 환자를 인덱싱하여 US Core 경합을 사용하도록 설정할 수 있습니다. `SearchParameter`

```rest
POST https://{{FHIR_URL}/Patient/{{PATIENT_ID}}/$reindex
```

그런 다음, 특정 경합이 있는 환자를 검색합니다.

```rest
GET https://{{FHIR_URL}}/Patient?race=2028-9
x-ms-use-partial-indices: true
```

테스트한 후 검색 매개 변수가 예상대로 작동하는지 만족한 후에는 프로덕션 사용 사례에 대해 FHIR 서버에서 검색 매개 변수를 사용할 수 있도록 다시 인덱스 작업을 실행하거나 예약합니다.

## <a name="update-a-search-parameter"></a>검색 매개 변수 업데이트

검색 매개 변수를 업데이트하려면 를 사용하여 `PUT` 검색 매개 변수의 새 버전을 만듭니다. `SearchParameter ID` `id` 요청 본문의 요소와 호출에 를 포함해야 `PUT` `PUT` 합니다.

> [!NOTE]
> 검색 매개 변수의 ID를 모르는 경우 검색할 수 있습니다. 를 사용하면 `GET {{FHIR_URL}}/SearchParameter` 모든 사용자 지정 검색 매개 변수가 반환되며, 검색 매개 변수를 스크롤하여 필요한 검색 매개 변수를 찾을 수 있습니다. 이름별로 검색을 제한할 수도 있습니다. 아래 예제에서는 를 사용하여 이름을 검색할 수 `USCoreRace: GET {{FHIR_URL}}/SearchParameter?name=USCoreRace` 있습니다.

```rest
PUT {{FHIR_ULR}}/SearchParameter/{SearchParameter ID}

{
  "resourceType" : "SearchParameter",
  "id" : "SearchParameter ID",
  "url" : "http://hl7.org/fhir/us/core/SearchParameter/us-core-race",
  "version" : "3.1.1",
  "name" : "USCoreRace",
  "status" : "active",
  "date" : "2019-05-21",
  "publisher" : "US Realm Steering Committee",
  "contact" : [
    {
      "telecom" : [
        {
          "system" : "other",
          "value" : "http://www.healthit.gov/"
        }
      ]
    }
  ],
  "description" : "New Description!",
  "jurisdiction" : [
    {
      "coding" : [
        {
          "system" : "urn:iso:std:iso:3166",
          "code" : "US",
          "display" : "United States of America"
        }
      ]
    }
  ],
  "code" : "race",
  "base" : [
    "Patient"
  ],
  "type" : "token",
  "expression" : "Patient.extension.where(url = 'http://hl7.org/fhir/us/core/StructureDefinition/us-core-race').extension.value.code"
}

```

결과는 `SearchParameter` 업데이트되고 버전은 증가합니다.

> [!Warning]
> 데이터베이스에 이미 인덱싱된 SearchParameters를 업데이트할 때는 주의해야 합니다. 기존 SearchParameter의 동작을 변경하면 예상 동작에 영향을 줄 수 있습니다. 다시 인덱스 작업을 즉시 실행하는 것이 좋습니다.

## <a name="delete-a-search-parameter"></a>검색 매개 변수 삭제

검색 매개 변수를 삭제해야 하는 경우 다음을 사용합니다.

```rest
Delete {{FHIR_URL}}/SearchParameter/{SearchParameter ID}
```

> [!Warning]
> 데이터베이스에 이미 인덱싱된 SearchParameters를 삭제할 때는 주의해야 합니다. 기존 SearchParameter의 동작을 변경하면 예상 동작에 영향을 줄 수 있습니다. 다시 인덱스 작업을 즉시 실행하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 검색 매개 변수를 만드는 방법을 알아보았습니다. 다음으로 FHIR 서버를 다시 인덱싱 하는 방법을 배울 수 있습니다.

>[!div class="nextstepaction"]
>[재색인 작업 실행 방법](how-to-run-a-reindex.md)