---
title: '시각적 인터페이스 예 #3: 신용 위험을 예측 하는 분류'
titleSuffix: Azure Machine Learning
description: 시각적 인터페이스를 사용 하 여 코드를 한 줄도 작성 하지 않고 기계 학습 분류자를 빌드하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 09/23/2019
ms.openlocfilehash: 4649303b8ee643130b8e254f01bfffbe8ad9eb2b
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693495"
---
# <a name="sample-3---classification-predict-credit-risk"></a>샘플 3-분류: 신용 위험 예측

시각적 인터페이스를 사용 하 여 코드를 한 줄도 작성 하지 않고 기계 학습 분류자를 빌드하는 방법에 대해 알아봅니다. 이 샘플 파이프라인은 신용 기록, 연령 및 신용 카드 수와 같은 크레딧 응용 프로그램 정보에 따라 신용 위험 (높음 또는 낮음)을 예측 하는 **2 클래스 승격 된 의사 결정 트리** 입니다.

질문에 대 한 응답은 이를 분류 문제 라고 합니다. 그러나 동일한 기본 프로세스를 적용 하 여 재발, 분류, 클러스터링 등의 모든 유형의 기계 학습 문제를 다룰 수 있습니다.

이 샘플에 대 한 최종 파이프라인 그래프는 다음과 같습니다.

![파이프라인 그래프](media/how-to-ui-sample-classification-predict-credit-risk-basic/overall-graph.png)

## <a name="prerequisites"></a>전제 조건

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. 샘플 3 파이프라인에 대 한 **열기** 단추를 선택 합니다.

    ![파이프라인 열기](media/how-to-ui-sample-classification-predict-credit-risk-basic/open-sample3.png)

## <a name="related-sample"></a>관련 샘플

[샘플 4-분류: 신용 위험 예측 (비용 중요)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md) 은이 샘플과 동일한 문제를 해결 하는 고급 파이프라인을 제공 합니다. **Python 스크립트 실행** 모듈을 사용 하 여 *비용에 민감한* 분류를 수행 하 고 두 이진 분류 알고리즘의 성능을 비교 하는 방법을 보여 줍니다. 분류 파이프라인을 빌드하는 방법에 대 한 자세한 내용을 보려면이 정보를 참조 하세요.


## <a name="data"></a>데이터

이 샘플에서는 UC Irvine 리포지토리에서 독일어 신용 카드 데이터 집합을 사용 합니다. 20 개의 기능이 포함 된 1000 샘플 및 1 개의 레이블이 포함 되어 있습니다. 각 샘플은 사람을 나타냅니다. 이 기능에는 숫자 및 범주 기능이 포함 됩니다. 범주 기능의 의미는 [UCI 웹 사이트](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29) 를 참조 하세요. 마지막 열은 신용 위험을 나타내며 두 개의 가능한 값, 즉 높은 신용 위험 = 2, 낮은 신용 위험 = 1만 포함 하는 레이블입니다.

## <a name="pipeline-summary"></a>파이프라인 요약

파이프라인을 만들려면 다음 단계를 수행 합니다.

1. 독일어 신용 카드 UCI 데이터 데이터 집합 모듈을 파이프라인 캔버스로 끌어다 놓습니다.
1. 각 열에 대해 의미 있는 이름을 추가할 수 있도록 **메타 데이터 편집** 모듈을 추가 합니다.
1. **데이터 분할** 모듈을 추가 하 여 학습 및 테스트 집합을 만듭니다. 첫 번째 출력 데이터 집합의 행 비율을 0.7로 설정 합니다. 이 설정은 데이터의 70%가 모듈의 왼쪽 포트에 출력 되 고 나머지는 오른쪽 포트에 출력 되도록 지정 합니다. 학습에 왼쪽 데이터 집합을 사용 하 고 테스트에 적합 한 데이터 집합을 사용 합니다.
1. **2 클래스 승격 된 의사 결정 트리** 모듈을 추가 하 여 승격 된 의사 결정 트리 분류자를 초기화 합니다.
1. **모델 학습** 모듈을 추가 합니다. 이전 단계의 분류자를 **학습 모델**의 왼쪽 입력 포트에 연결 합니다. 학습 **모델**의 오른쪽 입력 포트에 학습 집합 ( **분할 데이터**의 왼쪽 출력 포트)을 추가 합니다. **모델 학습** 은 분류자를 학습 합니다.
1. **모델 점수 매기기** 모듈을 추가 하 고 **모델 학습** 모듈을 연결 합니다. 그런 다음 **점수 모델**에 테스트 집합 ( **분할 데이터**의 오른쪽 포트)을 추가 합니다. **점수 모델** 은 예측을 수행 합니다. 해당 출력 포트를 선택 하 여 예측 및 긍정 클래스 확률을 확인할 수 있습니다.
1. **모델 평가** 모듈을 추가 하 고 점수가 매겨진 데이터 집합을 왼쪽 입력 포트에 연결 합니다. 평가 결과를 보려면 **모델 평가** 모듈의 출력 포트를 선택 하 고 **시각화**를 선택 합니다.

## <a name="results"></a>결과

![결과 평가](media/how-to-ui-sample-classification-predict-credit-risk-basic/evaluate-result.png)

평가 결과에서 모델의 인지 c가 0.776 임을 확인할 수 있습니다. 임계값 0.5에서 전체 자릿수는 0.621, 리콜은 0.456, F1 점수는 0.526입니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>다음 단계

시각적 인터페이스에 사용할 수 있는 다른 샘플을 탐색 합니다.

- [샘플 1-회귀: 자동차 가격 예측](how-to-ui-sample-regression-predict-automobile-price-basic.md)
- [샘플 2-회귀: 자동차 가격 예측에 대 한 알고리즘 비교](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [샘플 4-분류: 신용 위험 예측 (비용 구분)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [샘플 5-분류: 변동 예측](how-to-ui-sample-classification-predict-churn.md)
- [샘플 6-분류: 비행 지연 예측](how-to-ui-sample-classification-predict-flight-delay.md)
- [샘플 7-텍스트 분류: 책 리뷰](how-to-ui-sample-text-classification.md)