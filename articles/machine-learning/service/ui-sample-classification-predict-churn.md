---
title: '분류: 변동, 욕구를 및 상향 판매를 예측 합니다. '
titleSuffix: Azure Machine Learning service
description: 이 시각적 인터페이스 샘플 실험에는 이진 분류자 예측을 변동, 고객 관계 관리 (CRM)에 대 한 일반적인 작업을 보여 줍니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 42724f5fcb3101015cef0d218a3d548f349646be
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65785830"
---
# <a name="sample-5---classification-predict-churn-appetency-and-up-selling"></a>5-분류 샘플: 변동, 욕구를 및 상향 판매를 예측 합니다. 

시각적 인터페이스를 사용 하 여 코드를 전혀 작성 하지 않고 복잡 한 machine learning 실험을 빌드하는 방법에 알아봅니다.

이 실험을 학습 세 개 **2 클래스 승격 된 의사 결정 트리** 고객 관계 관리 (CRM) 시스템에 대 한 일반적인 작업을 예측 하는 분류자: 변동, 욕구를 및 상향 판매 합니다. 그러나 데이터 값 및 레이블을 여러 데이터 원본에서 분할 및 고객 정보 익명화를 변경 하면 여전히 시각적 인터페이스를 데이터 집합을 결합 하 여 암호화 된 값을 사용 하 여 모델을 학습 합니다.

"어느?" 질문에 답변 하려고 하기 때문에 이 분류 문제를 라고 합니다. 그러나 회귀, 분류, 클러스터링 및 등 든 관계 없이 모든 유형의 기계 학습 문제를 해결 하려면이 실험에서는 동일한 단계를 적용할 수 있습니다.

이 실험에 대 한 완성된 된 그래프는 다음과 같습니다.

![실험 그래프](./media/ui-sample-classification-predict-churn/experiment-graph.png)

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. 선택 된 **열려** 샘플 5 실험에 대 한 단추입니다.

    ![실험을 열으십시오](media/ui-sample-classification-predict-churn/open-sample5.png)

## <a name="data"></a>Data

이 실험에 대 한 사용 데이터는 KDD Cup 2009에서입니다. 데이터 집합에 50,000 개 행과 230 기능 열입니다. 작업 변동, 욕구를 및 이러한 기능을 사용 하는 고객에 대 한 상향 판매를 예측 하는 것입니다. 데이터 및 작업에 대 한 자세한 내용은 참조는 [KDD 웹 사이트](https://www.kdd.org/kdd-cup/view/kdd-cup-2009)합니다.

## <a name="experiment-summary"></a>실험 요약

이 시각적 인터페이스 샘플 실험에는 이진 분류자 예측을 변동, 욕구를 및 상향 판매, 고객 관계 관리 (CRM)에 대 한 일반적인 작업을 보여 줍니다.

먼저 몇 가지 간단한 데이터 처리를 수행합니다.

- 원시 데이터 집합에 누락 값에 많은 포함 되어 있습니다. 사용 하 여 합니다 **누락 데이터 정리** 0 값을 모듈을 대체 합니다.

    ![데이터 집합 정리](./media/ui-sample-classification-predict-churn/cleaned-dataset.png)

- 기능 및 해당 이탈 욕구에 있으며 상향 판매 레이블 서로 다른 데이터 집합입니다. 사용 된 **열 추가** 기능 열을 레이블 열을 추가 하는 모듈입니다. 첫 번째 열 **Col1**, 레이블 열이 있습니다. 열에서 나머지 **Var1**, **Var2**, 등의 기능 열입니다.

    ![열 데이터 집합 추가](./media/ui-sample-classification-predict-churn/added-column1.png)

- 사용 하 여는 **분할 데이터** 학습 데이터 집합을 분할할 및 테스트 집합에는 모듈입니다.

    그런 다음를 사용 하 여 승격 된 의사 결정 트리 이진 분류자 기본 매개 변수를 사용 하 여 예측 모델을 작성 합니다. 즉, 하나의 모델 상향 판매, 욕구를 및 이탈을 예측 하는 각 아니라 태스크 당 하나의 모델을 빌드합니다.

## <a name="results"></a>결과

출력을 시각화 합니다 **모델 평가** 테스트 집합에서 모델의 성능을 보려면 모듈입니다. ROC 곡선을 상향 판매 태스크에 대 한 모델을 임의 모델 보다 더 잘 않은 것을 보여 줍니다. 곡선 (AUC) 아래 영역에서 0.857를입니다. 임계값에 도달한 경우 0.5, 전체 자릿수 0.7, 회수 0.463, 이며 F1 점수는 0.545 합니다.

![결과 평가](./media/ui-sample-classification-predict-churn/evaluate-result.png)

 이동할 수는 **임계값** 슬라이더 및 이진 분류 작업에 대 한 메트릭을 변경 참조 하십시오.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>다음 단계

시각적 인터페이스에 사용할 수 있는 다른 샘플을 탐색 합니다.

- [1-회귀 샘플: 자동차의 가격 예측](ui-sample-regression-predict-automobile-price-basic.md)
- [2-회귀 샘플: 자동차 가격 예측 알고리즘 비교](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [3-분류 샘플: 신용 위험 예측](ui-sample-classification-predict-credit-risk-basic.md)
- [4-분류 샘플: (중요 한 비용) 신용 위험 예측](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
