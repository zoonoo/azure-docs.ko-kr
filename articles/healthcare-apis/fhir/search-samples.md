---
title: Azure API for FHIR 검색 예
description: 다른 검색 매개 변수, 한정자 및 기타 FHIR 검색 도구를 사용하여 검색하는 방법
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 05/21/2021
ms.author: cavoeg
ms.openlocfilehash: 6e3a074c24305209047fbd3e741fdb81256374e5
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110460106"
---
# <a name="fhir-search-examples"></a>FHIR 검색 예제

다음은 검색 매개 변수 및 한정자, 체인 및 역방향 체인 검색, 복합 검색, 검색 결과에 대한 다음 항목 집합 보기 및 요청으로 검색을 포함하여 FHIR 검색 작업을 사용하는 몇 가지 `POST` 예입니다. 검색에 대한 자세한 내용은 [FHIR 검색 개요를](overview-of-search.md)참조하세요.
   
## <a name="search-result-parameters"></a>검색 결과 매개 변수

### <a name="_include"></a>_include

`_include` 는 리소스에서 지정된 리소스 매개 변수를 포함하는 리소스를 검색합니다. 예를 들어 `MedicationRequest` 리소스를 검색하여 매개 변수인 특정 환자의개체에 대한 정보가 포함된 리소스만 찾을 수 `reference` `patient` 있습니다. 아래 예제에서는 에서 `MedicationRequests` 참조되는 모든 환자와 모든 환자를 끌어오게 됩니다. `MedicationRequests`

```rest
 GET [your-fhir-server]/MedicationRequest?_include=MedicationRequest:patient

```

> [!NOTE]
> **_include** 및 **_revinclude** 100개 항목으로 제한됩니다.

### <a name="_revinclude"></a>_revinclude

`_revinclude` 를 사용하면 반대 방향을 로 검색할 수 `_include` 있습니다. 예를 들어 환자를 검색한 다음, 환자를 참조하는 모든 발생을 역방향으로 포함할 수 있습니다.

```rest
GET [your-fhir-server]/Patient?_revinclude=Encounter:subject

```
### <a name="_elements"></a>_elements

`_elements` 는 불필요한 데이터를 생략하여 응답 크기를 줄이기 위해 검색 결과를 필드의 하위 집합으로 좁혀 줍니다. 매개 변수는 쉼표로 구분된 기본 요소 목록을 허용합니다.

```rest
GET [your-fhir-server]/Patient?_elements=identifier,active

```

이 요청에서는 환자 번들을 다시 받게 되지만 각 리소스에는 식별자 및 환자의 활성 상태만 포함됩니다. 이 반환된 응답의 리소스에는 `meta.tag` 불완전한 결과 집합임을 나타내는 값이 `SUBSETTED` 포함됩니다.

## <a name="search-modifiers"></a>검색 한정자

### <a name="not"></a>:not

`:not` 특성이 true가 아닌 리소스를 찾을 수 있습니다. 예를 들어 성별이 암이 아닌 환자를 검색할 수 있습니다.

```rest
GET [your-fhir-server]/Patient?gender:not=female

```

반환 값으로, 빈 값 (성별 없이 지정 된 항목)을 포함 하 여 성별을이 아닌 모든 환자 항목을 가져옵니다. 이는 성별이 남성 인 환자을 검색 하는 것과는 다릅니다 .이는 특정 성별이 없는 항목은 포함 되지 않기 때문입니다.

### <a name="missing"></a>: 없음

`:missing` 값이 이면 지정 된 요소에 대 한 값이 없는 모든 리소스를 반환 하 `true` 고, 값이 인 경우 지정 된 요소를 포함 하는 모든 리소스를 반환 합니다 `false` . 단순 데이터 형식 요소의 경우 `:missing=true` 은 요소가 확장에 있지만 빈 값이 있는 모든 리소스에 대해 일치 합니다. 예를 들어 `Patient` 생년월일에 정보가 누락 된 모든 리소스를 찾으려는 경우 다음을 수행할 수 있습니다.

```rest
GET [your-fhir-server]/Patient?birthdate:missing=true

```

### <a name="exact"></a>: 정확히
`:exact` 는 `string` 매개 변수에 사용 되 고는 대/소문자 구분 및 문자 연결과 같이 매개 변수와 정확히 일치 하는 결과를 반환 합니다.

```rest
GET [your-fhir-server]/Patient?name:exact=Jon

```

이 요청은 `Patient` 이름이와 정확히 동일한 리소스를 반환 `Jon` 합니다. 리소스가 또는와 같은 이름으로 환자 된 경우 `Jonathan` `joN` 검색은 지정 된 값과 정확 하 게 일치 하지 않으므로 리소스를 무시 하 고 건너뜁니다.

