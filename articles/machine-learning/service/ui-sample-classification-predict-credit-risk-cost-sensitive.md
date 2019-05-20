---
title: '분류: (중요 한 비용) 신용 위험 예측'
titleSuffix: Azure Machine Learning service
description: 이 문서에서는 복잡 한 machine learning 실험 시각적 인터페이스를 사용 하 여 빌드하는 방법을 보여 줍니다. 사용자 지정 Python 스크립트를 구현 하 고 가장 적합 한 옵션을 선택 하는 여러 모델을 비교 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: d714756c19b94eafc40cc0dbeffbc07704e8f94e
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65787811"
---
# <a name="sample-4---classification-predict-credit-risk-cost-sensitive"></a>4-분류 샘플: (중요 한 비용) 신용 위험 예측

이 문서에서는 복잡 한 machine learning 실험 시각적 인터페이스를 사용 하 여 빌드하는 방법을 보여 줍니다. Python 스크립트를 사용 하 여 사용자 지정 논리를 구현 하 고 가장 적합 한 옵션을 선택 하는 여러 모델을 비교 하는 방법을 알아봅니다.

이 샘플에는 신용 기록, 수명 및 신용 카드 번호와 같은 신용 응용 프로그램 정보를 사용 하 여 신용 위험 예측 분류자를 학습 합니다. 그러나 사용자 고유의 기계 학습 문제를 해결 하려면이 문서의 개념을 적용할 수 있습니다.

machine learning을 사용한 시작 메시지가 바로, 살펴보겠습니다 수행할 수 있습니다 합니다 [기본 분류자 샘플](ui-sample-classification-predict-credit-risk-basic.md) 첫 번째입니다.

이 실험에 대 한 완성된 된 그래프는 다음과 같습니다.

