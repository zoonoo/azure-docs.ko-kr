---
title: 'Designer: 자동차 가격 예측(고급) 예제'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 디자이너를 사용하여 기술적 특징에 따라 자동차 가격을 예측하는 여러 ML 회귀 모델을 빌드하고 비교합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: a80a1567c84ff3c2eda8ad22391aa862bb7d9d82
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2020
ms.locfileid: "77915829"
---
# <a name="train--compare-multiple-regression-models-to-predict-car-prices-with-azure-machine-learning-designer"></a>Azure Machine Learning 디자이너를 사용하여 자동차 가격을 예측하는 여러 회귀 모델 학습 및 비교

**디자이너(미리 보기) 샘플 2**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

코드를 한 줄도 작성하지 않고 디자이너(미리 보기)를 사용하여 기계 학습 파이프라인을 빌드하는 방법에 대해 알아봅니다. 이 샘플은 기술적 특징에 따라 자동차 가격을 예측하는 여러 회귀 모델을 학습시키고 비교합니다. 여러분이 각자 고유의 기계 학습 문제를 해결할 수 있도록, 이 파이프라인에서 옵션을 선택할 때 그 이유를 설명드리겠습니다.

기계 학습을 이제 막 시작하는 경우에는 이 파이프라인의 [기본 버전](how-to-designer-sample-regression-automobile-price-basic.md)를 살펴보세요.

다음은 이 파이프라인의 완성된 그래프입니다.

[![파이프라인 그래프](./media/how-to-designer-sample-regression-automobile-price-compare-algorithms/graph.png)](./media/how-to-designer-sample-regression-automobile-price-compare-algorithms/graph.png#lightbox)

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. 샘플 2를 클릭하여 엽니다. 

## <a name="pipeline-summary"></a>파이프라인 요약

다음 단계에 따라 기계 학습 파이프라인을 빌드합니다.

1. 데이터를 가져옵니다.
1. 데이터를 전처리합니다.
1. 모델을 학습시킵니다.
1. 모델을 테스트, 평가 및 비교합니다.

## <a name="get-the-data"></a>데이터 가져오기

이 샘플에서는 UCI Machine Learning 리포지토리의 **자동차 가격 데이터(원시)** 데이터 세트를 사용합니다. 이 데이터 세트에는 제조사, 모델, 가격, 차량 특징(예: 실린더 수), MPG 및 보험 위험 점수를 비롯한 자동차 정보를 포함하고 있는 26개 열이 있습니다.

## <a name="pre-process-the-data"></a>데이터 사전 처리

주요 데이터 준비 작업에는 데이터 정리, 통합, 변환, 축소, 분할 또는 양자화가 포함됩니다. 디자이너의 왼쪽 패널에 있는 **데이터 변환** 그룹에서 이러한 작업 및 기타 데이터 전처리 작업을 수행하는 모듈을 찾을 수 있습니다.

**데이터 세트에서 열 선택** 모듈을 사용하여 누락 값이 많은 정규화된 손실을 제외합니다. 그런 다음, **누락 데이터 정리**를 사용하여 누락된 값이 있는 행을 제거합니다. 이렇게 하면 깔끔한 학습 데이터 세트를 만들 수 있습니다.

![데이터 사전 처리](./media/how-to-designer-sample-regression-automobile-price-compare-algorithms/data-processing.png)

## <a name="train-the-model"></a>모델 학습

기계 학습 문제는 다양합니다. 일반적인 기계 학습 작업에는 분류, 클러스터링, 회귀 및 추천 시스템이 포함되며, 각각 다른 알고리즘이 필요할 수 있습니다. 알고리즘 선택은 종종 사용 사례의 요구 사항에 따라 달라집니다. 알고리즘을 선택한 후에는 모델을 보다 정확하게 학습시킬 수 있도록 매개 변수를 조정해야 합니다. 그런 다음, 정확도, 명료도, 효율성 같은 메트릭을 기반으로 모든 모델을 평가해야 합니다.

이 파이프라인의 목표는 자동차 가격을 예측하는 것이고 레이블 열(가격)에 실제 숫자가 포함되어 있으므로 회귀 모델을 선택하는 것이 좋습니다.

여러 알고리즘의 성능을 비교하기 위해 두 가지 비선형 알고리즘인 **향상된 의사 결정 트리 회귀** 및 **의사 결정 포리스트 회귀**를 사용하여 모델을 빌드하는 것입니다. 두 알고리즘 모두 변경할 수 있는 매개 변수가 있지만, 이 샘플은 이 파이프라인에 기본값을 사용합니다.

학습 데이터 세트에 원래 데이터의 70%가 포함되고 테스트 데이터 세트에 원래 데이터의 30%가 포함되도록 **데이터 분할** 모듈을 사용하여 입력 데이터를 임의로 나눕니다.

## <a name="test-evaluate-and-compare-the-models"></a>모델 테스트, 평가 및 비교

이전 섹션에서 설명한 대로 임의로 선택된 두 개의 서로 다른 세트를 사용하여 모델을 학습시킨 후 테스트합니다. 데이터 세트를 분할하고 다양한 데이터 세트로 모델을 학습 및 테스트하여 보다 객관적으로 모델을 평가합니다.

모델을 학습시킨 후에는 **모델 채점**및 **모델 평가** 모듈을 사용하여 예측 결과를 생성하고 모델을 평가합니다. **모델 채점**에서는 학습된 모델을 사용하여 테스트 데이터 세트에 대한 예측을 생성합니다. 그리고 점수를 **모델 평가** 모듈에 전달하여 평가 메트릭을 생성합니다.



결과는 다음과 같습니다.

![결과 비교](./media/how-to-designer-sample-regression-automobile-price-compare-algorithms/result.png)

결과를 보면 **향상된 의사 결정 트리 회귀**를 사용하여 빌드된 모델이 **의사 결정 포리스트 회귀**를 기반으로 빌드된 모델보다 제곱 평균 오차가 낮다는 것을 알 수 있습니다.



## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>다음 단계

디자이너에 사용할 수 있는 다른 샘플을 살펴봅니다.

- [샘플 1 - 회귀: 자동차 가격 예측](how-to-designer-sample-regression-automobile-price-basic.md)
- [샘플 3 - 기능 선택을 사용하여 분류: 수입 예측](how-to-designer-sample-classification-predict-income.md)
- [샘플 4 - 분류: 신용 위험 예측(비용 구분)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [샘플 5 - 분류: 이탈 예측](how-to-designer-sample-classification-churn.md)
- [샘플 6 - 분류: 항공편 지연 예측](how-to-designer-sample-classification-flight-delay.md)
- [샘플 7 - 텍스트 분류: Wikipedia SP 500 데이터 세트](how-to-designer-sample-text-classification.md)