### <a name="contains"></a>: contains
`:contains` 는 `string` 매개 변수에 사용 되며 검색 중인 필드 내의 문자열에서 지정 된 값과 부분적으로 일치 하는 리소스를 검색 합니다. `contains` 는 대/소문자를 구분 하지 않으며 문자 연결을 허용 합니다. 예를 들면 다음과 같습니다.

```rest
GET [your-fhir-server]/Patient?address:contains=Meadow

```

이 요청은 `Patient` `address` "목초지" 문자열이 포함 된 값이 있는 모든 리소스를 필드와 함께 반환 합니다. 즉, "Meadowers" 또는 "59 목초지 ST"와 같은 값을 포함 하는 주소가 검색 결과로 반환 될 수 있습니다.

## <a name="chained-search"></a>연결 된 검색 

여러 참조 매개 변수를 포함 하는 일련의 검색 작업을 수행 하기 위해 일련의 참조 매개 변수를 마침표를 사용 하 여 하나씩 서버 요청에 하나씩 추가 하 여 "체인" 할 수 있습니다 `.` . 예를 들어 특정 를 `DiagnosticReport` 포함하는 리소스에 `subject` 대한 참조를 사용하여 모든 `Patient` 리소스를 보려는 경우 `name`  

```rest
 GET [your-fhir-server]/DiagnosticReport?subject:Patient.name=Sarah

```

이 요청은 환자 주제가 "Sarah"인 모든 리소스를 반환합니다. `.`필드가 매개 `Patient` 변수의 참조 매개 변수에 대해 연결된 검색을 수행한 후의 `subject` 기간입니다.

일반적인 검색(체인 검색이 아닌)의 또 다른 일반적인 용도는 특정 환자의 모든 발견을 찾는 것입니다. `Patient`s에는 주체가 있는 가 하나 이상 `Encounter` 있는 경우가 많습니다. 제공된 를 사용하여 에 대한 모든 `Encounter` `Patient` 리소스를 검색하려면: `id`

```rest
GET [your-fhir-server]/Encounter?subject=Patient/78a14cbe-8968-49fd-a231-d43e6619399f

```

연결된 검색을 사용하여 와 같은 특정 정보와 일치하는 모든 `Encounter` `Patient` 리소스를 찾을 수 있습니다. `birthdate`

```rest
GET [your-fhir-server]/Encounter?subject:Patient.birthdate=1987-02-20

```

이렇게 하면 단일 `Encounter` 환자뿐만 아니라 지정된 생년월일 값이 있는 모든 환자에서 리소스를 검색할 수 있습니다. 

또한 하나의 요청에서 여러 조건을 검색할 수 있는 기호 를 사용하여 연결된 검색을 한 요청에서 두 번 이상 수행할 `&` 수 있습니다. 이러한 경우 연결된 검색은 모든 조건만 한 번에 충족하는 조건을 검색하는 대신 각 매개 변수를 "독립적으로" 검색합니다. AND 연산이 아닌 OR 연산입니다. 예를 들어 특정 이름 또는 특정 상태의 전문가를 가진 모든 환자를 얻으려면 다음을 수행합니다.

```rest
GET [your-fhir-server]/Patient?general-practitioner.name=Sarah&general-practitioner.address-state=WA

```

이렇게 하면 `Patient` "Sarah"가 로 있는 모든 `generalPractitioner` 리소스와 `Patient` WA `generalPractitioner` 상태의 주소가 있는 모든 리소스가 반환됩니다. 즉, NY 주의 Sarah와 WA 주의 Bill을 반환된 결과로 둘 다 사용할 수 있습니다. 연결된 검색은 모든 조건을 충족할 필요가 없으며 매개 변수에 따라 개별적으로 평가됩니다.

검색이 모든 조건을 그룹으로 포함하는 AND 작업이어야 하는 시나리오는 **아래의 복합 검색** 예제를 참조하세요.

## <a name="reverse-chain-search"></a>역방향 체인 검색

체인 검색을 사용하면 참조하는 리소스의 속성을 기반으로 리소스를 검색할 수 있습니다. 역방향 체인 검색을 사용 하 여 다른 방법으로이 작업을 수행할 수 있습니다. 매개 변수를 사용 하 여 참조 하는 리소스의 속성을 기준으로 리소스를 검색할 수 있습니다 `_has` . 예를 들어 `Observation` 리소스에는 `patient` 환자 리소스를 참조 하는 검색 매개 변수가 있습니다. 특정를 사용 하 여에서 참조 하는 모든 환자 리소스를 찾으려면 `Observation` `code` 다음을 수행 합니다.

```rest
GET [base]/Patient?_has:Observation:patient:code=527

```

이 요청은 코드에서 참조 하는 환자 리소스를 반환 `Observation` `527` 합니다. 

또한 역방향 체인 검색은 재귀 구조를 포함할 수 있습니다. 예를 들어 특정 사용자의 감사 이벤트를 관찰 하는 모든 환자를 검색 하려는 경우 `Observation` `janedoe` 다음을 수행할 수 있습니다.

