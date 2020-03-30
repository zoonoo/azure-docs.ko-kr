---
title: 텍스트 번역 인지 기술
titleSuffix: Azure Cognitive Search
description: 텍스트를 평가하고 각 레코드에 대해 Azure Cognitive Search의 AI 보강 파이프라인에서 지정된 대상 언어로 번역된 텍스트를 반환합니다.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 5089174fcfd5a97128c1f789b818243243a5282f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75460760"
---
#   <a name="text-translation-cognitive-skill"></a>텍스트 번역 인지 기술

**텍스트 번역** 기술은 텍스트를 평가하고 각 레코드에 대해 지정된 대상 언어로 번역된 텍스트를 반환합니다. 이 기술은 코그너티브 서비스에서 사용할 수 있는 [번역기 텍스트 API v3.0을](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate) 사용합니다.

이 기능은 문서가 모두 한 언어로 되어 있지 않을 수 있으며, 이 경우 텍스트를 번역하여 검색을 인덱싱하기 전에 텍스트를 단일 언어로 정규화할 수 있는 경우에 유용합니다.  또한 여러 언어로 동일한 텍스트의 복사본을 사용할 수 있도록 할 수 있는 지역화 사용 사례에도 유용합니다.

[번역기 텍스트 API v3.0은](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference) 비지역인지 서비스로, 데이터가 Azure Cognitive Search 또는 연결된 인지 서비스 리소스와 동일한 지역에 머무르지 않도록 보장되지 않습니다.

> [!NOTE]
> 처리 빈도를 늘리거나, 문서를 더 추가하거나, AI 알고리즘을 추가하여 범위를 확장하려면 [청구 가능한 인지 서비스 리소스를 연결해야](cognitive-search-attach-cognitive-services.md)합니다. Cognitive Services에서 API를 호출하는 경우와 Azure Cognitiv Search에서 문서 크래킹 단계의 일부로 이미지를 추출하는 경우에는 요금이 부과됩니다. 문서에서 텍스트 추출할 때는 요금이 발생하지 않습니다.
>
> 기본 제공 기술을 실행하는 요금은 기존 [Cognitive Services 종량제 가격](https://azure.microsoft.com/pricing/details/cognitive-services/)으로 청구됩니다. 이미지 추출 가격 책정은 [Azure Cognitiv Search 가격 책정 페이지](https://go.microsoft.com/fwlink/?linkid=2042400)에 설명되어 있습니다.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.TranslationSkill

## <a name="data-limits"></a>데이터 제한
레코드의 최대 크기는 에 의해 [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)측정된 대로 50,000자여야 합니다. 텍스트 변환 기술로 보내기 전에 데이터를 분할해야 하는 경우 [텍스트 분할 기술을](cognitive-search-skill-textsplit.md)사용하는 것이 좋습니다.

## <a name="skill-parameters"></a>기술 매개 변수

매개 변수는 대/소문자를 구분합니다.

| 입력                | 설명 |
|---------------------|-------------|
| 기본토언어코드 | (필수) 언어를 명시적으로 지정하지 않는 문서에 대해 문서를 변환하는 언어 코드입니다. <br/> [지원되는 언어의 전체 목록을](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)참조하십시오. |
| 기본값From언어코드 | (선택 사항) 언어를 명시적으로 지정하지 않는 문서에 대해 문서를 번역하는 언어 코드입니다.  defaultFromLanguageCode를 지정하지 않으면 번역기 텍스트 API에서 제공하는 자동 언어 검색이 언어에서 언어를 결정하는 데 사용됩니다. <br/> [지원되는 언어 전체 목록](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)을 참조합니다. |
| suggestedFrom | (선택 사항) fromLanguageCode 입력이나 기본FromLanguageCode 매개 변수가 제공되지 않고 자동 언어 검색이 실패할 때 문서를 번역하는 언어 코드입니다.  제안하는 경우From 언어가 지정되지 않은 경우, 영어 (en)는 제안으로 사용됩니다언어에서. <br/> [지원되는 언어의 전체 목록을](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)참조하십시오. |

## <a name="skill-inputs"></a>기술 입력

| 입력 이름     | 설명 |
|--------------------|-------------|
| text | 번역할 텍스트입니다.|
| to언어코드    | 텍스트를 번역해야 하는 언어를 나타내는 문자열입니다. 이 입력을 지정하지 않으면 기본ToLanguageCode가 텍스트를 번역하는 데 사용됩니다. <br/>[지원되는 언어 전체 목록](https://docs.microsoft.com/azure/cognitive-services/translator/language-support) 참조|
| 에서언어 코드  | 텍스트의 현재 언어를 나타내는 문자열입니다. 이 매개 변수를 지정하지 않으면 기본FromLanguageCode (또는 기본FromLanguageCode가 제공되지 않은 경우 자동 언어 검색)가 텍스트를 번역하는 데 사용됩니다. <br/>[지원되는 언어 의 전체 목록](https://docs.microsoft.com/azure/cognitive-services/translator/language-support) 보기|

## <a name="skill-outputs"></a>기술 출력

| 출력 이름    | 설명 |
|--------------------|-------------|
| translatedText | translatedFromLanguageCode에서 번역된ToLanguageCode로의 텍스트 번역의 문자열 결과입니다.|
| 번역토언어코드  | 텍스트가 번역된 언어 코드를 나타내는 문자열입니다. 여러 언어로 번역하고 어떤 텍스트가 어떤 언어인지 추적할 수 있도록 하려는 경우에 유용합니다.|
| 번역에서언어코드    | 텍스트가 번역된 언어 코드를 나타내는 문자열입니다. 이 출력은 해당 검색의 결과를 제공하므로 자동 언어 검색 옵션을 선택한 경우에 유용합니다.|

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
에서 또는 받는 번째 언어에 대해 지원되지 않는 언어 코드를 제공하면 오류가 생성되고 텍스트가 번역되지 않습니다.
텍스트가 비어 있는 경우 경고가 생성됩니다.
텍스트가 50,000자보다 큰 경우 처음 50,000자만 번역되고 경고가 발행됩니다.

## <a name="see-also"></a>참조

+ [기본 제공 기술](cognitive-search-predefined-skills.md)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
