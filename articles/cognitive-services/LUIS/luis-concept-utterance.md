---
title: Azure에서 LUIS 앱의 발언 | Microsoft Docs
description: LUIS(Language Understanding Intelligent Service) 앱에서 발언을 추가합니다.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/13/2018
ms.author: diberry
ms.openlocfilehash: 6f962d0aaf631051c841be29d2854a89bf58ac25
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224418"
---
# <a name="utterances-in-luis"></a>LUIS의 발언

**발언**은 앱이 해석해야 하는 사용자의 입력입니다. 해당 의도 및 엔터티를 추출하도록 LUIS를 학습하려는 경우 각 의도에 대해 다양한 입력을 캡처하는 것이 중요합니다. 활성 학습 또는 새 발언에 대한 지속적인 학습 프로세스는 LUIS에서 제공하는 기계 학습 인텔리전스에 반드시 필요합니다.

사용자가 입력할 것으로 생각되는 구를 수집합니다. 같은 내용을 의미하지만, 단어 길이 및 단어 배치가 다르게 구성된 발언을 포함합니다. 

## <a name="how-to-choose-varied-utterances"></a>다양한 발언을 선택하는 방법
LUIS 모델에 [예제 발언을 추가](luis-how-to-add-example-utterances.md)하여 처음 시작할 때 다음 원칙에 유의하세요.

### <a name="utterances-arent-always-well-formed"></a>발언이 항상 올바른 형식인 것은 아닙니다.
"파리행 티켓 예약"과 같은 문장이나 "예약" 또는 “파리행 항공권”과 같은 문장 부분이 있을 수 있습니다.  사용자는 종종 맞춤법 오류를 범합니다. 앱을 계획할 때 사용자 입력을 LUIS에 전달하기 전에 맞춤법을 검사할지 여부를 고려합니다. [Bing Spell Check API][BingSpellCheck]는 LUIS와 통합되어 있습니다. 게시할 때 LUIS 앱을 Bing Spell Check API용 외부 키에 연결할 수 있습니다. 사용자 발언의 맞춤법을 검사하지 않으면 입력 오류 및 철자 오류가 포함된 발언으로 LUIS를 학습하게 됩니다.

### <a name="use-the-representative-language-of-the-user"></a>사용자의 대표 언어 사용
발언을 선택할 때 일반적인 용어나 구라고 생각하는 것이 클라이언트 응용 프로그램의 일반적인 사용자에게는 그렇지 않을 수 있습니다. 도메인 환경이 없는 경우도 있습니다. 전문가인 것처럼 단어 또는 구 사용자 전문가 했는데 이 사람이 표시만 하려는 사용 하는 경우에 대해 주의 기울여야 합니다.

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>관용구 뿐만 아니라 변형 용어 선택
아무리 다양한 문장 패턴을 만들기 위해 애쓰더라도 일부 어휘가 여전히 반복될 수 있습니다.

다음과 같은 예제 발언을 사용하세요.
```
how do I get a computer?
Where do I get a computer?
I want to get a computer, how do I go about it?
When can I have a computer? 
```
여기서 핵심 용어인 "computer"는 변형되지 않습니다. 이 용어는 데스크톱 컴퓨터, 랩톱, 워크스테이션 또는 단순히 머신을 의미할 수 있습니다. LUIS는 컨텍스트에서 동의어를 지능적으로 유추하지만 학습을 위해 발언을 만들 때는 변형하는 것이 더 좋습니다.

## <a name="example-utterances-in-each-intent"></a>각 의도의 예제 발언
각 의도에는 10~15개 정도의 예제 발언이 필요합니다. 예제 발언이 없는 의도로는 LUIS를 학습시킬 수 없습니다. 의도에 하나 또는 소수의 예제 발언만 있으면 LUIS는 의도를 정확히 예측하지 못합니다. 

## <a name="add-small-groups-of-10-15-utterances-for-each-authoring-iteration"></a>각 작성 반복에 대해 10-15개의 소규모 발언 그룹 추가
모델의 각 반복에서 많은 양의 발언을 추가하지 않도록 합니다. 십여개 정도의 발언을 추가합니다. [학습](luis-how-to-train.md), [게시](luis-how-to-publish-app.md) 및 [테스트](luis-interactive-test.md)를 다시 진행합니다.  

LUIS는 신중하게 선택된 발언으로 효과적인 모델을 만듭니다. 너무 많은 발언을 추가하면 혼동을 가져오므로 유용하지 않습니다.  

처음에는 소수의 발언으로 시작한 후 정확한 의도 예측 및 엔터티 추출을 위해 [끝점 발언을 검토](luis-how-to-review-endoint-utt.md)합니다.

## <a name="ignoring-words-and-punctuation"></a>단어 및 문장 부호 무시
예제 발언의 특정 단어 또는 문장 부호를 무시하려는 경우 _ignore_ 구문과 함께 [패턴](luis-concept-patterns.md#pattern-syntax)을 사용합니다. 

## <a name="training-utterances"></a>발언 학습
학습은 비결정적입니다. 즉, 발언 예측이 버전 또는 앱에 따라 약간씩 다를 수 있습니다.

## <a name="testing-utterances"></a>발언 테스트 

개발자는 발언을 끝점으로 전송하여 실제 트래픽으로 LUIS 응용 프로그램 테스트를 시작해야 합니다. 이러한 발언은 [발언 검토](luis-how-to-review-endoint-utt.md)로 의도 및 엔터티의 성능을 개선하는 데 사용됩니다. LUIS 웹 사이트 테스트 창을 통해 제출된 테스트는 끝점으로 전송되지 않으므로 활성 학습에 기여하지 않습니다. 

## <a name="review-utterances"></a>발언 검토
모델이 학습되고, 게시되고 [끝점](luis-glossary.md#endpoint) 쿼리를 수신하면 LUIS에서 제안한 [발언을 검토](luis-how-to-review-endoint-utt.md)합니다. LUIS는 의도 또는 엔터티에 대해 낮은 점수를 갖는 끝점 발언을 선택합니다. 

## <a name="best-practices"></a>모범 사례
자세히 알아보려면 [모범 사례](luis-concept-best-practices.md)를 검토하세요.

## <a name="next-steps"></a>다음 단계
사용자 발언을 이해하도록 LUIS 앱을 학습하는 방법에 대한 내용은 [예제 발언 추가](luis-how-to-add-example-utterances.md)를 참조하세요.

[BingSpellCheck]: https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/proof-text