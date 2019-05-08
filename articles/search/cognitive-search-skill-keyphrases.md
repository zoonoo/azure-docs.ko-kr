---
title: 핵심 문구 추출 인식 검색 기술 - Azure Search
description: 구조화되지 않은 텍스트를 평가하 고 각 레코드에 대해 Azure Search 보강 파이프라인에서 핵심 문구 목록을 반환합니다.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 1d221e3bcdfd781da79c73e8f228b9e449a7f5bd
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65021809"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>핵심 문구 추출 인식 기술

**핵심 문구 추출** 기술은 구조화되지 않은 텍스트를 평가하고 각 레코드에 대해 핵심 문구 목록을 반환합니다. 이 기술은 Cognitive Services의 [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)에서 제공하는 기계 학습 모델을 사용합니다.

이 기능은 레코드에서 기본 대화 지점을 신속하게 식별해야 하는 경우 유용합니다. 예를 들어 "음식이 맛 있었으며 훌륭한 직원이 있었습니다"라는 입력 텍스트가 있는 경우 서비스는 "음식" 및 "훌륭한 직원"을 반환합니다.

> [!NOTE]
> 확장 하면 범위 처리의 빈도 늘려 더 많은 문서를 추가 하거나 자세한 AI 알고리즘에 추가 해야 합니다 [청구 가능한 Cognitive Services 리소스를 연결](cognitive-search-attach-cognitive-services.md)합니다. Cognitive Services에서 API를 호출할 때와 Azure Search에서 문서 해독 단계의 일부로 이미지를 추출할 때는 요금이 누적됩니다. 문서에서 텍스트 추출할 때는 요금이 발생하지 않습니다.
>
> 기본 제공 기술 실행은 기존 부과 [종 량 Cognitive Services 가격 이동](https://azure.microsoft.com/pricing/details/cognitive-services/)합니다. 에 설명 된 대로 이미지 추출 가격을 [Azure Search 가격 책정 페이지](https://go.microsoft.com/fwlink/?linkid=2042400)합니다.


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.KeyPhraseExtractionSkill 

## <a name="data-limits"></a>데이터 제한
레코드의 최대 크기는 `String.Length`에 의해 측정된 대로 50,000자여야 합니다. 핵심 구문 추출기로 보내기 전에 데이터를 분할해야 할 경우 [텍스트 분할 기술](cognitive-search-skill-textsplit.md) 사용을 고려합니다.

## <a name="skill-parameters"></a>기술 매개 변수

매개 변수는 대/소문자를 구분합니다.

| 입력                | 설명 |
|---------------------|-------------|
| defaultLanguageCode | (선택 사항) 명시적으로 언어를 지정하지 않은 문서에 적용할 언어 코드입니다.  기본 언어 코드가 지정되지 않은 경우 영어(en)가 기본 언어 코드로 사용됩니다. <br/> [지원되는 언어 전체 목록](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)을 참조합니다. |
| maxKeyPhraseCount   | (선택 사항) 생성할 핵심 구문의 최대 수입니다. |

## <a name="skill-inputs"></a>기술 입력

| 입력     | 설명 |
|--------------------|-------------|
| text | 분석할 텍스트입니다.|
| languageCode  |  레코드의 언어를 나타내는 문자열입니다. 이 매개 변수를 지정하지 않으면 레코드를 분석하는 데 기본 언어 코드가 사용됩니다. <br/>[지원되는 언어 전체 목록](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) 참조|

##  <a name="sample-definition"></a>샘플 정의

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      },
      {
        "name": "languageCode",
        "source": "/document/languagecode" 
      }
    ],
    "outputs": [
      {
        "name": "keyPhrases",
        "targetName": "myKeyPhrases"
      }
    ]
  }
```

##  <a name="sample-input"></a>샘플 입력

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. They accumulate ice from snowfall and lose it through melting. As global temperatures have risen, many of the world’s glaciers have already started to shrink and retreat. Continued warming could see many iconic landscapes – from the Canadian Rockies to the Mount Everest region of the Himalayas – lose almost all their glaciers by the end of the century.",
             "language": "en"
           }
      }
    ]
```


##  <a name="sample-output"></a>샘플 출력

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "keyPhrases": 
            [
              "world’s glaciers", 
              "huge rivers of ice", 
              "Canadian Rockies", 
              "iconic landscapes",
              "Mount Everest region",
              "Continued warming"
            ]
           }
      }
    ]
}
```


## <a name="errors-and-warnings"></a>오류 및 경고
지원되지 않는 언어 코드를 제공하는 경우 오류가 생성되고 핵심 문구는 추출되지 않습니다.
텍스트가 비어 있는 경우 경고가 생성됩니다.
텍스트가 50,000자보다 큰 경우 처음 50,000자만 분석하고 경고를 발생합니다.

## <a name="see-also"></a>참고 항목

+ [미리 정의된 기술](cognitive-search-predefined-skills.md)
+ [기능을 정의하는 방법](cognitive-search-defining-skillset.md)
