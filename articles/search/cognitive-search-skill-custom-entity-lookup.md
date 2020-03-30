---
title: 사용자 지정 엔터티 조회 인지 검색 기술
titleSuffix: Azure Cognitive Search
description: Azure 인지 검색 인지 검색 파이프라인의 텍스트에서 다른 사용자 지정 엔터티를 추출합니다. 이 기술은 현재 공개 미리 보기 상태입니다.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 8674438032ebd925296c95e9ffa0a2a0b95322f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79369780"
---
#     <a name="custom-entity-lookup-cognitive-skill-preview"></a>사용자 지정 엔터티 조회 인지 기술(미리 보기)

> [!IMPORTANT] 
> 이 기술은 현재 공개 미리 보기 상태입니다. 미리 보기 기능은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. 현재 포털 또는 .NET SDK 지원이 없습니다.

**사용자 지정 엔터티 조회** 기술은 사용자 정의 단어 및 구 목록에서 텍스트를 찾습니다. 이 목록을 사용하면 일치하는 엔터티로 모든 문서에 레이블을 지정합니다. 이 기술은 유사하지만 정확하지 않은 일치 항목을 찾기 위해 적용할 수 있는 퍼지 매칭도 지원합니다.  

이 기술은 코그너티브 서비스 API에 구속되지 않으며 미리 보기 기간 동안 무료로 사용할 수 있습니다. 그러나 일일 보강 한도를 재정의하려면 [코그너티브 서비스 리소스를 계속 연결해야](https://docs.microsoft.com/azure/search/cognitive-search-attach-cognitive-services)합니다. 일일 한도는 Azure 인지 검색을 통해 액세스할 때 코그너티브 서비스에 대한 무료 액세스에 적용됩니다.

## <a name="odatatype"></a>@odata.type  
마이크로소프트.스킬.텍스트.커스텀엔티룩업스킬 

## <a name="data-limits"></a>데이터 제한
+ 지원되는 최대 입력 레코드 크기는 256MB입니다. 사용자 지정 엔터티 조회 기술로 보내기 전에 데이터를 분할해야 하는 경우 [텍스트 분할 기술을](cognitive-search-skill-textsplit.md)사용하는 것이 좋습니다.
+ 지원되는 최대 엔터티 정의 테이블은 *엔터티DefitionUri* 매개 변수를 사용하여 제공되는 경우 10MB입니다. 
+ *인라인엔터티정의* 매개변수를 사용하여 엔터티가 인라인으로 정의된 경우 지원되는 최대 크기는 10KB입니다.

## <a name="skill-parameters"></a>기술 매개 변수

매개 변수는 대/소문자를 구분합니다.

| 매개 변수 이름     | 설명 |
|--------------------|-------------|
| 엔터티정의Uri    | 일치하는 모든 대상 텍스트를 포함하는 JSON 또는 CSV 파일에 대한 경로입니다. 이 엔터티 정의는 인덱서 실행의 시작 부분에서 읽습니다. 이 파일 중간 실행에 대한 업데이트는 후속 실행될 때까지 실현되지 않습니다. 이 구성은 HTTPS를 통해 액세스할 수 있어야 합니다. 예상 CSV 또는 JSON 스키마는 [아래의 사용자 지정 엔터티 정의](#custom-entity-definition-format) 형식"을 참조하십시오.|
|인라인엔티티정의 | 인라인 JSON 엔터티 정의입니다. 이 매개 변수는 엔터티를 대체합니다정의 매개 변수가 있는 경우. 10KB 이하의 구성은 인라인으로 제공될 수 있습니다. 예상 JSON 스키마는 아래 [사용자 지정 엔터티 정의를](#custom-entity-definition-format) 참조하십시오. |
|defaultLanguageCode |    (선택 사항) 입력 텍스트를 토큰화하고 설명하는 데 사용되는 입력 텍스트의 언어 코드입니다. 다음 언어가 지원됩니다. `da, de, en, es, fi, fr, it, ko, pt` 기본값은 영어()입니다.`en` languagecode-countrycode 형식을 전달하는 경우 형식의 languagecode 부분만 사용됩니다.  |


## <a name="skill-inputs"></a>기술 입력

| 입력 이름      | 설명                   |
|---------------|-------------------------------|
| text          | 분석할 텍스트입니다.          |
| languageCode    | (선택 사항) 기본값은 `"en"`입니다.  |


## <a name="skill-outputs"></a>기술 출력


| 출력 이름      | 설명                   |
|---------------|-------------------------------|
| 엔터티 | 발견된 일치 항목 및 관련 메타데이터에 대한 정보를 포함하는 개체의 배열입니다. 식별된 각 엔터티에는 다음 필드가 포함될 수 있습니다.  <ul> <li> *이름*: 식별된 최상위 엔터티입니다. 엔터티는 "정규화된" 양식을 나타냅니다. </li> <li> *id*: "사용자 지정 엔터티 정의 형식"의 사용자가 정의한 엔터티에 대한 고유 식별자입니다.</li> <li> *설명*: "사용자 지정 엔터티 정의 형식"에서 사용자가 정의한 엔터티 설명입니다. </li> <li> *유형:* "사용자 지정 엔터티 정의 형식"의 사용자가 정의한 엔터티 형식입니다.</li> <li> *하위 유형:* "사용자 지정 엔터티 정의 형식"의 사용자가 정의한 엔터티 하위 유형입니다.</li>  <li> *일치*: 원본 텍스트에서 해당 엔터티에 대한 각 일치 항목을 설명하는 컬렉션입니다. 각 경기에는 다음과 같은 멤버가 있습니다. </li> <ul> <li> *텍스트*: 원본 문서의 원시 텍스트가 일치합니다. </li> <li> *offset*: 텍스트에서 일치하는 영역이 발견된 위치입니다. </li> <li> *길이*: 일치하는 텍스트의 길이입니다. </li> <li> *matchDistance*: 이 일치가 다른 문자 수는 원래 엔터티 이름 또는 별칭과 다릅니다.  </li> </ul> </ul>
  |

## <a name="custom-entity-definition-format"></a>사용자 지정 엔터티 정의 형식

사용자 지정 엔터티 조회 기술에 사용자 지정 엔터티 목록을 제공하는 방법에는 3가지가 있습니다. 에서 목록을 제공할 수 있습니다. CSV 파일, . JSON 파일 또는 기술 정의의 일부로 인라인 정의로.  

정의 파일이 있는 경우 . CSV 또는 . JSON 파일, 파일의 경로는 엔티티의 일부로 제공 될 *필요가DefitionUri* 매개 변수. 이 경우 파일은 각 인덱서 실행의 시작 부분에 한 번 다운로드됩니다. 인덱서가 실행되도록 하는 한 파일에 액세스할 수 있어야 합니다. 또한 이 파일은 UTF-8을 인코딩해야 합니다.

정의가 인라인으로 제공되는 경우 *inlineEntitiesDefinition* 기술 매개 변수의 내용으로 인라인으로 제공되어야 합니다. 

### <a name="csv-format"></a>CSV 형식

파일에 대한 경로를 제공하고 *엔터티에* 설정하여 CSV(Comma 분리된 값) 파일에서 찾을 사용자 지정 엔터티의 정의를 제공할 수 있습니다. 경로는 https 위치에 있어야 합니다. 정의 파일의 크기는 최대 10MB입니다.

CSV 형식은 간단합니다. 각 줄은 아래와 같이 고유한 엔터티를 나타냅니다.

```
Bill Gates, BillG, William H. Gates
Microsoft, MSFT
Satya Nadella 
```

이 경우 발견된 엔터티(빌 게이츠, 사티나 나델라, 마이크로소프트)로 반환할 수 있는 세 개의 엔터티가 있지만 줄에 있는 용어(별칭)가 텍스트에 일치하는 경우 식별됩니다. 예를 들어 문서에서 "윌리엄 H. 게이츠" 문자열이 발견되면 "Bill Gates" 엔터티에 대한 일치 가 반환됩니다.

### <a name="json-format"></a>JSON 형식

JSON 파일에서 찾을 사용자 지정 엔터티의 정의도 제공할 수 있습니다. JSON 형식은 용어별 일치 규칙을 정의할 수 있으므로 좀 더 유연하게 사용할 수 있습니다. 예를 들어 각 용어에 대해 퍼지 일치 거리(Damerau-Levenshtein 거리)를 지정하거나 일치가 대/소문자를 구분해야 하는지 여부를 지정할 수 있습니다. 

 CSV 파일과 마찬가지로 JSON 파일에 대한 경로를 제공하고 *엔터티DefitionUri* 기술 매개 변수에 설정해야합니다. 경로는 https 위치에 있어야 합니다. 정의 파일의 크기는 최대 10MB입니다.

가장 기본적인 JSON 사용자 지정 엔터티 목록 정의는 일치하는 엔터티 목록일 수 있습니다.

```json
[ 
    { 
        "name" : "Bill Gates"
    }, 
    { 
        "name" : "Microsoft"
    }, 
    { 
        "name" : "Satya Nadella"
    }
]
```

JSON 정의의 보다 복잡한 예제는 선택적으로 각 엔터티의 ID, 설명, 형식 및 하위 유형뿐만 아니라 다른 *별칭을*제공할 수 있습니다. 별칭 용어가 일치하는 경우 엔터티도 반환됩니다.

```json
[ 
    { 
        "name" : "Bill Gates",
        "description" : "Microsoft founder." ,
        "aliases" : [ 
            { "text" : "William H. Gates", "caseSensitive" : false },
            { "text" : "BillG", "caseSensitive" : true }
        ]
    }, 
    { 
        "name" : "Xbox One", 
        "type": "Harware",
        "subtype" : "Gaming Device",
        "id" : "4e36bf9d-5550-4396-8647-8e43d7564a76",
        "description" : "The Xbox One product"
    }, 
    { 
        "name" : "LinkedIn" , 
        "description" : "The LinkedIn company", 
        "id" : "differentIdentifyingScheme123", 
        "fuzzyEditDistance" : 0 
    }, 
    { 
        "name" : "Microsoft" , 
        "description" : "Microsoft Corporation", 
        "id" : "differentIdentifyingScheme987", 
        "defaultCaseSensitive" : false, 
        "defaultFuzzyEditDistance" : 1, 
        "aliases" : [ 
            { "text" : "MSFT", "caseSensitive" : true }
        ]
    } 
] 
```

아래 표는 일치하는 엔터티를 정의할 때 설정할 수 있는 다양한 구성 매개 변수에 대해 자세히 설명합니다.

|  필드 이름  |        설명  |
|--------------|----------------------|
| name | 최상위 엔터티 설명자입니다. 스킬 출력의 일치 항목이 이 이름으로 그룹화되며 발견되는 텍스트의 "정규화" 형식을 나타내야 합니다.  |
| description  | (선택 사항) 이 필드는 일치하는 텍스트에 대한 사용자 지정 메타데이터에 대한 통과로 사용할 수 있습니다. 이 필드의 값은 기술 출력의 엔터티와 일치할 때마다 표시됩니다. |
| type | (선택 사항) 이 필드는 일치하는 텍스트에 대한 사용자 지정 메타데이터에 대한 통과로 사용할 수 있습니다. 이 필드의 값은 기술 출력의 엔터티와 일치할 때마다 표시됩니다. |
| subtype | (선택 사항) 이 필드는 일치하는 텍스트에 대한 사용자 지정 메타데이터에 대한 통과로 사용할 수 있습니다. 이 필드의 값은 기술 출력의 엔터티와 일치할 때마다 표시됩니다. |
| id | (선택 사항) 이 필드는 일치하는 텍스트에 대한 사용자 지정 메타데이터에 대한 통과로 사용할 수 있습니다. 이 필드의 값은 기술 출력의 엔터티와 일치할 때마다 표시됩니다. |
| 케이스민감성 | (선택 사항) 기본값은 false입니다. 엔터티 이름과의 비교가 문자 대/소문자에 민감해야 하는지 여부를 나타내는 부울 값입니다. "마이크로 소프트"의 샘플 케이스 무감각 일치 가 될 수 있습니다 : 마이크로 소프트, 마이크로 소프트, 마이크로 소프트 |
| 퍼지편집거리 | (선택 사항) 기본값은 0입니다. 최대값 5. 여전히 엔터티 이름과 일치를 구성하는 서로 다른 문자의 허용 가능한 수를 나타냅니다. 지정된 일치에 대해 가능한 가장 작은 퍼지가 반환됩니다.  예를 들어 편집 거리가 3으로 설정되어 있으면 "Windows 10"은 여전히 "Windows", "Windows10" 및 "Windows 7"과 일치합니다. <br/> 대/소문자 구분이 false로 설정된 경우 대/소문자 차이는 퍼지 허용 오차에 포함되지 않지만 그렇지 않으면 수행됩니다. |
| 기본 케이스 민감성 | (선택 사항) 이 엔터티의 기본 대/소문자 구분 값을 변경합니다. 모든 별칭 caseSensitive 값의 기본값을 변경 하는 데 사용 됩니다. |
| 디폴트퍼지에디트거리 | (선택 사항) 이 엔티티의 기본 퍼지 편집 거리 값을 변경합니다. 모든 별칭 퍼지Edit거리 값의 기본값을 변경하는 데 사용할 수 있습니다. |
| 별칭 | (선택 사항) 루트 엔터티 이름에 대체 맞춤법 또는 동의어를 지정하는 데 사용할 수 있는 복잡한 개체의 배열입니다. |

| 별칭 속성 | 설명 |
|------------------|-------------|
| text  | 일부 대상 엔터티 이름의 대체 맞춤법 또는 표현입니다.  |
| 케이스민감성 | (선택 사항) 위의 루트 엔터티 "caseSensitive" 매개 변수와 동일한 역할을 하지만 이 하나의 별칭에만 적용됩니다. |
| 퍼지편집거리 | (선택 사항) 위의 루트 엔티티 "퍼지EditDistance" 매개 변수와 동일한 역할을 하지만 이 하나의 별칭에만 적용됩니다. |


### <a name="inline-format"></a>인라인 형식

경우에 따라 기술 정의에 직접 인라인으로 일치하도록 사용자 지정 엔터티 목록을 제공하는 것이 더 편리할 수 있습니다. 이 경우 위에서 설명한 것과 유사한 JSON 형식을 사용할 수 있지만 기술 정의에 인라인되어 있습니다.
크기가 10KB 미만(직렬화 크기)인 구성만 인라인으로 정의할 수 있습니다. 

##    <a name="sample-definition"></a>샘플 정의

인라인 형식을 사용하는 샘플 기술 정의는 다음과 같습니다.

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.CustomEntityLookupSkill",
    "context": "/document",
    "inlineEntitiesDefinition": 
    [
      { 
        "name" : "Bill Gates",
        "description" : "Microsoft founder." ,
        "aliases" : [ 
            { "text" : "William H. Gates", "caseSensitive" : false },
            { "text" : "BillG", "caseSensitive" : true }
        ]
      }, 
      { 
        "name" : "Xbox One", 
        "type": "Harware",
        "subtype" : "Gaming Device",
        "id" : "4e36bf9d-5550-4396-8647-8e43d7564a76",
        "description" : "The Xbox One product"
      }
    ],    
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "entities",
        "targetName": "matchedEntities"
      }
    ]
  }
