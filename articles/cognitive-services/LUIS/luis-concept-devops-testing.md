---
title: LUIS apps에 대 한 DevOps 테스트
description: DevOps 환경에서 Language Understanding (LUIS) 앱을 테스트 하는 방법입니다.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/3/2020
ms.openlocfilehash: c41e9fe1f197334bce27241ab9f28309c92f7e0a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91316548"
---
# <a name="testing-for-luis-devops"></a>LUIS DevOps 테스트

LUIS (Language Understanding) 앱을 개발 하는 소프트웨어 엔지니어는 다음 지침에 따라 [소스 제어](luis-concept-devops-sourcecontrol.md), [자동화 된 빌드](luis-concept-devops-automation.md), [테스트](luis-concept-devops-testing.md)및 [릴리스 관리](luis-concept-devops-automation.md#release-management) 에 대 한 devops 사례를 적용할 수 있습니다.

Agile Software Development 방법론에서 테스트는 품질 소프트웨어 구축의 필수적인 역할을 합니다. LUIS 앱에 대 한 모든 주요 변경 내용에는 개발자가 앱에 빌드하는 새 기능을 테스트 하기 위해 설계 된 테스트가 수반 되어야 합니다. 이러한 테스트는 `.lu` LUIS 앱의 소스와 함께 소스 코드 리포지토리에 체크 인 됩니다. 앱이 테스트를 충족 하면 변경 내용의 구현이 완료 됩니다.

테스트는 [CI/CD 워크플로의](luis-concept-devops-automation.md)중요 한 부분입니다. LUIS 앱에 대 한 변경 내용이 끌어오기 요청 (PR)에서 제안 되거나 변경 내용이 마스터 분기로 병합 된 경우 CI 워크플로는 테스트를 실행 하 여 업데이트가 재발을 발생 하지 않았는지 확인 해야 합니다.

## <a name="how-to-do-unit-testing-and-batch-testing"></a>단위 테스트 및 배치 테스트를 수행 하는 방법

연속 통합 워크플로에서 수행 해야 하는 LUIS 앱에 대 한 테스트에는 두 가지 종류가 있습니다.

- **단위 테스트** -LUIS 앱의 주요 기능을 확인 하는 비교적 간단한 테스트입니다. 지정 된 테스트 utterance에 대해 예상 된 의도 및 예상 된 엔터티가 반환 될 때 단위 테스트가 통과 합니다. 테스트 실행을 성공적으로 완료 하려면 모든 단위 테스트를 통과 해야 합니다.  
이러한 종류의 테스트는 [LUIS 포털](https://www.luis.ai/)에서 수행할 수 있는 [대화형 테스트](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test) 와 비슷합니다.

- **일괄 처리 테스트** -일괄 처리 테스트는 현재 학습 된 모델에 대 한 포괄적인 테스트로, 성능을 측정 합니다. 단위 테스트와 달리 일괄 처리 테스트는 통과 되지 않습니다 | 테스트 실패. 일괄 테스트를 사용 하는 경우 모든 테스트에서 예상 된 의도 및 예상 된 엔터티를 반환 하는 것은 아닙니다. 대신, 일괄 처리 테스트를 사용 하면 앱에서 각 의도 및 엔터티의 정확성을 확인 하 고 향상 된 시간에 걸쳐 비교할 수 있습니다.  
이러한 종류의 테스트는 LUIS 포털에서 대화형으로 수행할 수 있는 [일괄 처리 테스트](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-batch-test) 와 동일 합니다.

프로젝트의 시작 부분에서 단위 테스트를 사용할 수 있습니다. 일괄 처리 테스트는 LUIS 앱의 스키마를 개발 하 고 정확도를 향상 시키기 위해 작업 하는 경우에만 가치를 발휘 합니다.

단위 테스트와 일괄 처리 테스트 모두에 대해 테스트 길이 발언 교육 길이 발언 별도로 유지 되는지 확인 합니다. 학습 하는 동일한 데이터에 대해 테스트 하는 경우 테스트 데이터에 과잉 맞춤 하는 것 처럼 앱이 잘 작동 하는 것을 알 수 있습니다. 모델에서 테스트를 확인 하지 않아야 합니다.

### <a name="writing-tests"></a>테스트 작성

테스트 집합을 작성 하는 경우 각 테스트에 대해 다음을 정의 해야 합니다.

* 발언 테스트
* 예상 의도
* 엔터티가 필요 합니다.

JSON 형식 파일에서 테스트 그룹을 정의 하려면 LUIS [batch 파일 구문을](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-batch-test#batch-syntax-template-for-intents-with-entities) 사용 합니다. 예를 들면 다음과 같습니다.

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

일부 테스트 도구 (예: [Nlu) DevOps](https://github.com/microsoft/NLU.DevOps) 는 또한 LUDown 형식의 테스트 파일도 지원 합니다.

#### <a name="designing-unit-tests"></a>단위 테스트 디자인

단위 테스트는 LUIS 앱의 핵심 기능을 테스트 하도록 설계 되어야 합니다. 앱 개발의 각 반복 또는 스 프린트에서 충분 한 수의 테스트를 작성 하 여 해당 반복에서 구현 하는 주요 기능이 제대로 작동 하는지 확인 해야 합니다.

각 단위 테스트에서 지정 된 테스트 utterance에 대해 다음을 수행할 수 있습니다.

* 올바른 의도가 반환 되는지 테스트 합니다.
* 솔루션에 중요 한 ' 키 ' 엔터티가 반환 되는지 테스트 합니다.
* 의도 및 엔터티에 대 한 [예측 점수가](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-prediction-score) 정의한 임계값을 초과 하는지 테스트 합니다. 예를 들어, 의도 및 키 엔터티에 대 한 예측 점수가 0.75를 초과 하는 경우 테스트를 통과 한 것으로 간주 합니다.

단위 테스트에서 키 엔터티가 예측 응답에서 반환 되었는지 테스트 하는 것이 좋지만 가양성을 무시 하는 것이 좋습니다. *가양성* 은 예측 응답에서 찾았지만 테스트에 대해 예상 되는 결과에 정의 되지 않은 엔터티입니다. 가양성을 무시 하 여 단위 테스트를 작성 하는 것이 더 번거로운, 솔루션에 대 한 키 데이터가 예측 응답에서 반환 되는지 테스트에 집중할 수 있습니다.

> [!TIP]
> [Nlu입니다. DevOps](https://github.com/microsoft/NLU.DevOps) 도구는 모든 LUIS 테스트 요구 사항을 지원 합니다. `compare` [단위 테스트 모드](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#unit-test-mode) 에서 사용 하는 경우 명령은 모든 테스트를 통과 하 고 예상 결과에서 레이블이 지정 되지 않은 엔터티의 가양성 결과를 무시 합니다.

#### <a name="designing-batch-tests"></a>일괄 처리 테스트 디자인

일괄 처리 테스트 집합에는 LUIS 앱의 모든 엔터티 및 모든 엔터티에 대해 테스트 하도록 설계 된 많은 수의 테스트 사례가 포함 되어야 합니다. 일괄 처리 테스트 집합을 정의 하는 방법에 대 한 자세한 내용은 [LUIS 포털에서 일괄 처리 테스트](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-batch-test) 를 참조 하세요.

### <a name="running-tests"></a>테스트 실행

LUIS 포털은 대화형 테스트에 도움이 되는 기능을 제공 합니다.

* [**대화형 테스트**](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test) 를 사용 하면 샘플 utterance를 제출 하 고 LUIS 인식 의도 및 엔터티에 대 한 응답을 받을 수 있습니다. 시각적 검사에의 한 테스트 성공 여부를 확인 합니다.

* [**일괄 처리 테스트**](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-batch-test) 는 일괄 처리 테스트 파일을 입력으로 사용 하 여 학습 된 활성 버전의 유효성을 검사 하 여 예측 정확도를 측정 합니다. 일괄 처리 테스트를 사용 하면 활성 버전에서 각 의도 및 엔터티의 정확성을 확인 하 고 차트를 사용 하 여 결과를 표시할 수 있습니다.

#### <a name="running-tests-in-an-automated-build-workflow"></a>자동화 된 빌드 워크플로에서 테스트 실행

LUIS 포털의 대화형 테스트 기능이 유용 하지만 DevOps의 경우 CI/CD 워크플로에서 자동화 된 테스트를 수행 하면 특정 요구 사항이 적용 됩니다.

* 테스트 도구는 빌드 서버의 워크플로 단계에서 실행 해야 합니다. 즉, 도구는 명령줄에서 실행할 수 있어야 합니다.
* 테스트 도구는 끝점에 대해 테스트 그룹을 실행 하 고 실제 결과에 대해 예상 결과를 자동으로 확인할 수 있어야 합니다.
* 테스트가 실패 하면 테스트 도구는 상태 코드를 반환 하 여 워크플로를 중지 하 고 "빌드 실패"를 수행 해야 합니다.

LUIS는 이러한 기능을 제공 하는 명령줄 도구나 고급 API를 제공 하지 않습니다. Nlu를 사용 하는 것이 좋습니다 [. ](https://github.com/microsoft/NLU.DevOps) 테스트를 실행 하 고 명령줄에서 및 CI/CD 워크플로 내에서 자동화 된 테스트를 수행 하 여 결과를 확인 하는 DevOps 도구입니다.

LUIS 포털에서 사용할 수 있는 테스트 기능은 게시 된 끝점이 필요 하지 않으며 LUIS 제작 기능의 일부입니다. 자동화 된 빌드 워크플로에서 테스트를 구현 하는 경우에는 NLU와 같은 테스트 도구를 위해 끝점에 테스트할 LUIS app 버전을 게시 해야 합니다. DevOps는 테스트의 일부로 예측 요청을 보낼 수 있습니다.

> [!TIP]
> * 사용자 고유의 테스트 솔루션을 구현 하 고 테스트 길이 발언을 끝점에 보내는 코드를 작성 하는 경우 LUIS authoring key를 사용 하는 경우 허용 되는 트랜잭션 rate는 5TPS로 제한 됩니다. 전송 속도로 제한 하거나 예측 키를 대신 사용 하십시오.
> * 테스트 쿼리를 끝점으로 보낼 때 `log=false` 예측 요청의 쿼리 문자열에를 사용 해야 합니다. 이렇게 하면 테스트 길이 발언 LUIS에 의해 기록 되지 않고 LUIS [활성 학습](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-review-endpoint-utterances) 기능에 의해 제공 되는 끝점 길이 발언 검토 목록에서 종료 되며, 결과적으로 앱의 학습 길이 발언에 실수로 추가 됩니다.

#### <a name="running-unit-tests-at-the-command-line-and-in-cicd-workflows"></a>명령줄 및 CI/CD 워크플로에서 단위 테스트 실행

Nlu를 사용할 수 있습니다 [. ](https://github.com/microsoft/NLU.DevOps) 명령줄에서 테스트를 실행할 DevOps 패키지:

* NLU를 사용 합니다. DevOps [테스트 명령을](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Test.md) 통해 테스트 파일에서 끝점으로 테스트를 제출 하 고 실제 예측 결과를 파일에 캡처합니다.
* NLU를 사용 합니다. DevOps [compare 명령을](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md) 사용 하 여 실제 결과를 입력 테스트 파일에 정의 된 예상 결과와 비교할 수 있습니다. `compare`명령은 NUnit 테스트 출력을 생성 하 고 플래그를 사용 하 여 [단위 테스트 모드](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#unit-test-mode) 에서 사용 될 경우 `--unit-test` 모든 테스트가 통과 한다는 것을 어설션 합니다.

### <a name="running-batch-tests-at-the-command-line-and-in-cicd-workflows"></a>명령줄 및 CI/CD 워크플로에서 배치 테스트 실행

또한 NLU를 사용할 수 있습니다. 명령줄에서 일괄 처리 테스트를 실행 하는 DevOps 패키지.

* NLU를 사용 합니다. DevOps [테스트 명령을](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Test.md) 사용 하 여 테스트 파일에서 끝점으로 테스트를 제출 하 고, 단위 테스트와 같이 파일에서 실제 예측 결과를 캡처합니다.
* NLU를 사용 합니다. [성능 테스트 모드](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#performance-test-mode) 의 devops [compare 명령을](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md) 통해 응용 프로그램의 성능을 측정 하는 것은 응용 프로그램의 성능을 기준 성능 벤치 마크와 비교할 수 있습니다. 예를 들어 최신 커밋 마스터나 현재 릴리스에 대 한 결과입니다. 성능 테스트 모드에서 `compare` 명령은 NUnit 테스트 출력과 [일괄 처리 테스트 결과](https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#batch-test) 를 JSON 형식으로 생성 합니다.

## <a name="luis-non-deterministic-training-and-the-effect-on-testing"></a>비결 정적 교육 및 테스트에 미치는 영향 LUIS

LUIS가 모델을 학습 하는 경우 (예: 모델에 대해 앱을 학습 하기 위해 제공 하는 레이블이 지정 된 학습 길이 발언 및 해당 모델 사용에 대 한 유효한 예제가 *아닌* 데이터 데이터) 두 개의 긍정적인 데이터가 모두 필요 합니다. 학습 하는 동안 LUIS는 다른 모델에 대해 제공한 모든 양의 데이터에서 한 모델의 음수 데이터를 작성 하지만 일부 경우에는 데이터 불균형을 생성할 수 있습니다. 이러한 불균형을 방지 하기 위해 LUIS는 더 나은 분산 된 학습 집합, 향상 된 모델 성능 및 더 빠른 학습 시간을 최적화 하기 위해 부정적인 데이터의 하위 집합을 명확 하지 않은 방식으로 샘플링 합니다.

이러한 비 결정적인 학습의 결과는 일반적으로 [다른 학습 세션 간에 약간 다른 예측 응답](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-prediction-score)을 얻을 수 있다는 것입니다. 일반적으로는 [예측 점수가](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-prediction-score) 높지 않은 의도 및/또는 엔터티에 대해 약간 다를 수 있습니다.

테스트 목적으로 빌드하는 LUIS 앱 버전에 대해 비결 정적 교육을 사용 하지 않도록 설정 하려면 설정이로 설정 된 [버전 설정 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) 를 사용 `UseAllTrainingData` `true` 합니다.

## <a name="next-steps"></a>다음 단계

* [CI/CD 워크플로 구현](luis-concept-devops-automation.md) 에 대 한 자세한 정보
* [GitHub를 사용 하 여 LUIS에 대 한 DevOps를 구현](luis-how-to-devops-with-github.md) 하는 방법 알아보기