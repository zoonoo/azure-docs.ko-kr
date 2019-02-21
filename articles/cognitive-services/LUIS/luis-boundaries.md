---
title: 제한
titleSuffix: Language Understanding - Azure Cognitive Services
description: 이 문서에는 Azure Cognitive Services Language Understanding(LUIS)의 알려진 제한이 포함됩니다. LUIS에는 여러 경계 영역이 있습니다. 모델 경계는 LUIS에서 의도, 엔터티 및 기능을 제어합니다. 할당량은 키 형식에 따라 제한됩니다. 키보드 조합은 LUIS 웹 사이트를 제어합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/12/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: df69cb5cc2e369a5e1f372d49a821616bf624db9
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56237555"
---
# <a name="boundaries-for-your-luis-model-and-keys"></a>LUIS 모델 및 키에 대한 경계
LUIS에는 여러 경계 영역이 있습니다. 첫 번째는 LUIS에서 의도, 엔터티 및 기능을 제어하는 [모델 경계](#model-boundaries)입니다. 두 번째 영역은 키 유형을 기반으로 하는 [할당량 한도](#key-limits)입니다. 세 번째 경계 영역은 LUIS 웹 사이트를 제어하기 위한 [키보드 조합](#keyboard-controls)입니다. 네 번째 영역은 LUIS 작성 웹 사이트와 LUIS [엔드포인트](luis-glossary.md#endpoint) API 간의 [세계 지역 매핑](luis-reference-regions.md)입니다. 


## <a name="model-boundaries"></a>모델 경계

앱이 LUIS 모델 제한 및 경계를 초과하는 경우 [LUIS 디스패치](luis-concept-enterprise.md#dispatch-tool-and-model) 앱 또는 [LUIS 컨테이너](luis-container-howto.md)를 사용하는 방안을 고려합니다. 

|영역|제한|
|--|:--|--|
| [앱 이름][luis-get-started-create-app] | *기본 문자 최댓값 |
| [일괄 테스트][batch-testing]| 10개 데이터 세트, 데이터 세트당 1000개 발화|
| 명시적 목록 | 애플리케이션당 50개|
| [의도][intents]|애플리케이션당 500개: 사용자 지정 의도 499개 및 필요한 _없음_ 의도.<br>[디스패치 기반](https://aka.ms/dispatch-tool) 애플리케이션에는 해당 디스패치 원본 500개가 있습니다.|
| [목록 엔터티](./luis-concept-entity-types.md) | 부모: 50, 자식: 20,000개 항목. 정식 이름은 *기본 문자 최댓값입니다. 동의어 값에는 길이 제한이 없습니다. |
| [기계 학습 엔터티](./luis-concept-entity-types.md):<br> 복합,<br>  계층적<br> 간단한|부모 엔터티 100개(계층적 자식 요소 제외) 또는 엔터티 330개(계층적 자식 요소 포함) 중 사용자가 먼저 도달하는 제한이 적용됩니다.<br><br>계층 구조의 예를 들자면 자식 요소 10개마다 계층 구조 30개가 있습니다.  자식 요소가 총 300개를 소비하고, 계층 구조 요소가 나머지 30개를 소비합니다. |
| [패턴](luis-concept-patterns.md)|애플리케이션당 500개 패턴.<br>패턴의 최대 길이는 400자입니다.<br>패턴당 3개의 Pattern.any 엔터티<br>패턴에 최대 2개의 선택적 중첩 텍스트|
| [Pattern.any](./luis-concept-entity-types.md)|애플리케이션당 100개, 패턴당 3개의 pattern.any 엔터티 |
| [구문 목록][phrase-list]|10개의 구 목록, 목록당 5,000개의 항목|
| [미리 빌드된 엔터티](./luis-prebuilt-entities.md) | 제한 없음|
| [정규식 엔터티](./luis-concept-entity-types.md)|20개 엔터티<br>정규식 엔터티 패턴당 최대 500자|
| [역할](luis-concept-roles.md)|애플리케이션당 300개 역할. 엔터티당 10개 역할|
| [발화][utterances] | 500자|
| [발화][utterances] | 애플리케이션당 15,000개|
| [버전](luis-concept-version.md)| 제한 없음 |
| [버전 이름][luis-how-to-manage-versions] | 영숫자 및 마침표(.)로 제한되는 10자 |

*기본 문자 최댓값은 50자입니다. 

## <a name="intent-and-entity-naming"></a>의도 및 엔터티 이름 지정
의도 및 엔터티 이름에 다음 문자를 사용하지 마세요.

|문자|Name|
|--|--|
|`{`|왼쪽 중괄호|
|`}`|오른쪽 중괄호|
|`[`|왼쪽 대괄호|
|`]`|오른쪽 대괄호|
|`\`|백슬래시|

## <a name="key-usage"></a>키 사용

언어 인식에는 예측 엔드포인트 작성 및 쿼리용으로 하나씩 두 가지 유형의 개별 키가 포함됩니다. 키 유형의 차이에 대해 자세히 알아보려면 [LUIS의 작성 및 쿼리 예측 엔드포인트 키](luis-concept-keys.md)를 참조하세요.

## <a name="key-limits"></a>키 제한

작성 키는 작성 및 엔드포인트에 대한 제한이 다릅니다. LUIS 서비스 끝점 키는 끝점 쿼리에만 유효합니다.


|키|작성|엔드포인트|목적|
|--|--|--|--|
|Language Understanding 작성/시작|100만/월, 5/초|1000/월, 5/초|LUIS 앱 작성|
|Language Understanding [구독][pricing] - F0 - 무료 계층 |잘못됨|10000/월, 5/초|LUIS 엔드포인트 쿼리|
|Language Understanding [구독][pricing] - S0 - 기본 계층|잘못됨|50/초|LUIS 엔드포인트 쿼리|
|Cognitive Service [구독][pricing] - S0 - 표준 계층|잘못됨|50/초|LUIS 엔드포인트 쿼리|
|[감정 분석 통합](luis-how-to-publish-app.md#enable-sentiment-analysis)|잘못됨|무료|핵심 구 데이터 추출을 포함하여 감정 정보 추가 |
|음성 통합|잘못됨|$5.50 USD/1000개 엔드포인트 요청|음성 발화를 텍스트 발화로 변환하고 LUIS 결과 반환|

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
