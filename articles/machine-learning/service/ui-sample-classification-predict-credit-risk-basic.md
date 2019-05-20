---
title: '분류: 신용 위험 예측'
titleSuffix: Azure Machine Learning service
description: 시각적 인터페이스를 사용 하 여 코드를 전혀 작성 하지 않고도 분류자를 학습 하는 컴퓨터를 빌드하는 방법에 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: f37c945758cfbd03889d79acf764e7f67022267a
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65789411"
---
# <a name="sample-3---classification-predict-credit-risk"></a>3-분류 샘플: 신용 위험 예측

시각적 인터페이스를 사용 하 여 코드를 전혀 작성 하지 않고도 분류자를 학습 하는 컴퓨터를 빌드하는 방법에 알아봅니다. 이 샘플은 학습을 **2 클래스 승격 된 의사 결정 트리** 예측할 신용 위험 (높거나 낮음) 신용 기록, 수명 및 신용 카드 번호와 같은 신용 응용 프로그램 정보에 따라 합니다.

"어느?" 질문에 답변 하려고 하기 때문에 이 분류 문제를 라고 합니다. 그러나 회귀, 분류, 클러스터링 및 등 든 관계 없이 모든 유형의 기계 학습 문제를 해결 하려면 동일한 기본 프로세스를 적용할 수 있습니다.

이 실험에 대 한 완성된 된 그래프는 다음과 같습니다.

![실험 그래프](media/ui-sample-classification-predict-credit-risk-basic/overall-graph.png)

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. 선택 된 **열고** 샘플 3 실험에 대 한 단추:

    ![실험을 열으십시오](media/ui-sample-classification-predict-credit-risk-basic/open-sample3.png)

## <a name="related-sample"></a>관련된 샘플

[4-분류 샘플: 신용 위험 예측 (비용/소문자 구분)](ui-sample-classification-predict-credit-risk-cost-sensitive.md) 이 실험으로 동일한 문제를 해결 하는 고급 실험을 제공 합니다. 수행 하는 방법을 보여 줍니다 _중요 한 비용_ 사용 하 여 분류를 **Python 스크립트 실행** 모듈 및 두 가지 이진 분류 알고리즘의 성능 비교 합니다. 분류 실험을 빌드하는 방법에 대 한 자세한 않으려면를 참조 하십시오.

## <a name="data"></a>Data

독일 신용 카드 데이터 집합에서는 UC Irvine 리포지토리에서 사용합니다.
데이터 집합에는 20 기능 및 1 개 레이블 1,000 샘플이 들어 있습니다. 각 샘플 사람을 나타냅니다. 숫자와 범주 기능을 포함 하는 기능입니다. 참조 된 [UCI 웹 사이트](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29) 범주 기능의 의미에 대 한 합니다. 마지막 열은 레이블 신용 위험을 나타내는 고 두 개의 가능한 값: 높은 신용 위험 2 및 신용 위험이 낮은 = = 1입니다.

## <a name="experiment-summary"></a>실험 요약

실험을 만들려면 다음이 단계를 따릅니다.

1. 독일어 신용 카드 UCI 데이터 데이터 집합 모듈을 실험 캔버스로 끌어옵니다.
1. 추가 된 **메타 데이터 편집** 모듈 각 열에 대 한 의미 있는 이름을 추가할 수 있도록 합니다.
1. 추가 된 **분할 데이터** 모듈을 학습 및 테스트 집합을 만들 합니다. 행의 비율을 0.7로 첫 번째 출력 데이터 집합의 설정 합니다. 이 설정은 데이터의 70% 되도록 출력 모듈의 왼쪽 포트에 쓰거나 나머지 오른쪽 포트를 지정 합니다. 학습 및 테스트에 적합 한 왼쪽된 데이터 집합을 사용합니다.
1. 추가 된 **2 클래스 승격 된 의사 결정 트리** 승격 된 의사 결정 트리 분류자를 초기화 하는 모듈입니다.
1. 추가 된 **모델 학습** 모듈입니다. 이전 단계에서 분류자의 왼쪽된 입력된 포트에 연결 합니다 **모델 학습**합니다. 학습 집합을 추가 (왼쪽 출력 포트를 **데이터 분할**)의 오른쪽 입력된 포트에는 **모델 학습**합니다. 합니다 **모델 학습** 분류자를 학습 합니다.
1. 추가 **모델 점수 매기기** 모듈 연결 합니다 **모델 학습** 를 모듈. 추가한 다음 테스트 집합 (의 오른쪽 포트를 **데이터 분할**)에 **모델 점수 매기기**합니다. 합니다 **모델 점수 매기기** 예측을 확인 합니다. 예측 및 확률은 긍정 클래스를 보려면 출력 포트를 선택할 수 있습니다.
1. 추가 **모델 평가** 모듈의 왼쪽된 입력된 포트에 점수가 매겨진된 데이터 집합을 연결 합니다. 평가 결과 보려면의 출력 포트를 선택 합니다 **모델 평가** 모듈과 선택 **시각화**합니다.

전체 실험 그래프는 다음과 같습니다.

![실험 그래프](media/ui-sample-classification-predict-credit-risk-basic/overall-graph.png)

## <a name="results"></a>결과

![결과 평가](media/ui-sample-classification-predict-credit-risk-basic/evaluate-result.png)

평가 결과 모델의 AUC 0.776 임을 볼 수 있습니다. 임계값에 도달한 경우 0.5, 전체 자릿수 0.621, 회수 0.456, 이며 F1 점수는 0.526 합니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>다음 단계

시각적 인터페이스에 사용할 수 있는 다른 샘플을 탐색 합니다.

- [1-회귀 샘플: 자동차의 가격 예측](ui-sample-regression-predict-automobile-price-basic.md)
- [2-회귀 샘플: 자동차 가격 예측 알고리즘 비교](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [4-분류 샘플: (중요 한 비용) 신용 위험 예측](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [5-분류 샘플: 이탈 예측](ui-sample-classification-predict-churn.md)