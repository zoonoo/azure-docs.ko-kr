---
title: '디자이너: 신용 위험 예측 예제'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning designer를 사용 하 여 분류자를 빌드하고 사용자 지정 Python 스크립트를 사용 하 여 신용 위험을 예측 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: bfa50665d427775b8b45d377869787086ca5bf3d
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660053"
---
# <a name="build-a-classifier--use-python-scripts-to-predict-credit-risk-using-azure-machine-learning-designer"></a>Azure Machine Learning designer를 사용 하 여 신용 위험을 예측 하는 Python 스크립트를 사용 하 & 분류자 빌드

**Designer (미리 보기) 샘플 4**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

이 문서에서는 디자이너 (미리 보기)를 사용 하 여 복잡 한 기계 학습 파이프라인을 빌드하는 방법을 보여 줍니다. Python 스크립트를 사용 하 여 사용자 지정 논리를 구현 하 고 여러 모델을 비교 하 여 가장 적합 한 옵션을 선택 하는 방법을 배웁니다.

이 샘플은 신용 기록, 연령 및 신용 카드 수와 같은 크레딧 응용 프로그램 정보를 사용 하 여 신용 위험을 예측 하는 분류자를 학습 합니다. 그러나이 문서의 개념을 적용 하 여 고유한 기계 학습 문제를 다룰 수 있습니다.

이 파이프라인에 대 한 완료 된 그래프는 다음과 같습니다.

