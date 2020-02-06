---
title: 'Designer: 이탈 예측 예제'
titleSuffix: Azure Machine Learning
description: 이 분류 예제에 따라 Azure Machine Learning 디자이너 및 향상된 의사 결정 트리를 사용하여 이탈을 예측해 보세요.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: sgilley
ms.date: 12/25/2019
ms.openlocfilehash: 701bf186080ef627ef4621e959b281fd58aa4132
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963371"
---
# <a name="use-boosted-decision-tree-to-predict-churn-with-azure-machine-learning-designer"></a>향상된 의사 결정 트리를 사용하여 Azure Machine Learning 디자이너에서 변동 예측

**디자이너(미리 보기) 샘플 5**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

코드를 한 줄도 작성하지 않고 디자이너(미리 보기)를 사용하여 복잡한 기계 학습 파이프라인을 빌드하는 방법에 대해 알아봅니다.

이 파이프라인은 CRM(고객 관계 관리) 시스템의 일반 작업인 고객 이탈을 예측하도록 **2클래스 향상된 의사 결정 트리** 분류자 2개를 학습시킵니다. 고객 정보를 익명화하기 위해 데이터 값과 레이블이 여러 데이터 원본에 걸쳐 분할되고 암호화됩니다. 하지만 여전히 디자이너에서 데이터 세트를 결합하고 가려진 값을 사용하여 모델을 학습시킬 수 있습니다.

"어떤 것?"이라는 질문의 답을 찾으려는 것이기 때문에 이를 분류 문제라고 부르지만, 이 샘플과 동일한 논리를 적용하여 회귀, 분류, 클러스터링 등 모든 유형의 기계 학습 문제를 해결할 수 있습니다.

다음은 이 파이프라인의 완성된 그래프입니다.

![파이프라인 그래프](./media/how-to-designer-sample-classification-churn/pipeline-graph.png)

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. 샘플 5를 클릭하여 엽니다. 

## <a name="data"></a>데이터

이 파이프라인의 데이터 원본은 KDD 컵 2009입니다. 행 50,000개와 기능 열 230개가 있습니다. 작업은 이러한 기능을 사용하는 고객의 이탈, 욕구 상향 판매를 예측하는 것입니다. 데이터 및 작업에 대한 자세한 내용은 [KDD 웹 사이트](https://www.kdd.org/kdd-cup/view/kdd-cup-2009)를 참조하세요.

## <a name="pipeline-summary"></a>파이프라인 요약

디자이너의 이 샘플 파이프라인에서는 CRM(고객 관계 관리)의 일반 작업인 고객 이탈, 욕구 및 상향 판매의 이진 분류자 예측을 보여줍니다.

먼저, 몇 가지 간단한 데이터 처리를 수행합니다.

- 원시 데이터 세트에 누락된 값이 많습니다. **누락된 데이터 정리** 모듈을 사용하여 누락된 값을 0으로 바꿉니다.

    ![데이터 세트 정리](media/how-to-designer-sample-classification-churn/sample5-dataset-1225.png)

- 기능과 해당 이탈은 서로 다른 데이터 세트에 있습니다. **열 추가** 모듈을 사용하여 기능 열에 레이블 열을 추가합니다. 첫 번째 열 **Col1**은 레이블 열입니다. 시각화 결과에서 데이터 세트가 불균형적이라는 것을 볼 수 있습니다. 긍정 예제(+1)보다 부정적인(-1) 예제가 훨씬 많습니다. 나중에 **SMOTE** 모듈을 사용하여 소외된 사례를 더 늘릴 예정입니다.

    ![열 데이터 세트 추가](./media/how-to-designer-sample-classification-churn/sample5-addcol-1225.png)



- **데이터 분할** 모듈을 사용하여 데이터 세트를 학습 및 테스트 세트로 분할합니다.

- 그런 다음, 승격된 의사 결정 트리 이진 분류자를 기본 매개 변수와 함께 사용하여 예측 모델을 빌드합니다. 작업당 모델 하나, 즉, 상향 판매, 욕구, 고객 이탈을 예측하는 모델을 각각 하나씩 만듭니다.

- 파이프라인의 오른쪽 부분에서는 **SMOTE** 모듈을 사용하여 긍정적인 예제의 비율을 늘립니다. 긍정적인 예제를 두 배로 늘리기 위해 SMOTE 비율은 100으로 설정됩니다. [SMOTE 모듈 참조0](algorithm-module-reference/smote.md)에서 SMOTE 모듈의 작동 원리에 대해 자세히 알아보세요.

## <a name="results"></a>결과

**모델 평가** 모듈의 출력을 시각화하여 테스트 세트에 대한 모델의 성능을 확인합니다. 

![결과 평가](./media/how-to-designer-sample-classification-churn/sample5-evaluate-1225.png)

 **임계값** 슬라이더를 이동하여 이진 분류 작업의 메트릭 변화를 볼 수 있습니다. 

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>다음 단계

디자이너에 사용할 수 있는 다른 샘플을 살펴봅니다.

- [샘플 1 - 회귀: 자동차 가격 예측](how-to-designer-sample-regression-automobile-price-basic.md)
- [샘플 2 - 회귀: 자동차 가격 예측 알고리즘에 대한 비교](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [샘플 3 - 기능 선택을 사용하여 분류: 수입 예측](how-to-designer-sample-classification-predict-income.md)
- [샘플 4 - 분류: 신용 위험 예측(비용 구분)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [샘플 6 - 분류: 항공편 지연 예측](how-to-designer-sample-classification-flight-delay.md)
- [샘플 7 - 텍스트 분류: Wikipedia SP 500 데이터 세트](how-to-designer-sample-text-classification.md)
