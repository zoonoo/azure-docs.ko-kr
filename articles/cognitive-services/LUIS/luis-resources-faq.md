---
title: Azure의 LUIS(Language Understanding) FAQ(질문과 대답) | Microsoft Docs
description: LUIS(Language Understanding)에 대한 FAQ(질문과 대답)에 대해 알아봅니다.
author: v-geberr
manager: kaiqb
services: cognitive-services
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: fd63ffd312e3ac17a6376eb3c9bef8f1978e3935
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "36333618"
---
# <a name="language-understanding-faq"></a>Language Understanding FAQ

이 문서에서는 LUIS(Language Understanding)에 대한 FAQ(질문과 대답)에 대해 알아봅니다.

## <a name="luis-authoring"></a>LUIS 작성

### <a name="what-are-the-luis-best-practices"></a>LUIS 모범 사례는 무엇인가요? 
[작성 주기](luis-concept-app-iteration.md)부터 시작해서 [모범 사례](luis-concept-best-practices.md)를 읽어봅니다. 

### <a name="what-is-the-best-way-to-start-building-my-app-in-luis"></a>LUIS에서 내 앱 작성을 시작하는 가장 좋은 방법은 무엇인가요?

앱을 빌드하는 가장 좋은 방법은 [증분 프로세스](luis-concept-app-iteration.md)를 사용하는 것입니다. 

### <a name="what-is-a-good-practice-to-model-the-intents-of-my-app-should-i-create-more-specific-or-more-generic-intents"></a>내 앱의 의도를 모델링하는 적절한 방법은 무엇인가요? 보다 구체적이거나 보다 일반적인 의도를 만들어야 하나요?

너무 일반적이어서 겹치지는 않지만, LUIS가 비슷한 의도 간을 구분하는 것이 어려울 정도로 너무 구체적이 않은 의도를 선택합니다. 구분되는 특정 의도를 만드는 것이 LUIS 모델링의 모범 사례 중 하나입니다.

### <a name="is-it-important-to-train-the-none-intent"></a>없음 의도를 학습하는 것이 중요한가요?

예. 다른 의도에 더 많은 레이블을 추가할 때처럼 많은 발언으로 **없음** 의도를 학습하는 것이 좋습니다. 좋은 비율은 의도에 추가되는 10개 레이블마다 1개 또는 2개의 레이블을 **없음**에 추가하는 것입니다. 이 비율은 증폭 LUIS의 구분 능력을 높여줍니다.

### <a name="how-can-i-correct-spelling-mistakes-in-utterances"></a>발언에서 맞춤법 오류를 수정하려면 어떻게 해야 하나요?

[Bing Spell Check API V7](luis-tutorial-bing-spellcheck.md) 자습서를 참조하세요. LUIS는 Bing Spell Check API v7에 따라 부여되는 제한을 적용합니다. 

