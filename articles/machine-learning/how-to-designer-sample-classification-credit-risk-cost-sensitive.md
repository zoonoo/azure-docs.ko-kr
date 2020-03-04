---
title: 'Designer: 신용 위험 예측 예제'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 디자이너를 사용하여 분류자를 빌드하고 사용자 지정 Python 스크립트를 통해 신용 위험을 예측합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: ed8ee9b1c711ee0056377154379b8df56e0785df
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964603"
---
# <a name="build-a-classifier--use-python-scripts-to-predict-credit-risk-using-azure-machine-learning-designer"></a>Azure Machine Learning 디자이너를 사용하여 분류자 빌드 및 Python 스크립트를 통해 신용 위험 예측

**디자이너(미리 보기) 샘플 4**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

이 문서에서는 디자이너(미리 보기)를 사용하여 복잡한 기계 학습 파이프라인을 빌드하는 방법을 보여 줍니다. Python 스크립트를 사용하여 사용자 지정 논리를 구현하고 여러 모델을 비교하여 최적의 옵션을 선택하는 방법에 대해 알아봅니다.

이 샘플은 신용 기록, 연령, 신용 카드 수와 같은 신용 애플리케이션 정보를 사용하여 신용 위험을 예측하도록 분류자를 학습시킵니다. 그러나 사용자 고유의 기계 학습 문제를 해결하기 위해 이 문서의 개념을 적용할 수 있습니다.

이 파이프라인에 대한 완전한 그래프는 다음과 같습니다.