```
또는 엔터티 정의 파일에 대한 포인터를 제공하기로 결정한 경우 엔터티DefinitionUri 형식을 사용하는 샘플 기술 정의가 다음과 같습니다.

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.CustomEntityLookupSkill",
    "context": "/document",
    "entitiesDefinitionUri": "https://myblobhost.net/keyWordsConfig.csv",    
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "entities",
        "targetName": "matchedEntities"
      }
    ]
  }

```

##    <a name="sample-input"></a>샘플 입력

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "The company microsoft was founded by Bill Gates. Microsoft's gaming console is called Xbox",
             "languageCode": "en"
           }
      }
    ]
}
```

##    <a name="sample-output"></a>샘플 출력

```json
  { 
    "values" : 
    [ 
      { 
        "recordId": "1", 
        "data" : { 
          "entities": [
            { 
              "name" : "Microsoft", 
              "description" : "This document refers to Microsoft the company", 
              "id" : "differentIdentifyingScheme987", 
              "matches" : [ 
                { 
                  "text" : "microsoft", 
                  "offset" : 13, 
                  "length" : 9, 
                  "matchDistance" : 0 
                }, 
                { 
                  "text" : "Microsoft",
                  "offset" : 49, 
                  "length" : 9, 
                  "matchDistance" : 0
                }
              ] 
            },
            { 
              "name" : "Bill Gates",
              "description" : "William Henry Gates III, founder of Microsoft.", 
              "matches" : [
                { 
                  "text" : "Bill Gates",
                  "offset" : 37, 
                  "length" : 10,
                  "matchDistance" : 0 
                }
              ]
            }
          ] 
        } 
      } 
    ] 
  } 
```

## <a name="errors-and-warnings"></a>오류 및 경고

### <a name="warning-reached-maximum-capacity-for-matches-skipping-all-further-duplicate-matches"></a>경고: 일치 항목의 최대 용량에 도달하여 중복 일치 항목을 모두 건너뜁니다.

검색된 일치 항목 수가 허용된 최대값보다 크면 이 경고가 내보전됩니다. 이 경우 중복 일치 항목 포함이 중지됩니다. 허용되지 않는 경우 개별 사용 사례를 지원할 수 있도록 [지원 티켓을](https://ms.portal.azure.com/#create/Microsoft.Support) 제출하십시오.

## <a name="see-also"></a>참조

+ [기본 제공 기술](cognitive-search-predefined-skills.md)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [엔터티 인식 기술(잘 알려진 엔터티 검색)](cognitive-search-skill-entity-recognition.md)