[![실험 그래프](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. 선택 된 **열고** 샘플 4 실험에 대 한 단추:

    ![실험을 열으십시오](media/ui-sample-classification-predict-credit-risk-cost-sensitive/open-sample4.png)

## <a name="data"></a>Data

독일 신용 카드 데이터 집합에서는 UC Irvine 리포지토리에서 사용합니다. 이 데이터 집합에는 20 기능 및 1 개 레이블 1,000 샘플이 들어 있습니다. 각 샘플 사람을 나타냅니다. 20 개의 기능이 숫자와 범주 기능을 포함 합니다. 참조 된 [UCI 웹 사이트](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29) 데이터 집합에 대 한 자세한 내용은 합니다. 마지막 열은 레이블 신용 위험을 나타내는 고 두 개의 가능한 값: 높은 신용 위험 2 및 신용 위험이 낮은 = = 1입니다.

## <a name="experiment-summary"></a>실험 요약

이 실험에서이 문제를 해결 하는 모델 생성에 대 한 두 가지 방법을 비교 합니다.

- 원래 데이터 집합을 사용 하 여 학습 합니다.
- 복제 된 데이터 집합을 사용 하 여 학습 합니다.

두 방법 모두를 사용 하 여 비용 함수를 사용 하 여 결과 정렬 되어 있는지 확인 하려면 복제를 사용 하 여 테스트 데이터 집합을 사용 하 여 모델 평가 합니다. 두 방법 모두를 사용 하 여 두 분류자 테스트: **2 클래스 Support Vector Machine** 하 고 **2 클래스 승격 된 의사 결정 트리**합니다.

높음 위험 예제를 비용은 1 이며 위험 수준이 높은 예제를 낮은 비용은 5입니다. 사용 하 여는 **Python 스크립트 실행** 이 오 분류 비용에 대 한 계정에 모듈입니다.

실험 그래프는 다음과 같습니다.

[![실험 그래프](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="data-processing"></a>데이터 처리

사용 하 여 시작 합니다 **메타 데이터 편집기** UCI 사이트에서 데이터 집합 설명에서 가져온 기본 열 이름을 더 의미 있는 이름을 바꾸려면 열 이름을 추가 하는 모듈입니다. 새 열 이름을 쉼표로 구분 된 값으로 제공 합니다 **새 열** 의 이름 필드는 **메타 데이터 편집기**합니다.

다음으로 학습을 생성 하 고 테스트 집합 위험 예측 모델을 개발 하는 데 사용 합니다. 사용 하 여 원래 데이터 집합을 같은 크기의 학습 및 테스트 집합으로 분할 합니다 **분할 데이터** 모듈입니다. 동일한 크기의 집합을 만들려면 설정 합니다 **첫 번째 출력 데이터 집합의 행 분수** 0.5 하는 옵션입니다.

### <a name="generate-the-new-dataset"></a>새 데이터 집합 생성

위험을 과소 평가의 비용이 높은 이기 때문에 이와 같은 오 분류 비용을 설정 했습니다.

- 낮은 위험으로 잘못 분류 하는 위험 수준이 높은 경우: 5
- 높은 위험으로 잘못 분류 하는 위험이 낮은 경우: 1

이 비용 함수를 반영 하기 위해 새 데이터 집합을 생성 합니다. 새 데이터 집합에서 위험 수준이 높은 각 예제는 5 번 복제 되지만 낮은 위험 예제 수가 변경 되지 않습니다. 데이터 집합 모두에 같은 행에 표시 되지 않게 하려면 복제 하기 전에 학습 및 테스트 데이터 집합으로 분할 합니다.

이 Python 코드를 설명 하겠습니다에 위험 수준이 높은 데이터를 복제 하는 **Python 스크립트 실행** 모듈:

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    df_label_1 = dataframe1[dataframe1.iloc[:, 20] == 1]
    df_label_2 = dataframe1[dataframe1.iloc[:, 20] == 2]

    result = df_label_1.append([df_label_2] * 5, ignore_index=True)
    return result,
```

합니다 **Python 스크립트 실행** 모듈 학습 및 테스트 데이터 집합을 복제 합니다.

### <a name="feature-engineering"></a>기능 엔지니어링

합니다 **2 클래스 Support Vector Machine** 알고리즘을 사용 하려면 정규화 된 데이터입니다. 하므로 사용 합니다 **Normalize-data** 모듈을 사용 하 여 모든 숫자 기능의 범위를 정규화를 `tanh` 변환. `tanh` 변환 하는 값 0과 1의 범위 내 값의 전체 분포를 유지 하면서 모든 숫자 기능을 변환 합니다.

합니다 **2 클래스 Support Vector Machine** 모듈 값이 0 또는 1 인 다음에 이진 기능과 범주 기능을 변환 하는 문자열 기능을 처리 합니다. 되므로 이러한 기능을 정규화 할 필요가 없습니다.

## <a name="models"></a>모델

두 분류자 적용 되므로 **2 클래스 Support Vector Machine** (SVM) 및 **2 클래스 승격 된 의사 결정 트리**, 두 개의 데이터 집합을 사용할 수도, 총 네 가지 모델의 생성 및:

- 원래 데이터를 사용 하 여 SVM 학습합니다.
- SVM 학습 데이터를 복제 합니다.
- 승격 된 의사 결정 트리는 원래 데이터를 사용 하 여 학습합니다.
- 승격 된 의사 결정 트리를 사용 하 여 학습 데이터를 복제 합니다.

표준 실험적 워크플로 만들기, 학습 및 테스트 모델을 사용 합니다.

1. 학습 알고리즘을 사용 하 여 초기화 **2 클래스 Support Vector Machine** 하 고 **2 클래스 승격 된 의사 결정 트리**합니다.
1. 사용 하 여 **모델 학습** 실제 모델을 만들고 데이터에 알고리즘을 적용 합니다.
1. 사용 하 여 **모델 점수 매기기** 테스트 예제를 사용 하 여 점수를 생성 합니다.

다음 다이어그램은이 실험에서 두 가지 SVM 모델을 학습에 사용 되는 원본 및 복제 된 학습 집합의 일부를 보여 줍니다. **모델 학습** 학습 집합에 연결 되어 및 **모델 점수 매기기** 테스트 집합에 연결 되어 있습니다.

![실험 그래프](media/ui-sample-classification-predict-credit-risk-cost-sensitive/score-part.png)

실험의 평가 단계에서는 각 네 가지 모델의 정확도 계산 했습니다. 이 실험에서는 사용 하 여 **모델 평가** 비용을 동일한 오 분류에 있는 예제를 비교 합니다.

합니다 **모델 평가** 모듈에는 점수 매긴된 두 모델에 대 한 성능 메트릭을 계산할 수 있습니다. 하므로 하나의 인스턴스만 사용할 **모델 평가** 의 다른 인스턴스 및 두 가지 SVM 모델을 평가 하 **모델 평가** 두 승격 된 의사 결정 트리 모델을 평가 합니다.

복제 된 테스트 데이터 집합에 대 한 입력으로 사용 됩니다 **모델 점수 매기기**합니다. 즉, 최종 정확도 점수를 사용 하는 레이블이 잘못 된 가져오기에 대 한 비용을 포함 합니다.

## <a name="combine-multiple-results"></a>여러 결과 결합 합니다.

합니다 **모델 평가** 모듈에서 다양 한 메트릭을 포함 하는 단일 행이 있는 테이블을 생성 합니다. 단일 정확도 결과 집합을 만들려면 먼저 사용 **행 추가** 을 단일 테이블로 결과 결합 합니다. 그런 다음 다음 Python 스크립트를 사용 합니다 **Python 스크립트 실행** 결과의 테이블에 있는 모델 이름 및 각 행에 대 한 학습 방법을 추가 모듈:

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

실험의 결과 보려면 마지막 시각화 출력 단추로 **Select Columns in Dataset** 모듈입니다.

![출력을 시각화](media/ui-sample-classification-predict-credit-risk-cost-sensitive/result.png)

첫 번째 열 기계 학습 모델을 생성 하는 데 사용 되는 알고리즘을 나열 합니다.
두 번째 열에는 학습 집합의 유형을 나타냅니다.
세 번째 열에는 비용에 민감한 정확도 값을 포함합니다.

이러한 결과 볼 수 있습니다 사용 하 여 만든 모델에서 가장 높은 정확도 제공 되도록 **2 클래스 Support Vector Machine** 복제 된 학습 데이터 집합에서 학습 하 고 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>다음 단계

시각적 인터페이스에 사용할 수 있는 다른 샘플을 탐색 합니다.

- [1-회귀 샘플: 자동차의 가격 예측](ui-sample-regression-predict-automobile-price-basic.md)
- [2-회귀 샘플: 자동차 가격 예측 알고리즘 비교](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [3-분류 샘플: 신용 위험 예측](ui-sample-classification-predict-credit-risk-basic.md)
- [5-분류 샘플: 이탈 예측](ui-sample-classification-predict-churn.md)
