---
title: 'Designer: 자동차 가격 예측(기본) 예제'
titleSuffix: Azure Machine Learning
description: 코드를 한 줄도 작성하지 않고 Azure Machine Learning 디자이너를 사용하여 자동차 가격을 예측하는 ML 회귀 모델을 빌드합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 02/11/2020
ms.openlocfilehash: 58adbc7607b0b32e79123b701c37f55ce7cc1d2e
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77138124"
---
# <a name="use-regression-to-predict-car-prices-with-azure-machine-learning-designer"></a>회귀를 사용하여 Azure Machine Learning 디자이너로 자동차 가격 예측

**디자이너(미리 보기) 샘플 1**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

코드를 한 줄도 작성하지 않고 디자이너(미리 보기)를 사용하여 기계 학습 회귀 모델을 빌드하는 방법에 대해 알아봅니다.

이 파이프라인은 제조사, 모델, 출력, 크기 등의 기술적 특징에 따라 자동차 가격을 예측하는 **선형 회귀**를 학습시킵니다. "가격은 얼마?"라는 질문의 답을 찾으려는 것이기 때문에 이를 회귀 문제라고 부릅니다. 하지만 이 예제와 동일한 기본 단계를 적용하여 회귀, 분류, 클러스터링 등 모든 유형의 기계 학습 문제를 해결할 수 있습니다.

기계 학습 모델을 학습시키는 기본 단계는 다음과 같습니다.

1. 데이터 가져오기
1. 데이터 사전 처리
1. 모델 학습
1. 모델 평가

다음은 이 파이프라인의 최종적으로 완성된 그래프입니다. 이 문서에서는 여러분 스스로 비슷한 결정을 내릴 수 있도록 모든 모듈의 근거를 제공합니다.

![파이프라인 그래프](./media/how-to-designer-sample-regression-automobile-price-basic/overall-graph.png)

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. 샘플 1을 클릭하여 엽니다.


## <a name="get-the-data"></a>데이터 가져오기

이 샘플에서는 UCI Machine Learning 리포지토리의 **자동차 가격 데이터(원시)** 데이터 세트를 사용합니다. 이 데이터 세트에는 제조사, 모델, 가격, 차량 특징(예: 실린더 수), MPG 및 보험 위험 점수를 비롯한 자동차 정보를 포함하고 있는 26개 열이 있습니다. 이 샘플의 목표는 자동차 가격을 예측하는 것입니다.

## <a name="pre-process-the-data"></a>데이터 사전 처리

주요 데이터 준비 작업에는 데이터 정리, 통합, 변환, 축소, 분할 또는 양자화가 포함됩니다. 디자이너의 왼쪽 패널에 있는 **데이터 변환** 그룹에서 이러한 작업 및 기타 데이터 전처리 작업을 수행하는 모듈을 찾을 수 있습니다.

**데이터 세트에서 열 선택** 모듈을 사용하여 누락 값이 많은 정규화된 손실을 제외합니다. 그런 다음, **누락 데이터 정리**를 사용하여 누락된 값이 있는 행을 제거합니다. 이렇게 하면 깔끔한 학습 데이터 세트를 만들 수 있습니다.

![데이터 사전 처리](./media/how-to-designer-sample-regression-automobile-price-basic/data-processing.png)

## <a name="train-the-model"></a>모델 학습

기계 학습 문제는 다양합니다. 일반적인 기계 학습 작업에는 분류, 클러스터링, 회귀 및 추천 시스템이 포함되며, 각각 다른 알고리즘이 필요할 수 있습니다. 알고리즘 선택은 종종 사용 사례의 요구 사항에 따라 달라집니다. 알고리즘을 선택한 후에는 모델을 보다 정확하게 학습시킬 수 있도록 매개 변수를 조정해야 합니다. 그런 다음, 정확도, 명료도, 효율성 같은 메트릭을 기반으로 모든 모델을 평가해야 합니다.

이 샘플의 목표는 자동차 가격을 예측하는 것이고 레이블 열(가격)이 연속 데이터이므로 회귀 모델을 선택하는 것이 좋습니다. 이 파이프라인에 **선형 회귀**를 사용합니다.

학습 데이터 세트에 원래 데이터의 70%가 포함되고 테스트 데이터 세트에 원래 데이터의 30%가 포함되도록 **데이터 분할** 모듈을 사용하여 입력 데이터를 임의로 나눕니다.

## <a name="test-evaluate-and-compare"></a>학습, 평가 및 비교

데이터 세트를 분할하고 다양한 데이터 세트로 모델을 학습 및 테스트하여 보다 객관적으로 모델을 평가합니다.

모델을 학습시킨 후에는 **모델 채점**및 **모델 평가** 모듈을 사용하여 예측 결과를 생성하고 모델을 평가할 수 있습니다.

**모델 채점**에서는 학습된 모델을 사용하여 테스트 데이터 세트에 대한 예측을 생성합니다. 결과를 확인하려면 **모델 채점** 출력 포트를 선택하고 **시각화**를 선택합니다.

![채점 결과](./media/how-to-designer-sample-regression-automobile-price-basic/sample1-score-1225.png)

점수를 **모델 평가** 모듈에 전달하여 평가 메트릭을 생성합니다. 결과를 확인하려면 **모델 평가** 출력 포트를 선택하고 **시각화**를 선택합니다.

![평가 결과](./media/how-to-designer-sample-regression-automobile-price-basic/sample1-evaluate-1225.png)

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>다음 단계

디자이너에 사용할 수 있는 다른 샘플을 살펴봅니다.

- [샘플 2 - 회귀: 자동차 가격 예측 알고리즘에 대한 비교](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [샘플 3 - 기능 선택을 사용하여 분류: 수입 예측](how-to-designer-sample-classification-predict-income.md)
- [샘플 4 - 분류: 신용 위험 예측(비용 구분)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [샘플 5 - 분류: 이탈 예측](how-to-designer-sample-classification-churn.md)
- [샘플 6 - 분류: 항공편 지연 예측](how-to-designer-sample-classification-flight-delay.md)
- [샘플 7 - 텍스트 분류: Wikipedia SP 500 데이터 세트](how-to-designer-sample-text-classification.md)