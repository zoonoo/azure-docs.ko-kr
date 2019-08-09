---
title: 완료 가격 예측
titleSuffix: Azure Machine Learning service
description: 한 줄의 코드를 작성 하지 않고도 자동차의 가격을 예측 하는 기계 학습 모델을 빌드하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 95373bdd58332f49307004067cdd04b9c313b935
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68845959"
---
# <a name="sample-1---regression-predict-price"></a>샘플 1-회귀: 가격 예측

시각적 인터페이스를 사용 하 여 코드를 한 줄도 작성 하지 않고 machine learning 회귀 모델을 빌드하는 방법에 대해 알아봅니다.

이 실험에서는 회귀 변수, 모델, 마 력, 크기 등의 기술 기능에 따라 자동차 가격을 예측 하는 **의사 결정 포리스트** 를 학습 합니다. "어떻게 되나요?" 라는 질문에 답변 하려고 합니다. 이를 회귀 문제 라고 합니다. 그러나이 실험에서 동일한 기본 단계를 적용 하 여 재발, 분류, 클러스터링 등의 모든 유형의 기계 학습 문제를 다룰 수 있습니다.

학습 기계 학습 모델의 기본 단계는 다음과 같습니다.

1. 데이터 가져오기
1. 데이터를 미리 처리 합니다.
1. 모델 학습
1. 모델 평가

다음은 작업 중인 실험의 완료 된 마지막 그래프입니다. 모든 모듈에 대 한 근거를 제공 하므로 사용자에 게 비슷한 결정을 내릴 수 있습니다.

![실험 그래프](media/ui-sample-regression-predict-automobile-price-basic/overall-graph.png)

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. 샘플 1 실험의 **열기** 단추를 선택 합니다.

    ![실험 열기](media/ui-sample-regression-predict-automobile-price-basic/open-sample1.png)

## <a name="get-the-data"></a>데이터 가져오기

이 실험에서는 UCI Machine Learning 리포지토리에서 **자동차 가격 데이터 (원시)** 데이터 집합을 사용 합니다. 데이터 집합에는 제조업체, 모델, 가격, 차량 기능 (예: 실린더 수), MPG 및 보험 위험 점수를 비롯 하 여 자동차에 대 한 정보가 포함 된 26 개의 열이 포함 되어 있습니다. 이 실험의 목표는 자동차의 가격을 예측 하는 것입니다.

## <a name="pre-process-the-data"></a>데이터를 미리 처리 합니다.

기본 데이터 준비 태스크에는 데이터 정리, 통합, 변환, 감소, 분할 또는 양자화이 포함 됩니다. 시각적 인터페이스에서는 왼쪽 패널의 **데이터 변환** 그룹에서 이러한 작업을 수행 하는 모듈과 기타 데이터 전처리 작업을 찾을 수 있습니다.

**데이터 집합에서 열 선택** 모듈을 사용 하 여 누락 값이 많은 정규화 된 패를 제외 합니다. 그런 다음 누락 된 **데이터 정리** 를 사용 하 여 누락 된 값이 있는 행을 제거 합니다. 이렇게 하면 명확한 학습 데이터 집합을 만들 수 있습니다.

![데이터 사전 처리](./media/ui-sample-regression-predict-automobile-price-basic/data-processing.png)

## <a name="train-the-model"></a>모델 학습

기계 학습 문제는 다양 합니다. 일반적인 기계 학습 작업에는 분류, 클러스터링, 회귀 및 추천 시스템이 포함 되며, 각각에는 다른 알고리즘이 필요할 수 있습니다. 알고리즘 선택은 종종 사용 사례의 요구 사항에 따라 달라 집니다. 알고리즘을 선택한 후 보다 정확한 모델을 학습 하려면 해당 매개 변수를 조정 해야 합니다. 그런 다음 정확도, intelligibility 및 효율성과 같은 메트릭에 기반 하 여 모든 모델을 평가 해야 합니다.

이 실험의 목표는 자동차 가격을 예측 하는 것이 고, 레이블 열 (price)에 실제 숫자가 포함 되어 있기 때문에 회귀 모델을 선택 하는 것이 좋습니다. 기능 수가 상대적으로 작으며 (100 미만) 이러한 기능이 스파스가 아닌 경우 의사 결정 경계는 비선형 일 가능성이 높습니다. 따라서이 실험에는 **의사 결정 포리스트 회귀** 를 사용 합니다.

데이터 분할 모듈을 사용 하 여 학습 데이터 집합에 원래 데이터의 70%가 포함 되 고 테스트 데이터 집합에 원래 데이터의 30%가 포함 되도록 **데이터 분할** 모듈을 사용 하 여 입력 데이터를 무작위로 나눕니다.

## <a name="test-evaluate-and-compare"></a>테스트, 평가 및 비교

 데이터 집합을 분할 하 고 다양 한 데이터 집합을 사용 하 여 모델을 학습 하 고 테스트 하 여 모델을 더 목표로 계산 합니다.

모델을 학습 한 후 **모델 점수 매기기** 및 **모델 평가** 모듈을 사용 하 여 예측 결과를 생성 하 고 모델을 평가 합니다.

**모델 점수 매기기** 는 학습 된 모델을 사용 하 여 테스트 데이터 집합에 대 한 예측을 생성 합니다. 결과를 확인 하려면 **모델 점수 매기기** 의 출력 포트를 선택한 다음 **시각화**를 선택 합니다.

![점수 매기기 결과](./media/ui-sample-regression-predict-automobile-price-basic/score-result.png)

그런 다음 점수를 **모델 평가** 모듈에 전달 하 여 평가 메트릭을 생성 합니다. 결과를 확인 하려면 **평가 모델** 의 출력 포트를 선택한 다음 **시각화**를 선택 합니다.

![결과 평가](./media/ui-sample-regression-predict-automobile-price-basic/evaluate-result.png)

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>다음 단계

시각적 인터페이스에 사용할 수 있는 다른 샘플을 탐색 합니다.

- [샘플 2-회귀: 자동차 가격 예측에 대 한 알고리즘 비교](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [샘플 3-분류: 신용 위험 예측](ui-sample-classification-predict-credit-risk-basic.md)
- [샘플 4-분류: 신용 위험 예측 (비용 구분)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [샘플 5-분류: 변동 예측](ui-sample-classification-predict-churn.md)
- [샘플 6-분류: 비행 지연 예측](ui-sample-classification-predict-flight-delay.md)