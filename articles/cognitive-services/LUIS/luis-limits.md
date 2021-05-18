---
title: 제한 - LUIS
description: 이 문서에는 Azure Cognitive Services Language Understanding(LUIS)의 알려진 제한이 포함됩니다. LUIS에는 여러 제한 영역이 있습니다. 모델 제한은 LUIS에서 의도, 엔터티 및 기능을 제어합니다. 할당량은 키 형식에 따라 제한됩니다. 키보드 조합은 LUIS 웹 사이트를 제어합니다.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 06/04/2020
ms.openlocfilehash: 41423ce34a62dfdbd5b9a60f683a2366a94d1bfd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "97976795"
---
# <a name="limits-for-your-luis-model-and-keys"></a>LUIS 모델 및 키에 대한 제한
LUIS에는 여러 제한 영역이 있습니다. 첫 번째는 LUIS에서 의도, 엔터티 및 기능을 제어하는 [모델 제한](#model-limits)입니다. 두 번째 영역은 키 유형을 기반으로 하는 [할당량 한도](#key-limits)입니다. 세 번째 제한 영역은 LUIS 웹 사이트를 제어하기 위한 [키보드 조합](#keyboard-controls)입니다. 네 번째 영역은 LUIS 작성 웹 사이트와 LUIS [엔드포인트](luis-glossary.md#endpoint) API 간의 [세계 지역 매핑](luis-reference-regions.md)입니다.

<a name="model-boundaries"></a>

## <a name="model-limits"></a>모델 제한

앱이 LUIS 모델 제한을 초과하는 경우 [LUIS 디스패치](luis-concept-enterprise.md#dispatch-tool-and-model) 앱 또는 [LUIS 컨테이너](luis-container-howto.md)를 사용하는 방안을 고려합니다.

|영역|제한|
|--|:--|
| [앱 이름][luis-get-started-create-app] | *기본 문자 최댓값 |
| 애플리케이션| Azure 작성 리소스당 500개 애플리케이션 |
| [일괄 테스트][batch-testing]| 10개 데이터 세트, 데이터 세트당 1000개 발화|
| 명시적 목록 | 애플리케이션당 50개|
| 외부 엔터티 | 제한 없음. |
| [의도][intents]|애플리케이션당 500개: 499개의 사용자 지정 의도와 필수 _None_ 의도.<br>[디스패치 기반](https://aka.ms/dispatch-tool) 애플리케이션에는 해당 디스패치 원본 500개가 있습니다.|
| [엔터티 나열](./luis-concept-entity-types.md) | 부모: 50, 자식: 20,000개 항목 정식 이름은 *기본 문자 최댓값입니다. 동의어 값에는 길이 제한이 없습니다. |
| [기계 학습 엔터티 + 역할](./luis-concept-entity-types.md):<br> 복합,<br>단순,<br>엔터티 역할|100개 부모 엔터티 또는 330개 엔터티의 제한, 사용자가 먼저 도달하는 제한. 역할은 이 제한의 목적에 대한 엔터티로 계산됩니다. 예를 들어 단순 엔터티가 포함된 복합 엔터티에 2개의 역할이 있는 경우는 복합 1 + 단순 1 + 역할 2 = 330개 엔터티 중 4개가 됩니다.<br>하위 엔터티는 수준당 최대 10개의 자식을 포함하여 최대 5개 수준까지 중첩될 수 있습니다.|
|기능으로서의 모델| 특정 모델의 기능으로 사용할 수 있는 최대 모델 수는 10개입니다. 특정 모델의 기능으로 사용되는 최대 구문 목록 수는 10개입니다.|
| [미리 보기 - 동적 목록 엔터티](./luis-migration-api-v3.md)|쿼리 예측 엔드포인트 요청당 최대 1k의 두 목록|
| [패턴](luis-concept-patterns.md)|애플리케이션당 500개 패턴.<br>패턴의 최대 길이는 400자입니다.<br>패턴당 3개의 Pattern.any 엔터티<br>패턴에 최대 2개의 선택적 중첩 텍스트|
| [Pattern.any](./luis-concept-entity-types.md)|애플리케이션당 100개, 패턴당 3개의 pattern.any 엔터티 |
| [구문 목록][phrase-list]|500개의 구문 목록. 기능 제한 모델로 인한 10개의 전역 구문 목록. 교환할 수 없는 구문 목록에는 최대 5,000개의 구문이 있습니다. 교환할 수 있는 구문 목록에는 최대 50,000개의 구문이 있습니다. 애플리케이션당 총 구문 수는 최대 500,000개입니다.|
| [미리 빌드된 엔터티](./howto-add-prebuilt-models.md) | 제한 없음|
| [정규식 엔터티](./luis-concept-entity-types.md)|20개 엔터티<br>정규식 엔터티 패턴당 최대 500자|
| [역할](./luis-concept-entity-types.md)|애플리케이션당 300개 역할. 엔터티당 10개 역할|
| [발화][utterances] | 500자<br><br>이 문자 제한보다 긴 텍스트가 있는 경우 LUIS에 입력하기 전에 발화를 분할해야 합니다. 그러면 세그먼트별로 개별 의도 응답을 받게 됩니다. 음성에 문장 부호와 긴 일시 중지 등 사용할 수 있는 명확한 중단이 있습니다.|
| [발화 예제][utterances] | 애플리케이션당 15,000개 - 의도당 발화 수에 제한이 없음<br><br>더 많은 예를 사용하여 애플리케이션을 학습시켜야 하는 경우 [디스패치](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch) 모델 접근 방법을 사용합니다. 하나 이상의 의도를 사용하여 개별 LUIS 앱(부모 디스패치 앱에 대한 자식 앱이라고 함)을 학습시킨 다음 각 자식 LUIS 앱의 발화에서 샘플링하는 디스패치 앱을 학습시켜 예측 요청을 올바른 자식 앱으로 전달합니다. |
| [버전](./luis-concept-app-iteration.md)| 애플리케이션당 100개 버전 |
| [버전 이름][luis-how-to-manage-versions] | 128자 |

*기본 문자 최댓값은 50자입니다.

<a name="intent-and-entity-naming"></a>

## <a name="name-uniqueness"></a>이름 고유성

개체 이름은 같은 수준의 다른 개체와 비교하여 고유해야 합니다.

|개체|제한|
|--|--|
|의도, 엔터티|모든 의도 및 엔터티 이름은 앱 버전에서 고유해야 합니다.|
|ML 엔터티 구성 요소|모든 기계 학습 엔터티 구성 요소(자식 엔터티)는 동일한 수준의 구성 요소에 대한 해당 엔터티 내에서 고유해야 합니다.|
|기능 | 구문 목록과 같은 모든 명명된 기능은 앱 버전 내에서 고유해야 합니다.|
|엔터티 역할|엔터티 또는 엔터티 구성 요소의 모든 역할은 동일한 엔터티 수준(부모, 자식, 손자 등)에서 고유해야 합니다.|

## <a name="object-naming"></a>개체 이름 지정

다음 이름에 다음 문자를 사용하지 마세요.

|Object|문자 제외|
|--|--|
|의도, 엔터티 및 역할 이름|`:`<br>`$` <br> `&`|
|버전 이름|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="resource-usage-and-limits"></a>리소스 사용량 및 제한

언어 인식에는 예측 엔드포인트 작성 및 쿼리용으로 하나씩 두 가지 유형의 개별 리소스가 포함됩니다. 키 유형의 차이에 대해 자세히 알아보려면 [LUIS의 작성 및 쿼리 예측 엔드포인트 키](luis-how-to-azure-subscription.md)를 참조하세요.

<a name="key-limits"></a>

### <a name="authoring-resource-limits"></a>작성 리소스 제한

Azure Portal에서 리소스를 필터링할 때 _종류_(`LUIS.Authoring`)를 사용합니다. LUIS는 Azure 작성 리소스당 애플리케이션을 500개로 제한합니다.

|작성 리소스|작성 TPS|
|--|--|
|Starter|100만/월, 5/초|
|F0 - 무료 계층 |100만/월, 5/초|

* TPS - 초당 트랜잭션 수

[가격 책정에 대해 자세히 알아보세요.][pricing]

### <a name="query-prediction-resource-limits"></a>쿼리 예측 리소스 제한

Azure Portal에서 리소스를 필터링할 때 _종류_(`LUIS`)를 사용합니다. 런타임에 사용되는 LUIS 쿼리 예측 엔드포인트 리소스는 엔드포인트 쿼리에만 유효합니다.

|쿼리 예측 리소스|쿼리 TPS|
|--|--|
|F0 - 무료 계층 |10000/월, 5/초|
|S0 - 표준 계층|50/초|

### <a name="sentiment-analysis"></a>정서 분석

[감정 분석 통합](luis-how-to-publish-app.md#enable-sentiment-analysis)은 감정 정보를 제공하며 다른 Azure 리소스를 요구하지 않고 제공됩니다.

### <a name="speech-integration"></a>음성 통합

[음성 통합](../speech-service/how-to-recognize-intents-from-speech-csharp.md)은 단위 비용당 1000개의 엔드포인트 요청을 제공합니다.

[가격 책정에 대해 자세히 알아보세요.][pricing]

## <a name="keyboard-controls"></a>키보드 제어

|키보드 입력 | Description |
|--|--|
|Control+E|발화 목록에서 토큰과 엔터티 간 전환|

## <a name="website-sign-in-time-period"></a>웹 사이트 로그인 기간

로그인 액세스는 **60분** 동안 가능합니다. 이 기간이 지나면 이 오류가 표시됩니다. 다시 로그인해야 합니다.

[luis-get-started-create-app]: ./luis-get-started-create-app.md
[batch-testing]: ./luis-concept-test.md#batch-testing
[intents]: ./luis-concept-intent.md
[phrase-list]: ./luis-concept-feature.md
[utterances]: ./luis-concept-utterance.md
[luis-how-to-manage-versions]: ./luis-how-to-manage-versions.md
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