```rest
GET [base]/Patient?_has:Observation:patient:_has:AuditEvent:entity:user=janedoe

``` 

> [!NOTE]
> FHIR 용 Azure API와 Cosmos에서 지 원하는 오픈 소스 FHIR 서버에서 연결 된 검색 및 역방향 연결 검색은 MVP 구현입니다. Cosmos DB에 대 한 연결 된 검색을 수행 하기 위해 구현은 검색 식을 아래로 이동 하 고 하위 쿼리를 발행 하 여 일치 하는 리소스를 확인 합니다. 식의 각 수준에 대해 수행 됩니다. 쿼리가 100 개 보다 많은 결과를 반환 하면 오류가 throw 됩니다. 기본적으로 연결 된 검색은 기능 플래그 뒤에 있습니다. Cosmos DB에서 연결 된 검색을 사용 하려면 헤더를 사용 합니다.--연결-검색: true를 사용 합니다.

## <a name="composite-search"></a>복합 검색

한 번에 여러 조건을 충족 하는 리소스를 검색 하려면 단일 매개 변수 값의 시퀀스를 기호와 조인 하는 복합 검색을 사용 `$` 합니다. 반환 된 결과는 조인 된 검색 매개 변수로 지정 된 모든 조건과 일치 하는 리소스의 교집합입니다. 이러한 검색 매개 변수를 복합 검색 매개 변수 라고 하며, 중첩 된 구조체의 여러 매개 변수를 결합 하는 새 매개 변수를 정의 합니다. 예를 `DiagnosticReport` 들어 `Observation` 9.2 보다 작거나 같은 potassium 값을 포함 하는 모든 리소스를 찾으려는 경우:

```rest
GET [your-fhir-server]/DiagnosticReport?result.code-value-quantity=2823-3$lt9.2

``` 

이 요청은 코드를 포함 하는 구성 요소를 지정 합니다 `2823-3` .이 경우에는 potassium입니다. 기호 다음에는 `$` `lt` "보다 낮거나 같음"에 를 `9.2` 사용하고, 을(를) 시차 값 범위에 사용하는 구성 요소의 값 범위를 지정합니다. 

## <a name="search-the-next-entry-set"></a>다음 항목 집합 검색

단일 검색 쿼리당 반환할 수 있는 최대 항목 수는 1000개입니다. 그러나 검색 쿼리와 일치하는 항목이 1000개 이상 있을 수 있으며, 반환된 처음 1000개 항목 이후의 다음 항목 집합을 볼 수 있습니다. 이러한 경우 `url` `searchset` 아래 결과와 같이 에서 연속 토큰 값을 사용합니다. `Bundle`

```json
    "resourceType": "Bundle",
    "id": "98731cb7-3a39-46f3-8a72-afe945741bd9",
    "meta": {
        "lastUpdated": "2021-04-22T09:58:16.7823171+00:00"
    },
    "type": "searchset",
    "link": [
        {
            "relation": "next",
            "url": "[your-fhir-server]/Patient?_sort=_lastUpdated&ct=WzUxMDAxNzc1NzgzODc5MjAwODBd"
        },
        {
            "relation": "self",
            "url": "[your-fhir-server]/Patient?_sort=_lastUpdated"
        }
    ],

```

그리고 필드 아래에 제공된 URL에 대한 GET 요청을 수행합니다. `relation: next`

```rest
GET [your-fhir-server]/Patient?_sort=_lastUpdated&ct=WzUxMDAxNzc1NzgzODc5MjAwODBd

```

그러면 검색 결과에 대한 다음 항목 집합이 반환됩니다. 는 `searchset` 검색 결과 항목의 전체 집합이며 연속 `url` 토큰은 검색 쿼리에 대해 반환되는 최대 항목 수에 대한 제한 때문에 첫 번째 집합에 표시되지 않는 항목을 검색하기 위해 서버에서 제공하는 링크입니다.

## <a name="search-using-post"></a>POST를 사용하여 검색

위에서 언급한 모든 검색 예제에서 `GET` 요청을 사용했습니다. 를 사용하여 요청을 사용하여 검색 작업을 수행할 수도 `POST` 있습니다. `_search`

```rest
POST [your-fhir-server]/Patient/_search?_id=45

```

이 요청은 `Patient` 값이 45인 모든 리소스를 `id` 반환합니다. GET 요청과 마찬가지로 서버는 조건을 충족하는 리소스 집합을 결정하고 HTTP 응답에서 번들 리소스를 반환합니다.

쿼리 매개 변수가 양식 본문으로 제출되는 POST를 사용하여 검색하는 또 다른 예는 다음과 같습니다.

```rest
POST [your-fhir-server]/Patient/_search
content-type: application/x-www-form-urlencoded

name=John

```
## <a name="next-steps"></a>다음 단계

>[!div class="nextstepaction"]
>[FHIR 검색 개요](overview-of-search.md)