[![파이프라인 그래프](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png)](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. 샘플 4를 클릭하여 엽니다.

## <a name="data"></a>데이터

이 샘플에서는 UC Irvine 리포지토리의 독일 신용 카드 데이터 세트를 사용합니다. 여기에는 20개의 기능과 하나의 레이블이 있는 1,000개의 샘플이 포함되어 있습니다. 각 샘플은 사람을 나타냅니다. 20개의 기능에는 숫자 및 범주별 기능이 포함되어 있습니다. 데이터 세트에 대한 자세한 내용은 [UCI 웹 사이트](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29)를 참조하세요. 마지막 열은 신용 위험을 나타내며, 높은 신용 위험 = 2 및 낮은 신용 위험 = 1의 두 가지 가능한 값만 포함하는 레이블입니다.

## <a name="pipeline-summary"></a>파이프라인 요약

이 파이프라인에서는 이 문제를 해결하기 위한 모델을 생성하는 두 가지 방법을 비교합니다.

- 원본 데이터 세트를 사용하여 학습합니다.
- 복제된 데이터 세트를 사용하여 학습합니다.

두 가지 방법 모두에서는 결과가 비용 함수와 일치하는지 확인하기 위해 복제된 테스트 데이터 세트를 사용하여 모델을 평가합니다. 두 가지 방법을 모두 사용하여 **2클래스 Support Vector Machine** 및 **2클래스 향상된 의사 결정 트리**라는 두 개의 분류자를 테스트합니다.

낮은 위험 예제를 높은 위험으로 잘못 분류하는 비용은 1이며, 높은 위험 예제를 낮은 위험으로 잘못 분류하는 비용은 5입니다. 이러한 오분류 비용은 **Python 스크립트 실행** 모듈을 사용하여 설명합니다.

파이프라인의 그래프는 다음과 같습니다.

[![파이프라인 그래프](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png)](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="data-processing"></a>데이터 처리

먼저, 기본 열 이름을 UCI 사이트의 데이터 세트 설명에서 가져온 더 의미 있는 이름으로 바꾸기 위해 **메타데이터 편집기** 모듈을 사용하여 열 이름을 추가합니다. **메타데이터 편집기**의 **새 열** 이름 필드에서 새 열 이름을 쉼표로 구분된 값으로 제공합니다.

다음으로, 위험 예측 모델을 개발하는 데 사용되는 학습 및 테스트 세트를 생성합니다. **데이터 분할** 모듈을 사용하여 원본 데이터 세트를 동일한 크기의 학습 및 테스트 세트로 분할합니다. 동일한 크기의 세트를 만들려면 **첫 번째 출력 데이터 세트의 행 비율** 옵션을 0.7로 설정합니다.

### <a name="generate-the-new-dataset"></a>새 데이터 세트 생성

위험을 과소 평가하는 비용이 높으므로 오분류 비용을 다음과 같이 설정합니다.

- 높은 위험 사례를 낮은 위험으로 잘못 분류하는 경우: 5
- 낮은 위험 사례를 높은 위험으로 잘못 분류하는 경우: 1

이 비용 함수를 반영하려면 새 데이터 세트를 생성합니다. 새 데이터 세트에서 위험이 높은 각 예제는 5번 복제되지만, 위험이 낮은 예제의 수는 변경되지 않습니다. 동일한 행이 두 세트 모두에 포함되어 있지 않도록 복제하기 전에 데이터를 학습 및 테스트 데이터 세트로 분할합니다.

위험이 높은 데이터를 복제하려면 이 Python 코드를 **Python 스크립트 실행** 모듈에 배치합니다.

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    df_label_1 = dataframe1[dataframe1.iloc[:, 20] == 1]
    df_label_2 = dataframe1[dataframe1.iloc[:, 20] == 2]

    result = df_label_1.append([df_label_2] * 5, ignore_index=True)
    return result,
```

**Python 스크립트 실행** 모듈에서 학습 및 테스트 데이터 세트를 모두 복제합니다.

### <a name="feature-engineering"></a>기능 엔지니어링

**2클래스 Support Vector Machine** 알고리즘에는 정규화된 데이터가 필요합니다. 따라서 **데이터 정규화** 모듈을 사용하여 `tanh` 변환을 통해 모든 숫자 기능의 범위를 정규화합니다. `tanh` 변환은 값의 전체 분포를 유지하면서 모든 숫자 기능을 0과 1 범위 내의 값으로 변환합니다.

**2클래스 Support Vector Machine** 모듈은 문자열 기능을 처리하여 범주별 기능으로 변환한 다음, 값이 0 또는 1인 이진 기능으로 변환합니다. 따라서 이러한 기능을 정규화할 필요가 없습니다.

## <a name="models"></a>모델

두 개의 분류자(**2클래스 SVM(Support Vector Machine)** 및 **2클래스 향상된 의사 결정 트리**)와 두 개의 데이터 세트를 적용했으므로 다음과 같이 총 네 개의 모델이 생성됩니다.

- 원본 데이터를 사용하여 학습된 SVM
- 복제된 데이터를 사용하여 학습된 SVM
- 원본 데이터를 사용하여 학습된 향상된 의사 결정 트리
- 복제된 데이터를 사용하여 학습된 향상된 의사 결정 트리

이 샘플에서는 표준 데이터 과학 워크플로를 사용하여 모델을 만들고, 학습시키고, 테스트합니다.

1. **2클래스 Support Vector Machine** 및 **2클래스 향상된 의사 결정 트리**를 사용하여 학습 알고리즘을 초기화합니다.
1. **모델 학습**을 사용하여 알고리즘을 데이터에 적용하고 실제 모델을 만듭니다.
1. **모델 점수 매기기**에서 테스트 예제를 사용하여 점수를 생성합니다.

다음 다이어그램에서는 원본 학습 세트와 복제된 학습 세트를 사용하여 서로 다른 두 개의 SVM 모델을 학습시키는 이 파이프라인의 일부를 보여 줍니다. **모델 학습**은 학습 세트에 연결되고, **모델 점수 매기기**는 테스트 세트에 연결됩니다.

![파이프라인 그래프](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/score-part.png)

파이프라인의 평가 단계에서는 네 가지 모델 각각의 정확도를 계산합니다. 이 파이프라인의 경우 **모델 평가**를 사용하여 오분류 비용이 동일한 예제를 비교합니다.

**모델 평가** 모듈은 두 개의 점수 매기기 모델에 대한 성능 메트릭을 계산할 수 있습니다. 따라서 하나의 **모델 평가** 인스턴스를 사용하여 두 개의 SVM 모델을 평가하고, 다른 **모델 평가** 인스턴스를 사용하여 두 개의 향상된 의사 결정 트리 모델을 평가할 수 있습니다.

복제된 테스트 데이터 세트는 **모델 점수 매기기**의 입력으로 사용됩니다. 즉, 최종 정확도 점수에는 레이블이 잘못 지정되는 비용이 포함됩니다.

## <a name="combine-multiple-results"></a>여러 결과 결합

**모델 평가** 모듈은 다양한 메트릭이 포함된 단일 행의 테이블을 생성합니다. 단일 정확도 결과 세트를 만들려면 먼저 **행 추가**를 사용하여 결과를 단일 테이블로 결합합니다. 그런 다음, **Python 스크립트 실행** 모듈에서 다음 Python 스크립트를 사용하여 결과 테이블의 각 행에 대한 모델 이름과 학습 방법을 추가합니다.

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    new_cols = pd.DataFrame(
            columns=["Algorithm","Training"],
            data=[
                ["SVM", "weighted"],
                ["SVM", "unweighted"],
                ["Boosted Decision Tree","weighted"],
                ["Boosted Decision Tree","unweighted"]
            ])

    result = pd.concat([new_cols, dataframe1], axis=1)
    return result,
```

## <a name="results"></a>결과

파이프라인 결과를 보려면 마우스 오른쪽 단추로 마지막 **데이터 세트에서 열 선택** 모듈의 출력 시각화를 클릭합니다.

![출력 시각화](media/how-to-designer-sample-classification-credit-risk-cost-sensitive/sample4-lastselect-1225.png)

첫 번째 열에는 모델을 생성하는 데 사용되는 기계 학습 알고리즘이 나열됩니다.

두 번째 열은 학습 세트의 형식을 나타냅니다.

세 번째 열에는 비용에 민감한 정확도 값이 포함됩니다.

이러한 결과를 통해 **2클래스 Support Vector Machine**을 사용하여 만들어지고 복제된 학습 데이터 세트에서 학습된 모델에서 가장 적합한 정확도를 제공할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>다음 단계

디자이너에 사용할 수 있는 다른 샘플을 살펴봅니다.

- [샘플 1 - 회귀: 자동차 가격 예측](how-to-designer-sample-regression-automobile-price-basic.md)
- [샘플 2 - 회귀: 자동차 가격 예측 알고리즘에 대한 비교](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [샘플 3 - 기능 선택을 사용하여 분류: 수입 예측](how-to-designer-sample-classification-predict-income.md)
- [샘플 5 - 분류: 이탈 예측](how-to-designer-sample-classification-churn.md)
- [샘플 6 - 분류: 비행 지연 예측](how-to-designer-sample-classification-flight-delay.md)
- [샘플 7 - 텍스트 분류: Wikipedia SP 500 데이터 세트](how-to-designer-sample-text-classification.md)
