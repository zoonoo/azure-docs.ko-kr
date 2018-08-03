---
title: LUIS 앱 게시 | Microsoft Docs
description: LUIS(Language Understanding)를 사용하여 앱을 빌드하고 테스트한 후에는 Azure에 웹 서비스로 게시합니다.
services: cognitive-services
titleSuffix: Azure
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr;
ms.openlocfilehash: 1bd24af0498755b7cdcb170624fd8f9f3b39c85a
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2018
ms.locfileid: "37341292"
---
# <a name="publish-your-trained-app"></a>학습된 앱 게시
LUIS 앱의 빌드 및 테스트가 끝나면 게시합니다. 앱이 게시된 후에 게시 페이지에는 연결된 모든 HTTP [끝점](luis-glossary.md#endpoint)이 표시됩니다. 그런 후에 이러한 끝점은 [지역](luis-reference-regions.md) 및 [키](luis-how-to-manage-keys.md)별로 클라이언트, 챗봇 또는 백 엔드 응용 프로그램에 통합됩니다. 

언제든지 앱을 게시하기 전에 [테스트](interactive-test.md)할 수 있습니다. 

## <a name="production-and-staging-slots"></a>프로덕션 및 스테이징 슬롯
앱을 **스테이징 슬롯** 또는 **프로덕션 슬롯**에 게시할 수 있습니다. 두 게시 슬롯을 사용하면 게시된 끝점에 2가지 다른 버전이 있거나 2가지 다른 끝점에 같은 버전이 있을 수 있습니다. 

<!-- TBD: what is the technical difference? log files, endpoint quota? -->

## <a name="settings-configuration-requires-publishing-model"></a>설정을 구성하는 데 게시 모델 필요
다음 설정을 변경한 후 끝점에 게시합니다. 

## <a name="external-services-settings"></a>외부 서비스 설정
외부 서비스 설정에는 **[감정 분석](#enable-sentiment-analysis)** 및  **[음성 초기화](#enable-speech-priming)** 가 포함됩니다.

### <a name="enable-sentiment-analysis"></a>감정 분석 사용
**외부 서비스 설정**에서 **감정 분석 사용** 확인란을 사용하면 LUIS에서 [Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/)와 통합되어 감정 및 핵심 구 분석을 제공할 수 있습니다. Text Analytics 키를 제공할 필요는 없으며, 이 서비스에 대한 비용은 사용자의 Azure 계정에 부과되지 않습니다. 이 설정을 선택하면 지속됩니다. 

감정 데이터는 데이터의 긍정적(1에 가까움) 또는 부정적(0에 가까움) 감정을 나타내는 1과 0 사이의 점수입니다.

감정 분석에서 JSON 끝점의 응답에 대한 자세한 내용은 [감정 분석](luis-concept-data-extraction.md#sentiment-analysis)을 참조하세요.

### <a name="enable-speech-priming"></a>음성 초기화 사용 
**외부 서비스 설정**에서 **음성 초기화 사용** 확인란을 사용하여 단일 끝점에 챗봇 또는 LUIS 응용 프로그램의 음성 발언을 제공되고 LUIS 예측 응답이 수신되도록 할 수 있습니다. 음성 초기화는 Cognitive Service [Speech API](../Speech-Service/rest-apis.md)를 사용합니다. 

![음성 초기화 확인 대화 상자 이미지](./media/luis-how-to-publish-app/speech-prime-modal.png)

이 기능이 사용되도록 설정되면 앱을 게시합니다. LUIS 앱을 게시할 경우 앱 모델이 Speech Service를 초기화하기 위해 자체 Speech Service로 전송됩니다. 모델 정보는 자체 서비스 외부에서 사용되지 **않습니다**. 

음성 초기화 사용을 완료하려면 [Speech SDK](../speech-service/speech-sdk-reference.md)에서 사용하기 위한 다음 정보가 필요합니다.
* LUIS 끝점 키
* LUIS 앱 ID
* Speech SDK에서 "호스트 이름"이라고도 하는 끝점 도메인(예: "westus.api.cognitive.microsoft.com"). 여기에서 첫 번째 하위 도메인은 앱이 게시되는 지역입니다.

자세한 내용은 [음성 의도 변환](http://aka.ms/speechsdk) 샘플을 참조하세요.

LUIS 앱이 삭제되거나 Speech Service가 삭제되면 모델 데이터가 제거됩니다. 

## <a name="endpoint-url-settings"></a>끝점 URL 설정
끝점 URL 서비스 설정에는 **[표준 시간대](#set-timezone-offset)** 오프셋, **[예측된 모든 의도 점수](#include-all-predicted-intent-scores)** 및 **[Bing Spell Checker](#enable-bing-spell-checker)** 가 포함됩니다.

### <a name="set-timezone-offset"></a>표준 시간대 오프셋 설정 
슬롯 선택 옵션에는 표준 시간대 선택이 포함되어 있습니다. 이 표준 시간대 설정을 사용하면 반환된 엔터티 값이 선택된 표준 시간대에 적절하도록 LUIS에서 예측 동안 미리 빌드된 datetimeV2 시간 값을 [변경](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)할 수 있습니다. 

### <a name="include-all-predicted-intent-scores"></a>예측된 모든 의도 점수 포함
**예측된 모든 의도 점수 포함** 확인란을 사용하여 끝점 쿼리 응답에 각 의도의 예측 점수를 포함할 수 있습니다. 

이 설정을 사용하면 챗봇 또는 LUIS 호출 응용 프로그램이 반환된 의도의 점수에 따라 프로그래밍 방식으로 의사 결정을 내릴 수 있습니다. 일반적으로 상위 두 의도가 가장 흥미롭습니다. 상위 점수가 없음 의도이면, 챗봇은 없음 의도 및 기타 고득점 의도 간을 명확히 구분할 수 있는 후속 질문을 수행하도록 선택할 수 있습니다. 

의도 및 해당 점수도 끝점 로그에 포함됩니다. 해당 로그를 [내보내고](create-new-app.md#export-app) 점수를 분석할 수 있습니다. 

```
{
  "query": "book a flight to Cairo",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.5223427
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.5223427
    },
    {
      "intent": "BookFlight",
      "score": 0.372391433
    }
  ],
  "entities": []
}
```

### <a name="enable-bing-spell-checker"></a>Bing spell checker 사용 
**끝점 URL 설정**에서 **Bing Spell Checker 사용** 확인란을 사용하면 LUIS에서 예측 전에 철자가 잘못된 단어를 수정할 수 있습니다. 이렇게 하려면 **[Bing Spell Check 키](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)** 를 만들어야 합니다. 이 키가 만들어지면 게시 페이지의 끝점 URL에 두 개의 쿼리 문자열 매개 변수가 추가됩니다. 

LUIS 호출 응용 프로그램에 대한 자체 URL을 생성하는 경우 **spellCheck=true** 쿼리 문자열 매개 변수 및 **bing-spell-check-subscription-key={YOUR_BING_KEY_HERE}** 를 확인합니다. `{YOUR_BING_KEY_HERE}`를 Bing Spell Checker 키로 바꿉니다.

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

## <a name="publish-your-trained-app-to-an-http-endpoint"></a>학습된 앱을 HTTP 끝점에 게시
**내 앱** 페이지에서 해당 이름을 클릭하여 앱을 열고 위쪽 패널에서 **게시**를 클릭합니다. 

![게시 페이지-](./media/luis-how-to-publish-app/publish-to-production.png)
 
앱이 성공적으로 게시되면 브라우저 위쪽에 녹색 성공 알림이 표시됩니다. 

* **슬롯 선택**에서 드롭다운 메뉴를 선택하여 **프로덕션** 또는 **스테이징** 중에서 어디에 게시할지 선택합니다. 

## <a name="assign-key"></a>키 할당

표시되는 무료 Starter_Key 이외의 키를 사용하려는 경우 **키 추가** 단추를 클릭합니다. 이렇게 하면 앱에 할당할 기존 끝점 키를 선택할 수 있는 대화 상자가 열립니다. 끝점 키를 만들고 LUIS 앱에 추가하는 방법에 대한 자세한 내용은 [키 관리](luis-how-to-manage-keys.md)를 참조하세요.

다른 지역과 연결된 끝점 및 키를 보려면 라디오 단추를 사용하여 지역 간을 전환합니다. **리소스 및 키** 표의 각 행에는 사용자 계정과 연결된 Azure 리소스 및 해당 리소스와 연결된 끝점 키가 표시됩니다.

## <a name="endpoint-url-construction"></a>끝점 URL 생성
끝점 URL은 끝점 키와 연결된 Azure 지역에 해당합니다.

이 표는 편의를 위해 URL 끝점의 게시 구성과 경로 선택 사항 및 쿼리 문자열 값을 제공합니다. LUIS 호출 응용 프로그램에 대한 끝점 URL을 생성하는 경우 사용되는 끝점에 대해 이러한 동일한 경로 및 쿼리 문자열 값을 설정해야 합니다(설정하려는 경우).

URL 경로는 지역 및 앱 ID를 사용하여 생성됩니다. 다른 지역에 또는 다른 앱을 사용하여 게시될 경우 지역 및 앱 ID 값을 변경하여 끝점 URL을 생성할 수 있습니다. 

* 프로덕션 슬롯과 **게시** 단추를 선택합니다. 게시가 성공하면 표시된 끝점 URL을 사용하여 LUIS 앱에 액세스합니다. 

### <a name="optional-query-string-parameters"></a>선택적 쿼리 문자열 매개 변수
다음 쿼리 문자열 매개 변수는 끝점 URL에 사용할 수 있습니다.

<!-- TBD: what about speech priming? -->

|쿼리 문자열|type|예제 값|목적|
|--|--|--|--|
|verbose|부울|true|발언에 대한 [모든 의도 점수](#include-all-predicted-intent-scores) 포함|
|timezoneOffset|숫자(분 단위)|60|[datetimeV2 미리 빌드된 엔터티](luis-reference-prebuilt-datetimev2.md)에 대해 [표준 시간대 오프셋](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) 설정|
|spellCheck|부울|true|발언의 [올바른 철자](#enable-bing-spell-checker) - bing-spell-check-subscription-key 쿼리 문자열 매개 변수와 함께 사용|
|bing-spell-check-subscription-key|구독 ID||spellCheck 쿼리 문자열 매개 변수와 함께 사용|
|staging|부울|false|스테이징 또는 프로덕션 끝점 선택|
|로그|부울|true|로그에 쿼리 및 결과 추가|


## <a name="test-your-published-endpoint-in-a-browser"></a>브라우저에서 게시된 끝점 테스트
**끝점** 열에서 URL을 선택하여 게시된 끝점을 테스트합니다. 기본 브라우저는 생성된 URL로 열립니다. URL 매개 변수 "&q"를 테스트 쿼리로 설정합니다. 예를 들어, URL에 `&q=Book me a flight to Boston on May 4`를 추가한 후 Enter 키를 누릅니다. 브라우저는 HTTP 끝점의 JSON 응답을 표시합니다. 

![게시된 HTTP 끝점의 JSON 응답](./media/luis-how-to-publish-app/luis-publish-app-json-response.png)

## <a name="next-steps"></a>다음 단계

* LUIS 앱에 키를 추가하고 키가 지역에 매핑되는 방식을 알아보려면 [키 관리](./luis-how-to-manage-keys.md)를 참조하세요.
* 테스트 콘솔에서 게시된 앱을 테스트하는 방법에 대한 지침은 [앱 학습 및 테스트](interactive-test.md)를 참조하세요.
