---
title: LUIS의 데이터 변경 개념 이해 - Azure | Microsoft Docs
description: Language Understanding(LUIS)에서 예측 전에 데이터를 변경하는 방법을 알아봅니다.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/26/2018
ms.author: diberry
ms.openlocfilehash: d8421114bb5a7416ad2523fe9b0353f03f672619
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223986"
---
# <a name="data-alterations"></a>데이터 변경
LUIS는 예측 전이나 예측 중에 발화를 조작할 수 있는 방법을 제공합니다. 

## <a name="correct-spelling-errors-in-utterance"></a>발화에서 맞춤법 오류 수정
LUIS는 [Bing Spell Check API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/)을 사용하여 발화의 맞춤법 오류를 수정합니다. LUIS에는 해당 서비스와 연관된 키가 필요합니다. 키를 만든 다음, [끝점](https://aka.ms/luis-endpoint-apis)에서 쿼리 문자열 매개 변수로 키를 추가합니다. 

**테스트** 패널에서 [키를 입력](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel)하여 맞춤법 오류를 수정할 수도 있습니다. 키는 브라우저에서 테스트 패널에 대한 세션 변수로 유지됩니다. 맞춤법을 수정하려는 각 브라우저 세션에서 테스트 패널에 키를 추가합니다. 

테스트 패널 및 끝점에서의 키 사용은 [키 사용](https://azure.microsoft.com/pricing/details/cognitive-services/spellcheck-api/) 할당량에 포함됩니다. LUIS는 텍스트 길이에 대한 Bing Spell Check 제한을 구현합니다. 

끝점에서 맞춤법 수정이 작동하려면 두 개의 매개 변수가 필요합니다.

|매개 변수|값|
|--|--|
|`spellCheck`|부울|
|`bing-spell-check-subscription-key`|[Bing Spell Check API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) 끝점 키|

[Bing Spell Check API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/)에서 오류를 검색하면 원래 발화와 수정된 발화가 끝점의 예측과 함께 반환됩니다.

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```
 
## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>미리 빌드된 datetimeV2 엔터티의 표준 시간대 변경
LUIS 앱에서 미리 빌드된 datetimeV2 엔터티를 사용하는 경우, datetime 값이 예측 응답으로 반환될 수 있습니다. 요청의 표준 시간대는 반환할 올바른 datetime을 확인하는 데 사용됩니다. LUIS에 연결하기 전에 봇이나 다른 중앙 집중식 응용 프로그램에서 요청을 가져오는 경우, LUIS에서 사용하는 표준 시간대를 수정하세요. 

### <a name="endpoint-querystring-parameter"></a>끝점 쿼리 문자열 매개 변수
표준 시간대는 `timezoneOffset` 매개 변수를 사용하여 사용자의 표준 시간대를 [끝점](https://aka.ms/luis-endpoint-apis)에 추가하여 수정합니다. 시간을 변경하려면 `timezoneOffset` 값이 분 단위의 양수 또는 음수여야 합니다.  

|매개 변수|값|
|--|--|
|`timezoneOffset`|분 단위의 양수 또는 음수|

### <a name="daylight-savings-example"></a>일광 절약 시간제 예제
반환된 미리 빌드된 datetimeV2를 일광 절약 시간제에 맞게 조정해야 하는 경우, 분 단위의 +/- 값과 함께 `timezoneOffset` 쿼리 문자열 매개 변수를 [끝점](https://aka.ms/luis-endpoint-apis) 쿼리에 사용해야 합니다.

60분 추가: 

https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?**timezoneOffset=60**&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}

60분 제거: 

https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?**timezoneOffset=-60**&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>timezoneOffset에 올바른 값을 결정하는 C# 코드
다음 C# 코드는 [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo?view=netframework-4.7.1) 클래스의 [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid?view=netframework-4.7.1#examples) 메서드를 사용하여 시스템 시간을 기반으로 올바른 `timezoneOffset`을 결정합니다.

```CSharp
// Get CST zone id
TimeZoneInfo targetZone = TimeZoneInfo.FindSystemTimeZoneById("Central Standard Time");

// Get local machine's value of Now
DateTime utcDatetime = DateTime.UtcNow;

// Get Central Standard Time value of Now
DateTime cstDatetime = TimeZoneInfo.ConvertTimeFromUtc(utcDatetime, targetZone);

// Find timezoneOffset
int timezoneOffset = (int)((cstDatetime - utcDatetime).TotalMinutes);
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [이 자습서에서 맞춤법 오류 수정](luis-tutorial-bing-spellcheck.md)