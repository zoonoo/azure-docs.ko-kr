---
title: 제한-LUIS
description: 이 문서에는 Azure Cognitive Services Language Understanding(LUIS)의 알려진 제한이 포함됩니다. LUIS에는 몇 가지 제한 영역이 있습니다. 모델 제한은 LUIS의 의도, 엔터티 및 기능을 제어 합니다. 할당량은 키 형식에 따라 제한됩니다. 키보드 조합은 LUIS 웹 사이트를 제어합니다.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 06/04/2020
ms.openlocfilehash: 5ef681e335cf49a1759a096766b5ccd70545e60a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324708"
---
# <a name="limits-for-your-luis-model-and-keys"></a>LUIS 모델 및 키에 대 한 제한
LUIS에는 몇 가지 제한 영역이 있습니다. 첫 번째는 LUIS의 의도, 엔터티 및 기능을 제어 하는 [모델 제한](#model-limits)입니다. 두 번째 영역은 키 유형을 기반으로 하는 [할당량 한도](#key-limits)입니다. 제한의 세 번째 영역은 LUIS 웹 사이트를 제어 하는 [키보드 조합](#keyboard-controls) 입니다. 네 번째 영역은 LUIS 작성 웹 사이트와 LUIS [엔드포인트](luis-glossary.md#endpoint) API 간의 [세계 지역 매핑](luis-reference-regions.md)입니다.

<a name="model-boundaries"></a>

## <a name="model-limits"></a>모델 제한

앱이 LUIS 모델 제한을 초과 하는 경우 [LUIS 디스패치](luis-concept-enterprise.md#dispatch-tool-and-model) 앱을 사용 하거나 [LUIS 컨테이너](luis-container-howto.md)를 사용 하는 것이 좋습니다.

|영역|제한|
|--|:--|
| [앱 이름][luis-get-started-create-app] | *기본 문자 최댓값 |
| 애플리케이션| 500 Azure 제작 리소스 당 응용 프로그램 |
| [일괄 테스트][batch-testing]| 10개 데이터 세트, 데이터 세트당 1000개 발화|
| 명시적 목록 | 애플리케이션당 50개|
| 외부 엔터티 | 제한 없음 |
| [의도][intents]|응용 프로그램당 500:499 사용자 지정 의도 및 필요한 _없음_ 의도입니다.<br>[디스패치 기반](https://aka.ms/dispatch-tool) 응용 프로그램에는 해당 하는 500 디스패치 소스가 있습니다.|
| [엔터티 나열](./luis-concept-entity-types.md) | 부모: 50, 자식: 20,000개 항목 정식 이름은 *기본 문자 최댓값입니다. 동의어 값에는 길이 제한이 없습니다. |
| [기계 학습 엔터티 + 역할](./luis-concept-entity-types.md):<br> 합성할<br>쉽게<br>엔터티 역할|100 부모 엔터티 또는 330 엔터티 중 하나는 사용자가 먼저 적중 하는 것을 제한 합니다. 역할은이 제한의 용도에 대 한 엔터티로 계산 됩니다. 예를 들어 두 개의 역할이 있는 복합 엔터티는 1 개의 복합 + 1 단순 + 2 역할 = 4 인 330 엔터티입니다.<br>하위 엔터티는 최대 5 수준까지 중첩할 수 있습니다.|
|기능으로 서의 모델| 특정 모델의 기능으로 사용할 수 있는 모델의 최대 수를 10 개 모델로 사용할 수 있습니다. 특정 모델의 기능으로 사용 되는 최대 문구 목록 수를 10 개 구 목록으로 표시 합니다.|
| [미리 보기-동적 목록 엔터티](https://aka.ms/luis-api-v3-doc#dynamic-lists-passed-in-at-prediction-time)|2-쿼리 예측 끝점 요청당 ~ 1k 목록|
| [패턴](luis-concept-patterns.md)|애플리케이션당 500개 패턴.<br>패턴의 최대 길이는 400자입니다.<br>패턴당 3개의 Pattern.any 엔터티<br>패턴에 최대 2개의 선택적 중첩 텍스트|
| [Pattern.any](./luis-concept-entity-types.md)|애플리케이션당 100개, 패턴당 3개의 pattern.any 엔터티 |
| [구 목록][phrase-list]|500 구 목록. 모델에 기능 제한으로 인 한 10 개의 글로벌 문구 목록 교환 불가능 한 구 목록에는 최대 5000 구가 있습니다. 교환 가능한 구 목록에는 최대 5만 구가 있습니다. 50만 구의 응용 프로그램당 최대 총 문구 수입니다.|
| [미리 빌드된 엔터티](./luis-prebuilt-entities.md) | 제한 없음|
| [정규식 엔터티](./luis-concept-entity-types.md)|20개 엔터티<br>정규식 엔터티 패턴당 최대 500자|
| [역할](luis-concept-roles.md)|애플리케이션당 300개 역할. 엔터티당 10개 역할|
| [발화][utterances] | 500자<br><br>이 문자 제한 보다 긴 텍스트가 있는 경우 LUIS에 입력 하기 전에 utterance를 분할 해야 합니다. 그러면 세그먼트 마다 개별 의도 응답을 받게 됩니다. 문장 부호와 음성의 긴 일시 중지와 같이 사용할 수 있는 명확한 나누기가 있습니다.|
| [Utterance 예제][utterances] | 15000 응용 프로그램당-길이 발언 수에 제한이 없습니다.<br><br>더 많은 예제를 사용 하 여 응용 프로그램을 학습 해야 하는 경우 [디스패치](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch) 모델 방법을 사용 합니다. 하나 이상의 의도를 사용 하 여 개별 LUIS 앱 (부모 디스패치 앱에 자식 앱 이라고 함)을 학습 한 다음 각 자식 LUIS 앱의 길이 발언에서 샘플링 하는 디스패치 앱을 학습 하 여 예측 요청을 올바른 자식 앱으로 전달 합니다. |
| [버전](luis-concept-version.md)| 응용 프로그램당 100 버전 |
| [버전 이름][luis-how-to-manage-versions] | 128자 |

*기본 문자 최댓값은 50자입니다.

<a name="intent-and-entity-naming"></a>

## <a name="name-uniqueness"></a>이름 고유성

개체 이름은 같은 수준의 다른 개체와 비교 했을 때 고유 해야 합니다.

|개체|제한|
|--|--|
|의도, 엔터티|모든 의도 및 엔터티 이름은 앱 버전에서 고유 해야 합니다.|
|ML 엔터티 구성 요소|모든 기계 학습 엔터티 구성 요소 (자식 엔터티)는 동일한 수준의 구성 요소에 대 한 해당 엔터티 내에서 고유 해야 합니다.|
|기능 | 구 목록과 같은 모든 명명 된 기능은 앱 버전 내에서 고유 해야 합니다.|
|엔터티 역할|엔터티 또는 엔터티 구성 요소의 모든 역할은 동일한 엔터티 수준 (부모, 자식, 손자 등)에 있는 경우 고유 해야 합니다.|

## <a name="object-naming"></a>개체 이름 지정

다음 이름에는 다음 문자를 사용 하지 마십시오.

|Object|문자 제외|
|--|--|
|의도, 엔터티 및 역할 이름|`:`<br>`$` <br> `&`|
|버전 이름|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="resource-usage-and-limits"></a>리소스 사용 및 제한

언어 이해에는 별도의 리소스, 제작을 위한 한 가지 형식, 예측 끝점을 쿼리 하는 한 가지 형식이 있습니다. 키 유형의 차이에 대해 자세히 알아보려면 [LUIS의 작성 및 쿼리 예측 엔드포인트 키](luis-how-to-azure-subscription.md)를 참조하세요.

<a name="key-limits"></a>

### <a name="authoring-resource-limits"></a>리소스 한도 작성

_kind_ `LUIS.Authoring` Azure Portal에서 리소스를 필터링 할 때 종류를 사용 합니다. LUIS는 Azure 제작 리소스 당 500 응용 프로그램을 제한 합니다.

|제작 리소스|TPS 제작|
|--|--|
|Starter|100만/월, 5/초|
|F0-무료 계층 |100만/월, 5/초|

* TPS = 초당 트랜잭션 수

[가격 책정에 대해 자세히 알아보세요.][pricing]

### <a name="query-prediction-resource-limits"></a>쿼리 예측 리소스 제한

_kind_ `LUIS` Azure Portal에서 리소스를 필터링 할 때 종류를 사용 합니다. 런타임에 사용 되는 LUIS 쿼리 예측 끝점 리소스는 끝점 쿼리에만 유효 합니다.

|쿼리 예측 리소스|쿼리 TPS|
|--|--|
|F0-무료 계층 |10000/월, 5/초|
|S0-표준 계층|50/초|

### <a name="sentiment-analysis"></a>정서 분석

감정 정보를 제공 하는 [감정 analysis 통합](luis-how-to-publish-app.md#enable-sentiment-analysis)은 다른 Azure 리소스를 요구 하지 않고 제공 됩니다.

### <a name="speech-integration"></a>음성 통합

[음성 통합](../speech-service/how-to-recognize-intents-from-speech-csharp.md) 은 단가 당 1000 끝점 요청을 제공 합니다.

[가격 책정에 대해 자세히 알아보세요.][pricing]

## <a name="keyboard-controls"></a>키보드 제어

|키보드 입력 | 설명 |
|--|--|
|Control+E|발화 목록에서 토큰과 엔터티 간 전환|

## <a name="website-sign-in-time-period"></a>웹 사이트 로그인 기간

로그인 액세스는 **60분** 동안 가능합니다. 이 기간이 지나면 이 오류가 표시됩니다. 다시 로그인해야 합니다.

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app
[batch-testing]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test#batch-testing
[intents]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent
[phrase-list]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[utterances]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-utterance
[luis-how-to-manage-versions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
