---
title: 제한 - LUIS
titleSuffix: Azure Cognitive Services
description: 이 문서에는 Azure Cognitive Services Language Understanding(LUIS)의 알려진 제한이 포함됩니다. LUIS에는 여러 경계 영역이 있습니다. 모델 경계는 LUIS에서 의도, 엔터티 및 기능을 제어합니다. 할당량은 키 형식에 따라 제한됩니다. 키보드 조합은 LUIS 웹 사이트를 제어합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/07/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: d584b00caef628eb9dfd085b1fdce2bb7b353988
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221346"
---
# <a name="boundaries-for-your-luis-model-and-keys"></a>LUIS 모델 및 키에 대한 경계
LUIS에는 여러 경계 영역이 있습니다. 첫 번째는 LUIS에서 의도, 엔터티 및 기능을 제어하는 [모델 경계](#model-boundaries)입니다. 두 번째 영역은 키 유형을 기반으로 하는 [할당량 한도](#key-limits)입니다. 세 번째 경계 영역은 LUIS 웹 사이트를 제어하기 위한 [키보드 조합](#keyboard-controls)입니다. 네 번째 영역은 LUIS 작성 웹 사이트와 LUIS [엔드포인트](luis-glossary.md#endpoint) API 간의 [세계 지역 매핑](luis-reference-regions.md)입니다.


## <a name="model-boundaries"></a>모델 경계

앱이 LUIS 모델 제한 및 경계를 초과하는 경우 [LUIS 디스패치](luis-concept-enterprise.md#dispatch-tool-and-model) 앱 또는 [LUIS 컨테이너](luis-container-howto.md)를 사용하는 방안을 고려합니다.

|영역|제한|
|--|:--|
| [앱 이름][luis-get-started-create-app] | *기본 문자 최댓값 |
| 애플리케이션| Azure 작성 리소스당 500개의 응용 프로그램 |
| [일괄 테스트][batch-testing]| 10개 데이터 세트, 데이터 세트당 1000개 발화|
| 명시적 목록 | 애플리케이션당 50개|
| 외부 엔터티 | 제한 없음 |
| [의도][intents]|응용 프로그램당 500: 499개의 사용자 지정 의도 및 필수 _없음_ 의도입니다.<br>[디스패치 기반](https://aka.ms/dispatch-tool) 응용 프로그램에는 해당 500개의 디스패치 소스가 있습니다.|
| [목록 엔터티](./luis-concept-entity-types.md) | 부모: 50, 자식: 20,000개 항목 정식 이름은 *기본 문자 최댓값입니다. 동의어 값에는 길이 제한이 없습니다. |
| [기계 학습 엔터티 + 역할:](./luis-concept-entity-types.md)<br> 복합<br>간단한<br>엔터티 역할|100개의 상위 엔터티 또는 330개 엔터티 중 하나(사용자 조회 를 먼저 제한하는 제한)로 제한됩니다. 역할은 이 경계의 목적을 위해 엔터티로 계산됩니다. 예를 들어 2개의 역할이 있는 간단한 엔터티가 있는 복합체는 330개 엔터티 중 1개 + 단순 1개 + 2개의 역할 = 4개입니다.<br>하위 구성 요소는 최대 5개 수준까지 중첩될 수 있습니다.|
|피쳐로 모델링| 특정 모델에 대한 설명자(피처)로 사용할 수 있는 최대 모델 수는 10개입니다. 특정 모델의 설명자(피처)로 사용되는 최대 구 목록 수는 10개의 구 목록입니다.|
| [미리 보기 - 동적 목록 엔터티](https://aka.ms/luis-api-v3-doc#dynamic-lists-passed-in-at-prediction-time)|쿼리 예측 끝점 요청당 ~1k 의 2개 목록|
| [패턴](luis-concept-patterns.md)|애플리케이션당 500개 패턴.<br>패턴의 최대 길이는 400자입니다.<br>패턴당 3개의 Pattern.any 엔터티<br>패턴에 최대 2개의 선택적 중첩 텍스트|
| [Pattern.any](./luis-concept-entity-types.md)|애플리케이션당 100개, 패턴당 3개의 pattern.any 엔터티 |
| [구문 목록][phrase-list]|500 개의 구문 목록. 서로 교환할 수 없는 구문 목록에는 최대 5,000개의 구가 있습니다. 교환 가능한 구문 목록에는 최대 50,000개의 구가 있습니다. 500,000개의 구문 적용당 최대 총 구수입니다.|
| [미리 빌드된 엔터티](./luis-prebuilt-entities.md) | 제한 없음|
| [정규식 엔터티](./luis-concept-entity-types.md)|20개 엔터티<br>정규식 엔터티 패턴당 최대 500자|
| [역할](luis-concept-roles.md)|애플리케이션당 300개 역할. 엔터티당 10개 역할|
| [발화][utterances] | 500자|
| [발화][utterances] | 응용 프로그램당 15,000개 - 의도당 발언 수에 제한이 없음|
| [버전](luis-concept-version.md)| 응용 프로그램당 100개 버전 |
| [버전 이름][luis-how-to-manage-versions] | 영숫자 및 마침표(.)로 제한되는 10자 |

*기본 문자 최댓값은 50자입니다.

<a name="intent-and-entity-naming"></a>

## <a name="name-uniqueness"></a>이름 고유성

다음 명명 고유성 규칙을 사용합니다.

다음은 LUIS 앱 내에서 고유해야 합니다.

* 버전 이름
* intent
* 엔터티
* roles

다음은 적용된 범위 내에서 고유해야 합니다.

* 구 목록

## <a name="object-naming"></a>개체 이름 지정

다음 이름의 다음 문자는 사용하지 마십시오.

|Object|문자 제외|
|--|--|
|의도, 엔터티 및 역할 이름|`:`<br>`$` <br> `&`|
|버전 이름|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="key-usage"></a>키 사용

언어 인식에는 예측 엔드포인트 작성 및 쿼리용으로 하나씩 두 가지 유형의 개별 키가 포함됩니다. 키 유형의 차이에 대해 자세히 알아보려면 [LUIS의 작성 및 쿼리 예측 엔드포인트 키](luis-concept-keys.md)를 참조하세요.

<a name="key-limits"></a>

## <a name="resource-key-limits"></a>리소스 키 제한

리소스 키에는 작성 및 끝점에 대한 제한이 다릅니다. LUIS 예측 쿼리 끝점 키는 끝점 쿼리에대해서만 유효합니다.

* Azure 작성 리소스당 500개의 응용 프로그램

|Key|작성|엔드포인트|목적|
|--|--|--|--|
|Starter|100만/월, 5/초|1000/월, 5/초|LUIS 앱 작성|
|F0 - 프리 티어 |100만/월, 5/초|10000/월, 5/초|LUIS 엔드포인트 쿼리|
|S0 - 기본 계층|-|50/초|LUIS 엔드포인트 쿼리|
|S0 - 표준 계층|-|50/초|LUIS 엔드포인트 쿼리|
|[감정 분석 통합](luis-how-to-publish-app.md#enable-sentiment-analysis)|-|-|키 구 데이터 추출을 포함한 감정 정보를 추가하는 것은 다른 Azure 리소스를 필요로 하지 않고 제공됩니다. |
|[음성 통합](../speech-service/how-to-recognize-intents-from-speech-csharp.md)|-|단가당 엔드포인트 요청 1,000개|음성 발화를 텍스트 발화로 변환하고 LUIS 결과 반환|

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
