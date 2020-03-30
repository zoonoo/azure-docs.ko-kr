---
title: PII 감지 인지 기술(미리 보기)
titleSuffix: Azure Cognitive Search
description: Azure 인지 검색의 보강 파이프라인에서 텍스트에서 개인 식별 정보를 추출하고 마스킹합니다. 이 기술은 현재 공개 미리 보기 상태입니다.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: f21200bc6f5b25f3330f5bb87c0843caa5a84e56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298890"
---
#    <a name="pii-detection-cognitive-skill"></a>PII 감지 인지 기술

> [!IMPORTANT] 
> 이 기술은 현재 공개 미리 보기 상태입니다. 미리 보기 기능은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. 현재 포털 또는 .NET SDK 지원이 없습니다.

**PII 검색** 기술은 입력 텍스트에서 개인 식별 정보를 추출하고 다양한 방법으로 해당 텍스트에서 마스크할 수 있는 옵션을 제공합니다. 이 기술은 Cognitive Services의 [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)에서 제공하는 기계 학습 모델을 사용합니다.

> [!NOTE]
> 처리 빈도를 늘리거나, 문서를 더 추가하거나, AI 알고리즘을 추가하여 범위를 확장하려면 [청구 가능한 인지 서비스 리소스를 연결해야](cognitive-search-attach-cognitive-services.md)합니다. Cognitive Services에서 API를 호출하는 경우와 Azure Cognitiv Search에서 문서 크래킹 단계의 일부로 이미지를 추출하는 경우에는 요금이 부과됩니다. 문서에서 텍스트 추출할 때는 요금이 발생하지 않습니다.
>
> 기본 제공 기술을 실행하는 요금은 기존 [Cognitive Services 종량제 가격](https://azure.microsoft.com/pricing/details/cognitive-services/)으로 청구됩니다. 이미지 추출 가격 책정은 [Azure Cognitiv Search 가격 책정 페이지](https://go.microsoft.com/fwlink/?linkid=2042400)에 설명되어 있습니다.


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.PIIDetectionSkill

## <a name="data-limits"></a>데이터 제한
레코드의 최대 크기는 에 의해 [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)측정된 대로 50,000자여야 합니다. 기술로 보내기 전에 데이터를 분할해야 하는 경우 [텍스트 분할 기술을](cognitive-search-skill-textsplit.md)사용하는 것이 좋습니다.

## <a name="skill-parameters"></a>기술 매개 변수

매개 변수는 대/소문자를 구분하며 모두 선택 사항입니다.

| 매개 변수 이름     | 설명 |
|--------------------|-------------|
| defaultLanguageCode |    입력 텍스트의 언어 코드입니다. 지금은 지원됩니다. `en` |
| minimumPrecision | 0.0에서 1.0 사이의 값입니다. `piiEntities` 신뢰점수(출력)가 설정된 `minimumPrecision` 값보다 낮으면 엔터티가 반환되거나 마스머쳐되지 않습니다. 기본값은 0.0입니다. |
| 마스킹 모드 | 입력 텍스트에서 감지된 PII를 마스킹하는 다양한 방법을 제공하는 매개 변수입니다. 다음과 같은 옵션이 지원됩니다. <ul><li>`none`(기본값): 마스킹이 수행되지 않고 `maskedText` 출력이 반환되지 않음을 의미합니다. </li><li> `redact`: 이 옵션은 입력 텍스트에서 검색된 엔터티를 제거하고 아무 것도 대체하지 않습니다. 이 경우 출력의 `piiEntities` 오프셋은 마스크된 텍스트가 아니라 원본 텍스트와 관련이 있습니다. </li><li> `replace`: 이 옵션은 검색된 엔터티를 `maskingCharacter` 매개 변수에 지정된 문자로 바꿉니다.  오프셋이 입력 텍스트와 출력 `maskedText`모두에 올바르게 일치하도록 문자가 검색된 엔터티의 길이까지 반복됩니다.</li></ul> |
| 마스킹 문자 | `maskingMode` 매개 변수가 로 설정된 경우 텍스트를 마스머처하는 `replace`데 사용할 문자입니다. 다음 옵션이 지원됩니다. `*` `#` `X` 이 매개 `null` 변수는 `maskingMode` `replace`로 설정되지 않은 경우에만 할 수 있습니다. |


## <a name="skill-inputs"></a>기술 입력

| 입력 이름      | 설명                   |
|---------------|-------------------------------|
| languageCode    | (선택 사항) 기본값은 `en`입니다.  |
| text          | 분석할 텍스트입니다.          |

## <a name="skill-outputs"></a>기술 출력

| 출력 이름      | 설명                   |
|---------------|-------------------------------|
| 파이엔티티 | 다음 필드가 포함된 복합 형식의 배열입니다. <ul><li>텍스트(추출된 실제 PII)</li> <li>type</li><li>하위</li><li>점수(값이 높을수록 실제 엔터티가 될 가능성이 높음)</li><li>오프셋(입력 텍스트로)</li><li>length</li></ul> </br> [가능한 형식 및 하위 유형은 여기에서 찾을 수 있습니다.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/named-entity-types?tabs=personal) |
| 마스크된 텍스트 | 이 출력이 이외의 값으로 설정된 경우 `maskingMode` 이 출력은 선택한 `maskingMode`에 의해 설명된 대로 입력 텍스트에서 수행된 마스킹의 문자열 결과가 됩니다. `none`  로 설정된 경우 `maskingMode` 이 출력은 존재하지 않습니다. `none` |

##    <a name="sample-definition"></a>샘플 정의

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.PIIDetectionSkill",
    "defaultLanguageCode": "en",
    "minimumPrecision": 0.5,
    "maskingMode": "replace",
    "maskingCharacter": "*",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "piiEntities"
      },
      {
        "name": "maskedText"
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
             "text": "Microsoft employee with ssn 859-98-0987 is using our awesome API's."
           }
      }
    ]
}
```

##    <a name="sample-output"></a>샘플 출력

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "piiEntities":[ 
           { 
              "text":"859-98-0987",
              "type":"U.S. Social Security Number (SSN)",
              "subtype":"",
              "offset":28,
              "length":11,
              "score":0.65
           }
        ],
        "maskedText": "Microsoft employee with ssn *********** is using our awesome API's."
      }
    }
  ]
}
```

이 기술의 출력에 있는 엔터티에 대해 반환되는 오프셋은 [텍스트 분석 API에서](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)직접 반환되므로 원래 문자열로 인덱싱하는 데 사용하는 경우 올바른 콘텐츠를 추출하려면 .NET에서 [StringInfo](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) 클래스를 사용해야 합니다.  [자세한 내용은 여기에서 확인할 수 있습니다.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/concepts/text-offsets)

## <a name="error-and-warning-cases"></a>오류 및 경고 사례
문서의 언어 코드가 지원되지 않으면 경고가 반환되고 엔터티가 추출되지 않습니다.
텍스트가 비어 있는 경우 경고가 생성됩니다.
텍스트가 50,000자보다 큰 경우 처음 50,000자만 분석하고 경고를 발생합니다.

기술이 경고를 반환하면 출력이 `maskedText` 비어 있을 수 있습니다.  즉, 이후 기술에 입력하기 위해 출력이 존재할 것으로 예상되면 의도한 대로 작동하지 않습니다. 기술 집합 정의를 작성할 때 이 점을 염두에 두십시오.

## <a name="see-also"></a>참조

+ [기본 제공 기술](cognitive-search-predefined-skills.md)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
