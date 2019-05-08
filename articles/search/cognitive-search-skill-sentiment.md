---
title: 감성 인식 검색 기술 - Azure Search
description: Azure Search 보강 파이프라인의 텍스트에서 긍정-부정적 감성 점수를 추출합니다.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: f52f5200f33d11db44d94b5a5f26d246f711e224
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65023799"
---
#   <a name="sentiment-cognitive-skill"></a>감성 인식 기술

**감성** 기술은 긍정과 부정 지속성에 따라 구조화되지 않은 텍스트를 평가하고 각 레코드에 대해 0과 1 사이의 점수를 반환합니다. 점수가 1에 가까울수록 긍정적 감정을 나타내고 점수가 0에 가까울수록 부정적 감정을 나타냅니다. 이 기술은 Cognitive Services의 [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)에서 제공하는 기계 학습 모델을 사용합니다.

> [!NOTE]
> 확장 하면 범위 처리의 빈도 늘려 더 많은 문서를 추가 하거나 자세한 AI 알고리즘에 추가 해야 합니다 [청구 가능한 Cognitive Services 리소스를 연결](cognitive-search-attach-cognitive-services.md)합니다. Cognitive Services에서 API를 호출할 때와 Azure Search에서 문서 해독 단계의 일부로 이미지를 추출할 때는 요금이 누적됩니다. 문서에서 텍스트 추출할 때는 요금이 발생하지 않습니다.
>
> 기본 제공 기술 실행은 기존 부과 [종 량 Cognitive Services 가격 이동](https://azure.microsoft.com/pricing/details/cognitive-services/)합니다. 에 설명 된 대로 이미지 추출 가격을 [Azure Search 가격 책정 페이지](https://go.microsoft.com/fwlink/?linkid=2042400)합니다.


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SentimentSkill

## <a name="data-limits"></a>데이터 제한
레코드의 최대 크기는 `String.Length`에 의해 측정된 대로 5000자여야 합니다. 감성 분석기로 보내기 전에 데이터를 분할해야 할 경우 [텍스트 분할 기술](cognitive-search-skill-textsplit.md)을 사용합니다.


## <a name="skill-parameters"></a>기술 매개 변수

매개 변수는 대/소문자를 구분합니다.

| 매개 변수 이름 |                      |
|----------------|----------------------|
| defaultLanguageCode | (선택 사항) 명시적으로 언어를 지정하지 않은 문서에 적용할 언어 코드입니다. <br/> [지원되는 언어 전체 목록](../cognitive-services/text-analytics/text-analytics-supported-languages.md) 참조 |

## <a name="skill-inputs"></a>기술 입력 

| 입력 이름 | 설명 |
|--------------------|-------------|
| text | 분석할 텍스트입니다.|
| languageCode  |  (선택 사항) 레코드의 언어를 나타내는 문자열입니다. 이 매개 변수를 지정하지 않을 경우 기본값은 "en"입니다. <br/>[지원되는 언어 전체 목록](../cognitive-services/text-analytics/text-analytics-supported-languages.md)을 참조합니다.|

## <a name="skill-outputs"></a>기술 출력

| 출력 이름 | 설명 |
|--------------------|-------------|
| score에 대한 임계값 수준 보기 | 분석된 텍스트의 감성를 나타내는 0과 1 사이의 값입니다. 값 0에 가까우면 부정적 감정이고, 0.5에 가까우면 중립적 감성이고, 1에 가까우면 긍정적인 감성입니다.|


##  <a name="sample-definition"></a>샘플 정의

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/languagecode"
        }
    ],
    "outputs": [
        {
            "name": "score",
            "targetName": "mySentiment"
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
            "data": {
                "text": "I had a terrible time at the hotel. The staff was rude and the food was awful.",
                "languageCode": "en"
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
            "data": {
                "score": 0.01
            }
        }
    ]
}
```

## <a name="notes"></a>메모
비어 있는 경우 해당 레코드에 대해 감성 점수는 반환되지 않습니다.

## <a name="error-cases"></a>오류 사례
언어가 지원되지 않는 경우 오류가 발생하고 감성 점수는 반환되지 않습니다.

## <a name="see-also"></a>참고 항목

+ [미리 정의된 기술](cognitive-search-predefined-skills.md)
+ [기능을 정의하는 방법](cognitive-search-defining-skillset.md)
