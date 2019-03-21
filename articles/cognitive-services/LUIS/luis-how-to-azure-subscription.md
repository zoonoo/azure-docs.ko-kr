---
title: 구독 키
titleSuffix: Language Understanding - Azure Cognitive Services
description: 사용 가능한 처음 1,000개 엔드포인트 쿼리를 사용하기 위해서는 구독 키를 만들 필요가 없습니다. HTTP 403 및 429 형식의 _할당량 초과_ 오류가 발생하면 키를 만든 후 앱에 할당해야 합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/01/2019
ms.author: diberry
ms.openlocfilehash: 7315c80ad74eae07e41577fb2ac13742002e729e
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57781700"
---
# <a name="using-subscription-keys-with-your-luis-app"></a>LUIS 앱에서 구독 키 사용

사용 가능한 처음 1,000개 엔드포인트 쿼리를 사용하기 위해서는 구독 키를 만들 필요가 없습니다. 이러한 엔드포인트 쿼리가 사용되면 [Azure Portal](https://portal.azure.com)에서 Azure 리소스를 만들고 해당 리소스를 [LUIS 포털](https://www.luis.ai)의 LUIS 앱에 할당합니다.

HTTP 403 및 429 형식의 _할당량 초과_ 오류가 발생하면 키를 만든 후 앱에 할당해야 합니다. 

테스트 및 프로토타입에 대해서만 무료(F0) 계층을 사용합니다. 프로덕션 시스템의 경우 [유료](https://aka.ms/luis-price-tier) 계층을 사용합니다. 프로덕션의 엔드포인트 쿼리에 [작성 키](luis-concept-keys.md#authoring-key)를 사용하지 마세요.

<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"/>

## <a name="create-prediction-endpoint-runtime-resource-in-the-azure-portal"></a>Azure portal에서 예측 끝점 런타임 리소스 만들기

사용 하 여 자세한 정보는 [응용 프로그램을 구축](get-started-portal-build-app.md) 빠른 시작 합니다.

<a name="programmatic-key" ></a>
<a name="authoring-key" ></a>
<a name="endpoint-key" ></a>
<a name="use-endpoint-key-in-query" ></a>
<a name="api-usage-of-ocp-apim-subscription-key" ></a>
<a name="key-limits" ></a>
<a name="key-limit-errors" ></a>
<a name="key-concepts"></a>
<a name="authoring-key"></a>
<a name="create-and-use-an-endpoint-key"></a>
<a name="assign-endpoint-key"></a>
<a name="assign-resource"></a>


## <a name="assign-resource-key-to-luis-app-in-luis-portal"></a>LUIS 포털의 LUIS 앱에 리소스 키 할당

사용 하 여 자세한 정보는 [배포](get-started-portal-deploy-app.md) 빠른 시작 합니다.

<!-- content moved to luis-reference-regions.md, need replacement links-->
<a name="regions-and-keys"></a>
<a name="publishing-to-europe"></a>
<a name="publishing-to-australia"></a>

### <a name="unassign-resource"></a>리소스 할당 해제
엔드포인트 키를 할당 해제할 경우 Azure에서 삭제되지 않습니다. LUIS에서만 할당 해제됩니다. 

엔드포인트가 할당 해제되었거나, 앱에 할당되지 않은 경우 엔드포인트 URL에 대한 모든 요청은 오류 `401 This application cannot be accessed with the current subscription`을 반환합니다. 

### <a name="include-all-predicted-intent-scores"></a>예측된 모든 의도 점수 포함
**예측된 모든 의도 점수 포함** 확인란을 사용하여 엔드포인트 쿼리 응답에 각 의도의 예측 점수를 포함할 수 있습니다. 

이 설정을 사용하면 챗봇 또는 LUIS 호출 애플리케이션이 반환된 의도의 점수에 따라 프로그래밍 방식으로 의사 결정을 내릴 수 있습니다. 일반적으로 상위 두 의도가 가장 흥미롭습니다. 상위 점수가 없음 의도이면, 챗봇은 없음 의도 및 기타 고득점 의도 간을 명확히 구분할 수 있는 후속 질문을 수행하도록 선택할 수 있습니다. 

의도 및 해당 점수도 엔드포인트 로그에 포함됩니다. 해당 로그를 [내보내고](luis-how-to-start-new-app.md#export-app) 점수를 분석할 수 있습니다. 

```JSON
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
**엔드포인트 URL 설정**에서 **Bing Spell Checker** 토글을 통해 LUIS에서 예측 전에 철자가 잘못된 단어를 수정할 수 있습니다. **[Bing Spell Check 키](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)** 를 만듭니다. 

**spellCheck=true** querystring 매개 변수 및 **bing-spell-check-subscription-key={YOUR_BING_KEY_HERE}** 를 추가합니다. `{YOUR_BING_KEY_HERE}`를 Bing Spell Checker 키로 바꿉니다.

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

### <a name="publishing-regions"></a>게시 지역

[유럽](luis-reference-regions.md#publishing-to-europe) 및 [오스트레일리아](luis-reference-regions.md#publishing-to-australia)의 게시를 포함하여 게시 [지역](luis-reference-regions.md)을 자세히 알아봅니다. 게시 지역은 작성 지역과 다릅니다. 쿼리 엔드포인트에 원하는 게시 지역에 해당하는 작성 지역에서 앱을 만듭니다.

## <a name="assign-resource-without-luis-portal"></a>LUIS 포털 없이 리소스 할당

CI/CD 파이프라인과 같은 자동화 용도로 LUIS 리소스의 LUIS 앱 할당을 자동화하려고 할 수 있습니다. 이렇게 하려면 다음 단계를 수행해야 합니다.

1. 이 [웹 사이트](https://resources.azure.com/api/token?plaintext=true)에서 Azure Resource Manager 토큰을 가져옵니다. 이 토큰은 만료되므로 즉시 사용합니다. 요청은 Azure Resource Manager 토큰을 반환합니다.

    ![Azure Resource Manager 토큰 요청 및 Azure Resource Manager 토큰 받기](./media/luis-manage-keys/get-arm-token.png)

1. 사용자 계정으로 액세스할 수 있는 [LUIS azure 계정 받기 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c)에서 토큰을 사용하여 구독 간에 LUIS 리소스를 요청합니다. 

    이 POST API에는 다음 설정이 필요합니다.

    |헤더|값|
    |--|--|
    |`Authorization`|`Authorization`의 값은 `Bearer {token}`입니다. 토큰 값 앞에 단어 `Bearer`와 공백이 와야 합니다.| 
    |`Ocp-Apim-Subscription-Key`|[작성 키](luis-how-to-account-settings.md)|

    이 API는 계정 이름으로 반환된 리소스 이름, 리소스 그룹 및 구독 ID를 포함하여 LUIS 구독의 JSON 개체 배열을 반환합니다. LUIS 앱에 할당할 LUIS 리소스인 배열에서 항목 하나를 찾습니다. 

1. [애플리케이션에 LUIS azure 계정 할당](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) API를 사용하여 LUIS 리소스에 토큰을 할당합니다. 

    이 POST API에는 다음 설정이 필요합니다.

    |Type|설정|값|
    |--|--|--|
    |헤더|`Authorization`|`Authorization`의 값은 `Bearer {token}`입니다. 토큰 값 앞에 단어 `Bearer`와 공백이 와야 합니다.|
    |헤더|`Ocp-Apim-Subscription-Key`|[작성 키](luis-how-to-account-settings.md)|
    |헤더|`Content-type`|`application/json`|
    |쿼리 문자열|`appid`|LUIS 앱 ID 
    |본문||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup-2",<br>"AccountName": "luis-uswest-S0-2"}|

    이 API가 성공하면 201 - 생성된 상태를 반환합니다. 

## <a name="change-pricing-tier"></a>가격 책정 계층 변경

1.  [Azure](https://portal.azure.com)에서 LUIS 구독을 찾습니다. LUIS 구독을 선택합니다.
    ![LUIS 구독 찾기](./media/luis-usage-tiers/find.png)
1.  사용 가능한 가격 책정 계층을 보려면 **가격 책정 계층**을 선택합니다. 
    ![가격 책정 계층 보기](./media/luis-usage-tiers/subscription.png)
1.  가격 책정 계층을 선택하고 **선택**을 선택하여 변경 내용을 저장합니다. 
    ![LUIS 결제 계층 변경](./media/luis-usage-tiers/plans.png)
1.  가격 변경이 완료되면 팝업 창에서 새로운 가격 책정 계층을 확인합니다. 
    ![LUIS 결제 계층 확인](./media/luis-usage-tiers/updated.png)
1. **게시** 페이지에서 [이 엔드포인트 키를 할당](#assign-endpoint-key)하고 모든 엔드포인트 쿼리에서 이 엔드포인트 키를 사용해야 합니다. 

## <a name="how-to-fix-out-of-quota-errors-when-the-key-exceeds-pricing-tier-usage"></a>키가 가격 책정 계층 사용량을 초과할 때 발생하는 할당량 초과 오류를 해결하는 방법
각 계층은 특정 요금으로 LUIS 계정에 대한 엔드포인트 요청을 허용합니다. 요청 요금이 분당 또는 월별 계정 요금제의 허용된 요금보다 높은 경우 요청은 HTTP 오류 “429: 요청이 너무 많음”을 수신합니다.

각 계층은 월별 누적 요청을 허용합니다. 총 요청이 허용된 요금보다 높은 경우 요청은 HTTP 오류 “403: 금지됨”을 수신합니다.  

## <a name="viewing-summary-usage"></a>요약 사용량 보기
Azure에서 LUIS 사용량 정보를 볼 수 있습니다. **개요** 페이지에는 호출 및 오류를 포함한 최근 요약 정보가 표시됩니다. LUIS 엔드포인트 요청을 만들고 나서 **개요 페이지**를 즉시 확인할 경우 사용량이 표시되는 데 최대 5분이 걸릴 수 있습니다.

![요약 사용량 보기](./media/luis-usage-tiers/overview.png)

## <a name="customizing-usage-charts"></a>사용 현황 차트 사용자 지정
메트릭은 데이터에 대한 더 자세한 보기를 제공합니다.

![기본 메트릭](./media/luis-usage-tiers/metrics-default.png)

기간 및 메트릭 유형에 대한 메트릭 차트를 구성할 수 있습니다. 

![사용자 지정 메트릭](./media/luis-usage-tiers/metrics-custom.png)

## <a name="total-transactions-threshold-alert"></a>총 트랜잭션 임계값 경고
언제 특정 트랜잭션 임계값(예: 10,000개의 트랜잭션)에 도달했는지 확인하려는 경우 경고를 만들 수 있습니다. 

![기본 경고](./media/luis-usage-tiers/alert-default.png)

특정 기간 동안 **총 호출** 메트릭에 대한 메트릭 경고를 추가합니다. 경고를 받아야 하는 모든 사람의 메일 주소를 추가합니다. 경고를 받아야 하는 모든 시스템의 웹후크를 추가합니다. 경고가 트리거될 때 논리 앱을 실행할 수도 있습니다. 

## <a name="next-steps"></a>다음 단계

[버전](luis-how-to-manage-versions.md)을 사용하여 LUIS 앱에 대한 변경 내용을 관리하는 방법에 대해 알아봅니다.