[파이프라인의 ![그래프](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png)](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. Sample 4를 클릭 하 여 엽니다.

## <a name="data"></a>데이터

이 샘플에서는 UC Irvine 리포지토리에서 독일어 신용 카드 데이터 집합을 사용 합니다. 20 개의 기능과 레이블이 있는 1000 샘플이 포함 되어 있습니다. 각 샘플은 사람을 나타냅니다. 20 개의 기능에는 숫자 및 범주 기능이 포함 됩니다. 데이터 집합에 대 한 자세한 내용은 [UCI 웹 사이트](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29)를 참조 하세요. 마지막 열은 신용 위험을 나타내며 두 개의 가능한 값, 즉 높은 신용 위험 = 2, 낮은 신용 위험 = 1만 포함 하는 레이블입니다.

## <a name="pipeline-summary"></a>파이프라인 요약

이 파이프라인에서는이 문제를 해결 하기 위해 모델을 생성 하는 두 가지 방법을 비교 합니다.

- 원래 데이터 집합으로 학습 합니다.
- 복제 된 데이터 집합으로 학습 합니다.

두 방법을 모두 사용 하 여 테스트 데이터 집합을 복제와 함께 사용 하 여 결과를 cost 함수에 맞추도록 모델을 평가 합니다. 두 가지 방법, 즉 **2 클래스 지원 벡터 컴퓨터** 와 **2 클래스 승격 의사 결정 트리**를 사용 하 여 두 분류자를 테스트 합니다.

낮은 위험 수준 예제를 high로 잘못 분류 하는 비용은 1이 고 높은 위험 수준 예제를 낮음으로 잘못 분류 비용은 5입니다. **Python 스크립트 실행** 모듈을 사용 하 여이 오 분류 비용을 고려 합니다.

파이프라인의 그래프는 다음과 같습니다.

[파이프라인의 ![그래프](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png)](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="data-processing"></a>데이터 처리

먼저 **메타 데이터 편집기** 모듈을 사용 하 여 열 이름을 추가 하 여 기본 열 이름을 UCI 사이트의 데이터 집합 설명에서 얻은 의미 있는 이름으로 바꿉니다. **메타 데이터 편집기**의 **새 열** 이름 필드에 새 열 이름을 쉼표로 구분 된 값으로 제공 합니다.

그런 다음 위험 예측 모델을 개발 하는 데 사용 되는 학습 및 테스트 집합을 생성 합니다. **데이터 분할** 모듈을 사용 하 여 원래 데이터 집합을 동일한 크기의 학습 및 테스트 집합으로 분할 합니다. 같은 크기의 집합을 만들려면 **첫 번째 출력 데이터 집합 옵션에서 행의 분수** 를 0.7으로 설정 합니다.

### <a name="generate-the-new-dataset"></a>새 데이터 집합 생성

위험을 과소 평가 하는 비용은 매우 높아 다음과 같이 오 분류 비용을 설정 합니다.

- 위험 수준이 높은 경우에는 잘못 분류: 5
- 위험 수준이 낮은 경우 잘못 분류: 1

이 비용 함수를 반영 하려면 새 데이터 집합을 생성 합니다. 새 데이터 집합에서 위험이 높은 각 예제는 5 번 복제 되지만 위험이 낮은 예제의 수는 변경 되지 않습니다. 복제 전에 데이터를 학습 및 테스트 데이터 집합으로 분할 하 여 동일한 행이 두 집합에 있는 것을 방지 합니다.

위험 수준이 높은 데이터를 복제 하려면 python **스크립트 실행** 모듈에이 python 코드를 추가 합니다.

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    df_label_1 = dataframe1[dataframe1.iloc[:, 20] == 1]
    df_label_2 = dataframe1[dataframe1.iloc[:, 20] == 2]

    result = df_label_1.append([df_label_2] * 5, ignore_index=True)
    return result,
```

**Python 스크립트 실행** 모듈은 학습 및 테스트 데이터 집합을 모두 복제 합니다.

### <a name="feature-engineering"></a>기능 엔지니어링

**2 클래스 지원 벡터 컴퓨터** 알고리즘에는 정규화 된 데이터가 필요 합니다. 따라서 **데이터 정규화** 모듈을 사용 하 여 모든 숫자 기능의 범위를 `tanh` 변환으로 정규화 합니다. `tanh` 변환은 값의 전체 분포를 유지 하면서 모든 숫자 기능을 0과 1의 범위 내에 있는 값으로 변환 합니다.

**2 클래스 지원 벡터 컴퓨터** 모듈은 문자열 기능을 처리 하 고, 범주 기능으로 변환한 다음, 값이 0 또는 1 인 이진 기능으로 변환 합니다. 따라서 이러한 기능을 정규화 할 필요가 없습니다.

## <a name="models"></a>모델

2 클래스 SVM ( **Support Vector Machine** ) 및 2 **클래스 승격 된 의사 결정 트리와**두 개의 데이터 집합을 적용 했으므로 총 4 개의 모델을 생성 합니다.

- 원래 데이터로 학습 된 SVM.
- 복제 된 데이터로 학습 된 SVM.
- 원본 데이터로 학습 된 승격 된 의사 결정 트리입니다.
- 복제 된 데이터로 학습 된 승격 된 의사 결정 트리입니다.

이 샘플에서는 표준 데이터 과학 워크플로를 사용 하 여 모델을 만들고 학습 하 고 테스트 합니다.

1. **2 클래스 지원 벡터 컴퓨터** 와 **2 클래스 승격 된 의사 결정 트리**를 사용 하 여 학습 알고리즘을 초기화 합니다.
1. **모델 학습** 을 사용 하 여 데이터에 알고리즘을 적용 하 고 실제 모델을 만듭니다.
1. 테스트 예제를 사용 하 여 점수를 산출 하려면 **모델 점수 매기기** 를 사용 합니다.

다음 다이어그램에서는 원래 및 복제 된 학습 집합을 사용 하 여 두 개의 서로 다른 SVM 모델을 학습 하는이 파이프라인의 일부를 보여 줍니다. 학습 **모델** 은 학습 집합에 연결 되 고 **모델 점수 매기기** 는 테스트 집합에 연결 됩니다.

![파이프라인 그래프](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/score-part.png)

파이프라인의 평가 단계에서 4 개의 각 모델에 대 한 정확도를 계산 합니다. 이 파이프라인의 경우 **모델 평가** 를 사용 하 여 동일한 오 분류 비용을 가진 예를 비교 합니다.

**모델 평가** 모듈은 점수가 매겨진 두 모델에 대 한 성능 메트릭을 계산할 수 있습니다. **모델 평가** 의 한 인스턴스를 사용 하 여 두 개의 SVM 모델을 평가 하 고 **모델 평가** 의 다른 인스턴스를 사용 하 여 승격 된 두 의사 결정 트리 모델을 평가할 수 있습니다.

복제 된 테스트 데이터 집합이 **점수 모델**에 대 한 입력으로 사용 되는지 확인 합니다. 즉, 최종 정확도 점수에는 레이블 가져오기에 대 한 비용이 포함 됩니다.

## <a name="combine-multiple-results"></a>여러 결과 결합

**모델 평가** 모듈은 다양 한 메트릭이 포함 된 단일 행이 있는 테이블을 생성 합니다. 단일 정확도 결과 집합을 만들려면 먼저 **행 추가** 를 사용 하 여 결과를 단일 테이블로 결합 합니다. 그런 다음 **Python 스크립트 실행** 모듈에서 다음 python 스크립트를 사용 하 여 결과 테이블의 각 행에 대 한 모델 이름과 학습 방법을 추가 합니다.

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

파이프라인의 결과를 보려면 **데이터 집합에서 마지막 열 선택** 모듈의 시각화 출력을 마우스 오른쪽 단추로 클릭 합니다.

![출력 시각화](media/how-to-designer-sample-classification-credit-risk-cost-sensitive/sample4-lastselect-1225.png)

첫 번째 열에는 모델을 생성 하는 데 사용 되는 기계 학습 알고리즘이 나열 됩니다.

두 번째 열은 학습 집합의 유형을 나타냅니다.

세 번째 열은 비용에 민감한 정확도 값을 포함 합니다.

이러한 결과를 통해 **2 클래스 지원 벡터 컴퓨터** 를 사용 하 여 생성 되 고 복제 된 학습 데이터 집합에 대해 학습 된 모델에서 가장 높은 정확도를 제공 하는 것을 알 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>다음 단계

디자이너에 사용할 수 있는 다른 샘플을 탐색 합니다.

- [샘플 1-회귀: 자동차 가격 예측](how-to-designer-sample-regression-automobile-price-basic.md)
- [샘플 2-회귀: 자동차 가격 예측에 대 한 알고리즘 비교](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [샘플 3-기능 선택이 포함 된 분류: 수입 예측](how-to-designer-sample-classification-predict-income.md)
- [샘플 5-분류: 변동 예측](service/how-to-designer-sample-classification-churn.md)
- [샘플 6-분류: 비행 지연 예측](how-to-designer-sample-classification-flight-delay.md)
- [샘플 7-텍스트 분류: 위키백과 SP 500 데이터 집합](how-to-designer-sample-text-classification.md)
