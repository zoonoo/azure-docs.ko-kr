---
title: Azure API에서 FHIR에 대 한 사용자 지정 검색을 수행 하는 방법
description: 이 문서에서는 데이터베이스에서 사용할 사용자 지정 검색 매개 변수를 정의 하는 방법을 설명 합니다.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 4/23/2021
ms.author: cavoeg
ms.openlocfilehash: 1482bd7f054c75c4f26b77907d50a6471c389d68
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108322573"
---
# <a name="defining-custom-search-parameters"></a>사용자 지정 검색 매개 변수 정의

FHIR 사양은 리소스와 관련 된 모든 리소스 및 검색 매개 변수에 대 한 검색 매개 변수 집합을 정의 합니다. 그러나 사양에 정의 되지 않은 리소스의 필드를 표준 검색 매개 변수로 검색 하려는 경우가 있습니다. 이 문서에서는 FHIR 서버에서 사용할 사용자 고유의 [검색 매개 변수](https://www.hl7.org/fhir/searchparameter.html) 를 정의 하는 방법을 설명 합니다.

> [!NOTE]
> 검색 매개 변수를 만들거나, 업데이트 하거나, 삭제할 때마다 데이터베이스에서 변경 내용을 설정 하기 위해 [인덱스 작업](how-to-run-a-reindex.md) 을 실행 해야 합니다.

> [!Warning]
> 검색 매개 변수를 업데이트 하거나 삭제 하는 경우에는 [재 인덱스 작업](how-to-run-a-reindex.md)을 즉시 실행 해야 합니다. 변경 내용에 대 한 인덱싱이 필요 하기 때문에 여전히 활성 상태로 표시 되는 업데이트 또는 삭제 검색 매개 변수를 사용 하 여 데이터베이스의 비정상 상태가 발생할 수 있습니다. 

## <a name="create-new-search-parameter"></a>새 검색 매개 변수 만들기

새 검색 매개 변수를 만들려면 `POST` 데이터베이스에 대 한 새 검색 매개 변수를 만듭니다. 아래 코드 예제에서는 환자 리소스에 [US Core 경합 검색 매개 변수](http://hl7.org/fhir/us/core/STU3.1.1/SearchParameter-us-core-race.html) 를 추가 하는 방법을 보여 줍니다.

```json
POST {fhirurl}/SearchParameter
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
> 새 검색 매개 변수는 FHIR 서버에 게시 하 고 데이터베이스를 다시 인덱싱 한 후 기능 문에 표시 됩니다. 다시 [인덱싱 작업](how-to-run-a-reindex.md)을 실행할 때까지 사용할 수 없습니다. 지금은 검색 매개 변수가 지원 되는지 데이터베이스 내에서 지원 되는지 여부를 확인 하는 유일한 방법입니다. 검색 매개 변수를 검색 하 여 검색 매개 변수를 찾을 수 있지만이 매개 변수를 기능 문에 표시 하지 않으면 다시 인덱싱 작업을 실행 합니다. 여러 검색 매개 변수를 게시 한 후에는 인덱스 작업을 트리거할 수 있습니다.

중요 한 필드 설명:

* **url**: 검색 매개 변수를 설명 하는 고유 키입니다. HL7와 같은 많은 조직에서는 위에서 설명한 대로 미국 코어 경합 검색 매개 변수에서 정의 하는 Url에 대해 표준 형식을 사용 합니다.

* **code**: 여기에 있는 값은 검색할 때 사용할 것입니다. 위의 예에서는로 검색 `GET {FHIR URL}/Patient?race=2028-9` 하 여 모든 아시아 환자을 가져옵니다. 이 값은 적용 되는 리소스에 대해 고유 해야 합니다.

* **base**: 검색 매개 변수가 적용 되는 리소스를 설명 합니다. 모든 리소스에 적용 되는 경우에만 리소스를 사용할 수 있습니다. 그렇지 않으면 관련 된 모든 리소스를 나열할 수 있습니다.
 
* **유형**: 검색 매개 변수의 데이터 형식을 설명 합니다.

* **식**: 검색 매개 변수를 설명 하는 경우 사양에는 기술적으로 필요 하지 않은 경우에도 식을 포함 해야 합니다. 이는 식이나 xpath 구문이 필요 하 고 FHIR 용 Azure API는 현재 xpath 구문을 무시 하기 때문입니다. 검색 값을 찾는 방법에 대해 설명 합니다. 

> [!NOTE]
> "형식"은 FHIR 용 Azure API에 대 한 지원으로 제한 됩니다. 즉, 특수 형식의 검색 매개 변수를 정의 하거나, 지원 되는 형식이 아니면 [복합 검색 매개 변수](overview-of-search.md) 를 정의할 수 없습니다.

검색 매개 변수를 추가한 후에는 검색 매개 변수를 FHIR 서버에서 사용할 수 있도록 인덱스 작업을 실행 하거나 예약 합니다.

## <a name="update-a-search-parameter"></a>검색 매개 변수 업데이트

검색 매개 변수를 업데이트 하려면를 사용 `PUT` 하 여 새 버전의 검색 매개 변수를 만듭니다.

`PUT {fhirurl}/SearchParameter/{SearchParameter ID}`

`SearchParameter ID`요청 본문의 ID 필드 `PUT` 와 호출에를 포함 해야 합니다 `PUT` .

> [!NOTE]
> 검색 매개 변수의 ID를 모르는 경우 검색할 수 있습니다. 를 사용 하면 `GET {fhirurl}/SearchParameter` 사용자 지정 검색 매개 변수가 모두 반환 되 고 검색 매개 변수를 스크롤하여 필요한 검색 매개 변수를 찾을 수 있습니다. 검색을 이름으로 제한할 수도 있습니다. 아래 예제에서는를 사용 하 여 이름을 검색할 수 있습니다 `USCoreRace: GET {fhirurl}/SearchParameter?name=USCoreRace` .

```json
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

결과가 업데이트 되 `SearchParameter` 고 버전이 증가 합니다.

  > [!Warning]
> 데이터베이스에 이미 인덱싱되는 SearchParameters를 업데이트할 때는 주의 해야 합니다. 기존 SearchParameter의 동작을 변경 하면 예상 되는 동작에 영향을 줄 수 있습니다. 

## <a name="delete-a-search-parameter"></a>검색 매개 변수 삭제

검색 매개 변수를 삭제 해야 하는 경우 다음을 사용 합니다.

`Delete {fhirurl}/SearchParameter/{SearchParameter ID}`

> [!Warning]
> 데이터베이스에 이미 인덱싱되는 SearchParameters를 삭제할 때는 주의 해야 합니다. 기존 SearchParameter의 동작을 변경 하면 예상 되는 동작에 영향을 줄 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 검색 매개 변수를 만드는 방법을 알아보았습니다. 작업을 인덱스를 만드는 방법에 대 한 자세한 내용은 다음을 참조 하세요.

>[!div class="nextstepaction"]
>[인덱스 작업을 실행 하는 방법](how-to-run-a-reindex.md)