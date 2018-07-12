---
title: LUIS 앱 일괄 처리 테스트 - Azure | Microsoft Docs
description: 일괄 처리 테스트를 사용하여 응용 프로그램을 지속적으로 개선하고 해당 언어에 대한 이해를 향상합니다.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/14/2018
ms.author: v-geberr
ms.openlocfilehash: 3803df32d6431b8413e8df0837ed62b2e4344cdc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35375359"
---
# <a name="batch-testing-in-luis"></a>LUIS의 일괄 처리 테스트

일괄 처리 테스트는 [활성](luis-concept-version.md#active-version) 학습 모델의 유효성을 검사하여 예측 정확도를 측정합니다. 일괄 처리 테스트는 차트의 현재 학습 모델에 있는 각 의도와 엔터티의 정확도를 확인하는 데 도움이 됩니다. 일괄 처리 테스트 결과를 검토하여 앱이 올바른 의도를 자주 식별하지 못하는 경우 의도에 예제 발언 추가 등의 적절한 조치를 취해 정확도를 향상합니다.

## <a name="group-data-for-batch-test"></a>일괄 처리 테스트를 위해 데이터 그룹화
일괄 처리 테스트에 사용되는 발언은 LUIS에 새로 추가된 발언이어야 합니다. 발언 데이터 집합이 있는 경우 발언을 의도에 추가되는 발언, 게시된 끝점에서 받은 발언, 학습된 후 LUIS 일괄 처리 테스트에 사용되는 발언의 세 가지 집합으로 나눕니다. 

## <a name="a-dataset-of-utterances"></a>발언 데이터 집합
일괄 처리 테스트를 위해 *데이터 집합*이라는 발언 배치 파일을 제출합니다. 데이터 집합은 레이블이 지정된 최대 1,000개의 **중복되지 않는** 발언을 포함하는 JSON 형식 파일입니다. 한 앱에서 최대 10개의 데이터 집합을 테스트할 수 있습니다. 더 많은 데이터 집합을 테스트해야 하는 경우 데이터 집합을 삭제하고 새 데이터 집합을 추가합니다.

|**규칙**|
|--|
|*중복 발언 없음|
|계층 구조 엔터티 자식 없음|
|1000개 이하의 발언|

*중복은 먼저 토큰화된 일치가 아니라 정확한 문자열 일치로 간주됩니다. 

<a name="json-file-with-no-duplicates"></a>
<a name="example-batch-file"></a>
## <a name="batch-file-format"></a>배치 파일 형식
배치 파일은 발언으로 구성됩니다. 각 발언에는 감지될 것으로 예상하는 모든 [Machine Learning 엔터티](luis-concept-entity-types.md#types-of-entities)와 함께 예상된 의도 예측이 있어야 합니다. 

예제 배치 파일은 다음과 같습니다.

   [!code-json[Valid batch test](~/samples-luis/documentation-samples/batch-testing/travel-agent-1.json)]


## <a name="common-errors-importing-a-batch"></a>배치를 가져오는 중 발생하는 일반적인 오류
일반적인 오류는 다음과 같습니다. 

> * 1,000개 발언 초과
> * 엔터티 속성이 없는 발언 JSON 개체

## <a name="batch-test-state"></a>일괄 처리 테스트 상태
LUIS는 각 데이터 집합의 마지막 테스트 상태를 추적합니다. 여기에는 크기(일괄 처리의 발언 수), 마지막 실행 날짜, 마지막 결과(성공적으로 예측된 발언 수)가 포함됩니다.

<a name="sections-of-the-results-chart"></a>
## <a name="batch-test-results"></a>일괄 처리 테스트 결과
일괄 처리 테스트 결과는 오류 행렬이라고 하는 분산형 그래프입니다. 이 그래프는 파일의 발언과 현재 모델의 예측 의도 및 엔터티 간의 4 방향 비교입니다. 

**거짓 긍정** 및 **거짓 부정** 섹션의 데이터 요소는 오류를 나타내며, 조사해야 합니다. 모든 데이터 요소가 **참 긍정** 및 **참 부정** 섹션에 있는 경우 이 데이터 집합에서 앱의 정확도가 완벽합니다.

![차트의 4개 섹션](./media/luis-concept-batch-test/chart-sections.png)

이 차트는 현재 학습을 기준으로 LUIS에서 잘못 예측하는 발언을 찾는 데 도움이 됩니다. 차트의 영역별로 결과가 표시됩니다. 그래프에서 개별 요소를 선택하여 발언 정보를 검토하거나, 영역 이름을 선택하여 해당 영역의 발언 결과를 검토합니다.

![일괄 처리 테스트](./media/luis-concept-batch-test/batch-testing.png)

## <a name="errors-in-the-results"></a>결과 오류
일괄 처리 테스트의 오류는 배치 파일에 명시된 대로 예측되지 않은 의도를 나타냅니다. 차트의 두 빨간색 섹션에 오류가 표시됩니다. 

거짓 긍정 섹션은 발언이 의도 또는 엔터티와 일치하지 않았어야 하는데 일치했음을 나타냅니다. 거짓 부정은 발언이 의도 또는 엔터티와 일치했어야 하는데 일치하지 않았음을 나타냅니다. 

## <a name="fixing-batch-errors"></a>일괄 처리 오류 수정
일괄 처리 테스트에 오류가 있는 경우 의도에 발언을 더 추가하거나, LUIS에서 의도를 구분하는 데 도움이 되도록 더 많은 발언에 엔터티로 레이블을 지정할 수 있습니다. 발언을 추가하고 레이블을 지정했지만 일괄 처리 테스트에서 예측 오류가 계속 발생하는 경우에는 LUIS 학습 속도 향상에 도움이 되도록 도메인 특정 어휘가 포함된 [구 목록](luis-concept-feature.md) 기능을 추가하는 것이 좋습니다. 

## <a name="next-steps"></a>다음 단계

* [일괄 처리 테스트](luis-how-to-batch-test.md) 방법을 알아봅니다.