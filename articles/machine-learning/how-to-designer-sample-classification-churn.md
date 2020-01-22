---
title: '디자이너: 예측 변동 예'
titleSuffix: Azure Machine Learning
description: 이 분류 예제에 따라 Azure Machine Learning designer를 사용 하 여 향상 된 의사 결정 트리에서 변동 & 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.reviewer: sgilley
ms.date: 12/25/2019
ms.openlocfilehash: 88f688608a0ae3d435699362f9326c7c02d494a4
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76311119"
---
# <a name="use-boosted-decision-tree-to-predict-churn-with-azure-machine-learning-designer"></a>승격 된 의사 결정 트리를 사용 하 여 Azure Machine Learning 디자이너에서 변동 예측

**디자이너 샘플 5**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

디자이너를 사용 하 여 코드를 한 줄도 작성 하지 않고 복잡 한 기계 학습 파이프라인을 빌드하는 방법에 대해 알아봅니다.

이 파이프라인은 CRM (고객 관계 관리) 시스템-고객 변동에 대 한 일반 작업을 예측 하기 위해 2 **클래스 승격 된 의사 결정 트리** 분류자 2 개를 학습 합니다. 데이터 값과 레이블은 여러 데이터 원본에 걸쳐 분할 되 고 익명화 고객 정보를 제공 합니다. 그러나 디자이너를 사용 하 여 데이터 집합을 결합 하 고 가려진 값을 사용 하 여 모델을 학습 시킬 수 있습니다.

"무엇 인가요?" 라는 질문에 답변 하려고 하기 때문에 이를 분류 문제 라고 하지만,이 샘플에 표시 된 것과 동일한 논리를 적용 하 여 재발, 분류, 클러스터링 등의 모든 유형의 기계 학습 문제를 다룰 수 있습니다.

이 파이프라인에 대 한 완료 된 그래프는 다음과 같습니다.

![파이프라인 그래프](./media/how-to-designer-sample-classification-churn/pipeline-graph.png)

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. 샘플 5를 클릭 하 여 엽니다. 

## <a name="data"></a>데이터

이 파이프라인에 대 한 데이터는 KDD 컵 2009입니다. 5만 행 및 230 기능 열이 있습니다. 이 작업은 이러한 기능을 사용 하는 고객을 위한 변동, 욕구 및 업 판매를 예측 하는 것입니다. 데이터 및 작업에 대 한 자세한 내용은 [Kdd 웹 사이트](https://www.kdd.org/kdd-cup/view/kdd-cup-2009)를 참조 하세요.

## <a name="pipeline-summary"></a>파이프라인 요약

디자이너의이 샘플 파이프라인에는 CRM (고객 관계 관리)에 대 한 일반 작업 인 변동, 욕구 및 업 판매의 이진 분류자 예측이 표시 됩니다.

첫째, 몇 가지 간단한 데이터 처리를 수행 합니다.

- 원시 데이터 집합에 누락 값이 많이 있습니다. 누락 된 **데이터 정리** 모듈을 사용 하 여 누락 된 값을 0으로 바꿉니다.

    ![데이터 집합 정리](media/how-to-designer-sample-classification-churn/sample5-dataset-1225.png)

- 기능 및 해당 변동 (churn)은 서로 다른 데이터 집합에 있습니다. **열 추가** 모듈을 사용 하 여 레이블 열을 기능 열에 추가 합니다. 첫 번째 열인 **Col1**은 레이블 열입니다. 시각화 결과에서 데이터 집합이 불균형을 볼 수 있습니다. 긍정 예 (+ 1) 보다 더 음수 (-1) 예제가 있습니다. **SMOTE** 모듈을 사용 하 여 나중에 표시 되는 사례를 늘립니다.

    ![열 데이터 집합 추가](./media/how-to-designer-sample-classification-churn/sample5-addcol-1225.png)



- **데이터 분할** 모듈을 사용 하 여 데이터 집합을 학습 및 테스트 집합으로 분할할 수 있습니다.

- 그런 다음 기본 매개 변수와 함께 승격 된 의사 결정 트리 이진 분류자를 사용 하 여 예측 모델을 작성 합니다. 작업 당 하나의 모델, 즉 각 작업을 예측 하 고, 욕구 하 고, 변동 하는 모델을 만듭니다.

- 파이프라인의 오른쪽 부분에서는 **SMOTE** 모듈을 사용 하 여 긍정적인 예제의 비율을 늘립니다. SMOTE 비율은 100로 설정 되어 긍정적인 예를 두 배로 설정 합니다. SMOTE 모듈이 [SMOTE module reference0](algorithm-module-reference/smote.md)에서 작동 하는 방식에 대해 자세히 알아보세요.

## <a name="results"></a>결과

**모델 평가** 모듈의 출력을 시각화 하 여 테스트 집합에 대 한 모델의 성능을 확인 합니다. 

![결과 평가](./media/how-to-designer-sample-classification-churn/sample5-evaluate-1225.png)

 **임계값** 슬라이더를 이동 하 고 이진 분류 태스크에 대 한 메트릭 변경을 볼 수 있습니다. 

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>다음 단계

디자이너에 사용할 수 있는 다른 샘플을 탐색 합니다.

- [샘플 1-회귀: 자동차 가격 예측](how-to-designer-sample-regression-automobile-price-basic.md)
- [샘플 2-회귀: 자동차 가격 예측에 대 한 알고리즘 비교](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [샘플 3-기능 선택이 포함 된 분류: 수입 예측](how-to-designer-sample-classification-predict-income.md)
- [샘플 4-분류: 신용 위험 예측 (비용 구분)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [샘플 6-분류: 비행 지연 예측](how-to-designer-sample-classification-flight-delay.md)
- [샘플 7-텍스트 분류: 위키백과 SP 500 데이터 집합](how-to-designer-sample-text-classification.md)
