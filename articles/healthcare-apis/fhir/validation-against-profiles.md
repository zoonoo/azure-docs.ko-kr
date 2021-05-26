---
title: Azure API for FHIR 프로필에 대한 FHIR 리소스 $validate
description: 프로필에 대한 FHIR 리소스 $validate
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 05/06/2021
ms.author: ginle
ms.openlocfilehash: 2c367dbed14e0dba9a8a95a3ce2709d2415c7cd6
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110466704"
---
# <a name="how-to-validate-fhir-resources-against-profiles"></a>프로필에 대해 FHIR 리소스의 유효성을 검사하는 방법

HL7 FHIR은 의료 데이터를 저장하고 교환하는 표준적이고 상호 운용 가능한 방법을 정의합니다. 기본 FHIR 사양 내에서도 FHIR이 사용되는 컨텍스트에 따라 추가 규칙 또는 확장을 정의하는 것이 유용할 수 있습니다. FHIR의 이러한 컨텍스트별 사용의 경우 **FHIR 프로필은** 추가 사양 계층에 사용됩니다.

[FHIR 프로필은](https://www.hl7.org/fhir/profiling.html) 로 표시되는 리소스에 대한 추가 컨텍스트(예: 제약 조건 또는 확장)를 `StructureDefinition` 설명합니다. HL7 FHIR 표준은 기본 리소스 집합을 정의하며, 이러한 표준 기본 리소스에는 제네릭 정의가 있습니다. FHIR 프로필을 사용하면 제약 조건 및 확장을 사용하여 리소스 정의를 좁히고 사용자 지정할 수 있습니다.

Azure API for FHIR 사용하면 프로필에 대해 리소스의 유효성을 검사하여 리소스가 프로필을 준수하는지 확인할 수 있습니다. 이 문서에서는 FHIR 프로필의 기본 사항과 `$validate` 리소스를 만들고 업데이트할 때 프로필에 대해 리소스의 유효성을 검사하는 데 사용하는 방법을 안내합니다.

## <a name="fhir-profile-the-basics"></a>FHIR 프로필: 기본 사항

프로필은 리소스에 대한 추가 컨텍스트를 설정하며, 일반적으로 `StructureDefinition` 리소스로 표시됩니다. `StructureDefinition` 는 리소스의 콘텐츠 또는 데이터 형식에 대한 규칙 집합(예: 리소스에 있는 필드 및 이러한 필드가 사용할 수 있는 값)을 정의합니다. 예를 들어 프로필은 카디널리티를 제한하거나(예: 요소를 제외하기 위해 최대 카디널리티를 0으로 설정), 요소의 콘텐츠를 단일 고정 값으로 제한하거나, 리소스에 필요한 확장을 정의할 수 있습니다. 기존 프로필에 추가 제약 조건을 지정할 수도 있습니다. `StructureDefinition`는 정식 URL로 식별됩니다.

```rest
http://hl7.org/fhir/StructureDefinition/{profile}
```

`{profile}`여기서 필드의 프로필 이름을 지정합니다.

예를 들면 다음과 같습니다.

- `http://hl7.org/fhir/StructureDefinition/patient-birthPlace` 는 환자의 등록된 출생 주소에 대한 정보가 필요한 기본 프로필입니다.
- `http://hl7.org/fhir/StructureDefinition/bmi` 는 을 나타내는 방법을 정의하는 또 다른 기본 프로필입니다.
- `http://hl7.org/fhir/us/core/StructureDefinition/us-core-allergyintolerance` 는 환자와 연결 된 리소스에 대 한 최소 기대치를 설정 하 `AllergyIntolerance` 고 확장 및 값 집합과 같은 필수 필드를 식별 하는 미국 코어 프로필입니다.

### <a name="base-profile-and-custom-profile"></a>기본 프로필 및 사용자 지정 프로필

프로필에는 기본 프로필과 사용자 지정 프로필 이라는 두 가지 유형이 있습니다. 기본 프로필은 `StructureDefinition` 리소스가 준수 해야 하 고 또는와 같은 기본 리소스로 정의 된 기본 프로필입니다 `Patient` `Observation` . 예를 들어 본문 Mass Index (BMI) `Observation` 프로필은 다음과 같이 시작 됩니다.

```json
{
  "resourceType" : "StructureDefinition",
  "id" : "bmi",
...
}
```

사용자 지정 프로필은 기본 프로필 위에 있는 추가 제약 조건의 집합으로, 기본 사양에 포함 되지 않은 리소스 매개 변수를 제한 하거나 추가 합니다. 사용자 지정 프로필은 기존 기본 리소스에서 제약 조건 및 확장을 지정 하 여 사용자가 직접 리소스 정의를 사용자 지정할 수 있기 때문에 유용 합니다. 예를 들어 성별을 기반으로 리소스 인스턴스를 표시 하는 프로필을 빌드할 수 있습니다 `AllergyIntolerance` `Patient` .이 경우 프로필을 사용 하 여 기존 프로필 위에 사용자 지정 프로필을 만듭니다 `Patient` `AllergyIntolerance` .

> [!NOTE]
> 사용자 지정 프로필은 기본 리소스 위에 빌드해야 하며 기본 리소스와 충돌 하지 않습니다. 예를 들어 요소에 1.. 1의 카디널리티가 있는 경우 사용자 지정 프로필은 선택적으로 만들 수 없습니다.

사용자 지정 프로필은 다양 한 구현 가이드에도 지정 됩니다. 몇 가지 일반적인 구현 가이드는 다음과 같습니다.

|이름 |URL
|---- |----
Us 핵심 |<https://www.hl7.org/fhir/us/core/>
파랑 단추 |<http://hl7.org/fhir/us/carin-bb/>
Da 다빈치 지불자 데이터 교환 |<http://hl7.org/fhir/us/davinci-pdex/>
Argonaut |<http://www.fhir.org/guides/argonaut/pd/>

## <a name="accessing-profiles-and-storing-profiles"></a>프로필 액세스 및 프로필 저장

### <a name="storing-profiles"></a>프로필 저장

서버에 프로필을 저장 하는 경우 요청을 수행할 수 있습니다 `POST` .

```rest
POST http://<your FHIR service base URL>/{Resource}
```

필드를 `{Resource}` 로 대체 하 `StructureDefinition` 고 `StructureDefinition` `POST` 또는 형식으로 서버에 대 한 리소스를 사용할 수 있습니다 `JSON` `XML` . 예를 들어 프로필을 저장하려는 경우 `us-core-allergyintolerance` 다음을 수행합니다.

```rest
POST http://my-fhir-server.azurewebsites.net/StructureDefinition?url=http://hl7.org/fhir/us/core/StructureDefinition/us-core-allergyintolerance
```

US Core 지체 불관용 프로필이 저장 및 검색되는 위치:

```json
{
    "resourceType" : "StructureDefinition",
    "id" : "us-core-allergyintolerance",
    "text" : {
        "status" : "extensions"
    },
    "url" : "http://hl7.org/fhir/us/core/StructureDefinition/us-core-allergyintolerance",
    "version" : "3.1.1",
    "name" : "USCoreAllergyIntolerance",
    "title" : "US  Core AllergyIntolerance Profile",
    "status" : "active",
    "experimental" : false,
    "date" : "2020-06-29",
        "publisher" : "HL7 US Realm Steering Committee",
    "contact" : [
    {
      "telecom" : [
        {
          "system" : "url",
          "value" : "http://www.healthit.gov"
        }
      ]
    }
  ],
    "description" : "Defines constraints and extensions on the AllergyIntolerance resource for the minimal set of data to query and retrieve allergy information.",

...
```

대부분의 프로필에는 리소스 `StructureDefinition` 종류가 있지만 용어 리소스인 및 형식일 수도 `ValueSet` `CodeSystem` 있습니다. [](http://hl7.org/fhir/terminologies.html) 예를 들어 JSON 형식의 프로필인 경우 서버는 와 마찬가지로 프로필에 할당된 가 있는 `POST` `ValueSet` 저장된 프로필을 `id` 반환합니다. `StructureDefinition` 다음은 [조건/진단 심각도](https://www.hl7.org/fhir/valueset-condition-severity.html) 등급에 대한 조건을 지정하는 조건 심각도 프로필을 업로드할 때 얻을 수 있는 예제입니다.

```json
{
    "resourceType": "ValueSet",
    "id": "35ab90e5-c75d-45ca-aa10-748fefaca7ee",
    "meta": {
        "versionId": "1",
        "lastUpdated": "2021-05-07T21:34:28.781+00:00",
        "profile": [
            "http://hl7.org/fhir/StructureDefinition/shareablevalueset"
        ]
    },
    "text": {
        "status": "generated"
    },
    "extension": [
        {
            "url": "http://hl7.org/fhir/StructureDefinition/structuredefinition-wg",
            "valueCode": "pc"
        }
    ],
    "url": "http://hl7.org/fhir/ValueSet/condition-severity",
    "identifier": [
        {
            "system": "urn:ietf:rfc:3986",
            "value": "urn:oid:2.16.840.1.113883.4.642.3.168"
        }
    ],
    "version": "4.0.1",
    "name": "Condition/DiagnosisSeverity",
    "title": "Condition/Diagnosis Severity",
    "status": "draft",
    "experimental": false,
    "date": "2019-11-01T09:29:23+11:00",
    "publisher": "FHIR Project team",
...
```

가 `resourceType` `ValueSet` 이고 프로필의 가 `url` 형식임을 지정하는 것을 볼 수 `ValueSet` `"http://hl7.org/fhir/ValueSet/condition-severity"` 있습니다.

### <a name="viewing-profiles"></a>프로필 보기

요청을 사용하여 서버의 기존 사용자 지정 프로필에 액세스할 수 `GET` 있습니다. 구현 가이드의 유효한 정식 URL이 있는 프로필과 같은 모든 유효한 프로필은 다음을 쿼리하여 액세스할 수 있어야 합니다.

```rest
GET http://<your FHIR service base URL>/StructureDefinition?url={canonicalUrl} 
```

여기서 필드는 `{canonicalUrl}` 프로필의 정식 URL로 대체됩니다.

예를 들어 US Core 리소스 프로필을 보려면 다음을 수행합니다. `Goal`

```rest
GET http://my-fhir-server.azurewebsites.net/StructureDefinition?url=http://hl7.org/fhir/us/core/StructureDefinition/us-core-goal
```

그러면 다음과 `StructureDefinition` 같이 시작하는 US Core 목표 프로필에 대한 리소스가 반환됩니다.

```json
{
  "resourceType" : "StructureDefinition",
  "id" : "us-core-goal",
  "url" : "http://hl7.org/fhir/us/core/StructureDefinition/us-core-goal",
  "version" : "3.1.1",
  "name" : "USCoreGoalProfile",
  "title" : "US Core Goal Profile",
  "status" : "active",
  "experimental" : false,
  "date" : "2020-07-21",
  "publisher" : "HL7 US Realm Steering Committee",
  "contact" : [
    {
      "telecom" : [
        {
          "system" : "url",
          "value" : "http://www.healthit.gov"
        }
      ]
    }
  ],
  "description" : "Defines constraints and extensions on the Goal resource for the minimal set of data to query and retrieve a patient's goal(s).",
...
```

FHIR 서버는 기본 프로필에 대한 인스턴스를 반환하지 않지만 다음과 `StructureDefinition` 같이 HL7 웹 사이트에서 쉽게 찾을 수 있습니다.

- `http://hl7.org/fhir/Observation.profile.json.html`
- `http://hl7.org/fhir/Patient.profile.json.html`


### <a name="profiles-in-the-capability-statement"></a>capability 문에 있는 프로필

`Capability Statement`에는 구조 정의 및 값 집합과 같은 서버 기능의 문으로 사용할 수 있는 FHIR 서버의 가능한 모든 동작이 나열됩니다. Azure API for FHIR 다음 형식으로 업로드 및 저장된 프로필에 대한 정보로 capability 문을 업데이트합니다.

- `CapabilityStatement.rest.resource.profile`
- `CapabilityStatement.rest.resource.supportedProfile`

여기에는 카디널리티, 바인딩, 확장 또는 기타 제한에 대한 제약 조건을 포함하여 리소스에 대한 전반적인 지원을 설명하는 프로필의 모든 사양이 표시됩니다. 따라서 `POST` 의 형식으로 프로필을 `StructureDefinition` 만들고 `GET` 리소스 메타 데이터를 통해 전체 기능 설명을 볼 때 `supportedProfiles` 업로드 한 프로필에 대 한 모든 세부 정보를 매개 변수 옆에 표시 합니다.

예를 들어 `POST` 미국 코어 환자 프로필은 다음과 같이 시작 됩니다.

```json
{
  "resourceType": "StructureDefinition",
  "id": "us-core-patient",
  "url": "http://hl7.org/fhir/us/core/StructureDefinition/us-core-patient",
  "version": "3.1.1",
  "name": "USCorePatientProfile",
  "title": "US Core Patient Profile",
  "status": "active",
  "experimental": false,
  "date": "2020-06-27",
  "publisher": "HL7 US Realm Steering Committee",
...
```

다음 `GET` 에 대 한 요청을 보냅니다 `metadata` .

```rest
GET http://<your FHIR service base URL>/metadata
```

`CapabilityStatement`FHIR 서버에 업로드 한 미국 코어 환자 프로필에 대 한 다음 정보를 포함 하는이 반환 됩니다.

```json
...
{
    "type": "Patient",
    "profile": "http://hl7.org/fhir/StructureDefinition/Patient",
    "supportedProfile":[
        "http://hl7.org/fhir/us/core/StructureDefinition/us-core-patient"
    ],
...
```

## <a name="validating-resources-against-the-profiles"></a>프로필에 대 한 리소스 유효성 검사

또는와 같은 FHIR 리소스 `Patient` 는 `Observation` 특정 프로필에 대 한 준수를 표현할 수 있습니다. 이를 통해 FHIR 서버는 연결 된 프로필 또는 지정 된 프로필에 대해 지정 된 리소스 **의 유효성을 검사할** 수 있습니다. 프로필에 대 한 리소스의 유효성을 검사 하는 것은 `Resource.meta.profile` 또는 구현 가이드에 나열 된 사양을 비롯 하 여 리소스가 프로필을 따르는지 확인 하는 것을 의미 합니다.

리소스의 유효성을 검사 하는 방법에는 두 가지가 있습니다. 먼저 `$validate` FHIR 서버에 이미 있는 리소스에 대해 작업을 사용할 수 있습니다. 둘째, `POST` 리소스 또는 작업의 일부로 서버에 사용할 수 있습니다 `Update` `Create` . 두 경우 모두, 리소스가 원하는 프로필에 맞지 않는 경우에 수행할 작업을 FHIR 서버 구성에서 결정할 수 있습니다.

### <a name="using-validate"></a>$validate 사용

`$validate`작업은 제공 된 프로필이 유효한 지 여부와 리소스가 지정 된 프로필을 따르는지 여부를 확인 합니다. [HL7 FHIR 사양](https://www.hl7.org/fhir/resource-operation-validate.html)에 설명 된 대로 및와 같은의을 지정할 수도 있습니다 `mode` `$validate` `create` `update` .

- `create`: 서버는 프로필 콘텐츠가 기존 리소스에서 고유한 지 확인 하 고 새 리소스로 만들 수 있는지 확인 합니다.
- `update`: 프로필이 지정한 기존 리소스에 대 한 업데이트 인지 확인 합니다 (예: 변경할 수 없는 필드는 변경 되지 않음).

서버는 항상 `OperationOutcome` 유효성 검사 결과로를 반환 합니다.

#### <a name="validating-an-existing-resource"></a>기존 리소스 유효성 검사

기존 리소스의 유효성을 검사 하려면 `$validate` 요청에서를 사용 합니다 `GET` .

```rest
GET http://<your FHIR service base URL>/{resource}/{resource ID}/$validate
```

예를 들면 다음과 같습니다.

```rest
GET http://my-fhir-server.azurewebsites.net/Patient/a6e11662-def8-4dde-9ebc-4429e68d130e/$validate
```

위의 예제에서는 기존 리소스 의 유효성을 `Patient` `a6e11662-def8-4dde-9ebc-4429e68d130e` 검사합니다. 유효한 경우 다음과 같은 를 얻게 됩니다. `OperationOutcome`

```json
{
    "resourceType": "OperationOutcome",
    "issue": [
        {
            "severity": "information",
            "code": "informational",
            "diagnostics": "All OK"
        }
    ]
}
```

리소스가 유효하지 않으면 리소스가 잘못된 이유에 대한 세부 정보가 있는 오류 코드와 오류 메시지가 표시됩니다. `4xx`또는 `5xx` 오류는 유효성 검사 자체를 수행할 수 없으며 리소스가 유효한지 여부를 알 수 없음을 의미합니다. 오류 `OperationOutcome` 메시지와 함께 반환된 예제는 다음과 같습니다.

```json
{
    "resourceType": "OperationOutcome",
    "issue": [
        {
            "severity": "error",
            "code": "invalid",
            "details": {
                "coding": [
                    {
                        "system": "http://hl7.org/fhir/dotnet-api-operation-outcome",
                        "code": "1028"
                    }
                ],
                "text": "Instance count for 'Patient.identifier.value' is 0, which is not within the specified cardinality of 1..1"
            },
            "location": [
                "Patient.identifier[1]"
            ]
        },
        {
            "severity": "error",
            "code": "invalid",
            "details": {
                "coding": [
                    {
                        "system": "http://hl7.org/fhir/dotnet-api-operation-outcome",
                        "code": "1028"
                    }
                ],
                "text": "Instance count for 'Patient.gender' is 0, which is not within the specified cardinality of 1..1"
            },
            "location": [
                "Patient"
            ]
        }
    ]
}
```

위의 예제에서 리소스는 `Patient` 환자 식별자 값과 성별이 필요한 제공된 프로필을 준수하지 않았습니다.

프로필을 매개 변수로 지정하려는 경우 US Core 프로필 및 에 대한 기본 프로필을 사용하여 다음 예제와 같이 유효성을 검사할 프로필의 정식 URL을 지정할 수 있습니다. `Patient` `heartrate`

```rest
GET http://<your FHIR service base URL>/{Resource}/{Resource ID}/$validate?profile={canonicalUrl}
```

예를 들면 다음과 같습니다.

```rest
GET http://my-fhir-server.azurewebsites.net/Patient/a6e11662-def8-4dde-9ebc-4429e68d130e/$validate?profile=http://hl7.org/fhir/us/core/StructureDefinition/us-core-patient
GET http://my-fhir-server.azurewebsites.net/Observation/12345678/$validate?profile=http://hl7.org/fhir/StructureDefinition/heartrate
```

#### <a name="validating-a-new-resource"></a>새 리소스 유효성 검사

서버에 업로드하는 새 리소스의 유효성을 검사하려면 다음 요청을 수행할 수 있습니다. `POST`

```rest
POST http://<your FHIR service base URL>/{Resource}/$validate
```

예를 들면 다음과 같습니다.

```rest
POST http://my-fhir-server.azurewebsites.net/Patient/$validate 
```

이 요청은 JSON 형식이든 XML 형식이든 관계없이 요청 페이로드에 지정하는 새 리소스를 만들고 업로드된 리소스의 유효성을 검사합니다. 그런 다음 새 `OperationOutcome` 리소스에 대한 유효성 검사의 결과로 를 반환합니다.

### <a name="validate-on-resource-create-or-resource-update"></a>리소스 CREATE 또는 리소스 UPDATE에 대한 유효성 검사

리소스 CREATE 또는 UPDATE와 같이 리소스의 유효성을 검사할 시기를 선택할 수 있습니다. 서버 구성 설정의 에서 이를 지정할 수 `CoreFeatures` 있습니다.

```json
{
   "FhirServer": {
      "CoreFeatures": {
            "ProfileValidationOnCreate": true,
            "ProfileValidationOnUpdate": false
        }
}
```

리소스가 제공된 를 `Resource.meta.profile` 준수하고 프로필이 시스템에 있는 경우 서버는 위의 구성 설정에 따라 작동합니다. 제공된 프로필이 서버에 없으면 유효성 검사 요청이 무시되고 에 남아 `Resource.meta.profile` 있습니다.
유효성 검사는 일반적으로 비용이 많이 드는 작업이므로 일반적으로 테스트 서버 또는 리소스의 작은 하위 집합에서만 실행됩니다. 따라서 이러한 방법으로 서버 쪽에서 유효성 검사 작업을 설정하거나 해제해야 합니다. 서버 구성에서 리소스 만들기/업데이트에 대 한 유효성 검사를 옵트아웃 (opt out) 하도록 지정 하는 경우 사용자는 `header` 만들기/업데이트 요청의에서 지정 하 여 동작을 재정의할 수 있습니다.

```rest
x-ms-profile-validation: true
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 $validate을 사용 하 여 프로필에 대 한 리소스의 유효성을 검사 하는 방법과 FHIR 프로필에 대해 알아보았습니다. Azure API에서 지원 되는 기타 기능에 대 한 자세한 내용은 다음을 확인 하세요.

>[!div class="nextstepaction"]
>[지원 되는 기능](fhir-features-supported.md)
