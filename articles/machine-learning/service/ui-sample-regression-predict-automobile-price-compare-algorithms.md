---
title: '시각적 인터페이스 예제 #3: 회귀를 가격과 비교 하는 알고리즘'
titleSuffix: Azure Machine Learning
description: 이 문서에서는 시각적 인터페이스를 사용 하 여 코드를 한 줄도 작성 하지 않고 복잡 한 기계 학습 실험을 빌드하는 방법을 보여 줍니다. 여러 회귀 모델을 학습 하 고 비교 하 여 기술 기능에 따라 자동차 가격을 예측 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 2c45ccf55b1adde7150dee17fd562b24ee4777ba
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997095"
---
# <a name="sample-2---regression-predict-price-and-compare-algorithms"></a>샘플 2-회귀: 가격 예측 및 알고리즘 비교

시각적 인터페이스를 사용 하 여 코드를 한 줄도 작성 하지 않고 복잡 한 기계 학습 실험을 빌드하는 방법을 알아봅니다. 이 샘플은 여러 회귀 모델을 학습 하 고 비교 하 여 기술 기능에 따라 자동차 가격을 예측 합니다. 사용자 고유의 기계 학습 문제를 다룰 수 있도록이 실험에서 선택한 항목에 대 한 근거를 제공 합니다.

기계 학습을 시작 하는 경우이 실험의 [기본 버전](ui-sample-regression-predict-automobile-price-basic.md) 을 살펴보면 기본적인 회귀 실험을 볼 수 있습니다.

이 실험의 완료 된 그래프는 다음과 같습니다.

