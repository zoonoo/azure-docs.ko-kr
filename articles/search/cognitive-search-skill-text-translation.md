---
title: 텍스트 변환 인식 검색 기술-Azure Search
description: 텍스트를 평가 하 고 각 레코드에 대해 Azure Search 보강 파이프라인에서 지정 된 대상 언어로 변환 된 텍스트를 반환 합니다.
services: search
manager: nitinme
author: careyjmac
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: chalton
ms.openlocfilehash: ddfb35cbfcfbc262f3eff0de67f5cedfc31ea27e
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265704"
---
#   <a name="text-translation-cognitive-skill"></a>텍스트 변환 인식 기술

**텍스트 번역** 기술은 텍스트를 평가 하 고 각 레코드에 대해 지정 된 대상 언어로 번역 된 텍스트를 반환 합니다. 이 기술은 Cognitive Services에서 사용할 수 있는 [Translator Text API v 3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate) 을 사용 합니다.

이 기능은 문서가 하나의 언어로 되어 있지 않을 것으로 간주 되는 경우에 유용 합니다 .이 경우 번역을 통해 검색을 인덱싱하기 전에 텍스트를 단일 언어로 정규화 할 수 있습니다.  동일한 텍스트의 복사본을 여러 언어로 사용할 수 있는 지역화 사용 사례에도 유용 합니다.

[Translator Text API v 3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference) 은 지역 인식 서비스 이며,이는 데이터가 Azure Search 또는 연결 된 Cognitive Services 리소스와 동일한 지역에 유지 되는 것이 보장 되지 않는다는 것을 의미 합니다.

> [!NOTE]
> 처리 빈도를 늘리거나 문서를 추가하거나 AI 알고리즘을 추가하여 범위를 확장할 때 [청구 가능한 Cognitive Services 리소스를 연결](cognitive-search-attach-cognitive-services.md)해야 합니다. Cognitive Services에서 API를 호출할 때와 Azure Search에서 문서 해독 단계의 일부로 이미지를 추출할 때는 요금이 누적됩니다. 문서에서 텍스트 추출할 때는 요금이 발생하지 않습니다.
>
> 기본 제공 기술을 실행하는 요금은 기존 [Cognitive Services 종량제 가격](https://azure.microsoft.com/pricing/details/cognitive-services/)으로 청구됩니다. 이미지 추출 가격 책정 정보는 [Azure Search 가격 페이지](https://go.microsoft.com/fwlink/?linkid=2042400)에 설명되어 있습니다.

## <a name="odatatype"></a>@odata.type  
TranslationSkill.

## <a name="data-limits"></a>데이터 제한
레코드의 최대 크기는에 따라 [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)측정 되는 5만 자 여야 합니다. 텍스트 번역 기술에 보내기 전에 데이터를 분할 해야 하는 경우 [텍스트 분할 기술을](cognitive-search-skill-textsplit.md)사용 하는 것이 좋습니다.

## <a name="skill-parameters"></a>기술 매개 변수

매개 변수는 대/소문자를 구분합니다.

| 입력                | 설명 |
|---------------------|-------------|
| defaultToLanguageCode | 하다 언어를 명시적으로 지정 하지 않는 문서의 문서를로 변환 하는 언어 코드입니다. <br/> [지원되는 언어 전체 목록](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)을 참조합니다. |
| defaultFromLanguageCode | 필드 에서 언어를 명시적으로 지정 하지 않는 문서의 문서를 변환 하는 언어 코드입니다.  DefaultFromLanguageCode가 지정 되지 않은 경우 Translator Text API에서 제공 하는 자동 언어 검색을 사용 하 여 from 언어를 결정 합니다. <br/> [지원되는 언어 전체 목록](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)을 참조합니다. |
| suggestedFrom | 필드 FromLanguageCode 입력 또는 defaultFromLanguageCode 매개 변수가 제공 되지 않고 자동 언어 검색이 실패 한 경우 문서를 변환 하는 언어 코드입니다.  SuggestedFrom 언어를 지정 하지 않으면 영어 (en)가 suggestedFrom 언어로 사용 됩니다. <br/> [지원되는 언어 전체 목록](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)을 참조합니다. |

## <a name="skill-inputs"></a>기술 입력

| 이름 입력     | 설명 |
|--------------------|-------------|
| text | 변환할 텍스트입니다.|
| toLanguageCode    | 텍스트를 변환할 언어를 나타내는 문자열입니다. 이 입력을 지정 하지 않으면 defaultToLanguageCode 텍스트를 변환 하는 데 사용 됩니다. <br/>[지원되는 언어 전체 목록](https://docs.microsoft.com/azure/cognitive-services/translator/language-support) 참조|
| fromLanguageCode  | 텍스트의 현재 언어를 나타내는 문자열입니다. 이 매개 변수를 지정 하지 않으면 defaultFromLanguageCode (또는 defaultFromLanguageCode가 제공 되지 않는 경우 자동 언어 검색)가 텍스트를 변환 하는 데 사용 됩니다. <br/>[지원되는 언어 전체 목록](https://docs.microsoft.com/azure/cognitive-services/translator/language-support) 참조|

## <a name="skill-outputs"></a>기술 출력

| 출력 이름    | 설명 |
|--------------------|-------------|
| translatedText | TranslatedFromLanguageCode에서 translatedToLanguageCode로 텍스트 변환의 문자열 결과입니다.|
| translatedToLanguageCode  | 텍스트를 번역 한 언어 코드를 나타내는 문자열입니다. 여러 언어로 번역 하 고 어떤 언어가 어떤 언어로 된 텍스트를 추적할 수 있게 하려는 경우에 유용 합니다.|
| translatedFromLanguageCode    | 텍스트를 번역 한 언어 코드를 나타내는 문자열입니다. 자동 언어 검색 옵션을 선택한 경우이 출력에서 해당 검색의 결과를 제공 하므로 유용 합니다.|

##  <a name="sample-definition"></a>샘플 정의

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.TranslationSkill",
    "defaultToLanguageCode": "fr",
    "suggestedFrom": "en",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "translatedText",
        "targetName": "translatedText"
      },
      {
        "name": "translatedFromLanguageCode",
        "targetName": "translatedFromLanguageCode"
      },
      {
        "name": "translatedToLanguageCode",
        "targetName": "translatedToLanguageCode"
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
          "text": "We hold these truths to be self-evident, that all men are created equal."
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "text": "Estamos muy felices de estar con ustedes."
        }
    }
  ]
}
```


##  <a name="sample-output"></a>샘플 출력

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
        {
          "translatedText": "Nous tenons ces vérités pour évidentes, que tous les hommes sont créés égaux.",
          "translatedFromLanguageCode": "en",
          "translatedToLanguageCode": "fr"
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "translatedText": "Nous sommes très heureux d'être avec vous.",
          "translatedFromLanguageCode": "es",
          "translatedToLanguageCode": "fr"
        }
    }
  ]
}
```


## <a name="errors-and-warnings"></a>오류 및 경고
From 또는 to 언어에 대해 지원 되지 않는 언어 코드를 제공 하면 오류가 발생 하 고 텍스트가 변환 되지 않습니다.
텍스트가 비어 있는 경우 경고가 생성됩니다.
텍스트가 5만 자 보다 큰 경우에는 첫 번째 5만 자만 변환 되 고 경고가 실행 됩니다.

## <a name="see-also"></a>참조

+ [미리 정의된 기술](cognitive-search-predefined-skills.md)
+ [기능을 정의하는 방법](cognitive-search-defining-skillset.md)