### <a name="how-do-i-edit-my-luis-app-programmatically"></a>프로그래밍 방식으로 LUIS 앱을 편집하려면 어떻게 해야 하나요?
프로그래밍 방식으로 LUIS 앱을 편집하려면 [작성 API](https://aka.ms/luis-authoring-apis)를 사용합니다. 작성 API 호출 방법에 대한 예제를 보려면 [LUIS 작성 API 호출](./luis-quickstart-node-add-utterance.md) 및 [Node.js를 사용하여 프로그래밍 방식으로 LUIS 앱 빌드](./luis-tutorial-node-import-utterances-csv.md)를 참조하세요. 작성 API에서는 끝점 키가 아닌 [작성 키](luis-concept-keys.md#authoring-key)를 사용해야 합니다. 프로그래밍 방식으로 작성할 경우 매월 최대 1,000,000개 호출과 초당 5개의 트랜잭션이 허용됩니다. LUIS에서 사용하는 키에 대한 자세한 내용은 [키 관리](./luis-concept-keys.md)를 참조하세요.

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>정규식 일치를 제공하는 패턴 기능은 어디에 있나요?
이전 **패턴 기능**이 현재는 더 이상 사용되지 않으며 **[패턴](luis-concept-patterns.md)** 으로 바뀌었습니다. 

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>엔터티를 사용하여 올바른 데이터를 끌어오려면 어떻게 해야 하나요? 
[엔터티](luis-concept-entity-types.md) 및 [데이터 추출](luis-concept-data-extraction.md)을 참조하세요.

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>예제 발언 변형에 문장 부호가 포함되어야 하나요? 
의도에 다른 변형을 예제 발언으로 추가하거나 문장 부호를 [무시하는 구문](luis-concept-patterns.md#pattern-syntax)을 사용하여 예제 발언 패턴을 추가합니다. 

## <a name="luis-endpoint"></a>LUIS 끝점

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>LUIS가 쿼리 주변 또는 단어 중간에 공백을 추가하는 이유는 무엇인가요?
LUIS는 [문화권](luis-supported-languages.md#tokenization)에 따라 발언을 [토큰화](luis-glossary.md#token)합니다. 원래 값과 토큰화된 값 둘다 [데이터 추출](luis-concept-data-extraction.md#tokenized-entity-returned)에 사용할 수 있습니다.

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>LUIS 끝점 키를 만들고 할당하려면 어떻게 해야 하나요?
Azure에서 [서비스](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) 수준에 대해 [끝점 키를 만듭니다](luis-how-to-azure-subscription.md#create-luis-endpoint-key). **[게시](publishapp.md)** 페이지에서 [키를 할당](Manage-keys.md#assign-endpoint-key)합니다. 이 작업에 해당하는 API는 없습니다. 그런 후 끝점에 대한 HTTP 요청을 [새 끝점 키를 사용하도록](luis-concept-keys.md#use-endpoint-key-in-query) 변경해야 합니다.

### <a name="how-do-i-interpret-luis-scores"></a>LUIS 점수는 어떻게 해석할 수 있나요? 
시스템은 해당 값에 관계 없이 점수가 가장 높은 의도를 사용해야 합니다. 예를 들어, 0.5(50%) 미만 점수라고 해서 LUIS가 반드시 낮은 신뢰도를 갖는다는 것을 의미하는 것은 아닙니다. 더 많은 학습 데이터를 제공하면 가능성이 가장 높은 의도의 점수를 높일 수 있습니다.

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>내 앱의 대시보드에서 끝점 적중이 표시되지 않는 이유는 무엇인가요?
앱의 대시보드에 표시되는 총 끝점 적중은 주기적으로 업데이트되지만 Azure Portal의 LUIS 구독 키와 연결된 메트릭은 좀 더 자주 업데이트됩니다. 

대시보드에서 업데이트된 끝점 적중이 표시되지 않으면 Azure Portal에 로그인하고 LUIS 구독 키와 연결된 리소스를 찾은 후 **메트릭**을 열어 **총 호출** 메트릭을 선택합니다. 구독 키가 둘 이상의 LUIS 앱에 사용되는 경우 Azure Portal의 메트릭은 해당 메트릭을 사용하는 모든 LUIS 앱의 집계된 호출 수를 나타냅니다.

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>내 LUIS 앱이 어제도 작동했으나 오늘 403 오류가 발생하고 있습니다. 앱을 변경한 적도 없었습니다. 이 문제를 어떻게 해결하나요? 
다음은 LUIS 끝점 키 만들고 앱에 할당하기 위한 다음 FAQ의 [지침](#how-do-i-create-and-assign-a-luis-endpoint-key)입니다. 그런 후 끝점에 대한 HTTP 요청을 [새 끝점 키를 사용하도록](luis-concept-keys.md#use-endpoint-key-in-query) 변경해야 합니다.

### <a name="how-do-i-secure-my-luis-endpoint"></a>내 LUIS 끝점을 어떻게 보호하나요? 
[끝점 보안](luis-concept-security.md#securing-the-endpoint)을 참조하세요.

## <a name="working-within-luis-limits"></a>LUIS 제한 내에서 작업

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>LUIS 앱이 지원할 수 있는 의도 및 엔터티의 최대 수는 몇 개인가요?
[경계](luis-boundaries.md) 참조를 참조하세요.

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>최대 의도보다 많은 수의 의도로 LUIS 앱을 빌드하려고 합니다. 어떻게 해야 하나요?

[의도에 대한 모범 사례](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents)를 참조하세요.

### <a name="i-want-to-build-an-app-in-luis-with-more-than-the-maximum-number-of-entities-what-should-i-do"></a>최대 엔터티보다 많은 수의 엔터티로 LUIS에서 앱을 빌드하려고 합니다. 어떻게 해야 하나요?

[엔터티에 대한 모범 사례](luis-concept-entity-types.md#if-you-need-more-than-the-maximum-number-of-entities)를 참조하세요.

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>구 목록의 수 및 크기 제한은 얼마나 되나요?
[구 목록](./luis-concept-feature.md)의 최대 길이에 대해서는 [경계](luis-boundaries.md) 참조를 참조하세요.

### <a name="what-are-the-limits-on-example-utterances"></a>예제 발언에 대한 제한은 얼마나 되나요?
[경계](luis-boundaries.md) 참조를 참조하세요.

## <a name="testing-and-training"></a>학습 및 테스트

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>앱의 일부 모델에 대한 일괄 처리 테스트 창에서 오류가 발생합니다. 이 문제를 해결하려면 어떻게 해야 하나요?

이 오류는 모델의 레이블 및 예측 간에 약간의 불일치가 있음을 나타냅니다. 이 문제를 해결하려면 다음 작업 중 하나 또는 둘 다를 수행합니다.
* LUIS의 의도 간 구분 능력을 향상시키려면 더 많은 레이블을 추가합니다.
* LUIS가 더 빠르게 학습하도록 하려면 도메인별 어휘를 도입하는 구 목록 기능을 추가합니다.

[일괄 처리 테스트](luis-tutorial-batch-testing.md) 자습서를 참조하세요.

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>앱을 내보낸 후 새 앱으로 다시 가져오면(새 앱 ID 사용) LUIS 예측 점수가 달라집니다. 그 이유는 무엇인가요? 

[동일한 앱 사본 간 예측 차이점](luis-concept-prediction-score.md#differences-with-predictions)을 참조하세요.

## <a name="app-publishing"></a>앱 게시

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>"앱에 키 추가" 창에서 테넌트 ID란 무엇인가요?
Azure에서 테넌트는 서비스와 연결된 클라이언트 또는 조직을 나타냅니다. Azure Portal에서 **Azure Active Directory** > **관리** > **속성**을 선택하여 **디렉터리 ID** 상자에서 테넌트 ID를 찾습니다.

![Azure Portal의 테넌트 ID](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

### <a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>앱의 게시 페이지에 앱에 할당한 것보다 더 많은 구독 키가 있는 이유는 무엇인가요? 
각 LUIS 앱에는 작성/시작 키가 있습니다. GA 시간 프레임 동안 만든 LUIS 구독 키는 앱에 추가했는지에 관계없이 게시 페이지에 표시됩니다. GA 마이그레이션을 좀 더 쉽게 진행하기 위해 이렇게 구현되었습니다. 새 LUIS 구독 키는 게시 페이지에 표시되지 않습니다. 

## <a name="app-management"></a>앱 관리

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>LUIS 앱의 소유권을 양도하려면 어떻게 해야 하나요?
LUIS 앱을 다른 Azure 구독으로 이전하려면 LUIS 앱을 내보낸 후 새 계정을 사용하여 가져옵니다. LUIS 앱을 호출하는 클라이언트 응용 프로그램에서 해당 앱 ID를 업데이트합니다. 새 앱은 원래 앱과는 약간 다른 LUIS 점수를 반환할 수 있습니다. 

### <a name="how-do-i-download-a-log-of-user-utterances"></a>사용자 발언의 로그를 다운로드하려면 어떻게 해야 하나요?
기본적으로 LUIS 앱은 사용자의 발언을 로깅합니다. 사용자가 LUIS 앱으로 전송하는 발언 로그를 다운로드하려면 **내 앱**으로 이동한 후 앱에 대한 목록에서 줄임표(***...***)를 클릭합니다. 그런 후 **끝점 로그 내보내기**를 클릭합니다. 로그 형식은 쉼표로 구분된 값(CSV) 파일로 지정됩니다.

### <a name="how-can-i-disable-the-logging-of-utterances"></a>발언 로깅을 사용하지 않도록 설정하려면 어떻게 해야 하나요?
클라이언트 응용 프로그램이 LUIS를 쿼리하는 사용하는 끝점 URL에서 `log=false`를 설정하여 사용자 발언의 로깅을 해제할 수 있습니다. 그렇지만 로깅을 해제하면 [활성 학습](luis-concept-review-endpoint-utterances.md#what-is-active-learning)에 따라 발언을 제안하거나 성능을 향상시키는 LUIS 앱 기능을 사용할 수 없게 됩니다. 데이터 개인 정보 문제 때문에 `log=false`를 설정하는 경우 LUIS에서 해당 사용자 발언의 기록을 다운로드하거나 해당 발언을 사용하여 앱을 향상시킬 수 없습니다.

로깅은 발언의 유일한 저장소입니다. 

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>모든 내 끝점 발언을 로깅하지 않아도 되는 이유는 무엇인가요?
예측 분석을 위해 로그를 사용하는 경우에는 로그에 테스트 발언을 캡처하지 않도록 합니다.

## <a name="data-management"></a>데이터 관리

### <a name="can-i-delete-data-from-luis"></a>LUIS에서 데이터를 삭제할 수 있나요? 

* LUIS를 학습시키는 데 사용되는 예제 발언은 항상 삭제할 수 있습니다. LUIS 앱에서 예제 발언을 삭제하면 LUIS 웹 서비스에서 제거되고 내보낼 수 없게 됩니다.
* **끝점 발언 검토** 페이지에서 LUIS가 제안하는 사용자 발언 목록에 있는 발언을 삭제할 수 있습니다. 이 목록에서 발언을 삭제하면 제안되지 않지만 로그에서 삭제되지는 않습니다.
* 계정을 삭제하면 모든 앱과 예제 발언 및 로그가 함께 삭제됩니다. 데이터는 영구적으로 삭제하기 전에 60일 동안 서버에 유지됩니다.

## <a name="language-and-translation-support"></a>언어 및 번역 지원 

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>앱이 한 언어로 되어 있고 다른 언어로 병렬 앱을 만들려고 합니다. 이렇게 하는 가장 쉬운 방법은 무엇인가요?
1. 앱을 내보냅니다.
2. 내보낸 앱의 JSON 파일에서 레이블이 지정된 발언을 대상 언어로 번역합니다.
3. 의도 및 엔터티의 이름을 변경하거나 원래대로 유지해야 할 수 있습니다.
4. 마지막으로, LUIS 앱을 가져와 대상 언어로 나타냅니다.

## <a name="app-notification"></a>앱 알림

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>할당량이 거의 초과되려고 한다는 전자 메일이 수신되는 이유는 무엇인가요?
작성/시작 키는 매월 1,000개의 끝점 쿼리만 허용합니다. LUIS 구독 키(무료 또는 유료)를 만들고 끝점 쿼리를 만들 때 해당 키를 사용합니다. 봇이나 다른 클라이언트 응용 프로그램에서 끝점 쿼리를 만드는 경우 해당 위치에서 LUIS 끝점을 변경해야 합니다. 

## <a name="integrating-luis"></a>LUIS 통합

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Azure 웹앱 봇 구독 프로세스 동안 내 LUIS 앱은 어디에서 만들어지나요?
LUIS 템플릿을 선택하고 템플릿 창에서 **선택** 단추를 선택하면 왼쪽 창이 해당 템플릿 유형을 포함하도록 변경되고 LUIS 템플릿을 만들 지역을 묻는 메시지가 표시됩니다. 그렇지만 웹앱 봇 프로세스는 LUIS 구독을 만들지 않습니다.

![LUIS 템플릿 웹앱 봇 지역](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>어떤 LUIS 지역에서 Bot Framework 음성 초기화를 지원하나요?
[음성 초기화](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming)는 미국 중부 인스턴스의 LUIS 앱에서만 지원됩니다. 

## <a name="luis-service"></a>LUIS 서비스 

### <a name="is-luis-available-on-premise-or-in-private-cloud"></a>온-프레미스 또는 사설 클라우드에서 LUIS를 사용할 수 있나요?
아니요. 

## <a name="changes-to-the-docs"></a>문서 변경 내용

### <a name="where-did-the-tutorials-go"></a>자습서는 어디로 이동되었나요? 
이전에 자습서 섹션에 제공되던 문서가 이제 문서의 방법 섹션에 제공됩니다. 

|자습서|
|--|
|[C#](luis-csharp-tutorial-build-bot-framework-sample.md) 및 [Node.js](luis-nodejs-tutorial-build-bot-framework-sample.md)를 사용하여 봇과 LUIS 통합|
|[C#](luis-tutorial-bot-csharp-appinsights.md) 및 [Node.js](luis-tutorial-function-appinsights.md)를 사용하여 봇에 Application Insights 추가|
|[Node.js](luis-tutorial-node-import-utterances-csv.md)를 사용하여 프로그래밍 방식으로 LUIS 앱 빌드|
|[복합 엔터티](luis-tutorial-composite-entity.md)를 사용하여 그룹화된 데이터 추출|
|Node.js를 사용하여 증가된 엔터티 검색을 위해 [목록 엔터티](luis-tutorial-list-entity.md) 추가|
|[구 목록](luis-tutorial-interchangeable-phrase-list.md), [패턴](luis-tutorial-pattern.md) 및 [일괄 처리 테스트](luis-tutorial-batch-testing.md)로 예측 정확도 향상|
|Bing Spell Check API v7을 사용한 [올바른 맞춤법](luis-tutorial-batch-testing.md)

### <a name="at-the-build-2018-conference-i-heard-about-a-language-understanding-feature-or-demo-but-i-dont-remember-what-it-was-called"></a>Build 2018 Conference에서 Language Understanding 기능이나 데모에 대해 들어보았으나 어떻게 지칭하는지 기억이 나지 않습니다. 

Build 2018 Conference에서는 다음 기능이 발표되었습니다.

|Name|Content|
|--|--|
|향상된 기능|[정규식](luis-concept-data-extraction.md##regular-expression-entity-data) 엔터티 및 [핵심 구](luis-concept-data-extraction.md#key-phrase-extraction-entity-data) 엔터티
|패턴|패턴 [개념](luis-concept-patterns.md), [자습서](luis-tutorial-pattern.md), [방법](luis-how-to-model-intent-pattern.md)<br>예외에 대한 [명시적 목록](luis-concept-patterns.md#explicit-lists)을 포함하는 [Patterns.Any](luis-concept-entity-types.md) 엔터티<br>[역할](luis-concept-roles.md) 개념|
|통합|[텍스트 분석](https://docs.microsoft.com/azure/cognitive-services/text-analytics/)과 [감정 분석](publishapp.md#enable-sentiment-analysis) 통합<br>[Speech](https://docs.microsoft.com/azure/cognitive-services/speech)에 [Speech SDK](https://aka.ms/SpeechSDK)와 [음성 초기화](publishapp.md#enable-speech-priming) 통합|
|Dispatch 도구|[BotBuilder-tools](https://github.com/Microsoft/botbuilder-tools)의 일부로서, 봇에서의 더 나은 의도 인식을 위해 여러 LUIS 및 QnA Maker 앱을 단일 LUIS 앱에 결합하기 위한 Dispatch 명령줄 [도구](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps)

추가적인 작성 [API 경로](https://github.com/Microsoft/LUIS-Samples/blob/master/authoring-routes.md)가 포함되었습니다. 

비디오: 
* [Build 2018의 Azure Friday: Cognitive Services - 언어(LUIS)](https://channel9.msdn.com/Shows/Azure-Friday/At-Build-2018-Cognitive-Services-Language-LUIS/player)
* [Build 2018 AI Show - Language Understanding Service의 새로운 기능](https://channel9.msdn.com/Shows/AI-Show/Whats-New-with-Language-Understanding-Service-LUIS/player)
* [Build 2018 Session - 봇 인텔리전스, 음성 기능 및 NLU 모범 사례](https://channel9.msdn.com/events/Build/2018/BRK3208)
* [Build 2018 - LUIS 업데이트](https://channel9.msdn.com/events/Build/2018/THR3118/player)

프로젝트: 
* [Contoso Cafe 봇](https://github.com/botbuilderbuild2018/build2018demo) 데모 - Github의 소스 코드

## <a name="next-steps"></a>다음 단계

LUIS에 대한 자세한 내용은 다음 리소스를 참조하세요.
* [LUIS로 태그가 지정된 스택 오버플로 질문](https://stackoverflow.com/questions/tagged/luis)
* [MSDN LUIS(Language Understanding Intelligent Services) 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=LUIS) 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
