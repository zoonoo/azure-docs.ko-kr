---
title: '회귀 분석: 가격을 예측 하 고 알고리즘 비교'
titleSuffix: Azure Machine Learning service
description: 이 문서에서는 시각적 인터페이스를 사용 하 여 코드를 전혀 작성 하지 않고 복잡 한 machine learning 실험을 작성 하는 방법을 보여 줍니다. 학습 및 기술 기능에 따라 자동차의 가격을 예측 하기 위해 여러 회귀 모델을 비교 하는 방법을 알아봅니다
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: aa0a1fc2acdc9687030040c23cdb1781e9529169
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605684"
---
# <a name="sample-2---regression-predict-price-and-compare-algorithms"></a>2-회귀 샘플: 가격을 예측 하 고 알고리즘 비교

시각적 인터페이스를 사용 하 여 코드를 전혀 작성 하지 않고 복잡 한 machine learning 실험을 빌드하는 방법에 알아봅니다. 이 샘플 학습 하 고 해당 기술 기능에 따라 자동차의 가격을 예측 하려면 여러 회귀 모델을 비교 합니다. 사용자 고유의 기계 학습 문제를 처리할 수 있도록이 실험에 대 한 선택 항목에 대 한 근거를 제공 됩니다.

machine learning을 사용한 시작 메시지가 바로, 살펴보겠습니다 수행할 수 있습니다 합니다 [기본 버전](ui-sample-regression-predict-automobile-price-basic.md) 기본 회귀 실험을 보려면이 실험의 합니다.

이 실험에 대 한 완성된 된 그래프는 다음과 같습니다.

[![실험 그래프](media/ui-sample-regression-predict-automobile-price-compare-algorithms/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>전제 조건

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. 선택 된 **열고** 샘플 2 실험에 대 한 단추:

    ![실험을 열으십시오](media/ui-sample-regression-predict-automobile-price-compare-algorithms/open-sample2.png)

## <a name="experiment-summary"></a>실험 요약

실험을 작성 하이 단계 사용 합니다.

1. 데이터를 가져옵니다.
1. 데이터를 전처리 합니다.
1. 모델을 학습 합니다.
1. 테스트, 평가 및 모델을 비교 합니다.

## <a name="get-the-data"></a>데이터 가져오기

이 실험에서 사용 된 **자동차 가격 데이터 (원시)** UCI 기계 학습 리포지토리에서 인 데이터 집합을 합니다. 이 데이터 집합, 제조업체, 모델, 가격, 차량 기능 (예: 실린더 수), MPG, 및 보험 위험 점수를 포함 하 여 자동차에 대 한 정보를 포함 하는 26 열을 포함 합니다. 이 실험의 목적은 자동차의 가격을 예측 하 하는 것입니다.

## <a name="pre-process-the-data"></a>데이터 전처리

기본 데이터 준비 작업에는 데이터 정리, 통합, 변환, 감소 하 고 분할 또는 양자화 포함 됩니다. 시각적 인터페이스에서 이러한 작업 및 다른 데이터 전처리 작업을 수행 하는 모듈을 찾을 수 있습니다 합니다 **데이터 변환** 왼쪽된 패널의 그룹입니다.

이 실험에서 사용 된 **Select Columns in Dataset** 많은 누락 값이 있는 normalized losses 제외할 모듈입니다. 사용 하 여 **누락 데이터 정리** 누락 값이 있는 행을 제거 합니다. 이 학습 데이터 집합을 새로 만들 수 있습니다.

![데이터 사전 처리](media/ui-sample-regression-predict-automobile-price-compare-algorithms/data-processing.png)

## <a name="train-the-model"></a>모델 학습

기계 학습 문제에는 다음이 달라 집니다. 일반적인 기계 학습 작업에는 분류, 클러스터링, 회귀 및 다른 알고리즘을 필요할 수 있으며 각 추천 시스템을 포함 합니다. 종종 선택한 알고리즘 사용 사례 요구 사항에 따라 달라 집니다. 알고리즘을 선택 하 고 나면 더 정확한 모델을 학습 하는 매개 변수를 튜닝 해야 합니다. 그런 다음 효율성, 정확도 및 명확성을 등의 메트릭을 기반으로 하는 모든 모델을 평가 해야 합니다.

이 실험의 목적은 자동차 가격 예측 하 고 레이블 열 (price) 실수를 포함 하기 때문에 회귀 모델은 것이 좋습니다. 의사 결정 경계는 기능 수가 상대적으로 작은 (100 보다 작은) 이며 이러한 기능을 스파스 없습니다를 고려 비선형 할 가능성이 높습니다.

다른 알고리즘의 성능을 비교할 두 비선형 알고리즘을 사용 **승격 된 의사 결정 트리 회귀** 및 **의사 결정 포리스트 회귀**, 모델을 작성 합니다. 두 알고리즘을 변경할 수는 매개 변수가 있지만이 실험에 대 한 기본 값을 사용 합니다.

사용 하 여 합니다 **분할 데이터** 임의로 학습 데이터 집합은 원본 데이터의 70%를 포함 하 고 원래 데이터의 30%를 포함 하는 테스트 데이터 집합 입력된 데이터를 분할 하는 모듈입니다.

## <a name="test-evaluate-and-compare-the-models"></a>테스트, 평가 및 모델 비교

이전 섹션에 설명 된 대로 서로 다른 두 임의로 선택한 데이터 집합 학습 한 다음 모델을 테스트 하려면 사용 합니다. 데이터 집합을 분할 하 고 학습 및 평가 모델의 자세한 목표 하는 모델을 테스트 하려면 다른 데이터 집합을 사용 합니다.

사용 모델을 학습 한 후는 **모델 점수 매기기** 하 고 **모델 평가** 예측된 결과 생성 하 고 모델을 평가 하는 모듈입니다. **모델 점수 매기기** 학습된 된 모델을 사용 하 여 테스트 데이터 집합에 대 한 예측을 생성 합니다. 에서는 점수에 전달한 **모델 평가** 평가 메트릭을 생성 합니다.

이 실험에서 사용 하 여 두 인스턴스의 **모델 평가** 모델의 두 쌍을 비교 합니다.

먼저 두 알고리즘 학습 데이터 집합을 비교 합니다.
둘째, 두 알고리즘 테스트 데이터 집합을 비교 합니다.
결과는 다음과 같습니다.

![결과 비교](media/ui-sample-regression-predict-automobile-price-compare-algorithms/result.png)

이러한 결과 따르면는 모델을 사용 하 여 빌드한 **승격 된 의사 결정 트리 회귀** 제곱 평균 오차 기반 모델 보다는 낮은 루트 **의사 결정 포리스트 회귀**합니다.

두 알고리즘 모두 경우 낮은 오류를 확인 되지 않은 테스트 데이터 집합에 대해 보다 학습 데이터 집합에서

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>다음 단계

시각적 인터페이스에 사용할 수 있는 다른 샘플을 탐색 합니다.

- [1-회귀 샘플: 자동차의 가격 예측](ui-sample-regression-predict-automobile-price-basic.md)
- [3-분류 샘플: 신용 위험 예측](ui-sample-classification-predict-credit-risk-basic.md)
- [4-분류 샘플: (중요 한 비용) 신용 위험 예측](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [5-분류 샘플: 이탈 예측](ui-sample-classification-predict-churn.md)
- [6-분류 샘플: 비행 지연을 예측합니다](ui-sample-classification-predict-flight-delay.md)