[![실험 그래프](media/ui-sample-regression-predict-automobile-price-compare-algorithms/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. 샘플 2 실험의 **열기** 단추를 선택 합니다.

    ![실험 열기](media/ui-sample-regression-predict-automobile-price-compare-algorithms/open-sample2.png)

## <a name="experiment-summary"></a>실험 요약

실험을 빌드하는 데 다음 단계를 사용 합니다.

1. 데이터를 가져옵니다.
1. 데이터를 미리 처리 합니다.
1. 모델을 학습 합니다.
1. 모델을 테스트, 평가 및 비교 합니다.

## <a name="get-the-data"></a>데이터 가져오기

이 실험에서는 UCI Machine Learning 리포지토리에서 **자동차 가격 데이터 (원시)** 데이터 집합을 사용 합니다. 이 데이터 집합에는 제조업체, 모델, 가격, 차량 기능 (예: 실린더 수), MPG 및 보험 위험 점수를 비롯 하 여 자동차에 대 한 정보가 포함 된 26 개의 열이 포함 되어 있습니다. 이 실험의 목표는 자동차의 가격을 예측 하는 것입니다.

## <a name="pre-process-the-data"></a>데이터를 미리 처리 합니다.

기본 데이터 준비 태스크에는 데이터 정리, 통합, 변환, 감소, 분할 또는 양자화이 포함 됩니다. 시각적 인터페이스에서는 왼쪽 패널의 **데이터 변환** 그룹에서 이러한 작업을 수행 하는 모듈과 기타 데이터 전처리 작업을 찾을 수 있습니다.

이 실험에서는 **데이터 집합의 열 선택** 모듈을 사용 하 여 누락 값이 많은 정규화 된 패를 제외 합니다. 그런 다음 누락 된 **데이터 정리** 를 사용 하 여 누락 된 값이 있는 행을 제거 합니다. 이렇게 하면 명확한 학습 데이터 집합을 만들 수 있습니다.

![데이터 사전 처리](media/ui-sample-regression-predict-automobile-price-compare-algorithms/data-processing.png)

## <a name="train-the-model"></a>모델 학습

기계 학습 문제는 다양 합니다. 일반적인 기계 학습 작업에는 분류, 클러스터링, 회귀 및 추천 시스템이 포함 되며, 각각에는 다른 알고리즘이 필요할 수 있습니다. 알고리즘 선택은 종종 사용 사례의 요구 사항에 따라 달라 집니다. 알고리즘을 선택한 후 보다 정확한 모델을 학습 하려면 해당 매개 변수를 조정 해야 합니다. 그런 다음 정확도, intelligibility 및 효율성과 같은 메트릭에 기반 하 여 모든 모델을 평가 해야 합니다.

이 실험의 목표는 자동차 가격을 예측 하는 것이 고, 레이블 열 (price)에 실제 숫자가 포함 되어 있기 때문에 회귀 모델을 선택 하는 것이 좋습니다. 기능 수가 상대적으로 작으며 (100 미만) 이러한 기능이 스파스가 아닌 경우 의사 결정 경계는 비선형 일 가능성이 높습니다.

다양 한 알고리즘의 성능을 비교 하기 위해 모델을 작성 하는 데 사용할 수 있는 두 가지 비선형 알고리즘, **의사 결정 트리 회귀** 및 **의사 결정 포리스트 회귀**를 사용 합니다. 두 알고리즘 모두 변경할 수 있는 매개 변수를 갖지만이 실험에 기본값을 사용 합니다.

데이터 분할 모듈을 사용 하 여 학습 데이터 집합에 원래 데이터의 70%가 포함 되 고 테스트 데이터 집합에 원래 데이터의 30%가 포함 되도록 **데이터 분할** 모듈을 사용 하 여 입력 데이터를 무작위로 나눕니다.

## <a name="test-evaluate-and-compare-the-models"></a>모델 테스트, 평가 및 비교

이전 섹션에 설명 된 대로 두 가지 무작위로 선택 된 데이터 집합을 사용 하 여 모델을 학습 한 다음 테스트 합니다. 데이터 집합을 분할 하 고 다양 한 데이터 집합을 사용 하 여 모델을 학습 하 고 테스트 하 여 모델을 더 목표로 계산 합니다.

모델을 학습 한 후 **모델 점수 매기기** 및 **모델 평가** 모듈을 사용 하 여 예측 결과를 생성 하 고 모델을 평가 합니다. **모델 점수 매기기** 는 학습 된 모델을 사용 하 여 테스트 데이터 집합에 대 한 예측을 생성 합니다. 그런 다음 점수를 전달 하 여 계산 메트릭을 생성 하는 **모델을 평가** 합니다.

이 실험에서는 **모델 계산** 의 두 인스턴스를 사용 하 여 두 개의 모델 쌍을 비교 합니다.

먼저 학습 데이터 집합의 두 알고리즘을 비교 합니다.
두 번째는 테스트 데이터 집합에서 두 알고리즘을 비교 하는 것입니다.
결과는 다음과 같습니다.

![결과 비교](media/ui-sample-regression-predict-automobile-price-compare-algorithms/result.png)

이러한 결과는 **승격 된 의사 결정 트리 회귀** 로 작성 된 모델에 **의사 결정 포리스트 회귀**를 기반으로 작성 된 모델 보다 낮은 근본 평균 오차 오류가 있음을 보여 줍니다.

두 알고리즘 모두 학습 데이터 집합에서 보이지 않는 테스트 데이터 집합 보다 낮은 오류가 발생 합니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>다음 단계

시각적 인터페이스에 사용할 수 있는 다른 샘플을 탐색 합니다.

- [샘플 1-회귀: 자동차 가격 예측](ui-sample-regression-predict-automobile-price-basic.md)
- [샘플 3-분류: 신용 위험 예측](ui-sample-classification-predict-credit-risk-basic.md)
- [샘플 4-분류: 신용 위험 예측 (비용 구분)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [샘플 5-분류: 변동 예측](ui-sample-classification-predict-churn.md)
- [샘플 6-분류: 비행 지연 예측](ui-sample-classification-predict-flight-delay.md)
