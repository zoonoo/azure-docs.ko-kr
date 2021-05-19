---
title: LUIS 앱에 대한 DevOps 테스트
description: DevOps 환경에서 LUIS(Language Understanding) 앱을 테스트하는 방법입니다.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/3/2020
ms.openlocfilehash: cd2fd8dc8c10864089b198db1ca1089f994a3ffb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98788454"
---
# <a name="testing-for-luis-devops"></a>LUIS DevOps에 대한 테스트

LUIS(Language Understanding) 앱을 개발하는 소프트웨어 엔지니어는 다음 지침을 수행하여 [소스 제어](luis-concept-devops-sourcecontrol.md), [자동화된 빌드](luis-concept-devops-automation.md), [테스트](luis-concept-devops-testing.md) 및 [릴리스 관리](luis-concept-devops-automation.md#release-management)에 대한 DevOps 사례를 적용할 수 있습니다.

Agile Software Development 방법론에서 테스트는 소프트웨어 품질 구축의 필수적인 역할을 합니다. LUIS 앱에 대한 모든 주요 변경에는 개발자가 앱에 빌드하는 새 기능을 시험하기 위해 설계된 테스트가 수반되어야 합니다. 이러한 테스트는 LUIS 앱의 `.lu` 소스와 함께 소스 코드 리포지토리에 체크 인됩니다. 앱이 테스트를 충족하면 변경 내용의 구현이 완료됩니다.

테스트는 [CI/CD 워크플로](luis-concept-devops-automation.md)의 중요한 부분입니다. LUIS 앱에 대한 변경 내용이 끌어오기 요청(PR)에서 제안되거나 변경 내용이 기본 분기에 병합된 경우 CI 워크플로는 테스트를 실행하여 업데이트가 재발을 발생하지 않았는지 확인해야 합니다.

## <a name="how-to-do-unit-testing-and-batch-testing"></a>단위 테스트 및 일괄 테스트를 수행하는 방법

연속 통합 워크플로에서 수행해야 하는 LUIS 앱에 대한 테스트에는 두 가지 종류가 있습니다.

- **단위 테스트** - LUIS 앱의 주요 기능을 확인하는 비교적 간단한 테스트입니다. 지정된 테스트 발화에 대해 예상된 의도 및 예상된 엔터티가 반환될 때 단위 테스트가 통과합니다. 테스트 실행을 성공적으로 완료하려면 모든 단위 테스트를 통과해야 합니다.  
이러한 종류의 테스트는 [LUIS 포털](https://www.luis.ai/)에서 수행할 수 있는 [대화형 테스트](./luis-concept-test.md)와 비슷합니다.

- **일괄 테스트** - 일괄 테스트는 현재 학습된 모델의 포괄적인 테스트로, 해당 성능을 측정할 수 있습니다. 단위 테스트와 달리 일괄 테스트는 테스트를 통과하거나 실패하지 않습니다. 일괄 테스트를 사용하는 경우 모든 테스트에서 예상된 의도 및 예상된 엔터티를 반환하는 것은 아닙니다. 대신, 일괄 테스트를 사용하면 앱에서 각 의도 및 엔터티의 정확성을 확인하고 시간에 따른 정확도 향상을 비교할 수 있습니다.  
이러한 종류의 테스트는 LUIS 포털에서 대화형으로 수행할 수 있는 [일괄 테스트](./luis-how-to-batch-test.md)와 동일합니다.

프로젝트의 시작 부분에서 단위 테스트를 사용할 수 있습니다. 일괄 테스트는 LUIS 앱의 스키마를 개발하고 정확도를 향상시키기 위해 작업하는 경우에만 가치를 발휘합니다.

단위 테스트와 일괄 테스트 모두에 대해 테스트 발화가 학습 발화와 별도로 유지되는지 확인합니다. 동일한 학습 데이터에 대해 테스트하는 경우 테스트 데이터에 과잉 맞춤될 경우 앱이 잘 작동하는 것으로 오해할 수 있습니다. 얼마나 잘 일반화되는지 확인하려면 모델에서 테스트를 확인하지 않아야 합니다.

### <a name="writing-tests"></a>테스트 작성

테스트 집합을 작성하는 경우 각 테스트에 대해 다음을 정의해야 합니다.

* 발언 테스트
* 예상 의도
* 예상 엔터티입니다.

LUIS [일괄 처리 파일 구문](./luis-how-to-batch-test.md#batch-syntax-template-for-intents-with-entities)을 사용하여 JSON 형식 파일에서 테스트 그룹을 정의합니다. 예를 들면 다음과 같습니다.

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities":
    [
        {
            "entity": "entity name 1 goes here",
            "startPos": 14,
            "endPos": 23
        },
        {
            "entity": "entity name 2 goes here",
            "startPos": 14,
            "endPos": 23
        }
    ]
  }
]
```

또한 일부 테스트 도구(예: [NLU.DevOps](https://github.com/microsoft/NLU.DevOps))는 LUDown 형식의 테스트 파일도 지원합니다.

#### <a name="designing-unit-tests"></a>단위 테스트 설계

단위 테스트는 LUIS 앱의 핵심 기능을 테스트하도록 설계해야 합니다. 앱 개발의 각 반복 또는 스프린트에서 충분한 수의 테스트를 작성하여 해당 반복에서 구현하는 주요 기능이 제대로 작동하는지 확인해야 합니다.

각 단위 테스트에서 지정된 테스트 발화에 대해 다음을 수행할 수 있습니다.

* 올바른 의도가 반환되는지 테스트합니다.
* 솔루션에 중요한 '키' 엔터티가 반환되는지 테스트합니다.
* 의도 및 엔터티에 대한 [예측 점수](./luis-concept-prediction-score.md)가 정의한 임계값을 초과하는지 테스트합니다. 예를 들어, 의도 및 키 엔터티에 대한 예측 점수가 0.75를 초과하는 경우에만 테스트를 통과한 것으로 간주할 수 있습니다.

단위 테스트에서 키 엔터티가 예측 응답에서 반환되었는지 테스트하되 가양성은 무시하는 것이 좋습니다. *가양성* 은 예측 응답에는 있지만 테스트에 대해 예상되는 결과에 정의되지 않은 엔터티입니다. 가양성을 무시하면 단위 테스트 작성이 쉬워지고 솔루션에 대한 키 데이터가 예측 응답에서 반환되는지 테스트하는 것에 집중할 수 있습니다.

> [!TIP]
> [NLU.DevOps](https://github.com/microsoft/NLU.DevOps) 도구는 모든 LUIS 테스트 요구 사항을 지원합니다. `compare` 명령을 [단위 테스트 모드](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#unit-test-mode)에서 사용하면 모든 테스트를 통과하고 예상 결과에서 레이블이 지정되지 않은 엔터티의 가양성 결과를 무시합니다.

#### <a name="designing-batch-tests"></a>일괄 테스트 설계

일괄 테스트 집합에는 LUIS 앱의 모든 의도 및 모든 엔터티에 대해 테스트하도록 설계된 많은 테스트 사례가 포함되어야 합니다. 일괄 테스트 집합을 정의하는 방법에 대한 자세한 내용은 [LUIS 포털에서 일괄 테스트](./luis-how-to-batch-test.md)를 참조하세요.

### <a name="running-tests"></a>테스트 실행

LUIS 포털은 대화형 테스트에서 유용한 기능을 제공합니다.

* [**대화형 테스트**](./luis-concept-test.md)를 사용하면 샘플 발화를 제출하고 LUIS 인식 의도 및 엔터티에 대한 응답을 얻을 수 있습니다. 시각적 검사로 테스트 성공 여부를 확인합니다.

* [**일괄 테스트**](./luis-how-to-batch-test.md)는 일괄 테스트 파일을 입력으로 사용하여 학습된 활성 버전의 유효성을 검사하여 예측 정확도를 측정합니다. 일괄 테스트는 결과를 차트로 표시하여 활성 버전의 각 의도와 엔터티의 정확도를 확인하는 데 도움이 됩니다.

#### <a name="running-tests-in-an-automated-build-workflow"></a>자동화된 빌드 워크플로에서 테스트 실행

LUIS 포털의 대화형 테스트 기능은 유용하지만 DevOps의 경우 CI/CD 워크플로에서 자동화된 테스트를 수행하려면 특정 요구 사항이 적용됩니다.

* 테스트 도구는 빌드 서버의 워크플로 단계에서 실행해야 합니다. 즉, 명령줄에서 도구를 실행할 수 있어야 합니다.
* 테스트 도구는 엔드포인트에 대해 테스트 그룹을 실행하고 실제 결과에 대해 예상 결과를 자동으로 확인할 수 있어야 합니다.
* 테스트가 실패하는 경우 테스트 도구는 워크플로를 중지하는 상태 코드를 반환하고 "빌드 실패"를 반환해야 합니다.

LUIS는 이러한 기능을 제공하는 명령줄 도구나 고급 API를 제공하지 않습니다. [NLU.DevOps](https://github.com/microsoft/NLU.DevOps) 도구를 사용하여 테스트를 실행하고 명령줄과 CI/CD 워크플로 내 자동화된 테스트 중에 모두 결과를 확인하는 것이 좋습니다.

LUIS 포털에서 사용할 수 있는 테스트 기능은 게시된 엔드포인트 필요하지 않으며 LUIS 작성 기능의 일부입니다. 자동화된 빌드 워크플로에서 테스트를 구현하는 경우에는 NLU.DevOps와 같은 테스트 도구에서 테스트의 일부로 예측 요청을 보낼 수 있도록 테스트할 LUIS 앱 버전을 엔드포인트에 게시해야 합니다.

> [!TIP]
> * 사용자 고유의 테스트 솔루션을 구현하고 테스트 발화를 엔드포인트에 보내는 코드를 작성할 때 LUIS 작성 키를 사용하는 경우 허용되는 트랜잭션 속도는 5TPS로 제한됩니다. 전송 속도를 스로틀하거나 예측 키를 대신 사용합니다.
> * 테스트 쿼리를 엔드포인트로 보낼 때 예측 요청의 쿼리 문자열에 `log=false`를 사용해야 합니다. 이렇게 하면 테스트 발화가 LUIS에 의해 기록되지 않고 LUIS [활성 학습](./luis-concept-review-endpoint-utterances.md) 기능에서 제공되는 엔드포인트 발화 검토 목록에서 종료되며, 결과적으로 앱의 학습 발화에 실수로 추가됩니다.

#### <a name="running-unit-tests-at-the-command-line-and-in-cicd-workflows"></a>명령줄 및 CI/CD 워크플로에서 단위 테스트 실행

명령줄에서 테스트를 실행하려면 [NLU.DevOps](https://github.com/microsoft/NLU.DevOps) 패키지를 사용합니다.

* 테스트 파일에서 엔드포인트로 테스트를 제출하여 파일의 실제 예측 결과를 캡처하려면 NLU.DevOps [test 명령](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Test.md)을 사용합니다.
* 실제 결과를 입력 테스트 파일에 정의된 예상 결과와 비교하려면 NLU.DevOps [compare 명령](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md)을 사용합니다. `compare` 명령은 NUnit 테스트 출력을 생성하고 `--unit-test` 플래그를 사용하여 [단위 테스트 모드](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#unit-test-mode)에서 사용되는 경우 모든 테스트가 통과하는지 확인합니다.

### <a name="running-batch-tests-at-the-command-line-and-in-cicd-workflows"></a>명령줄 및 CI/CD 워크플로에서 일괄 테스트 실행

또한 NLU.DevOps 패키지를 사용하여 명령줄에서 일괄 처리 테스트를 실행할 수도 있습니다.

* 테스트 파일에서 엔드포인트로 테스트를 제출하여 파일의 실제 예측 결과를 캡처하려면 단위 테스트와 동일한 NLU.DevOps [test 명령](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Test.md)을 사용합니다.
* 앱 성능을 측정하려면 [성능 테스트 모드](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#performance-test-mode)에서 NLU.DevOps [compare 명령](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md)을 사용합니다. 기준선 성능 벤치마크에 대해 앱의 성능을 비교할 수도 있습니다(예: 기본 또는 현재 릴리스에 대한 최신 커밋 결과). 성능 테스트 모드에서 `compare` 명령은 NUnit 테스트 출력과 [일괄 테스트 결과](./luis-glossary.md#batch-test)를 JSON 형식으로 생성합니다.

## <a name="luis-non-deterministic-training-and-the-effect-on-testing"></a>LUIS 비결정적 학습 및 테스트에 미치는 영향

LUIS가 모델을 학습하는 경우(예: 의도) 긍정 데이터(모델에 대해 앱을 학습하기 위해 제공한 레이블이 지정된 학습 발화)와 부정 데이터(해당 모델 사용에 대한 유효한 예가 *아닌* 데이터)가 모두 필요합니다. 학습하는 동안 LUIS는 다른 모델에 대해 제공한 모든 긍정 데이터에서 한 모델의 부정 데이터를 빌드하지만 일부 경우에는 데이터 불균형이 발생할 수 있습니다. 이러한 불균형을 방지하기 위해 LUIS는 보다 균형 있는 학습 집합, 향상된 모델 성능 및 더 빠른 학습 시간을 최적화하기 위해 부정 데이터의 하위 집합을 비결정적 방식으로 샘플링합니다.

이러한 비결정적 학습의 결과는 일반적으로 [예측 점수](./luis-concept-prediction-score.md)가 높지 않은 의도 및/또는 엔터티의 경우 [다른 학습 세션 간에 약간 다른 예측 응답](./luis-concept-prediction-score.md)을 얻을 수 있다는 것입니다.

테스트 목적으로 빌드하는 LUIS 앱 버전에 대해 비결정적 학습을 사용하지 않도록 설정하려면 `UseAllTrainingData` 설정이 `true`로 설정된 [버전 설정 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings)를 사용합니다.

## <a name="next-steps"></a>다음 단계

* [CI/CD 워크플로 구현](luis-concept-devops-automation.md)에 대해 알아봅니다.
* [GitHub를 사용하여 LUIS에 대한 DevOps를 구현](luis-how-to-devops-with-github.md)하는 방법을 알아봅니다.