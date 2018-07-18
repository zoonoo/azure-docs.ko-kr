---
title: Knowledge Exploration Service API의 스키마 형식 | Microsoft Docs
description: Cognitive Services에서 KES(Knowledge Exploration Service) API의 스키마 형식을 알아봅니다.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 3009392a5acb12a8f4df3d30a2cbe5e74f2172fc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373198"
---
# <a name="schema-format"></a>스키마 형식
스키마는 인덱스를 만드는 데 사용되는 데이터 파일에서 개체의 특성 구조를 설명하는 JSON 파일에 지정됩니다.  각 특성에서 스키마는 이름, 데이터 형식, 선택적 작업 및 선택적 동의어 목록을 지정합니다.  개체는 각 특성의 값이 0개 이상일 수 있습니다.  다음은 학술 저서 도메인의 간단한 예입니다.

``` json
{
  "attributes":[
    {"name":"Title", "type":"String"},
    {"name":"Year", "type":"Int32"},
    {"name":"Author", "type":"Composite"},
    {"name":"Author.Id", "type":"Int64", "operations":["equals"]},
    {"name":"Author.Name", "type":"String"},
    {"name":"Author.Affiliation", "type":"String"},
    {"name":"Keyword", "type":"String", "synonyms":"Keyword.syn"}
  ]
}
```

특성 이름은 대/소문자를 구분하는 식별자로, 문자로 시작하며 문자(A-Z), 숫자(0-9) 및 밑줄(\_)로만 구성됩니다.  예약된 “logprob” 특성은 개체 간의 상대적인 자연 로그 확률을 지정하는 데 사용됩니다.

## <a name="attribute-type"></a>특성 유형
다음은 지원되는 특성 데이터 형식의 목록입니다.

| type | 설명 | 작업 | 예 |
|------|-------------|------------|---------|
| 문자열 | 문자열(1-1024자) | equals, starts_with | “hello world” |
| Int32 | 서명된 32비트 정수 | equals, starts_with, is_between | 2016 |
| Int64 | 서명된 64비트 정수 | equals, starts_with, is_between | 9876543210 |
| Double | 배정밀도 부동 소수점 값 | equals, starts_with, is_between | 1.602e-19 |
| Date | 날짜(1400-01-01 ~ 9999-12-31) | equals, is_between | ‘2016-03-14’ |
| Guid | 전역적으로 고유한 식별자 | equals | “602DD052-CC47-4B23-A16A-26B52D30C05B” |
| Blob | 내부적으로 압축된 인덱싱되지 않은 데이터 | *없음* | “Empower every person and every organization on the planet to achieve more” |
| Composite | 여러 하위 특성의 복합| *해당 없음* | { “Name”:“harry shum”, “Affiliation”:“microsoft” } |

문자열 특성은 사용자 쿼리의 일환으로 나타날 수 있는 문자열 값을 나타내는 데 사용됩니다.  “micros”를 “microsoft”에 맞추는 것과 같이 쿼리 완성 시나리오에 *starts_with* 작업과 함께 정확히 일치 *equals* 작업을 지원합니다.  맞춤법 오류를 처리하는 대/소문자를 구분하지 않는 유사 일치는 이후 릴리스에서 지원됩니다.

Int32/Int64/Double 특성은 숫자 값을 나타내는 데 사용됩니다.  *is_between* 작업은 실행 시 같지 않음 지원을 활성화합니다(lt, le, gt, ge).  *starts_with* 작업은 “20”을 “2016”에 또는 “3”을 “3.14”에 맞추는 것과 같이 쿼리 완성 시나리오를 지원합니다.

날짜 특성은 날짜 값을 효율적으로 인코딩하는 데 사용됩니다.  *is_between* 작업은 실행 시 같지 않음 지원을 활성화합니다(lt, le, gt, ge).
  
Guid 특성은 *equals* 작업에 대한 기본 지원을 통해 GUID 값을 효율적으로 나타내는 데 사용됩니다.

Blob 특성은 blob 값의 콘텐츠를 기반으로 하는 모든 인덱싱 작업에 대한 지원 없이 해당하는 개체에서 런타임 조회에 대한 잠재적으로 큰 데이터 blob을 효율적으로 인코딩하는 데 사용됩니다.

### <a name="composite-attributes"></a>composite 특성
composite 특성은 특성 값의 그룹화를 나타내는 데 사용됩니다.  각 하위 특성의 이름은 뒤에 “.”이 붙는 composite 특성의 이름으로 시작됩니다.  composite 특성에 대한 값은 중첩된 특성 값을 포함하는 JSON 개체로 지정됩니다.  composite 특성은 여러 개체 값을 가질 수 있습니다.  그러나 composite 특성은 자체 composite 특성인 하위 특성을 가질 수 없습니다.

위의 학술 저서 예제에서 이를 통해 서비스는 “harry shum”이 “microsoft”에 있을 때 작성한 논문을 쿼리할 수 있습니다.  composite 특성을 사용하지 않으면 서비스는 저자 중 한 명이 “harry shum”이고, 저자 중 한 명이 “microsoft”에 있을 때 작성된 논문만 쿼리할 수 있습니다.  자세한 내용은 [Composite 쿼리](SemanticInterpretation.md#composite-function)를 참조하세요.

## <a name="attribute-operations"></a>특성 작업
기본적으로 각 특성은 특성 데이터 형식에 사용할 수 있는 모든 작업을 지원하도록 인덱싱됩니다.  특정 작업이 필요하지 않은 경우 인덱싱된 작업의 집합을 명시적으로 지정하여 인덱스의 크기를 줄일 수 있습니다.  위에 나온 예제 스키마의 다음 코드 조각에서 Author.Id 특성은 *equals* 작업만 지원하도록 인덱싱되었지만 Int32 특성에 대한 추가적인 *starts_with* 및 *is_between*  작업은 해당되지 않습니다.
```json
{"name":"Author.Id", "type":"Int32", "operations":["equals"]}
```

특성이 문법 내에서 참조되는 경우 서비스가 부분 쿼리에서 완성을 생성할 수 있도록 *starts_with* 작업을 스키마에서 지정해야 합니다.  

## <a name="attribute-synonyms"></a>특성 동의어
종종 동의어로 특정 문자열 특성 값을 참조하는 것이 좋습니다.  예를 들어 사용자가 “MSFT” 또는 “MS”로 “Microsoft”를 참조할 수 있습니다.  이러한 경우 특성 정의는 스키마 파일과 동일한 디렉터리에 있는 스키마 파일의 이름을 지정할 수 있습니다.  동의어 파일의 각 줄에는 다음 JSON 형식의 동의어 항목을 나타냅니다. `["<canonical>", "<synonym>"]`  예제 스키마에서 “AuthorName.syn”은 Author.Name 특성에 대한 동의어 값을 포함하는 JSON 파일입니다.

`{"name":"Author.Name", "type":"String", "synonyms":"AuthorName.syn"}`


Canonical 값에는 여러 동의어가 있을 수 있습니다.  여러 Canonical 값이 공용 동의어를 공유할 수 있습니다.  그러한 경우 서비스는 여러 해석을 통해 모호성을 해결합니다.  다음은 위에 나온 스키마에 해당하는 예제 AuthorName.syn 동의어 파일입니다.
```json
["harry shum","heung-yeung shum"]
["harry shum","h shum"]
["henry shum","h shum"]
...
```
