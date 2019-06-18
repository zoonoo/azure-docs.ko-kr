---
title: '회귀 분석: 가격 예측'
titleSuffix: Azure Machine Learning service
description: 기계 학습 코드 한 줄도 작성 하지 않고 자동차의 가격을 예측 하는 모델을 빌드하는 방법에 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 9dfa4b62f5cb79a5716f6f29651e85d0f8a3a409
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65787854"
---
# <a name="sample-1---regression-predict-price"></a>1-회귀 샘플: 가격 예측

Machine learning 회귀 모델의 시각적 인터페이스를 사용 하 여 코드 한 줄도 작성 하지 않고 빌드하는 방법에 알아봅니다.

이 실험의 트레인을 **포리스트 회귀 의사 결정** 자동차를 예측할 수의 가격에 따라 제조업체, 모델, 마 력, 및 크기와 같은 기술 기능입니다. "얼마?" 질문에 답변 하려고 하기 때문에 이 회귀 문제를 라고 합니다. 그러나 회귀, 분류, 클러스터링 및 등 든 관계 없이 모든 유형의 기계 학습 문제를 해결 하려면이 실험에서는 동일한 기본 단계를 적용할 수 있습니다.

교육 기계 학습 모델의 기본 단계를 다음과 같습니다.

1. 데이터 가져오기
1. 데이터 전처리
1. 모델 학습
1. 모델 평가

작업에서 실험의 최종, 완성 된 그래프는 다음과 같습니다. 직접 비슷한 결정을 내릴 수 있도록 모든 모듈에 대 한 근거를 제공 됩니다.

![실험 그래프](media/ui-sample-regression-predict-automobile-price-basic/overall-graph.png)

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. 선택 된 **열고** 샘플 1 실험에 대 한 단추:

    ![실험을 열으십시오](media/ui-sample-regression-predict-automobile-price-basic/open-sample1.png)

## <a name="get-the-data"></a>데이터 가져오기

이 실험에서 사용 된 **자동차 가격 데이터 (원시)** UCI 기계 학습 리포지토리에서 인 데이터 집합을 합니다. 데이터 집합, 제조업체, 모델, 가격, 차량 기능 (예: 실린더 수), MPG, 및 보험 위험 점수를 포함 하 여 자동차에 대 한 정보를 포함 하는 26 열을 포함 합니다. 이 실험의 목적은 자동차의 가격을 예측 하 하는 것입니다.

## <a name="pre-process-the-data"></a>데이터 전처리

기본 데이터 준비 작업에는 데이터 정리, 통합, 변환, 감소 하 고 분할 또는 양자화 포함 됩니다. 시각적 인터페이스에서 이러한 작업 및 다른 데이터 전처리 작업을 수행 하는 모듈을 찾을 수 있습니다 합니다 **데이터 변환** 왼쪽된 패널의 그룹입니다.

사용 합니다 **Select Columns in Dataset** 많은 누락 값이 있는 normalized losses 제외할 모듈입니다. 사용 하 여 **누락 데이터 정리** 누락 값이 있는 행을 제거 합니다. 이 학습 데이터 집합을 새로 만들 수 있습니다.

![데이터 사전 처리](./media/ui-sample-regression-predict-automobile-price-basic/data-processing.png)

## <a name="train-the-model"></a>모델 학습

기계 학습 문제에는 다음이 달라 집니다. 일반적인 기계 학습 작업에는 분류, 클러스터링, 회귀 및 다른 알고리즘을 필요할 수 있으며 각 추천 시스템을 포함 합니다. 종종 선택한 알고리즘 사용 사례 요구 사항에 따라 달라 집니다. 알고리즘을 선택 하 고 나면 더 정확한 모델을 학습 하는 매개 변수를 튜닝 해야 합니다. 그런 다음 효율성, 정확도 및 명확성을 등의 메트릭을 기반으로 하는 모든 모델을 평가 해야 합니다.

이 실험의 목적은 자동차 가격 예측 하 고 레이블 열 (price) 실수를 포함 하기 때문에 회귀 모델은 것이 좋습니다. 의사 결정 경계는 기능 수가 상대적으로 작은 (100 보다 작은) 이며 이러한 기능을 스파스 없습니다를 고려 비선형 할 가능성이 높습니다. 사용 하므로 **의사 결정 포리스트 회귀** 이 실험에 대 한 합니다.

사용 하 여 합니다 **분할 데이터** 임의로 학습 데이터 집합은 원본 데이터의 70%를 포함 하 고 원래 데이터의 30%를 포함 하는 테스트 데이터 집합 입력된 데이터를 분할 하는 모듈입니다.

## <a name="test-evaluate-and-compare"></a>테스트, 평가 및 비교

 데이터 집합을 분할 하 고 학습 및 평가 모델의 자세한 목표 하는 모델을 테스트 하려면 다른 데이터 집합을 사용 합니다.

사용 모델을 학습 한 후는 **모델 점수 매기기** 하 고 **모델 평가** 예측된 결과 생성 하 고 모델을 평가 하는 모듈입니다.

**모델 점수 매기기** 학습된 된 모델을 사용 하 여 테스트 데이터 집합에 대 한 예측을 생성 합니다. 결과 확인 하려면의 출력 포트를 선택 **모델 점수 매기기** 선택한 후 **시각화**합니다.

![점수 매기기 결과](./media/ui-sample-regression-predict-automobile-price-basic/score-result.png)

다음 점수에 전달 된 **모델 평가** 평가 메트릭을 생성 하는 모듈입니다. 결과 확인 하려면의 출력 포트를 선택 합니다 **모델 평가** 선택한 후 **시각화**합니다.

![결과 평가](./media/ui-sample-regression-predict-automobile-price-basic/evaluate-result.png)

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>다음 단계

시각적 인터페이스에 사용할 수 있는 다른 샘플을 탐색 합니다.

- [2-회귀 샘플: 자동차 가격 예측 알고리즘 비교](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [3-분류 샘플: 신용 위험 예측](ui-sample-classification-predict-credit-risk-basic.md)
- [4-분류 샘플: (중요 한 비용) 신용 위험 예측](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [5-분류 샘플: 이탈 예측](ui-sample-classification-predict-churn.md)