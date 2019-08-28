---
title: '시각적 인터페이스 예제 #5: 변동 + 욕구 + 위쪽 판매를 예측 하는 분류'
titleSuffix: Azure Machine Learning service
description: 이 시각적 인터페이스 샘플 실험에서는 CRM (고객 관계 관리)에 대 한 일반 작업 인 변동의 이진 분류자 예측을 보여 줍니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: a2eabdd601fcf350d2076d33882e3d90255fee92
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2019
ms.locfileid: "69016459"
---
# <a name="sample-5---classification-predict-churn-appetency-and-up-selling"></a>샘플 5-분류: 변동, 욕구 및 업 판매 예측 

시각적 인터페이스를 사용 하 여 코드를 한 줄도 작성 하지 않고 복잡 한 기계 학습 실험을 빌드하는 방법을 알아봅니다.

이 실험에서는 세 가지 **2 클래스 승격 된 의사 결정 트리** 분류자를 학습 하 여 CRM (고객 관계 관리) 시스템에 대 한 일반 작업 (예: 변동, 욕구 및 업 판매)을 예측 합니다. 데이터 값과 레이블은 여러 데이터 원본에 걸쳐 분할 되 고 익명화 고객 정보를 제공 합니다. 그러나 여전히 시각적 인터페이스를 사용 하 여 데이터 집합을 결합 하 고 스크램블 된 값을 사용 하 여 모델을 학습 시킬 수 있습니다.

"무엇 인가요?" 라는 질문에 답변 하려고 합니다. 이를 분류 문제 라고 합니다. 그러나이 실험에서 동일한 단계를 적용 하 여 재발, 분류, 클러스터링 등의 모든 유형의 기계 학습 문제를 다룰 수 있습니다.

이 실험의 완료 된 그래프는 다음과 같습니다.

![실험 그래프](./media/ui-sample-classification-predict-churn/experiment-graph.png)

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. 샘플 5 실험의 **열기** 단추를 선택 합니다.

    ![실험 열기](media/ui-sample-classification-predict-churn/open-sample5.png)

## <a name="data"></a>data

이 실험에 사용 하는 데이터는 KDD 컵 2009입니다. 데이터 집합에는 5만 개의 행과 230 기능 열이 있습니다. 이 작업은 이러한 기능을 사용 하는 고객을 위한 변동, 욕구 및 업 판매를 예측 하는 것입니다. 데이터 및 작업에 대 한 자세한 내용은 [Kdd 웹 사이트](https://www.kdd.org/kdd-cup/view/kdd-cup-2009)를 참조 하세요.

## <a name="experiment-summary"></a>실험 요약

이 시각적 인터페이스 샘플 실험에서는 CRM (고객 관계 관리)에 대 한 일반 작업 인 변동, 욕구 및 업 판매의 이진 분류자 예측을 보여 줍니다.

첫째, 몇 가지 간단한 데이터 처리를 수행 합니다.

- 원시 데이터 집합에는 누락 된 값이 많이 포함 되어 있습니다. 누락 된 **데이터 정리** 모듈을 사용 하 여 누락 된 값을 0으로 바꿉니다.

    ![데이터 집합 정리](./media/ui-sample-classification-predict-churn/cleaned-dataset.png)

- 기능과 해당 하는 변동, 욕구 및 업 판매 레이블은 서로 다른 데이터 집합에 있습니다. **열 추가** 모듈을 사용 하 여 레이블 열을 기능 열에 추가 합니다. 첫 번째 열인 **Col1**은 레이블 열입니다. **Var1**, **Var2**등의 나머지 열은 기능 열입니다.

    ![열 데이터 집합 추가](./media/ui-sample-classification-predict-churn/added-column1.png)

- **데이터 분할** 모듈을 사용 하 여 데이터 집합을 학습 및 테스트 집합으로 분할 합니다.

    그런 다음 기본 매개 변수와 함께 승격 된 의사 결정 트리 이진 분류자를 사용 하 여 예측 모델을 작성 합니다. 작업별, 욕구 및 변동 (code churn)을 예측 하는 하나의 모델인 모델을 하나씩 작성 합니다.

## <a name="results"></a>결과

**모델 평가** 모듈의 출력을 시각화 하 여 테스트 집합에 대 한 모델의 성능을 확인 합니다. 위로 판매 된 작업의 경우 ROC 곡선은 모델이 임의 모델 보다 성능이 더 우수 함을 보여 줍니다. 곡선의 면적 ()은 0.857입니다. 임계값 0.5에서 전체 자릿수는 0.7, 리콜은 0.463, F1 점수는 0.545입니다.

![결과 평가](./media/ui-sample-classification-predict-churn/evaluate-result.png)

 **임계값** 슬라이더를 이동 하 고 이진 분류 태스크에 대 한 메트릭 변경을 볼 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>다음 단계

시각적 인터페이스에 사용할 수 있는 다른 샘플을 탐색 합니다.

- [샘플 1-회귀: 자동차 가격 예측](ui-sample-regression-predict-automobile-price-basic.md)
- [샘플 2-회귀: 자동차 가격 예측에 대 한 알고리즘 비교](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [샘플 3-분류: 신용 위험 예측](ui-sample-classification-predict-credit-risk-basic.md)
- [샘플 4-분류: 신용 위험 예측 (비용 구분)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [샘플 6-분류: 비행 지연 예측](ui-sample-classification-predict-flight-delay.md)
